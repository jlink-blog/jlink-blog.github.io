---
title: "Why is Property-based Testing not Popular in Java"
description: "Speculations about PBT's life in the Shadow"
status: publish
categories: []
tags:
- property-based testing
- java
- jqwik
---

It's time to question one of my darlings...

### The Situation

Looking at languages like Haskell or Erlang Property-based testing (PBT) is
at the forefront of how developers test their own code. In Elixir it is
even supposed to become [part of the standard library](https://elixir-lang.org/blog/2017/10/31/stream-data-property-based-testing-and-data-generation-for-elixir/).

I have been promoting PBT for quite a while:
- I am maintainer of [jqwik](https://jqwik.net)
- I have written a [blog series on PBT in Java](https://blog.johanneslink.net/2018/03/24/property-based-testing-in-java-introduction/),
articles in German magazines as well as in [Oracle's Java Magazine](https://blogs.oracle.com/javamagazine/know-for-sure-with-property-based-testing).
- I've given more than two dozens of presentations and workshops on the topic
  and received universally enthusiastic feedback about the usefulness and
  potential of the approach.

And yet, the uptake of the approach in the Java community is very low.
Doing an Github search for the usage of the three most common
PBT libs in Java (junit-quickcheck, jqwik and quicktheories) only
returns about 3500 code hits.
Compared to the 900k of Jupiter alone this is almost non-existent.
And ScalaCheck - the PBT of choice for Scala development - finds itself
with 33k usages in Github, although there are much fewer Scala
projects on Github.

### Reasons

So maybe it's about OO languages not lending themselves well to this approach?  
If you look at the number of hits for "hypothesis" in Python source code,
which is larger than 37k, this argument seems not plausible.

Maybe the Java libraries are not mature enough or miss out on important
features? I don't think that's true either. I have been testing and using
all kinds of PBT libraries in a few languages for some years: There is no
fundamental disadvantage in overall performance or number of features.
Speaking of maturity: junit-quickcheck started in 2010, QuickTheories in 2015
and jqwik in 2016. Not exactly new kids on the block.

What is the remaining difference to languages with much PBT usage?
In my opinion it's the Java community. Java has become the epitome of
"Enterprise" development and conservative programming.
When in doubt Java folks go with the main stream tool and use
the library everybody else is using.
That's not always a bad thing because it provides stability for customers
and developers alike.

The downside of stability is stagnation. Clinging to what they know
keeps people from learning what might be helpful. For a topic to reach
critical mass in the Java world an extraordinary high amount of
interest, visibility and marketing is required.


### The Future

For me there are two open questions:
- Is it worth to promote PBT in Java any further at all?
  Maybe the approach does not bring enough benefit in a world where
  maintenance and stability are the dominating themes.
- How can the visibility problem of PBT be solved? A single person
  travelling from one Java User Group meet-up to the next does not scale.

Any comments and suggestions are welcome!
