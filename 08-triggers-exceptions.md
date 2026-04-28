# 08 - Triggers and Exception Handling (Detailed)

## Learning goals
- Build bulk-safe triggers using handler pattern.
- Use context variables correctly.
- Design exception handling for robust automation.

## Trigger basics
```apex
trigger AccountTrigger on Account (before insert, before update) {
    if (Trigger.isBefore && Trigger.isInsert) {
        AccountTriggerHandler.beforeInsert(Trigger.new);
    }
    if (Trigger.isBefore && Trigger.isUpdate) {
        AccountTriggerHandler.beforeUpdate(Trigger.new, Trigger.oldMap);
    }
}
```
What this snippet does:
- Routes trigger entry points to handler methods by event/context.
- Keeps trigger body thin and pushes business logic into a dedicated class.

### Trigger syntax and events (explicit)
```apex
trigger TriggerName on ObjectName (
    before insert, before update, before delete,
    after insert, after update, after delete, after undelete
) {
    // trigger logic
}
```
What this snippet does:
- Shows full trigger declaration syntax with all core lifecycle events.

Trigger types:
- **Before triggers**: update/validate values before save.
- **After triggers**: work with committed record IDs and related operations.

## Trigger context variables
- `Trigger.new`, `Trigger.old`
- `Trigger.newMap`, `Trigger.oldMap`
- `Trigger.isInsert`, `Trigger.isUpdate`, `Trigger.isDelete`
- `Trigger.isBefore`, `Trigger.isAfter`
- `Trigger.size`

### Context variables deep dive
#### `Trigger.new`
- Type: `List<sObject>`
- Available in: `before insert`, `before update`, `after insert`, `after update`, `after undelete`
- Use for looping through current (new) record values.

```apex
for (Account acc : Trigger.new) {
    if (Trigger.isBefore && Trigger.isInsert && String.isBlank(acc.Description)) {
        acc.Description = 'Default description';
    }
}
```
What this snippet does:
- Demonstrates safe mutation of incoming rows in `before insert`.
- Uses context guards and bulk iteration instead of single-record logic.

#### `Trigger.old`
- Type: `List<sObject>`
- Available in: `before update`, `after update`, `before delete`, `after delete`
- Use for comparing previous values.

```apex
for (Account oldAcc : Trigger.old) {
    System.debug('Previous name: ' + oldAcc.Name);
}
```
What this snippet does:
- Iterates prior-state records for comparison/logging in update/delete contexts.

#### `Trigger.newMap`
- Type: `Map<Id, sObject>`
- Available in: `before update`, `after insert`, `after update`, `after undelete`
- Best for direct Id-based lookup of new values.

```apex
for (Id accId : Trigger.newMap.keySet()) {
    Account newAcc = (Account)Trigger.newMap.get(accId);
    System.debug('New name: ' + newAcc.Name);
}
```
What this snippet does:
- Performs Id-keyed lookups of new record values using `newMap`.

#### `Trigger.oldMap`
- Type: `Map<Id, sObject>`
- Available in: `before update`, `after update`, `before delete`, `after delete`
- Best for old-vs-new comparisons.

```apex
for (Account newAcc : (List<Account>)Trigger.new) {
    Account oldAcc = (Account)Trigger.oldMap.get(newAcc.Id);
    if (oldAcc != null && oldAcc.Industry != newAcc.Industry) {
        System.debug('Industry changed from ' + oldAcc.Industry + ' to ' + newAcc.Industry);
    }
}
```
What this snippet does:
- Compares old and new values per record using `oldMap` to detect meaningful field changes.

Quick rule:
- Need current values -> `Trigger.new` / `Trigger.newMap`
- Need previous values -> `Trigger.old` / `Trigger.oldMap`

## Handler class pattern
```apex
public with sharing class AccountTriggerHandler {
    public static void beforeInsert(List<Account> newRows) {
        for (Account a : newRows) {
            if (String.isBlank(a.Name)) a.Name = 'Auto Generated';
        }
    }

    public static void beforeUpdate(List<Account> newRows, Map<Id, Account> oldMap) {
        for (Account a : newRows) {
            Account oldRow = oldMap.get(a.Id);
            if (oldRow != null && oldRow.Industry != a.Industry) {
                // Example place for validation/business rules
            }
        }
    }
}
```
What this snippet does:
- Implements handler pattern with separated `beforeInsert` and `beforeUpdate` methods.
- Centralizes validation/defaulting logic outside trigger declaration.

## Trigger best practices
- One trigger per object.
- No SOQL/DML in loops.
- Avoid recursion (use static guard if needed).
- Keep logic in classes, not inside trigger body.
- Do not assume trigger execution order when multiple triggers exist on the same object.

### Bulk-safe trigger pattern (single-record anti-pattern vs correct loop)
Avoid this pattern:
```apex
// Anti-pattern: only first record in Trigger.new is handled
Account firstRow = Trigger.new[0];
firstRow.Description = 'Updated';
```
What this snippet does:
- Highlights non-bulk-safe behavior that ignores all records except index 0.

Use this pattern:
```apex
for (Account acc : Trigger.new) {
    if (Trigger.isBefore && Trigger.isInsert) {
        acc.Description = 'Updated from Trigger';
    }
}
```
What this snippet does:
- Shows the correct bulk-safe pattern for updating every row in trigger context.

