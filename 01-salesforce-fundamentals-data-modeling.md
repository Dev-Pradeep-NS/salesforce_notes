# 01 - Salesforce Fundamentals and Data Modeling (Detailed)

## Learning goals
- Understand the platform concepts PD1 expects beyond pure Apex.
- Choose the right data model for common business requirements.
- Predict how schema design affects Apex, UI, sharing, and reporting.

## Multitenancy
Salesforce is a multi-tenant platform: many customers share the same underlying infrastructure while their data and configuration remain logically separated.

Developer impact:
- Governor limits protect shared resources.
- Bulk-safe code is mandatory, not optional.
- Long-running or repeated work should use async patterns when appropriate.
- Metadata changes can affect code at runtime, so Apex must be schema-aware.

## Salesforce and MVC
Salesforce maps well to the Model-View-Controller pattern:

- **Model:** standard objects, custom objects, fields, relationships, validation rules.
- **View:** Lightning pages, Lightning components, Visualforce pages, page layouts.
- **Controller:** Apex classes, Apex triggers, standard controllers, component JavaScript controllers.

Exam tip: MVC questions usually test whether you understand that objects/fields are the data model, UI technologies are views, and Apex/controller logic coordinates behavior.

## Core CRM objects
Know the business role of common standard objects:

- `Lead`: unqualified prospect.
- `Account`: company, household, or organization.
- `Contact`: person related to an Account.
- `Opportunity`: potential revenue/deal.
- `Case`: customer support issue.
- `Campaign`: marketing effort.
- `Task` / `Event`: activities.

Typical flow:
1. Capture a Lead.
2. Qualify and convert it.
3. Create Account, Contact, and optionally Opportunity.
4. Manage sales through Opportunity stages.
5. Support customers with Cases.

## AppExchange
Use AppExchange when:
- A mature package already solves the requirement.
- Building from scratch would be expensive or risky.
- The need is common: e-signature, document generation, CPQ, data quality, backup, analytics.

Considerations:
- Security review and vendor trust.
- License cost.
- Configuration vs custom code needs.
- Upgrade impact.
- Data access required by the package.

## Heroku awareness
PD1 does not require deep Heroku development, but you should know when it fits.

Use Heroku when:
- The app needs custom web runtime outside Salesforce limits.
- You need non-Salesforce languages/frameworks.
- You need external services, workers, or web apps that integrate with Salesforce.

Salesforce still remains the system of record when CRM data and platform automation are central.

## Data modeling building blocks
- **Object:** table-like structure for a business entity.
- **Field:** column-like data point on an object.
- **Record:** one row/instance of an object.
- **Relationship:** link between records.
- **Schema:** full object/field/relationship design.

Good data models are easy to query, secure, report on, and maintain.

## Standard vs custom objects
Prefer standard objects when they match the business process because they come with built-in features, reporting, automation support, and ecosystem compatibility.

Use custom objects when:
- The concept is unique to the business.
- Standard objects would be semantically wrong.
- You need separate lifecycle, ownership, security, or reporting.

## Field types to know
- Text, Number, Currency, Percent
- Date, Date/Time, Time
- Checkbox
- Picklist, Multi-select Picklist
- Lookup Relationship
- Master-Detail Relationship
- Formula
- Roll-Up Summary
- External ID
- Unique
- Auto Number
- Long Text Area / Rich Text Area

Exam tip: formula and roll-up summary fields often appear in "declarative vs Apex" decisions.

## Lookup vs master-detail
### Lookup relationship
- Loosely couples two objects.
- Child can usually exist without parent unless lookup is required.
- Parent record ownership does not automatically control child ownership.
- Roll-up summary fields are not available on normal lookup relationships.
- Useful for optional associations.

### Master-detail relationship
- Tightly couples child to parent.
- Detail record cannot exist without master.
- Detail inherits ownership and sharing from master.
- Deleting master deletes detail records.
- Supports roll-up summary fields on the master.
- Useful for true parent-owned child records.

## How to create lookup and master-detail in Setup
### Create a Lookup Relationship
1. Go to **Setup -> Object Manager**.
2. Open the child object (example: `Book__c`).
3. Open **Fields & Relationships** and click **New**.
4. Choose **Lookup Relationship** and click **Next**.
5. Select the parent object (example: `Category__c`) and click **Next**.
6. Set field label/name, choose required/optional behavior, and configure field-level security.
7. Add to page layouts and save.

When to choose this:
- Use lookup when the child can exist independently.
- Use it when ownership and sharing should remain separate between parent and child.

### Create a Master-Detail Relationship
1. Go to **Setup -> Object Manager**.
2. Open the detail (child) object (example: `Book_Review__c`).
3. Open **Fields & Relationships** and click **New**.
4. Choose **Master-Detail Relationship** and click **Next**.
5. Select the master object (example: `Book__c`) and click **Next**.
6. Configure related list label, field-level security, and page layouts.
7. Save and verify that the detail now inherits owner/sharing from master.

