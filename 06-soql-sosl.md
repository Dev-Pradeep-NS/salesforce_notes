# 06 - SOQL and SOSL (Detailed)

## Learning goals
- Write efficient SOQL/SOSL queries in Apex.
- Use relationship queries and aggregate queries correctly.
- Choose between SOQL and SOSL based on problem type.

## SOQL basics
SOQL is object-centric and structured. Query one object (with relationships) using explicit fields.

```apex
List<Account> accs = [SELECT Id, Name, Industry FROM Account LIMIT 20];
```
What this snippet does:
- Runs a basic SOQL query selecting explicit fields from `Account`.
- Uses `LIMIT` to keep result size bounded in sample/demo contexts.

## Variable binding
```apex
String targetName = 'Acme';
List<Account> rows = [
    SELECT Id, Name
    FROM Account
    WHERE Name = :targetName
];
```
What this snippet does:
- Demonstrates bind-variable filtering (`:targetName`) in static SOQL.
- Keeps the query safe and readable versus string-concatenated filters.

Benefits:
- Safer than string concatenation.
- Readable and type-aware.

## Date literals
```apex
List<Opportunity> opps = [
    SELECT Id, Name
    FROM Opportunity
    WHERE CloseDate = LAST_N_DAYS:30
];
```
What this snippet does:
- Filters opportunities using a date literal (`LAST_N_DAYS:30`) instead of hardcoded dates.
- Useful for rolling-window analytics without manual date math.

## Aggregate queries
```apex
AggregateResult[] stats = [
    SELECT BillingCountry country, COUNT(Id) total
    FROM Account
    GROUP BY BillingCountry
];
for (AggregateResult ar : stats) {
    System.debug(ar.get('country') + ' -> ' + ar.get('total'));
}
```
What this snippet does:
- Groups accounts by country and calculates record counts.
- Reads aliased aggregate values from `AggregateResult` with `get(...)`.

## Relationship queries
Child-to-parent:
```apex
List<Contact> contacts = [
    SELECT Id, LastName, Account.Name, Account.Industry
    FROM Contact
    WHERE Account.Industry = 'Technology'
];
```
What this snippet does:
- Uses child-to-parent traversal (`Account.Name`, `Account.Industry`) from `Contact`.
- Filters contacts by a parent field value in one query.

Parent-to-child:
```apex
List<Account> accounts = [
    SELECT Id, Name, (SELECT Id, LastName, Email FROM Contacts)
    FROM Account
    LIMIT 10
];
```
What this snippet does:
- Uses a parent-to-child subquery to return accounts and their related contacts together.
- Reduces extra round-trips by fetching hierarchy in a single SOQL call.

Multi-level example:
```apex
List<Case> cases = [
    SELECT Id, Subject, Contact.Account.Owner.Name
    FROM Case
    WHERE Contact.Account.Owner.IsActive = true
];
```
What this snippet does:
- Demonstrates multi-level relationship traversal in SOQL.
- Filters cases through related contact-account-owner properties.

## Dynamic SOQL
```apex
String fieldApi = 'Name';
String q = 'SELECT Id, ' + fieldApi + ' FROM Account LIMIT 5';
List<sObject> resultRows = Database.query(q);
```
What this snippet does:
- Builds and executes a dynamic SOQL statement at runtime.
- Useful for metadata-driven scenarios where selected fields are not known at compile time.

Use dynamic SOQL when:
- Field/object is decided at runtime.
- You need metadata-driven behavior.

## SOQL for loop
```apex
for (Account a : [SELECT Id, Name FROM Account]) {
    System.debug(a.Name);
}
```
What this snippet does:
- Demonstrates SOQL-for-loop iteration, a pattern that helps process large query results efficiently.

## SOQL keywords reference (high-value)
### `IN` and `NOT IN`
```apex
List<String> names = new List<String>{'GenePoint', 'United Oil & Gas, UK'};
List<Account> inRows = [
    SELECT Id, Name
    FROM Account
    WHERE Name IN :names
];
List<Account> notInRows = [
    SELECT Id, Name
    FROM Account
    WHERE Name NOT IN :names
];
```
What this snippet does:
- Shows include/exclude filtering with `IN` and `NOT IN` against a bound collection.

