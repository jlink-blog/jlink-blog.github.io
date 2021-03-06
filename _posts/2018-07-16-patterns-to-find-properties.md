---
title: "Property-based Testing in Java: Patterns to Find Properties"
description: "How to discover properties for your code"
status: publish
categories: []
tags:
- property-based testing
- patterns
- java
- jqwik
---
It's been 3 months that I tried to shed some light on the importance of
[shrinking failed properties]({% post_url 2018-04-20-the-importance-of-being-shrunk %}).
Now that we've covered many of the technicalities of the approach it's
time to occupy with how to use PBT in practice.

# Patterns to Find Good Properties

When you're taking your early steps with PBT finding suitable properties can
feel like an almost impossible task. Whereas examples often appear naturally
when thinking and talking about the functionality of a system, properties
are often more abstract. They require a different kind
of thinking. A set of useful _patterns_ for finding
properties would come in handy.

Luckily, we do not have to discover all things on our own. PBT has
been around for a while and there is a small but well known collection
of [Property-based Testing Patterns](https://blog.ssanj.net/posts/2016-06-26-property-based-testing-patterns.html).
My personal list is certainly incomplete, and apart from the first one
I've stolen all names elsewhere:

- __Business Rule as Property__ (aka _Obvious Property_)

  Sometimes the domain specification itself can be interpreted
  and [written as a property](#pattern-business-rule-as-property).

- __Fuzzying__

  Code should never explode, even if you feed it with lots of diverse and unforeseen
  input data. Thus, the main idea is to generate the input, execute the
  function under test and check that:

  - No exceptions occur, at least no unexpected ones
  - No 5xx return codes in http requests, maybe you even require 2xx all the time
  - All return values are valid
  - Runtime is under an acceptable threshold
  - Using the same input several times will produce the same outcome

  _Fuzzying_ is often done in retrospect when you want to examine the robustness
  of existing code. It's also more common in
  _integrated testing_ since during _micro or unit testing_ you can
  usually be more specific than just demanding "Don't blow up!".


- __Inverse Functions__

  If a function has an inverse function, then applying function first
  and inverse second [should return the original input](#inverse-functions).

- __Idempotent Functions__

  The multiple application of an idempotent function should not change results.
  Ordering a list a second time, for example, shouldn't change it anymore.

- __Invariant Functions__

  Some properties of our code do not change after applying our logic.
  For example:

  - Sorting and mapping should never change the size of a collection.
  - After filtering out some values, the remaining values should
    still be in the original order.
  - When you use stateful objects in sets or as keys in maps, whatever
    you do with them should never change their hash value.

- __Commutativity__

  If a set of functions is commutative, change of order in applying the functions
  should not change the final result. Think of _sorting then filtering_ should
  have the same effect as _filtering then sorting_.

- __Test Oracle__

  Sometimes we know of an alternative implementation for our function or algorithm
  which is called a _test oracle_.
  In this case any result of using the function should be the same for both
  original and alternative. There are a few sources where the alternatives
  can come from:

  - Simple and slow versus complicated but fast
  - Parallel versus single-threaded
  - Self-made versus commercial
  - Old (pre-refactoring) versus new (post-refactoring)

- __Hard to compute, easy to verify__ (aka _Black-box Testing_)

  Some logic is hard to execute but easy to check. Consider e.g.
  the effort for _finding prime numbers_ versus _checking a prime number_.

- __Induction: Solve a smaller problem first__

  You might be able to divert your domain check in a base case and
  a general rule [derived from that base case](#induction).

- __Stateful Testing__

  Especially in the OO world, an object's behaviour can often (partially)
  be described as a _state machine_ with a finite set of states and actions
  to move from state to state. Exploring the space of transitions is
  an important use case for PBT, that's why I will dedicate a later article
  to it.

Let's dive into more details and a bit of code for some of the patterns...

## Pattern: Business Rule as Property

Consider this business rule, which could be directly from a domain
expert's written specification:

> For all customers with last year's sales volume above 5000 EUR
> we will provide an additional discount of 5 percent, if the invoice's
> total amount is equal or above 500 EUR.

You could, for example, split this discount rule into two properties:

```java
@Property
@Label("High volume customer discount is 5% for total above 500 EUR")
boolean highVolumeCustomerDiscount(@ForAll Euro lastYearVolume, @ForAll Euro invoiceTotal) {
    Assume.that(lastYearVolume.compareTo(Euro.amount(5000)) > 0);
    Assume.that(invoiceTotal.compareTo(Euro.amount(500)) >= 0);

    return new DiscountCalculator().discountInPercent(lastYearVolume, invoiceTotal)
        .equals(new Percent(5));
}

@Property
@Label("No discount for low volume customer or total below 500 EUR")
boolean noDiscount(@ForAll Euro lastYearVolume, @ForAll Euro invoiceTotal) {
    Assume.that(
        lastYearVolume.compareTo(Euro.amount(5000)) <= 0 ||
        invoiceTotal.compareTo(Euro.amount(500)) < 0
    );

    return new DiscountCalculator().discountInPercent(lastYearVolume, invoiceTotal)
        .equals(new Percent(0));
}
```

These properties are on a micro testing level; the customer itself with
their transactions of last year and their current order does not show up
at all. Instead we check the property against an exposed discount calculating
function which only takes the necessary values as input.

You might also consider to verify the business rule with similar properties
using _integrated testing_ filling a real user database, creating transaction histories
and an invoice referring to an order for a set of items in your inventory store.
Keep in mind, however,
that _integrated property testing_ has the same drawbacks as _integrated
example testing_: High setup costs, slower execution, increased non-determinism
and low precision. In addition, you have to multiply the individual runtime
by the number of checks: If a single integrated check takes one second,
1000 runs of this check will take more than 15 minutes.


## Pattern: Inverse Functions

You have already seen an example of this pattern. In the
[second episode]({% post_url 2018-03-26-from-examples-to-properties %})
we checked JDK's `Collections.reverse()` method. _Reverse_ is a special
case since it is its own inverse function.

Let's therefore try something else, namely `URLEncoder.encode` and
`URLDecoder.decode`. Both functions take a string to en/decode and the
name of a charset as arguments. The property method to check the "invertibility"
of encoding any string with any charset is fairly simple:

```java
@Property
void encodeAndDecodeAreInverse(
        @ForAll @StringLength(min = 1, max = 20) String toEncode,
        @ForAll("charset") String charset
) throws UnsupportedEncodingException {
    String encoded = URLEncoder.encode(toEncode, charset);
    assertThat(URLDecoder.decode(encoded, charset)).isEqualTo(toEncode);
}

@Provide
Arbitrary<String> charset() {
    Set<String> charsets = Charset.availableCharsets().keySet();
    return Arbitraries.of(charsets.toArray(new String[charsets.size()]));
}
```

What surprised me - and might surprise you: _Decode_ is NOT an inverse
function of _encode_ for all charsets:

```
org.opentest4j.AssertionFailedError:
  Expecting:
   <"?">
  to be equal to:
   <"€">
  but was not.

                              |-----------------------jqwik-----------------------
tries = 13                    | # of calls to property
checks = 13                   | # of not rejected calls
generation-mode = RANDOMIZED  | parameters are randomly generated
after-failure = SAMPLE_FIRST  | try previously failed sample, then previous seed
seed = 4002273756594553579    | random seed to reproduce generated values
sample = ["€", "Big5"]
original-sample = ["⹡骞ḅ遈㻴謯갨㩌셖觯蔊⁕ṏ舰眸鿾꫆閨֒渺", "x-IBM1364"]
```

Even a standard character like '€' cannot be reliably encoded in all charsets!


## Pattern: Induction

Those of you with a bit of formal mathematical education are familiar with
the term [_complete induction_](https://en.wikipedia.org/wiki/Mathematical_induction#Complete_induction).
While mathematical induction is used as a technique to proof theorems we can
use a related approach to formulate functional domain properties without
duplicating the logic under examination in our tests.

I'll use _Sorting a List of Integers_ as example:

- Our base case is simple: Lists of length 1 or below are always sorted.
- The rule for larger lists is also straightforward:
  A list is sorted when its first element is smaller than its second element
  and when the list without the first element is also sorted.

Here's the equivalent code:


```java
class SortingProperties {

	@Property
	boolean sortingAListWorks(@ForAll List<Integer> unsorted) {
		return isSorted(sort(unsorted));
	}

	private boolean isSorted(List<Integer> sorted) {
		if (sorted.size() <= 1) return true;
		return sorted.get(0) <= sorted.get(1) //
				&& isSorted(sorted.subList(1, sorted.size()));
	}

	private List<Integer> sort(List<Integer> unsorted) {
		unsorted.sort((a, b) -> a > b ? 1 : -1);
		return unsorted;
	}
}
```

Mind that this property actually _does_ check a desired _functional quality_
of `List.sort`. In this case we might even consider to forgo example-based
testing completely.


## Next Episode

In the [forthcoming article]({% post_url 2018-09-06-stateful-testing %})
I will have a closer look at _Stateful Testing_,
which is well suited to be tackled by Property-Based-Testing ideas.

# Update

As a reaction to this blog post, Dierk König [suggested additional
patterns](https://twitter.com/mittie/status/1018958458949984256):

- __PureFunction__

  Check that multiple calls with same arguments produce the same result


- __Corridor__

  Check that the returned value of a function or metho call is in its required range

- __Rules__

  Test associativity, commutativity, symmetry, neutrality, transitivity where applicable
