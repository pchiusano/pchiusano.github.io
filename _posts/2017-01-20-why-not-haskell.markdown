---
layout: post
categories: [fp, programming]
title: If Haskell is so great, why hasn&apos;t it taken over the world? And the curious case of Go.
---

Programming is all about managing complexity. Without good tools to manage it, the complexity of programs becomes mentally intractable for our limited brains and we'd lose control and understanding of our programs (imagine writing a big software system entirely in assembly language).

And so the history of programming has been a series of advancements in both _removing barriers to composability_, and _building new programming technologies that better facilitate composition_. To the extent that software has _compositional structure_ (as opposed to _monolithic structure_), it can be understood and managed by our limited brains, and we can build more complex software via composition of smaller pieces. Also very important is that composable artifacts can be assembled by thousands of people in loose communication, often working in parallel, whereas monolithic artifacts require small teams in close communication, often working sequentially.

So we've proceed in stages:

* Stage 0: Composability is the most important thing; _without it, complexity swallows us_
* Stage 1: Composability requires atomic units of composition with means of combination, _therefore functions_
* Stage 2: Composability is limited by side effects, _therefore pure functions and functional programming_
* Stage 3: Composability without mechanized reasoning becomes difficult for humans to track, _therefore static types_
* Stage 4: Composability is destroyed at program boundaries, _therefore extend these boundaries outward, until all the computational resources of civilization are joined in a single planetary-scale computer... this is the idea of [Unison](http://unisonweb.org) and [unison.cloud](http://unison.cloud)_.
* ...
* Stage N: We'll get back to this at the end of this post

Pause here. While you can definitely still compose programs from impure functions, doing so is less flexible and also more complicated for the programmer. I'm not saying any form of composition is impossible with impure functions; I'm saying it's more difficult (for instance, it requires non-local reasoning). Likewise for the other stages. I'm not saying you have no composability without static types; I'm saying that static types more easily facilitate composition given our limited brains. (Also see: [Turing tarpit arguments](#turing-tarpit))

## If Haskell (or XYZ) is so great, why hasn't it taken over?

I've [written some posts about tech adoption generally][adoption]. Now, I'll give a thesis to explain a nettlesome question:

> If Haskell is so great, why hasn't it taken over the world?

But pick any non-mainstream tech that you think is better, it doesn't have to be Haskell. You can invent all kinds of responses:

* "It really IS taking over the world, even Java has lambdas (or `<feature-related-to-my-pet-tech>`) now!!" (okay, but let's be real, pure FP at large scale is still not that common)
* "It really IS taking over the world, just very slowly..." (I mean, maybe, but you could have said the same thing 10 years ago; how do you know you aren't fooling yourself?)
* "Everyone else outside my little tribe is a moron!" (You really shouldn't think this... but even if you did think it, you should ask yourself: if Haskell were really that much better, wouldn't eventually these supposed "morons" be convinced by the mountain of incontrovertible evidence that accumulated in favor of your preferred technology's vast superiority??)
* "It's too hard to learn" (If your pet technology were 1000x more productive than, say, Java, would this learning curve really be a substantive barrier? Though this question is more complicated than you think---see [the tech adoption post][adoption]---if the multiplier is big enough, will these complexities matter? Imagine if DVORAK or some other keyboard layout were 1000x faster than QWERTY for typing.)
* "It's all about MARKETING. JavaScript has better marketing!! And its projects have cooler logos on their landing pages!" (Okay, now you're really reaching...)

The simplest explanation is probably that Haskell is not _that_ much better than, say, Java, _for many of the software systems people write today_. Why might this be?

The reason I'll give is that Haskell's otherwise excellent composability is destroyed at I/O boundaries, _just like every other language_. That is, we are at stage 4 above, where the bottleneck to further composition is these program boundaries. Since most software systems (especially those that span multiple nodes), have a large surface area in contact with the outside world, the code devoted to merely getting information at these boundaries into some more computable form is often the bulk of the work; once the data is in computable form, the actual computation needing to be done is easy.

David MacIver has [this quip about early Haskell enthusiasm](http://www.drmaciver.com/2015/04/on-haskell-ruby-and-cards-against-humanity/):

> “Look, I used a monad! And defined my own type class for custom folding of data! Isn’t that amazing?“. “What does it do?” “It’s a CRUD app”.

If you're writing a CRUD app, or some other computationally boring system that has a large, complex surface area in contact with the outside world, writing code to deal with that program boundary often dominates the codebase.

Where we see Haskell (or more generally, typed FP) excel is for programs that have minimal surface area in contact with the outside world, but with a large amount of interesting computation happening internally. A good example: compilers. Compilers don't have much interaction with the outside world---just reading some files---but have lots of interesting computation happening internally, for things like typechecking, code generation, and so on. Haskell excels here; I would not be surprised if Haskell were 100x better than Java for writing compilers. Writing CRUD apps? Haskell isn't as much of a win.

I think this hypothesis also offers an explanation for why Go is popular, even though the language is "boring" and could have been designed in the 1970s. Go has found a niche as basically "a better C" or "a better Java" for writing high-performance servers that do lots of I/O. Unlike C or Java, it has a much more high-level I/O and concurrency story, but the language itself is otherwise very familiar to people with a background in these and other mainstream languages. Thus it serves a niche that wasn't previously well-covered.

As soon as you need to be defining lots of complex or interesting computations, you start needing languages with good support for composability to manage that complexity. Here Go fails, for all the reasons that people have criticized it. But there's still a good chunk of services where Go can do quite well!

Haskell programmers might object that, well, Haskell has its own very nice I/O and concurrency story, in many ways more sophisticated than Go (things like software-transactional memory, which make writing highly concurrent data structures and algorithms much simpler). But Haskell is "weird". A C, Java, Python, or Ruby programmer can pick up Go easily. They can't pick up Haskell so easily, as even in beginner Haskell, you are immediately confronted with lots of unfamiliar concepts. And since Haskell isn't enough of a win for these "boring" services, Go can still make sense.

## What's next?

The [Unison programming language](http://unisonweb.org), and the [unison.cloud](http://unison.cloud) platform I'd like to build around it, is my effort to move programming beyond Stage 4. By removing any friction and non-uniformity when programming multi-node software systems, such systems can once be assembled in a compositional fashion. The better composability of typed, pure FP once again becomes a significant lever, because process boundaries no longer destroy composition.

I wonder what comes after that? When all the obvious barriers to composability have been removed, the 'composability bottleneck' must move somewhere else, somewhere that might not be obvious. Like where? Time for some vague speculation...

One other problem we have today is that composability is destroyed at "application" boundaries, at the interface between humans and our programs. We write a bunch of "backend code" in a compositional fashion, then build a bespoke, single purpose UI for interacting with some ad hoc subset of this, which is a dead end for further composition. ([See this Conal Elliott talk on this](https://www.youtube.com/watch?v=faJ8N0giqzw)) This is a problem, and it can be solved.

But even if we move beyond that, there will be other composability bottlenecks, and when those are removed, there will be others, and on and on, and in the end... well, I don't know.

What do you think?

## <a id="turing-tarpit"></a>Appendix: Turing tarpit arguments

There's a kind of argument that comes up a lot in discussion of programming languages. I call it a "[Turing Tarpit](https://en.wikipedia.org/wiki/Turing_tarpit)" argument: programming tech A isn't really better than tech B, it's just that A is a bit more convenient than B for a few hand-picked little examples (implicitly: "Big deal, who cares?").

> Beware of the Turing tar-pit in which everything is possible but nothing of interest is easy..

The trouble is that very often, the sorts of examples that are easy to discuss aren't of sufficient scale to reveal any major differences between A and B. It's only when building much larger systems that the difference become more than "a little convenience". That is, Turing tarpit arguments skip doing any analysis of how or whether the "little more convenience" might becomes bigger as N gets larger, and tacitly assumes that any language that's Turing complete is just as good as any other. It's a bit like saying: "Oh, geez, this heapsort algorithm you've got seems rather baroque and complicated. My insertion sort algorithm runs just as fast on this 10 element list."

Imagine traveling back in time to the days before C, and trying to convince an assembly language programmer that C was a massive step forward for programming. _In principle_, you could build arbitrary programs by gluing together hand-written fragments of x86 assembly language. In practice, fragments of assembly language aren't very composable _given the limitations of our brains_. But you might have a hard time convincing the assembly language programmer of this, because toy examples of the sort that are easy to discuss would not reveal any major differences.

What WAS likely convincing to assembly language programmers was the idea of not having to write the same program 5 times, for each different hardware architecture. This was a clear productivity boost that was immediately understandable to anyone who wrote assembly language and needed to target different architectures. And this huge advantage was enough to get "high-level" languages like C in the door. With time and experience using C, the more subtle, abstract benefits of increased composability of C over assembly language would become more apparent.

[adoption]: /2016-02-25/tech-adoption.html