### `ORDER BY`, `LIMIT`
```apex
List<Opportunity> topRows = [
    SELECT Id, Name, Amount
    FROM Opportunity
    WHERE StageName = 'Closed Won'
    ORDER BY Amount DESC NULLS LAST
    LIMIT 10
];
```
What this snippet does:
- Sorts by amount descending, pushes nulls last, and returns top-N rows with `LIMIT`.

### `GROUP BY` and `HAVING`
```apex
List<AggregateResult> grouped = [
    SELECT Status, COUNT(Id) totalCount
    FROM Lead
    GROUP BY Status
    HAVING COUNT(Id) > 3
];
```
What this snippet does:
- Applies grouped aggregation with post-group filtering using `HAVING`.

### `FOR UPDATE`
Locks selected records for update in the current transaction.
```apex
List<Opportunity> lockedRows = [
    SELECT Id, Name
    FROM Opportunity
    WHERE StageName = 'Closed Won'
    FOR UPDATE
];
```
What this snippet does:
- Locks selected rows with `FOR UPDATE` to reduce concurrent update conflicts inside a transaction.

### `ALL ROWS`
Includes soft-deleted records (where supported) and archived activity records.
```apex
List<Account> deletedRows = [
    SELECT Id, Name, IsDeleted
    FROM Account
    WHERE IsDeleted = true
    ALL ROWS
];
```
What this snippet does:
- Includes soft-deleted records in query scope via `ALL ROWS`.

## Aggregate function notes
```apex
Integer totalOpps = [SELECT COUNT() FROM Opportunity];
AggregateResult ar = [
    SELECT COUNT_DISTINCT(Type) uniqueTypes, AVG(Amount) avgAmt
    FROM Opportunity
];
Integer uniqueTypeCount = (Integer)ar.get('uniqueTypes');
Decimal avgAmount = (Decimal)ar.get('avgAmt');
```
What this snippet does:
- Combines direct count and aggregate metrics (`COUNT_DISTINCT`, `AVG`) in SOQL.
- Casts aggregate aliases to concrete Apex types for typed downstream use.

## SOQL return types
- `List<sObject>` or `List<Account>` for multi-record queries.
- Single `sObject` when query is guaranteed to return one record.
- `Integer` for `COUNT()`.
- `AggregateResult` for aggregate/grouped queries.

## Safer dynamic SOQL pattern
Use allowlists for objects/fields and bind user values instead of direct concatenation.
```apex
Set<String> allowedFields = new Set<String>{'Id', 'Name', 'NumberOfEmployees'};
List<String> requestedFields = new List<String>{'Name', 'NumberOfEmployees'};
for (String f : requestedFields) {
    if (!allowedFields.contains(f)) {
        throw new IllegalArgumentException('Field not allowed: ' + f);
    }
}
String soql = 'SELECT ' + String.join(requestedFields, ',') + ' FROM Account WHERE Name = :nameFilter';
String nameFilter = 'GenePoint';
List<sObject> safeResult = Database.query(soql);
```
What this snippet does:
- Applies an allowlist to dynamic field selection before building query text.
- Preserves runtime flexibility while reducing injection and invalid-field risk.

## SOSL basics
SOSL is search-oriented (text search across objects/fields).

```apex
List<List<sObject>> searchResults = [
    FIND 'Acme*'
    IN ALL FIELDS
    RETURNING Account(Id, Name), Contact(Id, LastName, Email)
];
```
What this snippet does:
- Runs SOSL across multiple objects and returns grouped results by object order.

Reading SOSL return:
```apex
List<Account> foundAccounts = (List<Account>)searchResults[0];
List<Contact> foundContacts = (List<Contact>)searchResults[1];
```
What this snippet does:
- Casts each SOSL result bucket to its concrete sObject list type.

## SOSL deep dive (keywords, groups, clauses)
### SOSL syntax template
```apex
// Apex context
List<List<sObject>> results = [
    FIND 'searchTerm*'
    IN ALL FIELDS
    RETURNING Account(Id, Name), Contact(Id, FirstName, LastName)
];
```
What this snippet does:
- Provides a reusable SOSL skeleton for keyword search plus multi-object `RETURNING` clauses.