When to choose this:
- Use master-detail when child lifecycle must depend on parent.
- Use it when you need roll-up summaries on the parent.

## API naming: `__c` vs no suffix, and `__r`
### Why some fields end with `__c`
- `__c` means **custom** object or field created in your org.
- No `__c` usually means **standard** Salesforce object/field.

Examples:
- Custom field: `Category__c` (picklist or relationship, depending on field type).
- Standard field: `CreatedById` (system-managed lookup to `User`).
- Standard field: `Name` (default name field on each object).

### What `__r` means
- `__r` is used for **relationship traversal** in SOQL/Apex.
- If `Category__c` stores the parent Id, then `Category__r` lets you access parent fields.

Example (`Book__c` has lookup field `Category__c`):
```apex
// Relationship Id value on child
SELECT Id, Name, Category__c
FROM Book__c
```
What this snippet does:
- Returns book records and the raw parent Id stored in `Category__c`.

```apex
// Parent field traversal via relationship name
SELECT Id, Name, Category__r.Name
FROM Book__c
```
What this snippet does:
- Returns book records plus the parent category name using relationship traversal.

### Standard relationship example (`CreatedById` vs `CreatedBy.Name`)
```apex
SELECT Id, Name, CreatedById, CreatedBy.Name
FROM Book__c
```
What this snippet does:
- `CreatedById` is the raw user Id value.
- `CreatedBy.Name` traverses the standard relationship and returns the creator's display name.

## Junction object
A junction object models many-to-many relationships using two master-detail relationships.

Example:
- `Job_Application__c` connects `Candidate__c` and `Position__c`.
- One candidate can apply to many positions.
- One position can have many candidates.

## Formula fields
Formula fields calculate values at read time and do not store manually entered data.

Use formula fields when:
- The value can be derived from fields on the same record or related parent records.
- You need display/reporting logic without code.
- The result does not need historical storage.

Avoid formula fields when:
- The logic is too complex or hard to maintain.
- The value must be editable.
- The value must represent a point-in-time snapshot.

## Roll-up summary fields
Roll-up summary fields summarize child records on a master record.

Common operations:
- `COUNT`
- `SUM`
- `MIN`
- `MAX`

Use roll-up summary fields instead of Apex when the relationship is master-detail and the summary requirement fits standard capabilities.

Use Apex, Flow, or another tool when:
- The relationship is lookup.
- You need complex filters or cross-object logic.
- You need custom timing or integration behavior.

## Record types and page layouts
Record types separate business processes, picklist values, and page layout assignments for the same object.

Use record types when:
- Different teams use the same object differently.
- Picklist values differ by process.
- Page layouts or Lightning pages need to vary.

Avoid record types for simple visibility needs. Use profiles, permission sets, dynamic forms, or page layouts where appropriate.

## Schema Builder and ERD thinking
Schema Builder helps visualize:
- Objects
- Fields
- Relationships
- Required fields

For PD1 scenarios, practice translating requirements into:
1. Objects.
2. Relationships.
3. Field types.
4. Ownership and sharing impact.
5. Automation impact.
6. Reporting needs.

## Import and export options
Common tools:
- **Data Import Wizard:** simpler imports for common objects and custom objects.
- **Data Loader:** larger imports/exports, updates, deletes, upserts.
- **Workbench:** admin/developer utility for data and metadata inspection.
- **Bulk API:** large-volume programmatic data operations.

Key considerations:
- Required fields.
- External IDs for upsert.
- Duplicate rules.
- Validation rules and automation side effects.
- Sharing/ownership.
- Data quality and rollback plan.

## Schema changes and Apex impact
Schema design affects Apex strongly:
- Renaming/deleting fields can break code, queries, tests, and integrations.
- Required fields can break DML in tests and production logic.
- Relationship changes affect SOQL relationship names.
- Validation rules can cause unexpected `DmlException`.
- Field type changes can break assignments and formulas.

Safer approach:
1. Search references before changing metadata.
2. Update tests and factories.
3. Validate deployment.
4. Run impacted flows and Apex tests.

## Exam scenario patterns
- If a standard declarative feature satisfies the requirement, prefer it over Apex.
- If data ownership must follow the parent, consider master-detail.
- If child records must exist independently, consider lookup.
- If many-to-many is required, use a junction object.
- If a simple derived value is needed, consider formula.
- If a parent needs a summary of detail records, consider roll-up summary.

## Practice set
1. Model a recruiting app with Candidates, Positions, and Applications.
2. Decide lookup vs master-detail for Invoice and Invoice Line Item, then explain sharing impact.
3. Replace a trigger requirement with formula or roll-up summary where possible.
4. Design an import plan using external IDs and identify automation risks.
