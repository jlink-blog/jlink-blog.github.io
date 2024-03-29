---
title: "Property-based Testing in Java: The Importance of Being Shrunk"
description: "How PBT libraries try to be more helpful"
status: publish
categories: []
tags:
- property-based testing
- shrinking
- java
- jqwik
---
In [the previous episode]({% post_url 2018-03-29-jqwik-on-junit5 %})
we've been looking at some important features of jqwik and other PBT libraries.
It's now time to check out how those libs try to help us with overcoming
some of the drawbacks of random generation.

# The Importance of Being Shrunk

One problem that comes with random generation is the loose relation between
the randomly chosen falsifying example and the problem underlying the failing
property. A simple example illustrates this concern:

```java
@Property(shrinking = ShrinkingMode.OFF)
boolean rootOfSquareShouldBeOriginalValue(@Positive @ForAll int anInt) {
    int square = anInt * anInt;
    return Math.sqrt(square) == anInt;
}
```

The property states the trivial mathematical concept that _the square root of a
squared value should be equal to the originial value_.
We have switched off "shrinking" using
the `shrinking` annotation attribute.
Running this property will fail with something like the following message:

```
org.opentest4j.AssertionFailedError:
    Property [rootOfSquareShouldBeOriginalValue] falsified with sample [1207764160]

originalSample = [1207764160],
sample = [1207764160]
```

The falsified sample found by _jqwik_ is random. Unless you already have an inkling of what
the issue might be, the number itself does not give you an obvious hint.
Even the fact that it's rather large might be a coincidence. At this point
you will either add additional logging, introduce assertions or even
start up the debugger to get more information about the problem at hand.

Let's take a different route, turn shrinking on with (`ShrinkingMode.FULL`)
and rerun the property:


```
originalSample = [1207764160],
sample = [46341]

org.opentest4j.AssertionFailedError:
    Property [rootOfSquareShouldBeOriginalValue] falsified with sample [46341]
```

The number `46341` is much smaller and it is different from the _original
sample_. What _jqwik_ did after finding the failing data point `1207764160`
is trying to find a simpler or even "the simplest" example that
fails for (hopefully) the same reason. This searching phase is called
_shrinking_ because it starts with the original sample and tries to
make it smaller and check the property again.

In the case of integral numbers shrinking is rather simple because _smaller_
is well defined. Nevertheless, just counting the number down could take a
very long time, therefore _jqwik_ will use a few tricks to shrink fast and
still not miss a special value. If you want to see which steps are being tried
inbetween the original and the final sample, you can switch on reporting of
falsified values:

```
@Property
@Report(Reporting.FALSIFIED)
```

In this example 46341 is indeed the smallest integer
number that can falsify the property. And what's the special thing about it?
As you might have guessed the square of 46341 is 2147488281 which is larger
than `Integer.MAX_VALUE` and will thus lead to integer overflow.
Conclusion: The property above only holds for int-type numbers up to the
square root of `Integer.MAX_VALUE`.

## Container Types

The task of shrinking gets a bit trickier when we have to deal with
containers or other composed types. Here's a property which checks a broken
implementation of `reverse`.


```java
static <E> List<E> brokenReverse(List<E> aList) {
    if (aList.size() < 4) {
        aList = new ArrayList<>(aList);
            reverse(aList);
        }
        return aList;
    }

@Property
boolean reverseShouldSwapFirstAndLast(@ForAll List<Integer> aList) {
    Assume.that(!aList.isEmpty());
    List<Integer> reversed = brokenReverse(aList);
    return aList.get(0) == reversed.get(aList.size() - 1);
}
```   

The output should look similar to this:

```
org.opentest4j.AssertionFailedError:
    Property [reverseShouldSwapFirstAndLast] falsified with sample [[0, 0, 0, -1]]

sample = [[0, 0, 0, -1]]
originalSample = [[0, 1, -1, -2147483648, 2147483647, -932716982, ...]],
```

What we can see is that shrinking took place on different levels. Both the length of the list
and the individual list elements got stripped down to what _jqwik_ considered to be the
simplest example still failing: A list with 4 elements in which only a single element
differs from all the others. And already we could argue about if this really is
"the simplest" example: Wouldn't "1" be even simpler than "-1"? Should the different
element be on the first position?  

## The Simplest Falsifying Sample

So we might agree that in the examples above _jqwik_ was indeed able to find the simplest
values that produce the failure. In general, however, this is not always
the case. Why's that? A few reasons:

