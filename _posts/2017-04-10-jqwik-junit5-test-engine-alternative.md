---
layout: post
title: "Jqwik: An Alternative JUnit5 Test Engine"
description: "Jqwik is supposed to be an alternative to Jupiter (JUnit 5's default). I'm building it from the ground up."
status: publish
categories: []
tags:
- jqwik
- junit5
- property testing
---
As one of the original designers and contributors of [JUnit 5](https://github.com/junit-team/junit5)
I've always been intrigued by its basic idea to not only be a fresh start for a maintainable test framework
but to also _serve as a platform for all kinds of test engines_:
If anyone, so the idea, uses the platform to create their own _test engine_ with it,
IDE support and build tool support will come for free.

Since I'm [no longer part of the JUnit 5 contributors' team]({% post_url 2016-04-16-goodbye-junit-5 %})
I wanted to try out if this platform idea really holds the value it's promising.

## Jqwik

[Jqwik](http://jqwik.net/) is a test engine of its own; built from the ground up.
It focuses on both example-based and property-based testing and
can therefore both replace standard Jupiter tests and add the additional
perspective of [QuickCheck-like](https://en.wikipedia.org/wiki/QuickCheck)
randomized property checking.

I try hard to follow a few principles while developing Jqwik:

- Every additional feature must solve a _real testing problem_ that cannot be
  tackled by existing mechanism in a reasonably simple way.
- Keeping the design simpler - and thereby more maintainable - is a feature
  itself and will often prevail over adding another feature of unprove or rather
  esoteric value.
- Microtesting (see [video by Mike Hill](https://www.youtube.com/watch?v=H3LOyuqhaJA))
  is the foundation of all Agile test automation approaches. When in doubt,
  I'll rank features that simplify micro tests over those that are intended
  to facilitate or enable integrated testing.

## Contribute

Please, please, please add your suggestion, ideas and bug reports using the project's
[issue tracker on github](https://github.com/jlink/jqwik/issues).

Of course, you can also send in pull requests; be prepared, though, that
I'll be very strict with what I accept, since I consider
the first months of a project to be crucial for shaping and preparing the design core
for the mid and long-term future.
