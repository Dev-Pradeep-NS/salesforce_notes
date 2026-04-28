# 14 - Syllabus Checklist Mapping

Use this sheet to ensure every concept has examples and revision notes.

## Apex Basics
- [ ] What is Apex and features
- [ ] When to use Apex and execution flow
- [ ] Features not supported by Apex
- [ ] Apex environments
- [ ] Tools for writing Apex
- [ ] Variables and literals
- [ ] `with sharing` vs `without sharing`
- [ ] CRUD/FLS checks (`Schema.sObjectType`, `Security.stripInaccessible`)
- [ ] Bulkification patterns (no SOQL/DML in loops)

## Salesforce Fundamentals
- [ ] Multitenancy and governor limit reasoning
- [ ] Salesforce MVC mapping
- [ ] Core CRM objects: Lead, Account, Contact, Opportunity, Case, Campaign, Activity
- [ ] Standard object vs custom object decision
- [ ] AppExchange extension scenarios
- [ ] Heroku awareness and when it fits
- [ ] Salesforce Mobile / Lightning Experience awareness

## Datatypes and Collections
- [ ] Primitive datatypes: Integer, Float/Double, String, Date, Time, Datetime, Boolean, Id, Blob
- [ ] Rules of conversion
- [ ] Enums
- [ ] sObjects and generic sObjects
- [ ] Collections: List, Set, Map
- [ ] List initialization, array notation, nested lists
- [ ] Set initialization
- [ ] Map initialization
- [ ] Operators: add/subtract, shorthand, equality, relational

## OOP and Control Flow
- [ ] OOP foundations
- [ ] Classes, interfaces, inheritance, polymorphism
- [ ] Access modifiers (`private`, `public`, `global`, `protected`)
- [ ] Logic control and looping statements
- [ ] Loop assignment questions

## Data Modeling and Management
- [ ] Objects, fields, records, schema basics
- [ ] Field types and when to use each
- [ ] Lookup vs Master-Detail relationships
- [ ] Junction objects for many-to-many
- [ ] Formula fields and use cases
- [ ] Roll-up summary fields and limitations
- [ ] Record types and page layout assignment
- [ ] Schema Builder / ERD visualization
- [ ] Import/export options: Data Import Wizard, Data Loader, Workbench, Bulk API
- [ ] External IDs and upsert strategy
- [ ] Impact of schema changes on Apex, tests, SOQL, and automation

## SOQL
- [ ] SOQL basics
- [ ] SOQL in Apex
- [ ] Variable binding
- [ ] Keywords
- [ ] Date literals
- [ ] Aggregate functions
- [ ] Child-to-parent
- [ ] Multi-level relationships
- [ ] Parent-to-child
- [ ] Return types
- [ ] Dynamic SOQL
- [ ] SOQL for loops

## SOSL
- [ ] SOSL basics
- [ ] Accessing records from SOSL result
- [ ] Search groups / return fields
- [ ] SOQL vs SOSL and dynamic SOSL
- [ ] Wildcards and clauses

## DML and Database Class
- [ ] DML basics
- [ ] Insert, Update, Upsert, Delete, Undelete, Merge
- [ ] DML best practices
- [ ] Database class methods
- [ ] Empty recycle bin
- [ ] Count query
- [ ] Lead conversion
- [ ] Transaction control and rollback
- [ ] Result objects

## Triggers
- [ ] Trigger basics
- [ ] When to use triggers
- [ ] Trigger types
- [ ] Order of execution
- [ ] Flow-aware order of execution
- [ ] Context variables
- [ ] Trigger.new / old / newMap / oldMap
- [ ] Trigger best practices
- [ ] Trigger exceptions
- [ ] Trigger helper pattern
- [ ] Recursion and cascading automation prevention

## Declarative Automation
- [ ] Flow types: record-triggered, screen, autolaunched, scheduled
- [ ] Flow vs Apex decision patterns
- [ ] Validation rules
- [ ] Approval processes
- [ ] Workflow Rules and Process Builder legacy awareness
- [ ] Before-save vs after-save automation
- [ ] Same-record vs related-record updates
- [ ] Automation ownership and maintainability

