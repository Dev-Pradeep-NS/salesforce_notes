# 02 - Apex Basics (Detailed)

## Learning goals
- Understand what Apex is and where it fits in Salesforce architecture.
- Decide when to use Apex vs Flow.
- Write clean, testable Apex classes with platform constraints in mind.

## What is Apex
Apex is a strongly typed, object-oriented, server-side language that runs on Salesforce runtime.  
It is similar to Java syntax, but it is platform-governed (governor limits, multitenancy, managed transactions).

## Key Apex features
- Native support for `sObject` records and DML.
- Integrated query language support (`SOQL`, `SOSL`).
- Transaction management (`Savepoint`, rollback).
- Built-in test framework and deployment checks.
- Async capabilities (`future`, `queueable`, `batch`, `schedulable`).

## When to use Apex (and when not to)
Use Apex when:
- Business logic is too complex for declarative tools.
- You need reusable service-layer logic across trigger/UI/API contexts.
- You need custom integrations, error contracts, or transaction control.

Prefer Flow when:
- Logic is simple, admin-owned, and does not require complex abstractions.
- Requirements are mostly orchestration and straightforward data updates.

## Typical Apex execution flow
1. Entry point (trigger, REST method, invocable, LWC @AuraEnabled method).
2. Input validation and guard clauses.
3. Query data in bulk.
4. Apply domain/business rules.
5. Persist changes in grouped DML.
6. Return shaped response and log meaningful errors.

## Apex limitations to remember
- No file system I/O access.
- No arbitrary thread creation.
- No unlimited transaction duration (governor + CPU limits).
- No direct usage of external Java libraries.

## Apex environments
- **Developer Org / Playground**: personal experimentation.
- **Sandbox**: team development and QA.
- **Scratch Org**: source-driven, temporary org for modern DX workflow.
- **Production**: live business runtime.

## Tooling for Apex
- **VS Code + Salesforce Extensions** (recommended daily workflow).
- **Developer Console** for quick anonymous scripts/logs.
- **Workbench** for utility tasks.
- **Salesforce CLI (`sf`)** for auth, deploy, test, and org automation.

## Variables and literals (practical)
```apex
Integer quantity = 5;
Decimal unitPrice = 99.99;
String description = 'Starter pack';
Boolean isActive = true;
Date orderDate = Date.today();
Datetime createdAt = Datetime.now();
```

## Simple utility class example
```apex
public with sharing class OrderUtils {
    public static Boolean isHighValue(Decimal amount) {
        return amount != null && amount >= 10000;
    }

    public static String safeTrim(String input) {
        return String.isBlank(input) ? '' : input.trim();
    }

    public static String buildWelcome(String firstName, String lastName) {
        return 'Welcome, ' + safeTrim(firstName) + ' ' + safeTrim(lastName);
    }
}
```

## Design rules for production Apex
- Bulk-first mindset always.
- Never place SOQL/DML inside loops.
- Separate trigger logic from handler/service classes.
- Prefer explicit, readable code over clever one-liners.
- Handle nulls and edge cases explicitly.

## Security in Apex (Interview Critical)
### `with sharing` vs `without sharing`
- `with sharing`: respects record-level sharing rules of running user.
- `without sharing`: ignores sharing rules (still does not bypass object/field permissions automatically).

```apex
public with sharing class AccountReader {
    public static List<Account> getVisibleAccounts() {
        return [SELECT Id, Name FROM Account LIMIT 20];
    }
}
```

### CRUD/FLS checks
Before read/write on sensitive objects/fields, check permissions:

```apex
if (!Schema.sObjectType.Account.isCreateable()) {
    throw new SecurityException('No create access on Account');
}
if (!Schema.sObjectType.Account.fields.AnnualRevenue.isUpdateable()) {
    throw new SecurityException('No update access on AnnualRevenue');
}
```

Prefer `Security.stripInaccessible` when processing dynamic field sets for safer field-level enforcement.

## Bulkification (explicit pattern)
Bad pattern:
- Query/DML inside `for` loop.

Good pattern:
1. Collect ids from input list.
2. Query once using `WHERE Id IN :ids`.
3. Process in memory using maps/sets.
4. Perform one grouped DML operation.

## Common mistakes
- Treating Apex like normal Java without platform limits.
- Writing logic only for one record instead of collection input.
- Ignoring sharing/security context (`with sharing`, FLS/CRUD checks).
- No test strategy before implementation.

## Interview quick Q&A
- **Q:** Why use Apex if Flow exists?
- **A:** Apex is better for complex, reusable, testable, and integration-heavy logic.

- **Q:** Is Apex compiled or interpreted?
- **A:** Apex is compiled and saved as metadata on platform.

- **Q:** What is multitenancy impact?
- **A:** Shared resources enforce governor limits for fair usage and stability.

## Practice set
1. Build a utility class with 5 static methods for string/date/amount helpers.
2. Create one method that accepts `List<Account>` and validates naming rules.
3. Write tests for both happy path and edge cases (`null`, blanks, boundary values).
