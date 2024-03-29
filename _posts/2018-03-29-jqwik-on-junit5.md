---
title: "Property-based Testing in Java: Jqwik - a JUnit 5 Test Engine"
description: "What's the difference between example-based testing and properties?"
status: publish
categories: []
tags:
- property-based testing
- java
- jqwik
---
In [the previous episode]({% post_url 2018-03-26-from-examples-to-properties %})
you've already seen [jqwik](https://jqwik.net) in action.
One of the interesting aspects of this PBT library is the fact that it's not
a standalone framework but that it hooks into JUnit 5 in order to "inherit"
IDE and built-tool support.

## Jqwik and the JUnit Platform

The fifth generation of JUnit does not only come with a modernized approach to write
and execute tests, but it is based on the idea of providing a platform for a large spectrum
of different _test engines_. An engine provides two entry points: One entry point is for
discovering tests and test suites - e.g. through scanning parts of the classpath for methods with
a certain annotation. The other entry point is used to run tests and test suites, usually
the ones you have discovered during the discovery step. The rest, like
filtering or selecting subsets of tests, is done by the platform itself.

The big advantage of such an approach is that any IDE and any
build tool only has to integrate the platform and not the individual engines. It's also
a big plus for engine developers who don't have to bother with aspects like
public APIs for discovering and running their test specifications.

Moreover, the platform allows to have any number of engines in parallel. That's how
JUnit 5 provides full backwards-compatibility to JUnit 4 and how a smooth migration
path can be realized. Using JUnit 4 (called Vintage), JUnit 5 (called Jupiter) and
_jqwik_ in a single project is not only possible, it's really simple.

