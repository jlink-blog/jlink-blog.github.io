---
title: "Property-based Testing in Java: How to Specify it"
description: "Learning from John Hughes how to specify properties"
status: publish
categories: []
tags:
- property-based testing
- java
- jqwik
- validity testing
- postconditions
- metamorphic properties
- inductive testing
- model-based properties
---

The last article in the series was about
[driving implementation with properties]({% post_url 2019-05-11-property-based-driven-development %})
in a TDD-like way. Today's article is short, but not because there's so little
to say but because I put most of the contents to a repository and a URL
of its own.

## How to specify it! In Java!

A few weeks ago [John Hughes](https://twitter.com/rjmh),
unarguably one of the most prominent practitioners of
Property-based Testing, published
[_How to Specify it!_](https://www.dropbox.com/s/tx2b84kae4bw1p4/paper.pdf).
In this paper he presents "five generic approaches to writing specifications".

I found this paper so full of knowledge and good advice that I transferred
the code examples for Haskell / QuickCheck to Java / jqwik. Additionally
I adapted the text to fit these examples.

[Here you can read the fully transferred paper](https://johanneslink.net/how-to-specify-it/).
Enjoy and comment!