- It's not always easy to agree on what "simplest" means in a given context.
  Would you consider +5 to be simpler than -5? Maybe yes. +2 to be simpler than -1? Hmm...

  As soon as you get in the realm of combined arbitraries and more than
  one parameter there can be several shrinking targets that could all be
  considered to be "the simplest". There might even be an innumerous number of them.
  Later, I'll show an example with more than one potential best shrunk value.

- Even if we can agree on a common metric for "the simplest" shrinking
  might not find it. From a computational point of view, shrinking is a
  search problem with an almost infinite search space. That's why
  _jqwik_ - or any PBT library - has to use heuristics in order to prune the
  search space as much as possible. The heuristics of one tool might
  be superior in one scenario but fail to produce any reasonable simplification
  in another.

- For shrinking to work at all, deterministic property checking is crucial.
  As long as a property can be considered to be (practically) a pure
  function - i.e. only the generated values influence checking results - everything
  is fine. But as soon as you have indeterministic effects in falsification
  itself the whole approach breaks down. That's why concurrent property
  testing and shrinking often don't go together.

My recommendation: Don't expect any library to always find the simplest
falsifying sample for you. Instead, develop an intuition for what the
library can reasonable do in terms of simplifying randomly found samples.
And sometimes, simplifying the parameters you use in the property,
will make shrinking easier.

## Type-Based versus Integrated Shrinking

The original
[QuickCheck for Haskell](https://hackage.haskell.org/package/QuickCheck)
relies mostly on the type information for shrinking. That's to say
that when you have - for example - a parameter of type `String` that results
in a failed property, the framework will use a generic shrinking strategy for
`String` values, regardless of how the value was generated in the first place.

Given an expressive type system like Haskell's this _type-based shrinking_
is usually not a big problem since Haskell programmers aim at encoding
all domain constraints in type signatures. When you're dealing with a
less expressive type system like Java's or when essential property
preconditions are used explicitely for value generation, the type-based
approach can lead to problems.

Let's consider the following example:

```java
@Property
boolean shouldShrinkTo101(@ForAll("numberStrings") String aNumberString) {
    return Integer.parseInt(aNumberString) % 2 == 0;
}

@Provide
Arbitrary<String> numberStrings() {
    return Arbitraries.integers().between(100, 10000).map(String::valueOf);
}
```

When _jqwik_ is executing this property one of the created examples
will eventually be the string representation of an odd number. Let's
assume the value is `"197"`. A merely type-based shrinking approach
would probably try to remove characters and eventually end up at `"1"` as the
simplest falsifiable example. What a pity that `"1"` is outside the range
of values that can potentially be generated.
Therefore, it should never be tried during shrinking!

[_Integrated shrinking_](http://hypothesis.works/articles/integrated-shrinking/)
is an alternative technique which considers all
information present at generation time while shrinking. _jqwik_
actually has an _integrated shrinker_ and will therefore never shrink to values
outside the generating constraints.
In the example above the value to which _jqwik_ eventually shrinks
is `"101"`.

The drawback of _integrated shrinking_ is the complexity of shrinking
mechanisms. For the users of PBT libraries this is mostly transparent,
but for the implementors of those libraries this can be a real burden.
To get a grasp of the difficulty involved, try to understand the following
complicated (even if contrived) property:

```java
@Property
boolean shrinkingCanBeComplicated(
        @ForAll("first") String first,
        @ForAll("second") String second
) {
    String aString = first + second;
    return aString.length() < 4 || aString.length() > 5;
}

@Provide
Arbitrary<String> first() {
    return Arbitraries.strings()
            .withCharRange('a', 'z')
            .ofMinLength(1).ofMaxLength(10)
            .filter(string -> string.endsWith("h"));
}

@Provide
Arbitrary<String> second() {
    return Arbitraries.strings()
            .withCharRange('0', '9')
            .ofMinLength(0).ofMaxLength(10) 
            .filter(string -> string.length() >= 1);
}
```

_jqwik_ is able to shrink to either `["h", "000"]` or `["aah", "0"]`
or `["ah", "00"]` depending on the initial random seed. Given the
involved compositions, mappings, filters and constraints this is
quite an accomplishment. Don't you think so, too?

## Next Episode

In the [next article]({% post_url 2018-07-16-patterns-to-find-properties %})
we will look at a few patterns that can guide you to identify
and formulate properties for your own code.
