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

For me the core idea of PBT boils down to two things:

- Define a property with constraints and invariants.
- Exercise and evaluate the property by generating inputs that comply with the constraints.

The standard way is to generate (pseudo)-random inputs, run the property,
and shrink the input when the property fails.

There are many more ways you can make use of the core idea, though. 
For example:

- Generate all possible inputs exhaustively, if possible.
  For combinatorial reasons this is only feasible for small input domains;
  but when it is possible, it can be a powerful way to ensure correctness.
  Jqwik 1 already has that capability.

- Guide your random generation by external criteria, e.g. code coverage.
  A few PBT libraries offer this approach.
  Some studies suggest that this can be a good way to find bugs in fewer tries
  than by going random only.

- Generate "growing" inputs until the property fails or a threshold is reached.
  This idea is not new, some libs use it as a replacement for random generation.
  In my opinion, it is a useful approach when combined with other ideas.

- Generate only edge cases plus a small number of random ones in-between edge cases.
  This is what you would expect from handwritten test cases, but automated.

- Generate inputs based on a domain model (e.g. describing relations and
  constraints) or a specification.
    - Use those inputs to test the implementation.
    - Use those inputs to test the model itself by displaying generated
      instantiations of the model.
      This could be helpful in the realm of domain-driven design.

- Analyse a failing property by collecting additional information from the falsified samples:
     - Detect common laws across failing and succeeding samples, eg: "p1 > 100 always fails"
     - Start from failing sample and vary on parts of the input 
       to find parameters that are not related to the failure.

- Validate a property statistically, e.g. by requiring a certain percentage of successful runs.
  This is especially useful when you have non-deterministic systems like many machine learning applications.

- Automate part of the test-driving process through a combination of growing inputs and edge cases.
  This is a more crazy idea, and I will show you how it could work in the next section.

## Semi-Automated Test-Driven Development

I'll guide you through the steps of test-driving a simple FizzBuzz implementation
using the proof-of-concept of Jqwik 2 and a class `TDD` that serves as an API 
to the TDD process.
It's using a combination of generate growing input data and using edge cases. 

Imaging the code to be run as a JUnit Jupiter test method or a jqwik example method.


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

Mind that we did not write any assertion code yet. 
Our TDD automator notices that and reports missing coverage.

### Step 2

In good TDD style let's tackle the simplest test case (`1`) first:

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

Now we have a first test failure:

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

Now we see that the TDD automator can differentiate between falsified cases, 
satisfied cases and uncovered input values:

```
one:
  [1]: SATISFIED

NOT COVERED:
  [2]
```

### Step 4

The uncovered case `2` suggests that we can generalize our test case 
to cover all "normal" numbers, i.e. those that are not divisible by 3 or 5:

```java
TDD.forAll(Numbers.integers().between(1, 1_000_000))
   .verifyCase(
     "normal numbers", 
     number -> i -> i % 3 != 0 && i % 5 != 0,
     number -> {
       var result = fizzBuzz(number);
       assertThat(result).isEqualTo(Integer.toString(number));
     }
   ) 
   .drive();
```

The result now differs slightly from the previous one:

```
normal numbers:
  [1]: SATISFIED
  [2]: FALSIFIED
    org.opentest4j.AssertionFailedError:
      expected: "2"
       but was: "1"
```

We still see that `1` is satisfied, but `2` is failing.

### Step 5

Let's fix the failing test case...

```java
String fizzBuzz(int number) {
    return "" + number;
}
```

... and rerun the property:

```
normal numbers:
  [1]: SATISFIED
  [2]: SATISFIED
  [1..98]: SATISFIED
  [999998]: SATISFIED
  
NOT COVERED:
  [3]
```

And again, the TDD automator tells us two things:
1. The "normal numbers" case is fully covered until `98`;
   moreover, the edge case `999998` can also be satisfied.
2. We have input data that is not covered, the smallest of which is `3`.

### Alternative Syntax

In the 5 steps above I showed you how to use the `TDD` class to guide you through the TDD process.
The same could be achieved using a more JUnit-like syntax and test engine.
Maybe you prefer this style:

```java
@TestDrive
class FizzBuzzTests {
    @ForAll @IntRange(min = 1) number;
	
    @Case
    void normal_numbers() {
        Assume.that(!isDivisibleBy(number, 3) && !isDivisibleBy(number, 5));
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

With this syntax the same TDD automator could be triggered, providing results of the same kind:
  
```
normal numbers:
  [1]: SATISFIED
  [2]: SATISFIED
  [1..98]: SATISFIED
  [999998]: SATISFIED
  
three
  [3] FAILED: expected Fizz but was 3
```

## Conclusion

I hope I could convince you that the current state of PBT tooling is not the end of the road.
There are many more ways to use the core idea of Property-based Testing than just generating random values.
If you want to see some of these ideas come true, help me make Jqwik 2 a reality,
for example by becoming a [sponsor for the jqwik-team](https://github.com/sponsors/jqwik-team).
