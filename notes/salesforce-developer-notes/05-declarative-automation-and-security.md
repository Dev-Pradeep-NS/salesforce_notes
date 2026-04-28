# 05 - Declarative Automation and Security (Detailed)

## Learning goals
- Choose between declarative automation and Apex.
- Understand order-of-execution risks when multiple automations run.
- Apply Apex and Visualforce security techniques expected for PD1.

## Declarative vs programmatic automation
Prefer declarative tools when the requirement is understandable, maintainable, and supported by configuration.

Prefer Apex when:
- Logic is complex or algorithmic.
- Logic must be reused across trigger, UI, API, and async contexts.
- Transaction control is required.
- Integration behavior or custom error contracts are required.
- Bulk behavior must be carefully optimized.

Exam mindset: Salesforce usually wants the simplest maintainable platform feature, not Apex by default.

## Flow
Flow is the primary declarative automation tool.

Common types:
- **Record-triggered flow:** runs when records are created, updated, or deleted.
- **Screen flow:** guides users through UI steps.
- **Autolaunched flow:** runs without UI, often invoked by other automation.
- **Scheduled-triggered flow:** runs on a schedule.

Use Flow when:
- Admins should own the business process.
- Logic is mostly create/update/decision/orchestration.
- The process benefits from visual maintenance.

Use Apex instead when:
- Complex collection processing is required.
- Logic needs interfaces, service layers, or reusable domain patterns.
- Limits are hard to control declaratively.
- The operation requires advanced exception handling or callout architecture.

## Validation rules
Validation rules prevent invalid data from being saved.

Use them when:
- A rule applies consistently to user and automation updates.
- The error should appear at save time.
- The condition is field/formula based.

Remember:
- Validation rules run during save order.
- They can break Apex tests unless test data satisfies them.
- They should provide user-friendly error messages.

## Approval processes
Approval processes route records for approval based on criteria.

Use them when:
- A record needs formal review.
- Approvers, lock behavior, and approval/rejection actions matter.
- The process is business-governed rather than code-governed.

## Workflow rules and Process Builder
Workflow Rules and Process Builder are legacy automation tools. New development should generally use Flow, but PD1 candidates should recognize them in older orgs and order-of-execution scenarios.

Know at a high level:
- Workflow field updates can cause records to be saved again.
- Process Builder can invoke actions and create/update records.
- Legacy automation can interact with triggers and flows.

## Order of execution essentials
Exact release details can change, but the exam expects practical understanding:
1. Load original record.
2. Apply system validation.
3. Run before-save record-triggered flows.
4. Run before triggers.
5. Run custom validation rules.
6. Save record, but do not commit yet.
7. Run after triggers.
8. Run after-save flows and other automation.
9. Run assignment, auto-response, workflow, escalation, and entitlement logic where applicable.
10. Execute roll-up summary recalculations and cascading updates.
11. Commit transaction.
12. Run post-commit logic such as email and async work.

Exam focus:
- Before-save automation is good for same-record field updates.
- After-save automation is needed for related records, callouts via async, and actions needing record IDs.
- Triggers and flows can cause recursion or repeated saves.

## Recursion and cascading
Recursion happens when automation updates a record in a way that causes the same automation to run again.

Prevention patterns:
- Use before triggers/flows for same-record changes.
- Avoid unnecessary DML on records already in the transaction.
- Use static guards carefully in Apex.
- Compare old vs new values before acting.
- Keep one clear owner for each business rule.

## Security layers
Salesforce security is layered:

- **Organization-wide defaults:** baseline record access.
- **Role hierarchy:** record access inheritance.
- **Sharing rules/manual sharing/teams:** record access expansion.
- **Profiles and permission sets:** object, field, app, tab, and system permissions.
- **Field-level security:** field visibility/editability.
- **Apex sharing mode:** whether record sharing is enforced by class execution.

## Apex sharing keywords
### `with sharing`
Enforces record-level sharing rules of the running user.

### `without sharing`
Does not enforce record-level sharing rules. Use only when system-level access is intentional and reviewed.

### `inherited sharing`
Uses the sharing mode of the caller. This is often a safer default for service classes that may be called from different contexts.

Important: sharing keywords do not automatically enforce object permissions or field-level security.

## CRUD and FLS checks
CRUD means object-level create/read/update/delete permissions.
FLS means field-level security.

Common tools:
- `Schema.sObjectType.Account.isCreateable()`
- `Schema.sObjectType.Account.fields.Name.isUpdateable()`
- `Security.stripInaccessible(...)`
- SOQL `WITH SECURITY_ENFORCED` for read checks in supported scenarios.

Example:
```apex
List<Account> rows = [SELECT Id, Name FROM Account WITH SECURITY_ENFORCED];
```

Use `stripInaccessible` when receiving records from UI/API/dynamic payloads before DML:

```apex
SObjectAccessDecision decision = Security.stripInaccessible(
    AccessType.CREATABLE,
    incomingAccounts
);
List<Account> safeRows = (List<Account>)decision.getRecords();
```

## SOQL injection
SOQL injection happens when untrusted input is concatenated into dynamic SOQL.

Safer patterns:
- Prefer static SOQL with bind variables.
- Allowlist dynamic field names and sort directions.
- Use `String.escapeSingleQuotes()` only as one defensive tool, not the whole strategy.

Bad:
```apex
String q = 'SELECT Id FROM Account WHERE Name = \'' + userInput + '\'';
```

Better:
```apex
List<Account> rows = [SELECT Id FROM Account WHERE Name = :userInput];
```

## Visualforce security
Visualforce risks:
- Exposing data through custom controllers without CRUD/FLS checks.
- Cross-site scripting when output is not escaped.
- Performing DML from controller methods without permission checks.
- Heavy getter methods that query data repeatedly.

Safer patterns:
- Use standard controllers when possible.
- Keep custom controllers `with sharing`.
- Enforce CRUD/FLS in Apex.
- Use escaped output components for user-controlled data.
- Keep getters side-effect free.

## Custom metadata and custom settings
Use custom metadata for deployable configuration:
- Business rules.
- Mapping tables.
- Feature flags.
- Integration configuration without secrets.

Use custom settings mainly for older org patterns or runtime configuration needs. For modern source-driven development, custom metadata is usually preferred.

Never store secrets in custom metadata/settings. Use Named Credentials or secure platform features.

## Exam scenario patterns
- Same-record field update before save: before-save Flow or before trigger.
- Related record update: after-save Flow or after trigger.
- Simple validation: validation rule.
- Approval requirement: approval process.
- Complex reusable logic: Apex service + trigger/controller/invocable wrapper.
- User-specific record access: `with sharing` or inherited context.
- Field/object permission enforcement: CRUD/FLS checks or `stripInaccessible`.

## Practice set
1. Given five requirements, choose Flow, validation rule, approval process, or Apex.
2. Write a secure query pattern using `WITH SECURITY_ENFORCED`.
3. Refactor dynamic SOQL to use allowlisted fields and bind variables.
4. Explain why a trigger and flow are recursively updating the same record.
