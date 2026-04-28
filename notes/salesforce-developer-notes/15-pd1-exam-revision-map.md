# 15 - PD1 Exam Revision Map

## Purpose
Use this page as the final revision checklist before practice exams. It maps your notes to the main PD1 exam themes and highlights the decision patterns Salesforce commonly tests.

## Official prep areas
Current Trailhead prep groups the exam into:
- Developer Fundamentals
- Process Automation and Logic
- User Interface
- Testing, Debugging, and Deployment

Older and printable guides may split Salesforce Fundamentals and Data Modeling separately. Keep both views in mind: the concepts are still relevant even when grouped differently.

## Developer Fundamentals
Revise:
- Multitenancy and governor limits
- MVC on Salesforce
- Core CRM objects
- AppExchange and Heroku awareness
- Data model basics
- Relationship types
- Schema impact on Apex

Primary notes:
- `01-salesforce-fundamentals-data-modeling.md`
- `02-apex-basics.md`
- `03-datatypes-collections-operators.md`
- `04-oop-control-flow.md`

Quick questions:
- Why do governor limits exist?
- Which part of Salesforce is Model, View, and Controller?
- When should you use a standard object instead of a custom object?
- What breaks when a required field or validation rule is added?

## Process Automation and Logic
Revise:
- Flow vs Apex
- Formula fields
- Roll-up summary fields
- Validation rules
- Approval processes
- Apex classes and interfaces
- SOQL, SOSL, DML
- Trigger patterns
- Order of execution
- Exception handling
- Transaction control
- Security in Apex

Primary notes:
- `05-declarative-automation-and-security.md`
- `06-soql-sosl.md`
- `07-dml-database-class.md`
- `08-triggers-exceptions.md`
- `09-testing-governor-limits-async.md`

Quick questions:
- Can a roll-up summary replace this trigger?
- Should this same-record update be before-save Flow, before trigger, or after trigger?
- Where can recursion happen?
- What is the difference between DML statements and `Database` methods?

## User Interface
Revise:
- Visualforce page/controller architecture
- Standard controller vs custom controller vs extension
- Lightning Component framework benefits
- LWC component resources
- Aura component awareness
- Parent-child communication
- Data binding and events
- UI security concerns

Primary notes:
- `05-declarative-automation-and-security.md`
- `11-lwc.md`
- `12-visualforce.md`

Quick questions:
- When is Visualforce still relevant?
- What logic belongs in a controller vs service class?
- Where does client-side controller logic live in Lightning components?
- How does an LWC communicate child-to-parent?

## Testing, Debugging, and Deployment
Revise:
- Test framework and deployment requirements
- Test data setup
- `Test.startTest()` / `Test.stopTest()`
- `System.runAs`
- Mocking callouts
- Execute Anonymous vs unit tests
- Debug logs and log levels
- Developer Console and Salesforce CLI
- Metadata vs data deployment
- Sandbox types and deployment lifecycle

Primary notes:
- `09-testing-governor-limits-async.md`
- `10-apis-integration.md`
- `13-deployment-cicd.md`

Quick questions:
- Why should tests avoid `seeAllData=true`?
- What runs synchronously at `Test.stopTest()`?
- What is the difference between deploying metadata and loading business data?
- Which sandbox type is best for full-volume testing?

## High-value decision table
| Requirement | Prefer |
| --- | --- |
| Simple field validation | Validation rule |
| Same-record field update | Before-save Flow or before trigger |
| Parent summary of master-detail children | Roll-up summary |
| Derived display value | Formula field |
| Guided user input | Screen Flow |
| Complex reusable business logic | Apex service class |
| Large data processing | Batch Apex |
| Async integration follow-up | Queueable Apex or Platform Event pattern |
| Scheduled operation | Scheduled Flow or Scheduled Apex |
| Custom legacy UI page | Visualforce |
| Modern custom Salesforce UI | LWC |

## Common exam traps
- Choosing Apex when a declarative feature fully satisfies the requirement.
- Ignoring record access vs object/field permissions.
- Forgetting that `without sharing` still does not grant CRUD/FLS automatically.
- Putting SOQL or DML inside loops.
- Using after triggers for same-record field changes that belong before save.
- Assuming tests can see production data.
- Forgetting validation rules and required fields affect test data.
- Confusing lookup and master-detail ownership behavior.
- Confusing metadata deployment with data migration.

## Last-week study rhythm
- Day 1: Fundamentals and data modeling.
- Day 2: Apex basics, collections, OOP.
- Day 3: SOQL/SOSL/DML and transactions.
- Day 4: Triggers, order of execution, declarative automation.
- Day 5: Testing, limits, async.
- Day 6: LWC, Visualforce, security.
- Day 7: Debugging, deployment, full practice exam review.