IntelliJ has been an early platform adopter for over a year now. As of March 2018,
we also see native support from Eclipse, Gradle and Maven-Surefire. If you're already
using JUnit 5, using _jqwik_ as additional engine requires a single additional dependency.
If _jqwik_ is your first contact with the platform you should check out
[this part in jqwik's user guide](https://jqwik.net/user-guide.html#how-to-use).


## Wildcards and Type Parameters

Let's get back to the concrete property test of the previous episode:

```java
@Property
@Report(Reporting.GENERATED)
boolean reverseWithWildcardType(@ForAll List<?> original) {
    return reverse(reverse(original)).equals(original);
}
```

You might miss [the tiny change]({% post_url 2018-03-26-from-examples-to-properties %}#lets-do-it-in-java):
Instead of a concretely typed `List<Integer>`
I used the _wildcard_ variant: `List<?>`. Actually, this reflects the precondition better,
since the method under test - `Collections.reverse()` - should work with any element type.
Under the hood _jqwik_ will create instances of a special subtype of `Object`.
Just run the property with reporting switched on.

This would, by the way, also work with a _type variable_ instead of a wildcard.
Upper or lower bounds, however, are not fully supported yet.

## Many Parameters

You might have guessed that parameter generation is not restricted to a single one
but works for as many as you need:

```java
@Property
boolean joiningTwoLists(
    @ForAll List<String> list1,
    @ForAll List<String> list2
) {
    List<String> joinedList = new ArrayList<>(list1);
    joinedList.addAll(list2);
    return joinedList.size() == list1.size() + list2.size();
}
```

If you look at the generated lists you will notice that the variance in list size
and string length is quite high. You can also see that now and then an empty list
and an empty string is generated. This is due to the fact that value generation
is not purely random and not equally distributed across the allowed domain.
Instead, _jqwik_ tries to be "smarter":

- Smaller values (for numbers, sizes and lengths)
  are generated more frequently than higher values. The exact distribution depends
  on an internal `genSize` parameter which by default is set to the number
  of tries. The more often a property is tried, the larger the generated values are - on average.

- _jqwik_ will also routinely inject typical border cases like empty lists, empty strings,
  maximum and minimum values and others depending on the type of the values to generate
  and additional constraints.

This _smart generation approach_ aims at raising the probability to detect not so obvious
specification gaps and implementation bugs.

## Automatic Parameter Generation

Out of the box _jqwik_ is able to generate objects of the most common JDK types:

- All primitive numerical types, their boxed counter parts, as well as `BigInteger` and `BigDecimal`
- `String`, `Character` and `char`
- `Boolean` and `boolean`
- All enum types
- `List<T>`, `Set<T>`, `Stream<T>` and `Optional<T>` as long as T can be generated
- Arrays of types that can be generated
- `Map<K, V>` and `Map.Entry<K, V>`
- `java.util.Random` and `Object`

You might notice that `Map` and all calendar related classes are not covered (yet).
It's quite easy, though, to provide and register generators yourself.

What's also not covered are functional types. They are on the backlog but it's not
easy to come up with a good variance of functions to try. Constant functions are obvious,
everything else not so much.

### Influencing Automatic Parameter Generation

The easiest way to influence and constrain the domain of values considered for generation
is to use additional annotations provided for many of the default types. Here are a few
examples:

- All number types come with their respective range annotation.
  E.g. use `@DoubleRange(min=5.0, max=10.0)` to only generate doubles between 5 and 10.
- Strings can be constrained in both their length and the pool of character to be used.
  E.g. use `@StringLength(min = 1, max = 5) @AlphaChars` to generate strings of 1 to 5 characters
  with only upper and lower case letters.

Here is the [full list of built-in constraining annotations](https://jqwik.net/user-guide.html#constraining-default-generation).   

## Programmatic Generation

Sometimes we're dealing with classes that cannot be generated by default.
On other occasions the domain-specific constraints of a primitive type is so specific
that the existing annotations are not powerful enough. In these cases you can delegate
provision of parameter generators
to another method in your test container class.
The following example shows how to generate German zip codes:

```java
@Property
void letsGenerateGermanZipCodes(@ForAll("germanZipCodes") String zipCode) {
}

@Provide
Arbitrary<String> germanZipCodes() {
    return Arbitraries.strings().withCharRange('0', '9').ofLength(5);
}
```

The String value of the `@ForAll` annotation serves as a reference to a
method within the same class (or one of its superclasses or owning classes).
This reference refers to either the method's name or the String value
of the method's `@Provide` annotation.
The providing method has to return an object of type `@Arbitrary<T>`
where `T` is the static type of the parameter to be provided.
`Arbitrary` is a bit more than a value generator; think of it as a "configurator
for value generators".

Parameter provision methods usually start with a static method call to `Arbitraries`, maybe followed
by one or more filtering, mapping or combining actions as described in the next section.

## Filter and Map

As the core type of all value generation `Arbitrary` has quite a few default methods that can be used to
modify generating behaviour.
You usually start with one of the static basic generator functions in class `Arbitraries`. Most base
generators return a specific subtype of `Arbitrary` that gives you additional constraining possibilities.

Let's say we want to generate integers _between 1 and 300 that are multiples of 6_. Here are two
alternatives to do that:

- `Arbitraries.integers().between(1, 300).filter(anInt -> anInt % 6 == 0)`
- `Arbitraries.integers().between(1, 50).map(anInt -> anInt * 6)`

Which way is better? Sometimes it's only a matter of style or readability. Sometimes, however,
the way you choose can influence performance. When comparing the two options above, the former
is close to the given spec but it will - through filtering - throw away five sixths of all
generated values. The latter is therefore more efficient but also less comprehensible when
coming from the spec. Usually generating primitive values is so fast that readability trumps
efficiency.


## Combining Arbitraries

Real domain objects often have several distinct and mostly unrelated parts.
That's why - when you generate them - you want to start from unrelated base generators.

Given our domain class `Person`:

```java
class Person {
    private final String firstName, lastName;

    Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    public String fullName() {
        return firstName + " " + lastName;
    }

    @Override
    public String toString() {
        return String.format("Person(%s:%s)", firstName, lastName);
    }
}
```

We want to write a property which checks that any person has a full name.
The person generator method combines three arbitraries into the needed one:

```java
@Property
boolean anyValidPersonHasAFullName(@ForAll("validPerson") Person aPerson) {
    return aPerson.fullName().length() >= 5;
}

@Provide
Arbitrary<Person> validPerson() {
  Arbitrary<String> firstName = Arbitraries.strings()
      .withCharRange('a', 'z')
      .ofMinLength(2).ofMaxLength(10)
      .map(this::capitalize);
  Arbitrary<String> lastName = Arbitraries.strings()
      .withCharRange('a', 'z')
      .ofMinLength(2).ofMaxLength(20);
  return Combinators.combine(firstName, lastName).as(Person::new);
}
```

## Fighting Indeterminism

The values generated during a test run are random - at least to a large degree.
That way you enhance the chance of hitting upon bugs and specification gaps
that no one considered during conception and implementation.
The downside of introducing chance into testing is inherent indeterminism: A falsified
property in this run could succeed in the next since different test data might lead to different
test results.

There are two things _jqwik_ does to keep this problem in check:

- To fix a certain suite of generated values, you can set the random seed from a falsified test
  run directly in the property annotation:

```java
  @Property(seed = "424242")
  @Report(ReportingMode.GENERATED)
  void alwaysTheSameValues(@ForAll int aNumber) { ... }
```  

- If you run your tests from an IDE or through a build tool from the command line,
  _jqwik_ remembers the seeds from falsified properties. That means that until you get rid of
  the problem, e.g. by fixing the bug, you will always see the same suite of generated values.

## Other PBT Frameworks and Libs for Java

_jqwik_ being a JUnit 5 test engine requires you to use the JUnit platform.
If you cannot or do not want to use JUnit 5 yet, there are a few alternatives
 for doing PBT on the JVM:

- [JUnit-Quickcheck](http://pholser.github.io/junit-quickcheck):
  This is where _jqwik_ has stolen the annotation approach. So the way you
  write your properties is similar. JUnit-Quickcheck is tightly integrated with JUnit 4
  and also supports shrinking.

- [QuickTheories](https://github.com/ncredinburgh/QuickTheories):
  QuickTheories is completely test framework independent, which has pros and cons.
  Besides shrinking it also has an experimental feature in which value generation
  is being influenced by coverage data.

- [Vavr](http://www.vavr.io/): The functional library for Java also comes with a
  [property-based testing module](https://github.com/vavr-io/vavr/tree/master/vavr-test).
  Currently no shrinking support, though.

- [ScalaCheck](http://www.scalacheck.org/):
  Definitely a mature property based testing system with shrinking and all,
  iff you prefer Scala over Java. There's also
  [a book out there](https://www.artima.com/shop/scalacheck)
  about ScalaCheck.

- [test.check for Clojure](https://github.com/clojure/test.check):
  Strongly inspired and influenced by QuickCheck. Since Clojure
  does not have static types generators must always be declared explicitly.

- [KotlinTest](https://github.com/kotlintest/kotlintest) also has basic support for PBT.
  Currently no shrinking yet.

- [Frege, a Haskell for the JVM,](https://github.com/Frege/frege)
  comes with a classical QuickCheck implementation.
  [This article](https://dierk.gitbooks.io/fregegoodness/content/src/docs/asciidoc/qc_property.html)
  from Dierk König's Frege book provides a short introduction.


## Next Episode

The next article will focus on a crucial feature of mature Property-based
Testing libraries: [Shrinking]({% post_url 2018-04-20-the-importance-of-being-shrunk %}).
