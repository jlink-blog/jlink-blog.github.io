---
title: "The Next Generation of Property-Based Testing"
description: "Jqwik 2: What PBT can do, if we let it"
status: publish
categories: []
tags:
- property-based testing
- jqwik
---

I've written about my struggle to [find funding for Jqwik 2 before](2024-08-09-jqwik-future-and-api.md).
However, I have never publicly described why I think the next generation of Jqwik is worth doing at all.
In short, I believe that the current choice of PBT tools is not yet where it could be.
And I don't mean just in Java, but in general.

The argument for Jqwik 2 has several parts:

- Jqwik's implementation has a few weaknesses that are hard to fix incrementally.
  The most important ones:
  1. The current way to generate values from random seeds has its limits.
    Using an approach similar to Python's Hypothesis will simplify the implementation
    of all the generators and allow for more sophisticated shrinking.
  2. Parallelizing the execution of properties is hard to implement in the current architecture.
     But parallelization to speed up run times is essential for improving the dev experience.

  I will not write about implementation details today, but the
  [proof of concept](https://github.com/jqwik-team/jqwik2-poc/) shows that both aspects 
  can be solved.

- The current API of Jqwik builds on top of JUnit's Platform API, especially the concept of test engines.
  This is a powerful abstraction which allows to control a property's lifecycle to a very find degree.
  However, using an additional test engine, is a barrier to entry for the plain-old Java developer,
  who just wants to go on using the well-known `@Test` annotation.
  Therefore, Jqwik 2 will also provide a more lightweight API that can be used with JUnit Jupiter -
  or any other testing framework.

- The final part of the argument is that PBT could be about much more than just generating random values.
  Some parts of Jqwik 1 already show this, but there is much more to explore.
  This is what the rest of this article is about.

## Next Generation Property-Based Testing

For me the core of PBT boils down to two things:

- Define a property with constraints and invariants.
- Exercise and evaluate the property by generating inputs that comply with the constraints.

The standard way is to (pseudo)-randomly generate inputs, run the property,
and shrink the input when the property fails.

There are many more ways you can make use of this core, though. 
Some ideas:

- Generate all inputs exhaustively, if possible.
  Jqwik 1 already has that capability.

- Guide your random generation by external criteria, e.g. code coverage.
  One can also use these criteria to guide the shrinking process.

- Generate "growing" inputs until the property fails or a threshold is reached.
  This idea is not new, some libs use it as a replacement for random generation.

- Generate only typical edge cases plus a small number of random ones in-between edge cases.
  This is a good way to test the boundaries of your system.

- Generate inputs based on a domain model (e.g. describing relations and
  constraints) or a specification.
    - Use those inputs to test implementation
    - Use those inputs to test the model itself by displaying generated
      instantiations of the model.
      This could be helpful in the realm of domain-driven design.

- When you have a failing property, analyse it by collecting additional information from the
  falsified samples:
     - Collect all falsified and satisfied samples and find common laws, eg: "p1 > 100 always fails"
     - Start from failing sample and vary on parts of the input 
       to find parameters that are not related to the failure.

- Validate a property statistically, e.g. by requiring a certain percentage of successful runs.
  This is especially useful when you have non-deterministic systems like many machine learning applications.

- Use a combination of growing inputs and edge cases to (semi-)automatically test-drive a feature.
  Since this feels like a more crazy idea, I will show you how it could work in the next section.

## Automated Test-Driven Development

I'll guide you through the steps of test-driving a simple FizzBuzz implementation
using the proof-of-concept of Jqwik 2 and a class `TDD` that serves as an API 
to the TDD process.
The code can be run as a JUnit Jupiter test method or a jqwik example method.


### Step 1

Let's start with exploring our input domain, which is the range of positive integers between 1 and 1 million.

```java
TDD.forAll(Numbers.integers().between(1, 1_000_000))
   .drive();
```

Since the (automatic) test-driving process starts from the simplest possible case, we get the following result:

```
NOT COVERED:
  [1]
```

Mind that we did not have to write any assertion code yet. 
Our TDD automator will take care of that for us.

### Step 2

Let's tackle the failing test case (`1`) by verifying the expected output:

```java
TDD.forAll(Numbers.integers().between(1, 1_000_000))
   .verifyCase(
      "one", // Name of the case
      number -> number == 1, // Precondition for the case
      number -> { // Assertions
        var result = fizzBuzz(number);
        assertThat(result).isEqualTo("1");
      }
    )
   .drive();
```

To make the code compile, we need to provide a stub implementation for `fizzBuzz`:

```java
String fizzBuzz(int number) {
  return null;
}
```

Now we have a first semantically failing test case:

```
one:
  [1]: FALSIFIED
    org.opentest4j.AssertionFailedError:
      expected: "1"
       but was: null
```

### Step 3

Let's fix the failing test case:

```java
String fizzBuzz(int number) {
  return "1";
}
```

Now we see that the TDD automator can differentiate between falsified cases, satisfied cases and uncovered input values:

```
one:
  [1]: SATISFIED

NOT COVERED:
  [2]
```

### Step 4

__TODO: Let's continue from here__

```java
TDD.forAll(Numbers.integers().between(1, 1_000_000))
   .verifyCase(
     "normal numbers", 
     number -> true,
     number -> {
       var result = fizzBuzz(number);
       assertThat(result).isEqualTo(Integer.toString(number));
     }
   ) 
   .drive();
```

_Result:_

```
normal numbers:
  [1]: SATISFIED
  [2]: FALSIFIED
    org.opentest4j.AssertionFailedError:
      expected: "2"
       but was: "1"
```

### Step 5

```java
String fizzBuzz(int number) {
    return "" + number;
}
```

_Result:_

```
normal numbers:
  [1]: SATISFIED
  [2]: SATISFIED
  [100]: SATISFIED
```

### Step 6

```java
@TestDrive
class FizzBuzzTests {
    @ForAll @IntRange(min = 1) number;


    @Case
    void normal_numbers() {
        Assume.that(number % 3 != 0);
        var result = fizzBuzz(number);
        assertThat(result).isEqualTo(Integer.toString(number));
    }

    @Case
    void three() {
        Assume.that(number == 3);
        var result = fizzBuzz(number);
        assertThat(result).isEqualTo("Fizz");
    }

    String fizzBuzz(int number) {
        return "" + number;
    }
}
```

### Result

normal numbers
  [1] SATISFIED
  [2] SATISFIED
  [1000] SATISFIED

three
  [3] FAILED: expected Fizz but was 3


```java
String fizzBuzz(int number) {
    if (number % 3 == 0) {
        return "Fizz";
    }
    return "" + number;
}
```

### Result

normal numbers
  [1] SATISFIED
  [2] SATISFIED
  [6] FAILED: expected Fizz but was 3

three
  [3] SATISFIED


```java
@TestDrive
class FizzBuzzTests {
    @ForAll @IntRange(min = 1) number;


    @Case
    void normal_numbers() {
        Assume.that(number % 3 != 0);
        var result = fizzBuzz(number);
        assertThat(result).isEqualTo(Integer.toString(number));
    }

    @Case
    void divisible_by_3() {
        Assume.that(number % 3 == 0);
        var result = fizzBuzz(number);
        assertThat(result).isEqualTo("Fizz");
    }

    String fizzBuzz(int number) {
        if (number % 3 == 0) {
            return "Fizz";
        }
        return "" + number;
    }
}
```

### Result

normal numbers
  [1] SATISFIED
  [2] SATISFIED
  [1000] SATISFIED

divisible by 3
  [3] SATISFIED
  [6] SATISFIED
  [999] SATISFIED

########################

```java
@TestDrive
void fizzBuss(@ForAll @IntRange(min = 1) number) {
    var result = fizzBuzz(number);
    if (number % 3 == 0) {
        assertThat(result).isEqualTo("Fizz");
    } else if (number % 5 == 0) {
        assertThat(result).isEqualTo("Buzz");
    } else {
        assertThat(result).isEqualTo(Integer.toString(number));
    }
    done();
}
```

Result:
- "Input: 1 -> Success"
- "Input: 2 -> Success"
- "Input: 3 -> Success"
- "Input: 5 -> Failed: expected Buzz but was 5"

```java
@TestDrive
void fizzBuss(@ForAll @IntRange(min = 1) number) {
    var result = fizzBuzz(number);
    if (number % 3 == 0) {
        assertThat(result).isEqualTo("Fizz");
    } else if (number % 5 == 0) {
        assertThat(result).isEqualTo("Buzz");
    } else {
        assertThat(result).isEqualTo(Integer.toString(number));
    }
    done();
}
```

Result:
- "Input: 1 -> Success"
- "Input: 2 -> Success"
- "Input: 3 -> Success"
- "Input: 5 -> Success"
- "Input: 6 -> Success"
- "Rest -> Success"

```java
@TestDrive
void fizzBuss(@ForAll @IntRange(min = 1) number) {
    var result = fizzBuzz(number);
    if (number % 3 == 0) {
        assertThat(result).startsWith("Fizz");
    } 
    if (number % 5 == 0) {
        assertThat(result).endsWith("Buzz");
    }
    if (number % 3 != 0 && number % 5 != 0) {
        assertThat(result).isEqualTo(Integer.toString(number));
    }
    done();
}
```

Result:
- "Input: 1 -> Success"
- "Input: 2 -> Success"
- "Input: 3 -> Success"
- "Input: 5 -> Success"
- "Input: 6 -> Success"
- "Input: 15 -> Failed: expected Fizz to end with Buzz"
