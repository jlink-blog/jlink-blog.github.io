---
title: "To Gen or Not To Gen: The Ethical Use of Generative AI"
description: "Is it ethical to use hyper-scaled GenAI?"
status: publish
categories: []
tags:
- ai
- GenAI
- ethics
---

This blog entry started out as a translation of an article that my colleague [Jakob](#jakob-schnell) and I wrote for a German magazine.
After that we added more stuff and enriched it by additional references and sources.
We aim at giving an overview about many - but not all - aspects that we learned about GenAI and
that we consider relevant for an informed ethical opinion.
As for the depth of information, we are just scratching the surface;
hopefully, the loads of references can lead you to diving in deeper wherever you want.
Since we are both software developers our views are biased and distorted.
Keep also in mind that any writing about a "hot" topic like this is nothing but a snapshot of what we think to know today.
By the time you read it the authors' knowledge and opinions have already changed.

__Last Update:__ November 4, 2025.


<!-- doctoc --maxlevel 4 --update-only 2025-11-04-to-gen-or-not-to-gen.md -->

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
## Table of Contents

- [Abstract](#abstract)
- [About us](#about-us)
    - [Johannes Link](#johannes-link)
    - [Jakob Schnell](#jakob-schnell)
- [Introduction](#introduction)
    - [Ethics, what does that even mean?](#ethics-what-does-that-even-mean)
    - [Clarification of terms](#clarification-of-terms)
- [Basics](#basics)
    - [Can LLMs think?](#can-llms-think)
    - [What LLMs are good at](#what-llms-are-good-at)
    - [GenAI as a knowledge source](#genai-as-a-knowledge-source)
    - [GenAI in software development](#genai-in-software-development)
    - [Actual vs. promised benefits](#actual-vs-promised-benefits)
- [Harmful aspects of GenAI](#harmful-aspects-of-genai)
    - [GenAI is an ecological disaster](#genai-is-an-ecological-disaster)
        - [Power](#power)
        - [Water](#water)
        - [Electronic Waste](#electronic-waste)
    - [GenAI threatens education and science](#genai-threatens-education-and-science)
    - [GenAI is destroying the free internet.](#genai-is-destroying-the-free-internet)
    - [GenAI is a danger to democracy](#genai-is-a-danger-to-democracy)
    - [GenAI versus human creativity](#genai-versus-human-creativity)
    - [Digital colonialism](#digital-colonialism)
    - [Political aspects](#political-aspects)
- [Conclusion](#conclusion)
    - [Can there be ethical GenAI?](#can-there-be-ethical-genai)
    - [How to act ethical](#how-to-act-ethical)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


## Abstract

ChatGPT, Gemini, Copilot. The number of generative AI applications (GenAI) and models is growing every day.
In the field of software development in particular, code generation, coding assistants and vibe coding are on everyone's lips.
Like any technology, GenAI has two sides. The great promises are offset by numerous disadvantages:
immense energy consumption, mountains of electronic waste, the proliferation of misinformation on the internet
and the dubious handling of intellectual property are just a few of the many negative aspects.
Ethically responsible behaviour requires us to look at all the advantages,
disadvantages and collateral damages of a technology before we use it or recommend its use to others.

In this article, we examine both sides and eventually arrive at our personal and naturally subjective answer
to whether and how GenAI can be used in an ethical manner.

## About us

### Johannes Link

... has been programming for over 40 years, 30 of them professionally.
Since the end of the last century, extreme programming and other human-centred
software development approaches have been at the heart of his work.
The meaningful and ethical implementation of his private and professional life has been his driving force for years.
He has been involved with GenAI since the early days of OpenAI's GPT language models.
More about Johannes can be found at https://johanneslink.net.

### Jakob Schnell

... studied mathematics and computer science and has been working as a software developer for 5 years.
He works as a lecturer and course director in university and non-university settings.
As a youth leader, he also comes into regular contact with the lives of children and young people.
In all these environments, he observes the growing use of GenAI and its impact on people.


## Introduction

### Ethics, what does that even mean?

_Ethical behaviour_ sounds like the title of a boring university seminar.
However, if you look at the wikipedia article of the term [^1],
you will find that ‘how individuals behave when confronted with ethical dilemmas’ is at the heart of the definition.
So it's about us as humans taking responsibility
and weighing up whether and how we do or don't do certain things based on our values.

We have to consider ethical questions in our work because all the technologies
we use and promote have an impact on us and on others.
Therefore, they are neither neutral nor without alternative.
It is about weighing up the advantages and potential against the damage and risks;
and that applies to everyone, not just us personally.
Because often those who benefit from a development are different from those
who suffer the consequences.

As individuals and as a society, we have the right to decide
whether and how we want to use technologies.
Ideally, this should be in a way that benefits us all;
but under no circumstances should it be in a way that benefits a small group and harms the majority.

The crux of the matter is that ethical behaviour does not come for free.
Ethics are neither efficient nor do they enhance your profit.
That means that by acting according to your values you will, at some point, have to give something up.
If you're not willing to do that, you don't have values - just opinions.


### Clarification of terms

When we write ‘generative AI’ (GenAI), we are referring to a very specific subset of the many techniques and approaches
that fall under the term ‘artificial intelligence’.
Strictly speaking, these are a variety of approaches in the field of _machine learning (ML)_.
What these approaches have in common is that they use multi-layered _artificial neural networks_
to discover _statistical correlations_ based on very large amounts of data – often referred to as ‘training’ –
and to be able to reproduce them.

Large language models (LLM) and related methods for generating images, videos and speech
now make it possible to apply this idea to completely unstructured data.
While traditional ML methods often managed with a few dozen parameters,
these models now work with several trillion (10^12) parameters.
In order for this to produce the desired results, both the amount of training data
and the training duration must be increased by several orders of magnitude.

This brings us to the definition of what we mean by ‘GenAI’ in this article:
_Hyperscaled_ models that can only be developed, trained
and deployed by a handful of companies in the world.
These are primarily the GenAI services provided by OpenAI, Anthropic, Google and Microsoft,
or based on these services.
We also focus primarily on language models; the generation of images, videos, speech and music
plays only a minor role in this article.

Our focus on hyperscale services does not mean that other ML methods are free of ethical problems;
however, we are dealing with a completely different order of magnitude of damage and risk here.
For example, there do exist variations of GenAI that use the same or similar techniques,
but on a much smaller scale and restricted domains (e.g. AlphaFold [^31]).
These approaches tend to bring more value with fewer downsides.


## Basics

GenAI models are designed to interpolate and extrapolate [^44],
i.e. to fill in the gaps between training data and speculate beyond the limits of the training data.
Together with the stochastic nature of the training data, this results in some interesting properties:
- GenAI models ‘invent’ answers; with LLMs, we like to refer to this as ‘hallucinations’.
- GenAI models do not know what is true or false, good or bad, efficient or effective,
  only what is statistically probable or improbable in relation to the training data.
- The context, i.e. the set of input parameters provided, is decisive for the accuracy
  of the generated result, but can also steer the model in the wrong direction.
  Increasing the context window makes a query much more computation-intensive - likely in a quadratic way.
  Therefore, the promised increase of "maximum context window" of many models is mostly fake [^41].
- The reliability of LLMs cannot be fundamentally increased by even greater scaling [^18].


### Can LLMs think?

Proponents of the language-of-thought hypothesis [^2] believe it is possible for purely language-based models
to acquire the capabilities of the human brain – reasoning, modelling, abstraction and much more.
Some enthusiasts even claim that today's models have already acquired this capability.
However, recent studies [^3] [^4] show that today's models are neither capable of genuine reasoning
nor do they build internal models of the world.
Moreover, there is fundamental scientific doubt that achieving human cognition through computation
is achievable in practice let alone by scaling up training of deep networks [^45].

An example of a lack of understanding of the world is the prompt ‘Give me a random number between 0 and 50’.
The typical GenAI response to this is ‘27’, and it is significantly more reliable than true randomness would allow.
(If you don't believe it, just try it out!)
This is because 27 is the most likely answer in the GenAI training data –
and not because the model understands what ‘random’ means.

‘Chain of Thought (CoT)’ approaches attempt to improve reasoning
by breaking down a prompt, the query to the model, into individual (logical) steps
and then delegating these individual steps back to the LLM.
This allows some well-known reasoning benchmarks to be met, but it also multiplies the necessary
computational effort and individual errors chain together to form large errors.
And yet, CoT models do not seem to possess any real reasoning abilities [^8] [^9]
and improve the overall accuracy of LLMs only marginally [^42].

The following thought experiment from [^29] underscores the lack of real "thinking" capabilities:
LLMs have simultaneous access to significantly more knowledge than humans.
Together with the postulated ability of LLMs to think logically and draw conclusions, new insights should
just fall from the sky. But they don't.
Getting new insights from LLMs would require these to be already encoded in the existing training material,
and to be decoded and extracted by pure statistical means.


### What LLMs are good at

Undoubtedly, LLMs represent a major qualitative advance when it comes to
extracting information from texts, generating texts in natural and artificial languages,
and machine translation.
But even here, the error rate, and above all the type of error (‘hallucinations’), is so high
that autonomous, unsupervised use in serious applications must be considered highly negligent.

### GenAI as a knowledge source

As we have pointed out above, LLMs cannot differentiate between true and false - regardless of the training material.
It does not answer the question "What is XYZ?" but the question "How would an answer to question 'What is XYZ?' **look like**?".
Nevertheless, many people claim that the answers that ChatGPT and alike provide for the typical what-how-when-who queries
are good enough and often better than what a "normal" web search would have given us.
Arguably, this is the most prevalent use case for "AI" bots today.
The problem is that most of the time we will never learn about the inaccuracies, left-outs, distortions and biases
that the answer contained - unless we re-check everything, which defies the whole purpose of speeding up knowledge retrieval.
The less we already know, the better the "AI's" answer looks to us, but the less equipped we are to spot the problems.
A recent by the BBC and 22 Public Service Media organizations shows that 45% of all "AI" assistants' answers
on questions about news and current affairs have significant errors [^43].

Moreover, LLMs are easy prey for manipulation - either by the service providing organization or by third parties.
A recent study claims that even multi-billion-parameter models can be "poisoned"
by injecting just a few corrupted documents [^38].
So, if anything is at stake all output from LLMs must be carefully validated.
Doing that, however, would contradict the whole point of using "AI" to speed up knowledge acquisition.

### GenAI in software development

The creation and modification of computer programmes is considered a prime domain for the use of LLMs.
This is partly because programming languages have less linguistic variance and ambiguity than natural languages.
Moreover, there are many methods for automatically checking generated source code,
such as compiling, static code analysis and automated testing.
This simplifies the validation of generated code and thereby gives an additional feeling of trust.

Nevertheless, individual reports on the success of coding assistants such as Copilot, Cursor, etc. vary greatly.
They range from ‘completely replacing me as a developer’ to ‘significantly hindering my work’.
Some argue that coding agents considerably reduce the time they have to invest in "boilerplate" work,
like writing tests, creating data transfer objects or connecting your domain code to external libraries.
Others counter by pointing out that delegating these drudgeries to GenAI makes you miss opportunities
to get rid of them, e.g. by introducing a new abstraction or automating parts of your pipeline,
and to learn about the intricacies and failure modes of the external library.

Other than old-school code generation or code libraries prompting a coding agent is not "just another layer of abstraction".
It misses out on several crucial aspects of a useful abstraction:
1. Its output is **not deterministic**.
   You cannot rely on any agent producing the same code next time you feed it the same prompt.
2. The agent **does not hide the implementation details**, nor does it allow you to reliably change those details
   if the previous implementation turns out to be inadequate.
   Code that is output by an LLM, even if it is generated "for free", has to be considered and maintained
   each time you touch the related logic or feature.
3. The agent does not tell you if the amount of details you give in your prompt is sufficient for figuring out
   an adequate implementation.
   On the contrary, the LLM will always fill the specification holes with some statistically derived assumptions.

Sadly, serious studies on the actual benefits of GenAI in software development are rare.
The randomised trial by Metr [^5] provides an initial indication,
measuring a **decline in development speed** for experienced developers.
An informal study by ThoughtWorks estimates the potential productivity gain
from using GenAI in software development at around 5-15% [^6] .

Thus, even if we assume a productivity increase in coding through GenAI,
there are still two points that further diminish this postulated efficiency gain:
Firstly, the results of the generation must still be cross-checked by human developers.
However, it is well known that humans are poor checkers and lose both attention and enjoyment in the process.
Secondly, software development is only to a small extent about writing and changing code.
The most important part is discovering solutions and learning about the use of these solutions in their context.
Peter Naur calls this ‘programming as theory building’ [^7].
Even the perfect coding assistant can therefore only take over the coding part of software development.
For the essential rest, we still need humans.
If we now also consider the finding that using AI can relatively quickly
lead to a loss of problem-solving skills [^14] or that these skills are not acquired at all,
then the overall benefit of using GenAI in professional software development is more than questionable.

As long as programming - and every technicality that comes with it - will not be __fully replaced__ by some kind of AI,
we will still need expert developers who can programm, maintain and debug code to the finest level of detail.
Where, we wonder, will those senior developers come from when companies replace their junior staff with coding agents?

### Actual vs. promised benefits

If you read testimonials about the use of GenAI that people perceive as successful,
you will mostly encounter scenarios in which ‘AI’ helps to make tasks that are perceived as boring,
unnecessarily time-consuming or actually pointless faster or more pleasant.
So it's mainly about personal convenience and perceived efficiency.
Entertainment also plays a major role: the poem for Grandma's birthday, the funny song for the company anniversary
or the humorous image for the presentation are quickly and supposedly inexpensively generated by ‘AI’.

However, the promises made by the dominant GenAI companies are quite different:
solving the climate crisis, providing the best medical advice for everyone, revolutionising science,
‘democratising’ education and much more.
GPT5, for example, is touted by Sam Altman, CEO of OpenAI, as follows:
‘With GPT-5, it's now like talking to an expert — a legitimate PhD-level expert in any area you need
[...] they can help you with whatever your goals are.’ [^15]

However, to date, there is still no actual use case that provides a real qualitative benefit
for humanity or at least larger groups.
The question ‘What significant problem (for us as a society) does GenAI solve?’ remains unanswered.
On the contrary: While machine learning and deep learning methods certainly have useful applications,
the most profitable area of application for ‘AI’ at present is the discovery and development of new oil and gas fields [^34].


## Harmful aspects of GenAI

But regardless of how one assesses the benefits of this technology,
we must also consider the downsides, because only then can we ultimately make an informed and fair assessment.
In fact, the range of negative effects of hyperscaled generative AI that can already be observed is vast.
Added to this are numerous risks that have the potential to cause great social harm.
Let's take a look at what we consider to be the biggest threats:

### GenAI is an ecological disaster

#### Power

The data centres required for training and operating large generative models [^10]
far exceed today's dimensions in terms of both number and size.
The projected data centre energy demand in the USA is predicted to grow from 4.4% of total electricity in 2023
to 22% in 2028 [^11].
In addition, the typical data centre electricity mix is more CO2-intensive than the average mix.
There is an estimated raise of ~11 percent for coal generated electricity in the US,
as well as tripled emissions of greenhouse gases worldwide by 2030 - compared  to the scenario without GenAI technology [^39].

Just recently Sam Altman from OpenAI blogged some numbers about the energy and water usage of ChatGPT for
"the average query" [^35].
On the one hand, an average is rather meaningless when a distribution is heavily unsymmetric;
the numbers for queries with large contexts or "chain of reasoning" computations would be orders of magnitude higher.
Thus, the potential efficiency gains from more economical language models
are more than offset by the proliferation of use, e.g. through CoT approaches and ‘agent systems’.
On the other hand, big tech's disclosure of energy consumption (e.g. by Google [^36]) is intentionally selective.
Ketan Joshi goes into quite some details why experts think that the AI industry is hiding the full picture [^28].

Since building new power plants - even coal or gas fuelled ones - takes a lot of time,
data center companies are even reviving old jet engines for powering their new hyper-scalers [^46].
You have to be aware that those engines are not only much more noisy than other power plants
but also pump out nitrous oxide, one of the main chemicals responsible for acid rain [^47].


#### Water

Another problem is the immensely high water consumption of these data centres [^12].
After all, cooling requires clean water in drinking quality in order to not contaminate or clog the cooling pipes and pumps.
Already today, new data centre locations are competing with human consumption of drinking water.
According to Bloomberg News about two-thirds of data-centers that were built or developed in 2022
are located in areas that are already under "water-stress" [^13].


#### Electronic Waste

Another ecological problem is being noticeably exacerbated by ‘AI’:
the amount of electronic waste (e-waste) that we ship mainly to "Third World" countries
and which is responsible for soil contamination there.
Efficient training and querying of very large neural networks requires very large quantities of specialised chips (GPUs).
These chips often have to be replaced and disposed of within two years.
The typical data center might not last longer than 3 to 5 years before it has to be rebuilt in large parts[^40].

In summary, it can be said that GenAI is at least an accelerator of the ecological catastrophe
that threatens the earth.
And it is the argument for Google, Amazon and Microsoft to completely abolish their zero CO2 targets [^33]
and replace them with investments of several hundred billion dollars for new data centers.


### GenAI threatens education and science

People often try to use GenAI in areas where they feel overloaded and overwhelmed:
training, studying, nursing, psychotherapeutic care, etc.
The fields of application for ‘AI’ are therefore a good indication of socially neglected
and underfunded areas.

The fact that LLMs are very good at conveying **the impression of genuine knowledge and competence**
makes their use particularly attractive in these areas.
A teacher under the simultaneous pressure of lesson preparation, corrections and covering for sick colleagues
turns to ChatGPT to quickly create an exercise sheet.
A student under pressure to get good grades has their English essay corrected by ‘AI’.
The researcher under pressure to publish will ‘save’ research time by reading the AI-generated summary
of relevant papers – even if they are completely wrong in terms of content [^16].
Tech companies like OpenAI and Microsoft play on that situation by offering their ‘AI’ for free
or for little money to students and universities.

What falls by the wayside are problem-solving skills, engagement with complex sources,
and the generation of knowledge through understanding and supplementing existing knowledge.
The training cycle of schools and universities is fast.
Teachers are already reporting that pupils and students have acquired noticeably
less competence in recent years, but have instead become dependent on unreliable ‘tools’ [^17].


### GenAI is destroying the free internet.

The fight against bots on the internet is almost as old as the internet itself – and has been quite successful so far.
Multifactor authentication, reCaptcha, honeypots and browser fingerprinting are just a few of the tools
that help protect against automated abuse.
However, GenAI takes this problem to a new level – in two ways.
To make ‘the internet’ usable as the main source for training LLMs, AI companies use so-called ‘crawlers’.
These essentially behave like DDoS attackers:
They send tens of thousands of requests at once, from several hundred IPs in a very short time.
Robot.txt files are ignored; instead, the source IP and user agent are obscured [^19].

These practices have massive disadvantages for providers of genuine content:
- Costs for additional bandwidth.
- Lost advertising revenue, as search engines now offer LLM-generated summaries instead of links to the sources.
  This threatens the existence of remaining independent journalism in particular [^25].
- Misuse of own content for AI-supported competition.

If the place where knowledge is generated is separated from the place where it is consumed, and if this makes the performance of
generation even more opaque than before, the motivation to continue generating knowledge also declines.
For projects such as Wikipedia, this means fewer donors and fewer contributors.
Open communities often have no other option but to shut themselves off.

Another aspect is the flooding of the internet with generated content that cannot be automatically distinguished from
non-generated content. This content overwhelms the maintainers of
open source software or portals such as Wikipedia [^27]. If this content is then also entered by humans
– often in the belief that they are doing good – it is no longer possible to take action against the methodology.
In the long run, this means that less and less authentic training material
will lead to increasingly poor results from the models.

Last but not least, autonomously acting agents make the already dire state of internet security much worse [^49].
Think of handing all your personal data and credentials to a robot that
- is distributing and using that data across the web, wherever and whenever it deems it necessary for reaching some goal.
- is controlled by LLMs who are vulnerable to all kinds of prompt injection attacs [^50].
- is controlled by and reporting to companies that do not have your best interest in mind.
- has no awareness and knowledge about the implication of its actions.
- is acting on your behalf and thereby **making you accountable**.


### GenAI is a danger to democracy

The manipulation of public opinion through social media precedes the arrival of LLMs.
However, this technology gives the manipulators much more leverage.
By flooding the web with fake news, fake videos and fake everything undemocratic (or just criminal) parties
make it harder and harder for any serious media and journalism to get the attention of the public.

People no longer have a common factual basis, which is necessary for all social negotiations.
If you don't agree on at least some basic facts, arguing about policies and measures to take is pointless.
Without negotiations democracy will be dying; in many parts of the world it already is.


### GenAI versus human creativity

Art and creativity are also threatened by generative AI.
The impact on artists' incomes of logos, images and illustrations now being easily and quickly
created by AI prompts is obvious.
A similar effect can also be observed in other areas.
Studies show that poems written by LLMs are indistinguishable from those written by humans
and that generative AI products are often rated more highly [^20].
This can be explained by a trend towards the middle and the average, which can also be observed in the music and film scenes
film scene: due to its basic function, GenAI cannot create anything fundamentally new,
but replicates familiar patterns, which is precisely why it is so well received by the public.

Ironically, ‘AI’ draws its ‘creativity’ from the content of those it seeks to replace.
Much of this content was used as training material against the will of the rights holders.
Whether this constitutes a copyright infringement has not yet been decided; morally, the situation seems clear.
The creative community is the first to be seriously threatened by GenAI in its livelihood [^32].

It's not a coincidence that a big part of GenAI efforts is targeted at "democratizing art".
This framing is completely upside down. Art has been one of the most democratic activities for a very long time.
Everybody can do it; but not everybody wants to do put in the effort, the practicing time and the soul.
Real art is not about the product but about the process, which requires real humans.
Generating art without the friction is about getting rid of the humans in the loop - and still making money.


### Digital colonialism

The huge amount of data required by hyperscaled AI approaches makes it impossible to completely curate
the learning content.
And yet, one would like to avoid the reproduction of racist, inhuman and criminal content.
Attempts are being made to get the problem under control by subsequently adapting the models to human preferences
and local laws through additional ‘reinforcement learning from human feedback (RLHF)’ [^23].
The cheap labour for this very costly process can be found in the Global South.
There, people are exposed to hours of hate speech, child abuse, domestic violence and other horrific scenarios
in their poorly paid jobs in order to filter them out of the training material of large AI companies [^22].
Many emerge from these activities traumatised.

However, it is not only people who are exploited in the less developed regions of the world, but also nature:
the poisoning of the soil with chemicals during the extraction of raw materials for digital chips,
as well as the contamination caused by our electronic waste and its improper disposal,
are collateral damage that we willingly accept and whose long-term consequences are currently
extremely difficult to assess.
Here, too, the "developed" world profits, whereas the negative aspects are outsourced to the former colonies
and other poor regions of the world.


### Political aspects

As software developers, we would like to ‘leave politics out of it’ and instead focus entirely on the cool tech.
However, this is impossible when the advocates of this technology pursue strong political and ideological goals.
In the case of GenAI, we can cleary see that the US corporations behind it (OpenAI, Google, Meta, Microsoft, etc.)
have no problem with the current authoritarian – some say fascist – US government [^26].
In concrete terms, this means, among other things, that the models are explicitly manipulated to be less liberal
or simply not to generate any output that could upset the CEO or the president [^30].

Even more serious is the fact that many of the leading minds behind these corporations and their financiers
adhere to beliefs that can be broadly described as _digital fascism_.
These include Peter Thiel, Marc Andreessen, Alex Karp, JD Vance, and Elon Musk.
Their ideologies, disguised as rational theories, are called _longtermism_ and _effective altruism_.
What they have in common is that they consider democracy and the state to be obsolete models, compassion to be ‘woke’,
and that the current problems of humanity are insignificant, as our future lies in the colonisation of
space and the merging of humans with artificial superintelligence [^24].
Do we want to give people who adhere to these ideologies (even) more power,
money and influence by using and paying for their products?
Do we want to feed their computer systems with our data?
Do we really want to expose ourselves and our children to the answers from chatbots which they have manipulated?

Not quite as abstruse, but similarly misanthropic, is the imminent displacement of many jobs by AI,
as postulated by the same corporations in order to put pressure on employees with this claim.
Demanding a large salary? Insisting on your legal rights?
Complaining about too much workload? Doubts about the company's goals?
Then we'll just replace you with cheap and uncomplaining AI!

Whichever way you look at it, AI and GenAI are already being used politically.
If we go along without resistance, we are endorsing this approach and supporting it
with our time, our attention and our money.


## Conclusion

Ideally, we would like to quantify our assessment by adding up the advantages,
adding up the disadvantages and finally checking whether the balance is positive or negative.
Unfortunately, in our specific case, neither the benefits nor the harm are easily quantifiable;
we must therefore consult our social and personal values.

Discussions about GenAI usually revolve purely around its benefits.
Often, the capabilities of all ‘AI’ technologies (e.g. protein folding with AlphaFold [^31]) are lumped together,
even though they have little in common with hyperscaling GenAI.
However, if we consider the consequences and do not ignore the problems this technology entails
– i.e. if we consider both sides in terms of ethics – the assessment changes.
Convenience, speed and entertainment are then weighed against numerous damages and risks to the environment,
the state and humanity.
In this sense, the ethical use and further expansion of GenAI **in its current form** is not possible.


### Can there be ethical GenAI?

If the use of GenAI is not ethical today what would have to change,
which negative effects of GenAI would have to disappear or at least be greatly reduced
in order to tip the balance between benefits and harms in the other direction?

- The models would have to be trained exclusively with publicly known content
  whose original creators consent to its use in training AI models.
- The environmental damage would have to be reduced to such an extent that it does not further fuel the climate crisis.
- Society would have to get full access to the training and operation of the models in order to rule out manipulation
  by third parties and restrict their use to beneficial purposes.
  This would require democratic processes, good regulation and oversight through judges and courts.
- The misuse and harming of others, e.g., through copyright theft or digital colonialism, would have to be prevented.

Is such a change conceivable? Perhaps.
Is it likely, given the interest groups and political aspects involved? Probably not.


### How to act ethical

The advocates of generative AI, often direct beneficiaries, want to convey to us that this ‘technology of the future’
is without alternative and inevitable [^21].
In their opinion, we should therefore integrate the use of GenAI into our everyday lives as quickly as possible
and optimise how we use it.
This is a deliberate attempt at shifting the narrative.
Away from whether we want this future, towards the indisputable necessity of adapting as quickly as possible.
From self-determination to subordination.
Yet, as with almost every technology, there are alternatives to its use.
We humans have the right to decide whether and how we use technology, weighing up all the different factors.

The 19th-century _Luddites_ had a similar problem.
They were experts of their field (textile work) who opposed the use of certain types of automated machinery
due to concerns relating to worker pay and output quality.
We can learn from them in the literal sense as C.C. Radsch puts it [^48]:

> _... refuse to accept that the deployment of new technology [...] dictated unilaterally by corporations
> or in cahoots with the government, especially when it undermines workers’ ability to earn a living,
> social cohesion, public goods, and democratic institutions._
>
> _[...] To be a Luddite today is to refuse the fatalism of techno-inevitability
> and to demand that technology serve the many, not just the few.
> It is to assert that questions of labor, agency, and justice must come before speed, efficiency, and scale._

For us personally, this means that we no longer use generative AI – neither for private nor professional purposes.
Unfortunately, the increasing penetration of GenAI into many services is making this increasingly difficult.
But it also means that we constantly raise our voices against the normalisation of the use of this unethical technology;
both publicly, but also in private and when dealing with our family, friends, colleagues, students or business partners.
This makes us - from time to time - inconvenient for others and comes with personal consequences.
Is it worth it then? We strongly believe so.
Every query that is not sent to an LLM reduces GenAI's market share.
Every voice against it influences public opinion.
And every successful piece of work without contributions from "AI" shows that the technology is not without alternatives.

Maybe we non-AI coders are the coachmen of the 21st century. Frankly, we don’t care.
Humanity could have and should have skipped the era of combustion-based individual traffic
and directly gone to battery-powered small vehicles.
So maybe we are not the coachmen but the bicycle riders who pave the road for
the ethical way of next generation software development.


[^1]: Wikipedia article "Behavioral Ethics". <br>https://en.wikipedia.org/wiki/Behavioral_ethics

[^2]: Wikipedia article "Language of Thought Hypothesis". <br>https://en.wikipedia.org/wiki/Language_of_thought_hypothesis

[^3]: Shojaee, Mirzadeh, Alizadeh et al., Apple Research, "The Illusion of Thinking". <br>https://ml-site.cdn-apple.com/papers/the-illusion-of-thinking.pdf

[^4]: Gary Marcus, "Generative AI’s crippling and widespread failure to induce robust models of the world". <br>https://garymarcus.substack.com/p/generative-ais-crippling-and-widespread

[^5]: Joel Becker et. al, "Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity". <br>https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/

[^6]: Sichu Zhang, Thoughtworks, "How much faster can coding assistants really make software delivery?". <br>https://www.thoughtworks.com/insights/blog/generative-ai/how-faster-coding-assistants-software-delivery

[^7]: Peter Naur, (1985). "Programming as theory building". Microprocessing and Microprogramming, 15(5), 253–261.  

[^8]: Ars Technica, "LLMs’ simulated reasoning abilities". <br>https://arstechnica.com/ai/2025/08/researchers-find-llms-are-bad-at-logical-inference-good-at-fluent-nonsense/

[^9]: Chengshuai Zhao et al., (2025). "Is Chain-of-Thought Reasoning of LLMs a Mirage? A Data Distribution Lens". <br>https://arxiv.org/pdf/2508.01191

[^10]: Thomas Fricke, Ressourcenverbrauch von AI (German). <br>https://thomasfricke.de/post/energy-resource-ai-de/

[^11]: Technology Review, 5/2025. "We did the math on AI’s energy footprint". <br>https://www.technologyreview.com/2025/05/20/1116327/ai-energy-usage-climate-footprint-big-tech/

[^12]: The Atlantic, 3/2024. "AI Is Taking Water From the Desert". <br>https://www.theatlantic.com/technology/archive/2024/03/ai-water-climate-microsoft/677602/

[^13]: Bloomberg, 5/2025. "AI is draining water from the areas that need it most". <br>https://www.bloomberg.com/graphics/2025-ai-impacts-data-centers-water-data/

[^14]: Krzysztof Budzyń et. al, The Lancet, 8/2025. "Endoscopist deskilling risk after exposure to artificial intelligence in colonoscopy: a multicentre, observational study". <br>https://www.thelancet.com/journals/langas/article/PIIS2468-1253(25)00133-5/abstract

[^15]: Gary Marcus, "GPT-5: Overdue, overhyped and underwhelming". <br>https://garymarcus.substack.com/p/gpt-5-overdue-overhyped-and-underwhelming

[^16]: Iris van Rooij, 8/2025. "AI slop and the destruction of knowledge". <br>https://irisvanrooijcogsci.com/2025/08/12/ai-slop-and-the-destruction-of-knowledge/

[^17]: 404 Media, 6/2025. "Teachers Are Not OK". <br>https://www.404media.co/teachers-are-not-ok-ai-chatgpt/

[^18]: P.V. Coveney, S. Succi. "The wall confronting large language models". <br>https://arxiv.org/html/2507.19703v1

[^19]: TechCrunch, 01/2025, "How OpenAI's bot crushed this seven-person company's website 'like a DDoS attack'". <br>https://techcrunch.com/2025/01/10/how-openais-bot-crushed-this-seven-person-companys-web-site-like-a-ddos-attack/

[^20]: Brian Porte & Edouard Machery. Nature, Scientific Reports, 2024. "AI-generated poetry is indistinguishable from human-written poetry and is rated more favorably". <br>https://www.nature.com/articles/s41598-024-76900-1

[^21]: Time, 04/25, "15 Quotes on the Future of AI". <br>https://time.com/partner-article/7279245/15-quotes-on-the-future-of-ai/

[^22]: Noema, 09/24, "The human cost of our AI-driven future". <br>https://www.noemamag.com/the-human-cost-of-our-ai-driven-future/

[^23]: Wikipedia article "Reinforcement learning from human feedback". <br>https://en.wikipedia.org/wiki/Reinforcement_learning_from_human_feedback

[^24]: "Tech Bro Topia", DLF documentary (German), 2025. <br>https://www.deutschlandfunk.de/tech-bro-topia-f1-mit-der-roten-pille-zur-macht-100.html

[^25]: MIT Technology Review, 2/25, "AI crawler wars threaten to make the web more closed for everyone". <br>https://www.technologyreview.com/2025/02/11/1111518/ai-crawler-wars-closed-web/

[^26]: Jürgen Geuther, talk at GPN23, 6/25. "Against Tech-Fascism". <br>https://www.youtube.com/watch?v=fOCxosh0cqA

[^27]: Daniel Stenberg, 07/25, "Death by a thousand slops". <br>https://daniel.haxx.se/blog/2025/07/14/death-by-a-thousand-slops/

[^28]: Ketan Joshi, 08/25, "Big tech’s selective disclosure masks AI’s real climate impact". <br>https://ketanjoshi.co/2025/08/23/big-techs-selective-disclosure-masks-ais-real-climate-impact/

[^29]: Malwaretech, 08/25, "Every Reason Why I Hate AI and You Should Too". <br>https://malwaretech.com/2025/08/every-reason-why-i-hate-ai.html

[^30]: New York Times, 09/25, "How Elon Musk Is Remaking Grok in His Image". <br>https://www.nytimes.com/2025/09/02/technology/elon-musk-grok-conservative-chatbot.html

[^31]: Wikipedia article "AlphaFold". <br>https://en.wikipedia.org/wiki/AlphaFold

[^32]: Stanford Business, 5/25, "When AI-Generated Art Enters the Market, Consumers Win — and Artists Lose". <br>https://www.gsb.stanford.edu/insights/when-ai-generated-art-enters-market-consumers-win-artists-lose

[^33]: National Observer, 9/25, "Google deletes net-zero pledge from sustainability website". <br>https://www.nationalobserver.com/2025/09/04/investigations/google-net-zero-sustainability

[^34]: Heated, 07/25, "He helped Microsoft build AI to help the climate. Then Microsoft sold it to Big Oil." https://heated.world/p/he-helped-microsoft-build-ai-to-help

[^35]: Sam Altman, 6/25, "The Gentle Singularity". <br>https://blog.samaltman.com/the-gentle-singularity

[^36]: Google Cloud Blog, 8/26, "How much energy does Google’s AI use?". <br>https://cloud.google.com/blog/products/infrastructure/measuring-the-environmental-impact-of-ai-inference

[^37]: Footnote free to reuse.

[^38]: Anthropic Research, 10/25, "A small number of samples can poison LLMs of any size". <br>https://www.anthropic.com/research/small-samples-poison

[^39]: The Register, 10/25, "Climate goals go up in smoke as US datacenters turn to coal". <br>https://www.theregister.com/2025/10/10/datacenter_coal_power/

[^40]: Futurism, 10/25, "AI Data Centers Are an Even Bigger Disaster Than Previously Thought". <br>https://futurism.com/future-society/ai-data-centers-finances

[^41]: _"All models fell far short of their Maximum Context Window by as much as 99 percent"_.<br> Norman Paulsen, 9/25, "Context Is What You Need: The Maximum Effective Context Window for Real World Limits of LLMs". <br>https://arxiv.org/abs/2509.21361

[^42]: Lennart Meincke, Ethan Mollick, Lilach Mollick, Dan Shapiro; Wharton Generative AI Labs, 6/25, "The Decreasing Value of Chain of Thought in Prompting". <br>https://gail.wharton.upenn.edu/research-and-insights/tech-report-chain-of-thought/

[^43]: BBC, 10/25, "News Integrity in AI Assistants - An international PSM study". <br>https://www.bbc.co.uk/mediacentre/documents/news-integrity-in-ai-assistants-report.pdf

[^44]: Gary Marcus argues that LLMs are rather good at interpolating - filling in a few missing points - but very bad at extrapolating - finding or deriving new knowledge. <br>https://cacm.acm.org/opinion/not-on-the-best-path/

[^45]: Iris van Rooij et al., Computational Brain & Behavior, 9/25. "Reclaiming AI as a Theoretical Tool for Cognitive Science". <br>https://link.springer.com/article/10.1007/s42113-024-00217-5

[^46]: The Register, 10/25, "Grounded jet engines take off again as datacenter generators". <br>https://www.theregister.com/2025/10/22/datacenter_jet_engines/

[^47]: Pivot to AI, 10/25, "AI: powered by old jet turbines, near you!". <br>https://pivot-to-ai.com/2025/10/30/ai-powered-by-old-jet-turbines-near-you/

[^48]: Courtney C. Radsch, 10/25, "We should all be Luddites". <br>https://www.brookings.edu/articles/we-should-all-be-luddites/

[^49]: Korny Sietsma, 10/25, "Agentic AI and Security". <br>https://martinfowler.com/articles/agentic-ai-security.html

[^50]: OWASP - GenAI Security Project, 2025, "LLM01:2025 Prompt Injection". <br>https://genai.owasp.org/llmrisk/llm01-prompt-injection/ 