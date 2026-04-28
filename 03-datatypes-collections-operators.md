# 03 - Datatypes, Collections, and Operators (Detailed)

## Learning goals
- Master core Apex datatypes and conversion patterns.
- Use List/Set/Map effectively for bulk-safe logic.
- Apply operators correctly in business expressions.

## Primitive and special datatypes
- Numeric: `Integer`, `Long`, `Double`, `Decimal`
- Text/flag: `String`, `Boolean`
- Temporal: `Date`, `Time`, `Datetime`
- Platform-specific: `Id`, `Blob`

```apex
Integer units = 10;
Decimal totalAmount = 12499.50;
String accountName = 'Acme';
Boolean isEligible = true;
Date todayVal = Date.today();
Time nowTime = Time.newInstance(9, 30, 0, 0);
Datetime stamp = Datetime.now();
```
What this snippet does:
- Declares the most-used Apex primitives and temporal types in one place.
- Uses `Decimal` for precise business values and explicit `Time` construction for deterministic tests.

## Numeric guidance
- Use `Decimal` for money/business precision.
- Use `Integer` for counts/indexes.
- Avoid `Double` for exact currency math.

## Conversion rules and examples
```apex
String qtyText = '42';
Integer qty = Integer.valueOf(qtyText);
Decimal decVal = Decimal.valueOf('19.75');
String qtyOut = String.valueOf(qty);
```
What this snippet does:
- Converts textual input into typed values and back into string output.
- Models common parsing/serialization flow used in APIs and UI payload handling.

Handle parse failures:
```apex
public static Integer tryParseInt(String input) {
    try { return Integer.valueOf(input); }
    catch (Exception ex) { return null; }
}
```
What this snippet does:
- Wraps parsing in a safe utility that returns `null` instead of throwing.
- Centralizes conversion failure handling for cleaner caller logic.

## Enums for controlled values
```apex
public enum StageType { NEW, QUALIFIED, CLOSED }
StageType stage = StageType.NEW;
if (stage == StageType.NEW) {
    System.debug('Initial stage');
}
```
What this snippet does:
- Defines a constrained status set to avoid fragile raw string comparisons.
- Uses enum equality for safer branching logic.

## sObjects and generic sObject
```apex
Account acc = new Account(Name = 'GenePoint');
sObject genericRef = acc;
String typeName = genericRef.getSObjectType().getDescribe().getName();
```
What this snippet does:
- Shows conversion from concrete object (`Account`) to generic `sObject`.
- Uses runtime describe metadata to inspect object type dynamically.

## List usage
```apex
List<String> codes = new List<String>{'IN', 'US'};
codes.add('DE');
String firstCode = codes[0];
```
What this snippet does:
- Demonstrates ordered collection behavior and index access with `List`.

Nested list pattern:
```apex
List<List<Integer>> grid = new List<List<Integer>>{
    new List<Integer>{1,2,3},
    new List<Integer>{4,5,6}
};
```
What this snippet does:
- Demonstrates nested collections for matrix/grouped data modeling.

## Set usage
```apex
Set<Id> accountIds = new Set<Id>();
for (Account a : [SELECT Id FROM Account LIMIT 5]) {
    accountIds.add(a.Id);
}
```
What this snippet does:
- Builds a unique Id set from query results for deduped filters and lookups.
- Good for uniqueness and `contains` checks.

## Map usage
```apex
Map<Id, Account> accById = new Map<Id, Account>(
    [SELECT Id, Name FROM Account LIMIT 10]
);
Account anyAcc = accById.values().isEmpty() ? null : accById.values()[0];
```
What this snippet does:
- Creates an Id-keyed map for fast record retrieval during bulk processing.
- Uses a safe empty-check before accessing map values.

## Operators refresher
```apex
Integer a = 12, b = 5;
Integer sumVal = a + b;
Integer diffVal = a - b;
a += 3;
Boolean isEqual = (a == b);
Boolean isGreater = (a > b);
Boolean isWithinRange = (a >= 10 && a <= 20);
```
What this snippet does:
- Shows arithmetic, assignment, comparison, and boolean composition patterns.
- Demonstrates clear boundary validation with `>=` and `<=`.

## Common mistakes
- Accessing list index without bounds check.
- Assuming set/map ordering.
- Using `Double` for financial calculations.
- Forgetting null checks before map/list access.

## Interview quick Q&A
- **Q:** Difference between List and Set?
- **A:** List keeps order and allows duplicates; Set enforces uniqueness.

- **Q:** Why Map in Apex-heavy code?
- **A:** Fast lookup and bulk-friendly relationship mapping by key (`Id`, external key).

## Practice set
1. Build method: `Map<String, Integer> countByCountry(List<Account> rows)`.
2. Convert CSV-like string numbers into `List<Integer>` with invalid token handling.
3. Given contacts, build `Map<Id, List<Contact>>` grouped by Account Id.

