---
layout: post
title: "Results of Workshop on TDD with Functional Programming"
description: "A short summary of what we think we learned during the workshop."
status: publish
categories: []
tags:
- tdd
---
Early summer last year I organized
[a workshop on TDD with functional programming]({% post_url 2017-02-24-functional-tdd-workshop %})
with 10 attendees.
Quite a few people have asked me about our results, but I had been waiting for a
time with more leisure to write about it. Which, of course, never shows up.
Since the follow-up workshop will take place later this month,
it's high time to at least sum up the main learnings of those three intensive days.

So here's a few points I collected for an email I sent to one of inquirers.
Take them as rough and rather subjective notes.
The other participants, who are invited to comment, might not agree with parts
or even any of it...

### TL;DR

> _TDD with functional languages
> is certainly possible. A smooth experience, however, will require some
> tweaking and rethinking._

### More Details

Areas where tweaking and rethinking of OO-focused TDD will be necessary:

- With a stronger type system (think Haskell or even Idris) you can
  probably get rid of some tests. Which tests still provide benefit is
  up for discussion.

- Stronger types also influence your workflow. We had examples where thinking
  about the types beforehand seemed more natural than coming up with a
  test. But sometimes it was the other way round: A clear example communicated
  better than a complex type.

- Functional programming provides an easier path into
  a keep-side-effects-on-the-outside architecture.
  Especially with Haskell's strict separation of pure and non-pure/IO
  types. And we all know that writing tests for pure functions is much
  easier.

  That's also the main reason why mocking and stubbing plays
  only a tiny role in testing functional code, mostly for testing the
  integration layer between pure and non-pure code.

  A hexagonal architecture is basically the same as
  keep-side-effects-on-the-outside but much harder to follow in OOPLs.

- There's a yet unexplored balance between example-based and
  property-based-testing.

  A first and rather raw heuristic we came up with: Use examples
  for specification and properties for exploration. In my own
  experimentation since, I've often started with concrete examples, which I could
  later turn into properties.

- Strict types change the way you refactor. Changing a type to what
  you think it should be and changing your code until it successfully compiles again,
  seems to be a common pattern among haskellers.

- What we did not look into at all was REPL-driven development because
  none of the attendees had solid practice doing it.

#### Examples in Code

Most of the code we produced can be found
[on Github](https://github.com/jlink/Functional-TDD-Workshop).

### Personal Focus

I've personally focused on integrating property-based testing into my
style of TDD. Since the JVM is still the main platform I fight with for money,
I started to develop and use [jqwik](https://jqwik.net/).
