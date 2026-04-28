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

Handle parse failures:
```apex
public static Integer tryParseInt(String input) {
    try { return Integer.valueOf(input); }
    catch (Exception ex) { return null; }
}
```

## Enums for controlled values
```apex
public enum StageType { NEW, QUALIFIED, CLOSED }
StageType stage = StageType.NEW;
if (stage == StageType.NEW) {
    System.debug('Initial stage');
}
```

## sObjects and generic sObject
```apex
Account acc = new Account(Name = 'GenePoint');
sObject genericRef = acc;
String typeName = genericRef.getSObjectType().getDescribe().getName();
```

## List usage
```apex
List<String> codes = new List<String>{'IN', 'US'};
codes.add('DE');
String firstCode = codes[0];
```

Nested list pattern:
```apex
List<List<Integer>> grid = new List<List<Integer>>{
    new List<Integer>{1,2,3},
    new List<Integer>{4,5,6}
};
```

## Set usage
```apex
Set<Id> accountIds = new Set<Id>();
for (Account a : [SELECT Id FROM Account LIMIT 5]) {
    accountIds.add(a.Id);
}
```
- Good for uniqueness and `contains` checks.

## Map usage
```apex
Map<Id, Account> accById = new Map<Id, Account>(
    [SELECT Id, Name FROM Account LIMIT 10]
);
Account anyAcc = accById.values().isEmpty() ? null : accById.values()[0];
```

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