## Order of execution awareness
- Validation rules, before triggers, after triggers, workflow/flows, roll-up updates, commit.
- Always test side effects where multiple automations exist.

## Order of execution (detailed exam view)
Use this as a practical sequence for exam reasoning (platform internals evolve over releases).

1. Load original record (or initialize new record on insert).
2. Apply new field values from request.
3. Run before-save flows.
4. Execute before triggers.
5. Run system validation and custom validation rules.
6. Save record (not yet committed).
7. Execute after triggers.
8. Run assignment rules (Lead/Case when applicable).
9. Run auto-response rules (when applicable).
10. Execute workflow rules.
11. If workflow field updates occur, update record.
12. Re-run before/after triggers once more if workflow field updates changed the record.
13. Execute processes/flows on that record as applicable.
14. Execute escalation/entitlement style rules where applicable.
15. Recalculate roll-up summary and cross-object calculations.
16. Propagate updates to affected parent/grandparent records.
17. Evaluate criteria-based sharing.
18. Commit transaction and run post-commit logic (email, async jobs, etc.).

## Recursion handling patterns (critical)
Recursion happens when automation updates a record and causes itself (or related automation) to run again.

Common sources:
- `after update` trigger performs DML on the same object.
- Trigger updates a record that a flow also updates (or vice versa).
- Parent-child updates repeatedly trigger each other.

### Anti-pattern
```apex
trigger AccountAfterUpdate on Account (after update) {
    for (Account acc : Trigger.new) {
        acc.Description = 'Updated'; // cannot safely update Trigger.new in after trigger
    }
    update Trigger.new; // causes recursion risk and bad pattern
}
```
What this snippet does:
- Demonstrates recursion-prone anti-pattern: mutating and updating `Trigger.new` in `after` context.

### Pattern 1: Prefer before trigger for same-record updates
```apex
trigger AccountBeforeUpdate on Account (before update) {
    for (Account acc : Trigger.new) {
        Account oldAcc = Trigger.oldMap.get(acc.Id);
        if (oldAcc != null && oldAcc.Industry != acc.Industry) {
            acc.Description = 'Industry changed';
        }
    }
}
```
What this snippet does:
- Uses `before update` for same-record derived updates, avoiding extra DML and recursion risk.

### Pattern 2: Change detection + static guard
```apex
public class AccountTriggerRecursionGuard {
    public static Boolean isRunning = false;
}
```
What this snippet does:
- Defines a static transaction-scoped guard flag to prevent re-entry loops.

```apex
trigger AccountAfterUpdate on Account (after update) {
    if (AccountTriggerRecursionGuard.isRunning) return;
    AccountTriggerRecursionGuard.isRunning = true;
    try {
        List<Account> toUpdate = new List<Account>();
        for (Account acc : Trigger.new) {
            Account oldAcc = Trigger.oldMap.get(acc.Id);
            if (oldAcc != null && oldAcc.Rating != acc.Rating) {
                toUpdate.add(new Account(Id = acc.Id, Description = 'Rating changed'));
            }
        }
        if (!toUpdate.isEmpty()) update toUpdate;
    } finally {
        AccountTriggerRecursionGuard.isRunning = false;
    }
}
```
What this snippet does:
- Applies change detection and static guard to run follow-up DML safely once per transaction path.

### Pattern 3: Single-owner rule
- Keep one automation owner per business rule (either Flow or Trigger).
- Avoid splitting the same field update logic across both.
- Document ownership so future changes do not reintroduce loops.

Quick checklist before shipping trigger logic:
1. Did we compare `oldMap` vs `new`?
2. Can this be moved to `before` trigger for same-record updates?
3. Do we need a static guard?
4. Are Flow and Trigger updating the same fields?

## Exceptions in Apex
System-defined:
- `DmlException`, `QueryException`, `NullPointerException`, `CalloutException`, etc.

Custom exception:
```apex
public class BusinessRuleException extends Exception {}
```
What this snippet does:
- Defines a custom exception type for domain-specific rule violations.

Handling pattern:
```apex
try {
    // business operation
} catch (DmlException dmle) {
    System.debug('DML failed: ' + dmle.getMessage());
} catch (Exception ex) {
    System.debug('Unexpected failure: ' + ex.getMessage());
}
```
What this snippet does:
- Shows layered exception handling: specific `DmlException` first, then fallback `Exception`.

## Common mistakes
- Querying inside trigger loop.
- Updating same object in after trigger without recursion guard.
- Swallowing exceptions without actionable logging.

## Official references
- [Apex Triggers](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers.htm)
- [Trigger Context Variables](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers_context_variables.htm)
- [Order of Execution (Salesforce Architect)](https://architect.salesforce.com/diagrams/reference-architectures/order-of-execution)
- [Bulk Apex Triggers](https://trailhead.salesforce.com/content/learn/modules/apex_triggers/apex_triggers_bulk)
- [Exception Statements](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_exception_statements.htm)
- [Apex Trigger Best Practices to Avoid Recursion](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers_bestpract.htm)

## Practice set
1. Build `before insert/update` trigger validating Account naming rules.
2. Add recursion-safe update logic for a derived field.
3. Throw custom exception from service class for invalid state transition and test it.
4. Explain where workflow field updates can cause trigger logic to run again.
