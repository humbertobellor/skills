# Skill: /tdd-gen-implementation

## Trigger
`/tdd-j-gen-implementation`

## Purpose
Write the minimum production code required to make the current failing tests pass.
This is the TDD Green phase. No more code than necessary — no anticipating future features.

---

## Behavior

When this command is invoked:

1. **Read the existing test class(es)** from the current context. If no test file is visible, ask the user to provide or paste the failing tests before proceeding.
2. **Identify all failing assertions** and the interfaces they imply — class names, method signatures, return types, and exceptions.
3. **Write the minimum implementation** that satisfies every test:
   - Create only the classes and methods referenced by the tests.
   - Return only the values the tests assert — do not add logic for untested scenarios.
   - Do not write helper utilities, abstractions, or optimizations that are not demanded by a test.
   - Do not modify the test class.
   - Follow standard Java conventions: `src/main/java` package structure, proper access modifiers, Javadoc only where the method's contract is non-obvious.
4. **Annotate the implementation** with a `// GREEN` comment block confirming:
   - Which test methods it is designed to pass.
   - What was intentionally omitted (no gold-plating).
   - What the next step is (`/tdd-refactor`).
5. **Remind the user** to run the test suite (`mvn test` or `./gradlew test`) to confirm green before proceeding.

---

## Output Format

```
Java · GREEN Phase

<implementation class code block>

---
// GREEN
// Passes  : [list of test method names]
// Omitted : [anything intentionally left out]
// Next step : run /tdd-refactor
```

---

## Constraints

- **Minimum viable implementation only.** If a hardcoded return value makes the tests pass, that is acceptable at this phase.
- Do not refactor. Do not optimize. Do not extract interfaces or abstract classes unless directly required by a test.
- Do not add exception handling beyond what is explicitly tested.
- Do not introduce Lombok, Spring, or other frameworks unless the test already imports them.
- Do not create classes or methods that no test references.
- If the test suite is ambiguous or internally contradictory, surface the conflict and ask before writing code.

---

## Example

**Context (from `/tdd-gen-test`):**
```java
// ShoppingCartTest.java — three failing tests:
// shouldReturnZeroTotalWhenCartIsEmpty
// shouldReturnSubtotalPlusTaxWhenItemsArePresent
// shouldThrowWhenTaxRateIsNegative
```

**Output (Java):**
```java
// GREEN Phase — /tdd-gen-implementation
// File: src/main/java/com/example/cart/ShoppingCart.java

package com.example.cart;

import java.util.ArrayList;
import java.util.List;

public class ShoppingCart {

    private final List<Double> lineTotals = new ArrayList<>();

    public void addItem(double price, int quantity) {
        lineTotals.add(price * quantity);
    }

    public double total(double taxRate) {
        if (taxRate < 0) {
            throw new IllegalArgumentException(
                    "tax rate must be non-negative, got: " + taxRate);
        }
        double subtotal = lineTotals.stream()
                .mapToDouble(Double::doubleValue)
                .sum();
        return subtotal * (1 + taxRate);
    }
}

// GREEN
// Passes  : shouldReturnZeroTotalWhenCartIsEmpty,
//            shouldReturnSubtotalPlusTaxWhenItemsArePresent,
//            shouldThrowWhenTaxRateIsNegative
// Omitted : discount logic, currency rounding, item removal,
//            persistence, inventory validation — not tested yet
// Next step : run /tdd-refactor
// Verify  : mvn test  OR  ./gradlew test
```