### Search groups
- `IN ALL FIELDS`: searches across eligible text, phone, email, and name fields.
- `IN NAME FIELDS`: searches name-like fields only.
- `IN EMAIL FIELDS`: searches email fields.
- `IN PHONE FIELDS`: searches phone fields.
- `IN SIDEBAR FIELDS`: legacy sidebar-oriented searchable fields.

```apex
List<List<sObject>> allFieldsRes = [
    FIND 'Test'
    IN ALL FIELDS
    RETURNING Account(Name), Contact(FirstName, LastName)
];
List<List<sObject>> nameFieldsRes = [
    FIND 'Univ*'
    IN NAME FIELDS
    RETURNING Account(Name, BillingCountry), Contact(FirstName, LastName)
];
List<List<sObject>> emailFieldsRes = [
    FIND '*com'
    IN EMAIL FIELDS
    RETURNING Contact(FirstName, LastName, Email)
];
List<List<sObject>> phoneFieldsRes = [
    FIND '(512) 757-6000'
    IN PHONE FIELDS
    RETURNING Contact(FirstName, LastName, Phone)
];
```
What this snippet does:
- Demonstrates search-group targeting (`ALL/NAME/EMAIL/PHONE FIELDS`) to control SOSL search scope.

### Wildcards
- `*` matches zero or more characters.
- `?` matches a single character.

```apex
List<List<sObject>> wildcard1 = [FIND 'Univ*' IN NAME FIELDS RETURNING Account(Name)];
List<List<sObject>> wildcard2 = [FIND 'Jo?n' IN NAME FIELDS RETURNING Contact(FirstName, LastName)];
```
What this snippet does:
- Shows wildcard behavior: `*` for many characters and `?` for a single character.

### Clauses in RETURNING
You can apply `WHERE`, `ORDER BY`, and `LIMIT` inside each object specification in `RETURNING`.

```apex
List<List<sObject>> clauseRes = [
    FIND 'univ*'
    IN NAME FIELDS
    RETURNING
        Account(Name, BillingCountry WHERE CreatedDate < TODAY ORDER BY Name ASC LIMIT 50),
        Contact(FirstName, LastName)
];
```
What this snippet does:
- Applies object-specific `WHERE`, `ORDER BY`, and `LIMIT` inside SOSL `RETURNING`.

Note: when using object-specific clauses, include a field list for that returned object.

### Parsing pattern for `List<List<sObject>>`
```apex
List<List<sObject>> r = [
    FIND 'washington'
    RETURNING Account(Name, NumberOfEmployees), Contact(Name, Phone)
];
List<Account> accRows = (List<Account>)r[0];
List<Contact> conRows = (List<Contact>)r[1];
```
What this snippet does:
- Demonstrates the standard parsing pattern for multi-object SOSL results.

## SOQL vs SOSL
- **SOQL:** structured filters and exact record retrieval.
- **SOSL:** keyword search across multiple objects quickly.

## Performance and best practices
- Select only required fields.
- Keep queries selective in large orgs.
- Avoid queries in loops.
- Prefer bind variables over concatenated where clauses.

## Common mistakes
- Overusing dynamic SOQL when static is enough.
- Confusing relationship direction (`Account.Name` vs subquery syntax).
- Ignoring query row and CPU limits.
- Building dynamic SOQL from unvalidated field/object names.

## Official references
- [SOQL and SOSL Reference](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/)
- [SOSL Search Groups and Syntax](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_sosl.htm)
- [SOQL Clauses](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select.htm)
- [Aggregate Functions in SOQL](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_select_agg_functions.htm)
- [Dynamic SOQL](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_dynamic_soql.htm)
- [Date Formats and Date Literals](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm)

## Practice set
1. Write one aggregate SOQL per country and sort by count desc.
2. Build parent-child query for Accounts and last 5 Contacts.
3. Write SOSL for a search term and map each returned object type count.
4. Write one query each using `HAVING`, `FOR UPDATE`, and `ALL ROWS`.
5. Write SOSL examples for `IN NAME FIELDS`, `IN EMAIL FIELDS`, and a `RETURNING` clause with `WHERE` + `LIMIT`.
