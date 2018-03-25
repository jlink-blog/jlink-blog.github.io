---
layout: post
title: "Property-based Testing in Java: Introduction"
description: "Introduction to a short series about PBT in Java"
status: publish
categories: []
tags:
- property-based testing
- java
- jqwik
---
It's the year 2009. After two decades of doing mostly object-oriented programming
the claim that functional languages will greatly simplify the writing of correct concurrent
programs gets me interested in Clojure, Erlang and eventually Haskell.
Today, in 2018, my activities in concurrency have somewhat faded away,
but my curiosity about the functional side of software development has stayed with me.

As a developer I am being test-driven through and through, so a community's
approach to testing is one of the first things I look at
when trying to grasp how "the others" work and tick.
That's how I learned that functional programming folks are very much into
_properties_ as opposed to what we OO programmers call _unit tests_.
This change of perspective cannot only be seen in strictly typed languages like Haskell
but also in dynamically typed ones like Erlang.

However, Java, Groovy and to a lesser degree JavaScript are still my working horses
when it comes to earning money. Thus I began to wonder whether Property-based Testing (PBT)
can help me here, too. ~~Googling~~ DuckDuckGoing for "Java" and
"Property-based Testing" will give you a few results - some dating back to the time
when JUnit's Theories-Runner was still a thing - but there is much less
to be found on the web than I had expected. Some people
[e.g. Nat Price](https://semaphoreci.com/community/tutorials/diamond-kata-tdd-with-only-property-based-tests)
were experimenting with the approach in Java, but that was about it.

Call it a lucky coincidence that at this time - two years ago -
I left the JUnit-5 core team but decided to experiment with the
[JUnit platform](https://junit.org/junit5/docs/current/user-guide/#overview-what-is-junit-5).
That's why I created
[a test engine](http://blog.johanneslink.net/2017/04/10/jqwik-junit5-test-engine-alternative/)
called [jqwik](http://jqwik.net) with a strong focus on property-based tests.
Besides diving into the intricacies of Java's completely messed-up approach
to reflection, this task also required that I experimented with different ways
to write properties, that I looked at how other PBT libraries worked, and that
I found a way to integrate these new type of tests into my well-dried style
of TDD.

In the weeks to come I'll publish a series of blog entries describing the essence of what I
learned about PBT on the way, and how one can use it in Java. So far, six parts are
in the making:

- From Example Tests to Properties
- Jqwik and other Tools
- The Importance of Being Shrunk
- Patterns to Find Good Properties
- Stateful Testing
- PBT and Test-driven Development

I hope to see you again on my journey!
