---
title: "Jqwik Future and API Discussion"
description: "Let's design the future API of Jqwik"
status: publish
categories: []
tags:
- property-based testing
- jqwik
- funding
---

Sadly, all my applications for funding -
at [Prototype Fund](https://prototypefund.de/) and [Sovereign Tech fund](https://www.sovereigntechfund.de/) -
have been denied.
This raises a fundamental question about the future of jqwik.

## The Future of Jqwik

[HeiGIT, my current employer](https://heigit.org/) nicely pays for 2 hours of OSS development per week, 
which I use to maintain the current codebase of Jqwik.
Two hours are just enough for bug fixes, dependency upgrades and minor improvements.
It's definitely not enough for the planned version 2 rewrite.

So I see the following options:
- Find new sponsor that pays for more time.
  My estimate for getting jqwik 2 to a usable state is 3-6 months of full-time work.
- Keep jqwik as it is & let it slowly die or hand it over to 3rd party.
- Start with a minimal jqwik 2 and see if it can grow from there.
  It would probably take a year or more to get to a reasonable feature set & never
  be as powerful as version 1.
- Focus on Kotlin derivative kqwik and let jqwik be taken over by someone else.

I'm not sure where to go from here. But I'll decide over summer.
If you have any helpful thoughts or ideas, feel free to contact me.


## The Jqwik 2 API

Nevertheless, I am still enthusiastic about the fantastic possibilities that would
open with the next generation of Jqwik.
I probably should write about this vision first, but hey, who's never failed to do the first things first?
Many of the technical possibilities and challenges of a rewrite have been tackled and mostly solved in a 
[proof of concept](https://github.com/jqwik-team/jqwik2-poc/).
The best API, however, is still to be designed and discussed.

With appropriate funding for jqwik 2 the plan was to host a workshop - in presence and online - 
to work on the different API aspects. 
You all could have come or listened in. 
Food would have been free, maybe even accommodation. 
Life would have been good 🙂.

The next best thing that came to my mind is to discuss the API in the open.
That's why I created [a dedicated repository](https://github.com/jqwik-team/jqwik2-api-discussion) 
for this exact purpose.
In the days and weeks to come I will create 
[issues with concrete API design questions](https://github.com/jqwik-team/jqwik2-api-discussion/issues).
Subscribe to the repo if you want to be in the loop.

BTW, the workshop is still possible if a sponsor will show up!

## Update (2024-08-15)

As suggested by one of the readers of this blog post, 
I have created [a GitHub sponsor page for Jqwik](https://github.com/sponsors/jqwik-team).
