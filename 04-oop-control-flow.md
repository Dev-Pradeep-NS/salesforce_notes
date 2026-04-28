# 04 - OOP and Control Flow (Detailed)

## Learning goals
- Apply OOP principles in Apex class design.
- Use control structures to build predictable business logic.
- Write readable, maintainable branch/loop code.

## OOP in Apex
Core principles:
- Encapsulation
- Inheritance
- Polymorphism
- Abstraction

```apex
public virtual class DiscountPolicy {
    public virtual Decimal apply(Decimal amount) { return amount; }
}

public class VipDiscountPolicy extends DiscountPolicy {
    public override Decimal apply(Decimal amount) {
        return amount == null ? 0 : amount * 0.9;
    }
}
```
What this snippet does:
- Defines a base policy and a specialized VIP override to demonstrate polymorphism.
- Uses `virtual` and `override` so behavior can be extended cleanly.
- Includes null-safe handling in discount calculation.

## Class structure best practice
- Keep controller/trigger layers thin.
- Move business logic into service/domain classes.
- Keep utility classes stateless and static when possible.

## Access modifiers in practice
- `private`: class internal implementation.
- `protected`: extension-focused inheritance visibility.
- `public`: org-visible use.
- `global`: package/API-level broad visibility.

## Conditional logic patterns
```apex
public static String evaluateScore(Integer score) {
    if (score == null) return 'UNKNOWN';
    if (score >= 90) return 'A';
    if (score >= 75) return 'B';
    if (score >= 60) return 'C';
    return 'D';
}
```
What this snippet does:
- Uses early returns to keep grading logic flat and readable.
- Applies threshold checks in descending order and handles null input explicitly.

## Switch for cleaner branch rules
```apex
public static String statusLabel(String code) {
    switch on code {
        when 'NEW' { return 'New Lead'; }
        when 'QFD' { return 'Qualified'; }
        when 'CLO' { return 'Closed'; }
        when else { return 'Unknown'; }
    }
}
```
What this snippet does:
- Maps compact status codes to readable labels with `switch on`.
- Provides a default branch for unknown values.

## Looping strategies
- `for each` loop for collections.
- index loop only when position matters.
- avoid nested loops unless optimized with maps.

```apex
Map<Id, Account> accById = new Map<Id, Account>(
    [SELECT Id, Name FROM Account LIMIT 50]
);
for (Id accId : accById.keySet()) {
    System.debug(accById.get(accId).Name);
}
```
What this snippet does:
- Demonstrates map-based iteration for scalable Id-driven processing.
- Avoids nested lookups/loops by using keyed access.

## Anti-patterns to avoid
- Deep nested `if` chains without early exits.
- Complex loop logic that mixes querying, transformation, and DML.
- Hidden side effects inside getters.

## Interview quick Q&A
- **Q:** Can Apex support polymorphism?
- **A:** Yes, via `virtual` classes/methods and `override`.

- **Q:** Why use early returns?
- **A:** Reduces nesting, improves readability, and clarifies guard conditions.

## Practice set
1. Create base `NotificationService` and two implementations (Email/SMS stub).
2. Write method to classify records by score and status with clean branching.
3. Refactor nested loops into map-based lookup approach.

