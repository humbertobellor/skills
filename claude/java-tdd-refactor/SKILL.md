# Skill: /tdd-refactor

## Trigger
`/tdd-j-refactor`

## Purpose
Improve the quality of a passing implementation without changing its observable behavior.
This is the TDD Refactor phase. Tests must remain green throughout every change.

---

## Behavior

When this command is invoked:

1. **Read the current implementation and test classes** from context. If neither is visible, ask the user to paste them before proceeding.
2. **Confirm tests are green** — if the user has not confirmed a passing suite, prompt them to run `mvn test` or `./gradlew test` first and report results.
3. **Identify refactoring opportunities** across these dimensions (evaluate all; apply only those warranted):

   | Focus Area | What to Look For |
   |---|---|
   | **Readability** | Unclear names, missing Javadoc, magic literals, dense streams or conditionals |
   | **Performance** | Redundant iterations, unnecessary object creation, avoidable recomputation |
   | **Duplication** | Repeated logic that can be extracted into a named private method or shared utility |

4. **Apply refactors incrementally** — present one logical change at a time if multiple are needed, so each can be verified independently.
5. **Do not change behavior.** No new logic, no new branches, no new features. If a refactor would require a new test to verify, stop and flag it as a feature addition, not a refactor.
6. **Annotate the refactored output** with a `// REFACTOR` block listing:
   - What changed and why.
   - What was deliberately left unchanged.
   - Confirmation that no test modifications were needed.

---

## Output Format

```
Java · REFACTOR Phase

<refactored class code block>

---
// REFACTOR
// Changed  : [list of changes with rationale]
// Unchanged : [what was left alone and why]
// Tests modified : None  ← must always be None; if not, explain why
// Verify : mvn test  OR  ./gradlew test — all tests must remain green
```

---

## Constraints

- **Zero behavior change.** Any observable difference is a bug, not a refactor.
- Do not add new tests during this phase. If a refactor reveals untested behavior, note it as a follow-up for the next `/tdd-gen-test` cycle.
- Do not combine refactoring with feature additions. Flag scope creep and stop.
- If the implementation is already clean, say so explicitly — do not invent changes.
- Preserve all existing test method names, assertions, and structure. Refactor test *code* only if it contains obvious duplication (e.g., repeated `new ShoppingCart()` setup that belongs in a `@BeforeEach`) and the change is semantically neutral.
- Do not introduce frameworks (Lombok, Spring, Guava) unless the project already uses them.

---

## Refactoring Heuristics Applied (Java-Specific)

Claude evaluates the implementation against these signals:

| Signal | Check |
|---|---|
| **Naming** | Does every field, method, and variable communicate intent without a comment? |
| **Single Responsibility** | Does each class and method do exactly one thing? |
| **Magic Literals** | Are numeric/string literals replaced by named constants (`static final`)? |
| **Guard Clauses** | Can nested `if` blocks be flattened with early returns or `Objects.requireNonNull`? |
| **Stream Clarity** | Are stream pipelines readable, or should they be decomposed into named steps? |
| **Javadoc** | Do public API methods have accurate `@param`, `@return`, and `@throws` tags? |
| **Immutability** | Can mutable fields be made `final`? Can collections be wrapped in `Collections.unmodifiable*`? |
| **DRY** | Is any logic repeated two or more times in a way that obscures intent? |
| **Checked vs Unchecked** | Is the correct exception hierarchy used (`IllegalArgumentException` vs custom types)? |

---

## Example

**Input (from `/tdd-gen-implementation`):**
```java
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
```

**Output:**
```java
// REFACTOR Phase — /tdd-refactor
// File: src/main/java/com/example/cart/ShoppingCart.java

package com.example.cart;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

/**
 * Represents a shopping cart that accumulates line items
 * and calculates the order total inclusive of a given tax rate.
 */
public class ShoppingCart {

    private final List<Double> lineTotals = new ArrayList<>();

    /**
     * Adds an item to the cart by recording its line total.
     *
     * @param price    the unit price of the item (must be non-negative)
     * @param quantity the number of units purchased
     */
    public void addItem(double price, int quantity) {
        lineTotals.add(price * quantity);
    }

    /**
     * Returns the cart total with tax applied.
     *
     * @param taxRate a non-negative decimal multiplier (e.g., {@code 0.07} for 7%)
     * @return the subtotal multiplied by {@code (1 + taxRate)}
     * @throws IllegalArgumentException if {@code taxRate} is negative
     */
    public double total(double taxRate) {
        validateTaxRate(taxRate);
        double subtotal = computeSubtotal();
        return subtotal * (1 + taxRate);
    }

    // -------------------------------------------------------------------------
    // Private helpers
    // -------------------------------------------------------------------------

    private void validateTaxRate(double taxRate) {
        if (taxRate < 0) {
            throw new IllegalArgumentException(
                    String.format("taxRate must be non-negative, got: %.4f", taxRate));
        }
    }

    private double computeSubtotal() {
        return lineTotals.stream()
                .mapToDouble(Double::doubleValue)
                .sum();
    }
}

// REFACTOR
// Changed  :
//   - Extracted validateTaxRate(double) — separates guard logic from business logic
//   - Extracted computeSubtotal() — names the intent of the stream reduction
//   - Added class-level and method-level Javadoc with @param/@return/@throws
//   - Improved String.format in exception message for consistent decimal display
// Unchanged :
//   - All logic — behavior is identical to the GREEN phase implementation
//   - List<Double> backing store — no reason to change data structure
//   - Exception type (IllegalArgumentException) — already correct
// Tests modified : None
// Verify : mvn test  OR  ./gradlew test — all tests must remain green
```
