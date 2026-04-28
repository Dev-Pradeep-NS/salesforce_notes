# 09 - Testing, Governor Limits, and Async Apex (Detailed)

## Learning goals
- Write reliable unit tests with isolated test data.
- Understand governor limits and design for scale.
- Use async Apex patterns correctly and test them.

## Apex testing basics
```apex
@IsTest
private class AccountServiceTest {
    @IsTest
    static void shouldCreateAccount() {
        Account a = new Account(Name = 'Test Acc');
        insert a;
        System.assertNotEquals(null, a.Id);
    }
}
```
What this snippet does:
- Defines a minimal Apex unit test that inserts a record and asserts successful persistence.
- Shows baseline test structure with `@IsTest` class and method annotations.

## Test data strategy
- Build data in test methods or factory classes.
- Avoid `seeAllData=true` unless absolutely required.
- Keep tests deterministic and independent.

## Useful test methods
```apex
Test.startTest();
// invoke async / heavy logic
Test.stopTest();
```
What this snippet does:
- Resets governor counters for the measured test segment.
- Forces queued async work to execute at `Test.stopTest()` before assertions.

- `System.runAs(user)` for profile/role-specific behavior checks.
- `Test.setMock()` for callout tests.
- `Test.startTest()` resets limits for the measured section and isolates async enqueue timing.
- `Test.stopTest()` forces queued async jobs to run before assertions.

## Execute Anonymous vs unit tests
Execute Anonymous:
- Runs Apex immediately in an org.
- Is useful for quick experiments, data fixes, and debugging.
- Can access existing org data according to the running user's context.
- Does not prove deployment readiness.

Unit tests:
- Run in isolated test context.
- Should create their own data.
- Are required for Apex deployment.
- Should assert expected behavior.

Exam tip: Execute Anonymous is not a replacement for unit tests.

## Test coverage requirement
- Minimum deployment requirement is typically **75% org-wide Apex coverage**.
- Practical target should be behavior confidence, not just line coverage.
- Cover:
  - happy path
  - negative path
  - edge/boundary conditions
  - permission-related behavior where relevant

## Governor limits (important categories)
- SOQL queries
- DML statements/rows
- CPU time
- Heap size
- Callouts

Design implications:
- Bulk processing always.
- Map/set-driven lookups.
- Minimize repeated expensive operations.

## Limits class usage
Use `Limits` methods when troubleshooting or guarding logic:

```apex
System.debug('SOQL used: ' + Limits.getQueries() + '/' + Limits.getLimitQueries());
System.debug('DML used: ' + Limits.getDmlStatements() + '/' + Limits.getLimitDmlStatements());
System.debug('CPU ms used: ' + Limits.getCpuTime() + '/' + Limits.getLimitCpuTime());
```
What this snippet does:
- Prints current vs maximum governor usage for debugging and limit-aware tuning.

## Async Apex types
- `@future`: simple fire-and-forget.
- `Queueable`: chainable and more flexible.
- `Batchable`: large-volume chunk processing.
- `Schedulable`: time-based execution.

## Future vs Queueable vs Batch vs Scheduled
- Use `@future` for simple async work, especially older code patterns.
- Use `Queueable` when you need complex parameters, job IDs, or chaining.
- Use `Batchable` for large record volumes that must be processed in chunks.
- Use `Schedulable` when work must run at a specific time or recurring schedule.

Callout note:
- Async Apex is often used to perform callouts after database work.
- Future methods that make callouts need `@future(callout=true)`.

## Queueable example
```apex
public class UpdateIndustryQueueable implements Queueable {
    public void execute(QueueableContext qc) {
        List<Account> rows = [SELECT Id, Industry FROM Account LIMIT 50];
        for (Account a : rows) a.Industry = 'Updated';
        update rows;
    }
}
```
What this snippet does:
- Implements queueable async processing to update account industries in bulk.

## Batch example
```apex
global class AccountBatch implements Database.Batchable<sObject> {
    global Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator('SELECT Id, Name FROM Account');
    }
    global void execute(Database.BatchableContext bc, List<Account> scope) {
        for (Account a : scope) a.Name = a.Name + ' - Batch';
        update scope;
    }
    global void finish(Database.BatchableContext bc) {}
}
```
What this snippet does:
- Implements batch lifecycle (`start`, `execute`, `finish`) for chunked large-volume processing.

## Scheduling example
```apex
global class NightlyScheduler implements Schedulable {
    global void execute(SchedulableContext sc) {
        Database.executeBatch(new AccountBatch(), 200);
    }
}
```
What this snippet does:
- Schedules batch execution through a schedulable wrapper class.

## Common mistakes
- Asserting only coverage, not behavior.
- Missing negative and boundary test cases.
- Ignoring limits in loops and nested operations.
- Using `seeAllData=true` for normal unit tests.
- Forgetting to assert results after `Test.stopTest()` for async code.
- Creating test data that fails validation rules or required fields.

## Practice set
1. Create test factory for Account/Contact setup.
2. Write queueable + test validating post-`Test.stopTest()` changes.
3. Add a limit-aware bulk method and test with 200 records.
4. Compare Execute Anonymous and unit test usage in three scenarios.