## Exception Handling
- [ ] Exception handling in Apex
- [ ] System-defined exceptions
- [ ] Custom exceptions

## Testing / Limits / Async
- [ ] Testing basics
- [ ] Unit tests
- [ ] System-defined test methods
- [ ] `Test.startTest()` / `Test.stopTest()`
- [ ] Execute Anonymous vs unit tests
- [ ] 75% coverage requirement and behavior-focused assertions
- [ ] Test data patterns
- [ ] `System.runAs`
- [ ] `Test.setMock()` and callout tests
- [ ] Governor limits and limit types
- [ ] Limits class (`Limits.getQueries()`, `Limits.getCpuTime()`, etc.)
- [ ] Future, Queueable, Batch, and Scheduled Apex use cases
- [ ] Batch class, execute batch, schedule batch

## Security
- [ ] Record-level sharing vs object permissions vs field-level security
- [ ] Profiles and permission sets
- [ ] Org-wide defaults, role hierarchy, sharing rules
- [ ] `with sharing`, `without sharing`, `inherited sharing`
- [ ] CRUD/FLS enforcement in Apex
- [ ] `WITH SECURITY_ENFORCED`
- [ ] `Security.stripInaccessible`
- [ ] SOQL injection prevention
- [ ] Visualforce security and XSS awareness
- [ ] Named Credentials for secrets/auth configuration

## APIs
- [ ] APIs in Salesforce
- [ ] Need of APIs
- [ ] Communication flow in APIs
- [ ] API types
- [ ] REST API sequence (part 1 to part 10)
- [ ] REST fundamentals (resources, stateless design)
- [ ] HTTP methods (`GET`, `POST`, `PUT/PATCH`, `DELETE`)
- [ ] Request/response design patterns
- [ ] Callouts in Apex
- [ ] Authentication basics (OAuth/Named Credentials)

## LWC
- [ ] LWC basics
- [ ] Lightning Component framework benefits
- [ ] Aura component awareness (`.cmp`, JS controller/helper, style)
- [ ] Decorators (`@api`, `@wire`, `@track`)
- [ ] Calling Apex from LWC (`@wire`, imperative calls, `@AuraEnabled`)
- [ ] Field/object import vs string usage
- [ ] Data binding
- [ ] Getters and setters
- [ ] Query selector APIs
- [ ] Conditional rendering
- [ ] Parent-child communication
- [ ] List rendering
- [ ] Multiple templates
- [ ] CSS and sharing rules
- [ ] SLDS styling hooks
- [ ] Component composition
- [ ] Lifecycle hooks (`connectedCallback`, `renderedCallback`, `disconnectedCallback`)
- [ ] Lightning page exposure via `.js-meta.xml`

## Visualforce
- [ ] Visualforce basics and architecture
- [ ] MVC mapping for Visualforce
- [ ] Standard controller
- [ ] View page / edit page
- [ ] Custom controller
- [ ] Getter/setter patterns
- [ ] Page messages
- [ ] Controller extensions (part 1, part 2, combined usage)
- [ ] Order of execution
- [ ] Tabs
- [ ] System mode
- [ ] Visualforce inside Lightning Experience
- [ ] Static resources / HTML / JavaScript content in Visualforce
- [ ] Visualforce controller security

## Deployment and CI/CD
- [ ] Deployment process
- [ ] Workbench
- [ ] VS Code
- [ ] GitLab CI/CD pipeline for Salesforce
- [ ] Change Sets
- [ ] Salesforce CLI / Salesforce DX
- [ ] Developer Console debugging
- [ ] Debug logs and log levels
- [ ] Metadata vs business data deployment
- [ ] Sandbox types: Developer, Partial Copy, Full
- [ ] Scratch orgs and source-driven development
- [ ] Deployment validation and test execution
- [ ] Post-deploy verification and rollback planning

## PD1 Exam Revision
- [ ] Developer Fundamentals revision
- [ ] Process Automation and Logic revision
- [ ] User Interface revision
- [ ] Testing, Debugging, and Deployment revision
- [ ] Decision table: declarative vs Apex vs UI vs async
- [ ] Common exam traps
- [ ] Scenario-based practice questions
- [ ] Last-week study plan
