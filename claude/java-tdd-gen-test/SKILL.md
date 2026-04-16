# Skill: /tdd-gen-test

## Trigger
`/tdd-j-gen-test [feature description]`

## Purpose
Generate a failing test for a described feature, following strict TDD Red phase discipline.
The test must fail because the implementation does not exist yet — not because the test is wrong.

---

## Behavior

When this command is invoked:

1. **Parse** the `[feature description]` argument as the full specification for the test target.
2. **Detect** the Java test framework from the current project context (JUnit 5 preferred; fall back to JUnit 4 or TestNG if the project already uses them). If ambiguous, default to JUnit 5 + AssertJ and state that assumption.
3. **Write a failing test class** that:
   - Targets a class or method that does not yet exist.
   - Follows the `Arrange / Act / Assert` structure with inline comments.
   - Uses descriptive, behavior-specification method names in `snake_case` or `camelCase` per project convention (e.g., `shouldReturnZeroWhenCartIsEmpty`).
   - Covers the primary happy path described in the feature, plus at least one edge case if inferable from the description.
   - Imports only the class that will need to be created — do not create it.
   - Uses `@DisplayName` annotations to document intent in plain English.
4. **Do NOT write any implementation code.** No stubs, no empty class shells, no placeholder methods.
5. **Append a `// RED` comment block** at the bottom of the test file confirming:
   - What the test expects.
   - Why it will fail (missing class / method).
   - What the next step is (`/tdd-gen-implementation`).

---

## Output Format

```
Java · JUnit 5 + AssertJ · RED Phase

<test class code block>

---
// RED
// Expects : [brief description of expected behavior]
// Fails because : [ClassName] does not exist yet
// Next step : run /tdd-gen-implementation
```

---

## Constraints

- Do not implement anything, even as a stub, empty class, or `// TODO` shell.
- Do not modify existing source files.
- Do not make assumptions about implementation details — test only observable behavior described in `[feature description]`.
- Place the test class in `src/test/java` following standard Maven/Gradle project layout.
- If `[feature description]` is too vague to write a meaningful test, ask one clarifying question before proceeding.

---

## Example

**Invocation:**
```
/tdd-gen-test calculate the total price of a shopping cart including tax
```

**Output (Java / JUnit 5 + AssertJ):**
```java
// RED Phase — /tdd-gen-test
// Feature: Calculate total price of a shopping cart including tax
// File: src/test/java/com/example/cart/ShoppingCartTest.java

package com.example.cart;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;

// ShoppingCart does not exist yet — compilation will fail, confirming RED phase
@DisplayName("ShoppingCart")
class ShoppingCartTest {

    @Test
    @DisplayName("should return zero total when cart is empty")
    void shouldReturnZeroTotalWhenCartIsEmpty() {
        // Arrange
        ShoppingCart cart = new ShoppingCart();

        // Act
        double total = cart.total(0.07);

        // Assert
        assertThat(total).isEqualTo(0.00);
    }

    @Test
    @DisplayName("should return subtotal plus tax when items are present")
    void shouldReturnSubtotalPlusTaxWhenItemsArePresent() {
        // Arrange
        ShoppingCart cart = new ShoppingCart();
        cart.addItem(100.00, 2);   // line total = 200.00

        // Act
        double total = cart.total(0.07);   // 200.00 * 1.07 = 214.00

        // Assert
        assertThat(total).isEqualTo(214.00);
    }

    @Test
    @DisplayName("should throw IllegalArgumentException when tax rate is negative")
    void shouldThrowWhenTaxRateIsNegative() {
        // Arrange
        ShoppingCart cart = new ShoppingCart();

        // Act / Assert
        assertThatThrownBy(() -> cart.total(-0.05))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessageContaining("tax rate");
    }
}

// RED
// Expects  : ShoppingCart class with addItem(double, int) and total(double) methods
// Fails because : ShoppingCart.java does not exist — project will not compile
// Next step : run /tdd-gen-implementation
```

---

## Maven / Gradle Dependencies (JUnit 5 + AssertJ)

**Maven (`pom.xml`):**
```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.10.2</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.assertj</groupId>
        <artifactId>assertj-core</artifactId>
        <version>3.25.3</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**Gradle (`build.gradle`):**
```groovy
dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter:5.10.2'
    testImplementation 'org.assertj:assertj-core:3.25.3'
}
test { useJUnitPlatform() }
```
