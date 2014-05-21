---
layout: post
title: Is network I/O always an effect worth tracking?
categories: [fp]
---

One of the most important contributions of FP is the discipline that _effects which affect program meaning_ should be treated as values and explicitly sequenced. Following this discipline leads to an architecture which splits programs into two phases--in the first phase, first-class effects are built up and combined using pure functions, and in the second phase (the 'end of the universe'), these effects are interpreted in order to actually _do_ something.

What doesn't get said often enough is that while there are some standard choices, it's ultimately _up to us_ to decide what effects we want to consider part of our program's meaning. In all languages, there are some ambient effects allowed in 'pure' code, and we allow these effects because we don't choose to have them participate in our program's meaning:

* We don't typically track memory allocation as a typed effect (because we have GC)
* We don't typically track partiality as a typed effect (because this is unavoidable in a Turing-complete language)
* In Haskell, we don't track evaluation as an effect--both thunks and forced values have the same type in Haskell (ignoring `Int#` and the like).
* In Haskell, we don't necessarily track parallelism as an effect. Although [we can use typed effects here](http://ghcmutterings.wordpress.com/2010/08/20/parallel-programming-in-haskell-with-explicit-futures/), `par` and `pseq` are allowed in code which is typed 'pure'.

But we do track effects like file and network I/O, and certainly any mutation of in-memory data our program has access to.

_Aside:_ Chapter 13 of [FP in Scala](http://manning.com/bjarnason) discusses some of these issues in more detail, and [Runar also has a nice post](http://blog.higher-order.com/blog/2012/09/13/what-purity-is-and-isnt/) that formed the starting point for that chapter.

In general, the advantage to _not_ tracking an effect is that, without any sort of explicit effect polymorphism (for instance being parametric in the choice of monad), functions are more general, in particular higher order functions, which work for all possible combinations of the presence or absence of an effect. For instance, not tracking evaluation as an effect in Haskell means we avoid a combinatorial explosion of different functions (or lots of explicit effect polymorphism) for dealing with strictness and evaluation. My favorite example of this is the `map` function:

~~~ Haskell
map :: (a -> b) -> [a] -> [b]
~~~

In Haskell, we may pass an `a -> b` which is lazy, and avoids evaluating the `a` it is given, or a strict `a -> b`. Both variants are useful, and in a strict language, we could in theory define two versions of `map`, one which takes a strict function, and another which takes a non-strict function. In practice, no one bothers with this sort thing in strict languages, so 'optionally lazy' strict languages are often unnecessarily strict, at a loss to modularity. Haskell gets this effect polymorphism 'for free', as a consequence of lumping both forced values and thunks into a single type and using pervasive nonstrictness by default.

### Should we always track file and network I/O as an effect? ###

So now I want to ask a question--should we always track file and network I/O with typed effects? Might there be situations where we want to allow pure code to perform file and/or network I/O? Treat this as more of a thought experiment; I am not actually advocating for any of this for real code.

Suppose I am writing a piece of code that fetches data from the file `/foo/bar/baz.bin`, and I am prepared to assume that the contents of that file will never change over the course of my running application. Do I still need to do the file read in `IO`?

__Bob:__ Of course you do. The file system is external to your program, and other running programs (that you don't control) may modify that file while your program is running. You simply _have_ to track this as an effect if you're really doing FP.

__Alice:__ You're right, I certainly can't guarantee that assumption of immutability of that file. But then again, a gamma ray could flip some bits in the region of memory corresponding to my purely functional data structure, and I can't guarantee this won't occur either! And I'm certainly not modeling this possibility explicitly in my program.

__Bob:__ Oh, come on. Gamma ray corruption is extremely unlikely. Some other process modifying the file is much more possible.

__Alice:__ Is gamma ray corruption [really so unlikely](http://research.google.com/pubs/pub35162.html)? I don't know. But anyway, who are you to say how likely either of those events are? Maybe I am the sys admin, and have granted no one else write access to that file. Anyway, let's not get mired in these details. My general point is that there is no _fundamental_ distinction between fetching data from an (assumed to be immutable) region of memory and an (assumed to be immutable) region on disk. At the end of the day, we as programmers get to decide what possibilities we want to account for and model explicitly in our programs.

__Bob:__ I'm not entirely happy with that reasoning, but I'll grant you your point. You've still forgotten one thing.

__Alice:__ Yes, Bob?

__Bob:__ You've forgotten that reading from a file uses a file handle, which are in limited in number by the OS! Therefore, in my opinion, file reads even from your so-called 'assumed to be immutable' files are worth tracking as a typed effect.

__Alice:__ I'm glad you said 'in your opinion', because it is indeed an opinion and these are indeed choices. But actually, I don't _necessarily_ agree with you--one can imagine an OS in which file handles were no more scarce than memory. Or we could simply imagine a more sophisticated I/O manager which makes the underlying OS file handle scarcity irrelevant to the vast majority of programs. 

__Bob:__ Hmm, sounds magical. I'd like to hear more about how exactly this hypothetical OS or I/O manager would work. At the end of the day, computers do have finite resources..

__Alice:__ And at the end of the day, computation proceeds by a series of electrical impulses and produces the effect of little tiny dots on a screen being lit up and changing colors. Who cares? I'm talking about abstractions here. For purpose of this thought experiment, let's just imagine that such a thing exists such as to make worrying about the finite nature of file handles _irrelevant_. Then do still need to track reading the immutable file `/foo/bar/baz.bin`?

__Bob:__ Okay I see where you're going with this. Although what about the fact that reading from a file, even an immutable one, has a higher possibility of failure and will certainly take longer than reading the same data from memory?

__Alice:__ I'm going to just wait for you to think of what my answer will be to that remark.

__Bob:__ You know, I always found you rather pretentious in these discussions we have... But _fine_, I know that you'll tell me that a) who is to say that reading from a file has a higher possibility of failure or takes longer than reading from memory, and b) we already include partiality as an ambient effect in pure code, so as long as we aren't 'catching' this exception anywhere, purity isn't violated, and c) we don't usually include running time as part of our program's meaning anyway.

__Alice:__ Exactly! I couldn't have said it better myself.

Now that we've come this far, suppose we modify our program to read instead from `http://example.com/foo/bar/baz.bin`. Once again, suppose I am prepared to assume that the data available at that URL will never change over the course of my running application. 

__Bob:__ Once again, you can't really guarantee that assumption will hold, but let me guess...

__Alice:__ Yes, so what? Our gamma ray argument still carries. If we are being precise, we might say that there is some probability distribution which models the likelihood of the data changing out from under us, and depending on this distribution we make a decision about whether to model the effect in our programs. But there is no One True way of making such a decision.

__Bob:__ Perhaps there is no One True way of making such a decision, but _in my opinion_ (does this qualification make you happy, Alice?), the possibility of modification has become a lot higher now that we are talking about an external URL. Surely at some point you must concede we have to start tracking these effects.

__Alice:__ All right, then, suppose for purposes of my thought experiment that the URL is `http://immutabledata.com/e4d909c290d0fb1ca068ffaddf22cbd0`. I manage this domain. People send me files, and I compute their hash and publish the file at `immutabledata.com/<file-hash>`. If the file 'changes', I simply publish the file at its new hash. And to make you happy, I have a super-secure process by which this is all done, such that it is exceedingly unlikely the data at any URL in this domain will _ever_ change. In fact, for purposes of this thought experiment, assume my process is so good that the probability of data ever changing is _less_ than the possibility of gamma ray corruption! Are we still required to track reading from that URL as a typed effect?

__Bob:__ That's a lot of hypotheticals. There is still the small detail of the limited number of network connections one can make, but I already know you'll deploy your magic unlimited file handle OS / sufficiently smart I/O manager argument again!

__Alice:__ Of course.

__Bob:__ Okay, so I'll grant you the general point. Under a certain (in my opinion unrealistic) set of assumptions, we need not track network reads with typed effects. But now I think the burden is on you to show that you can actually build this magical hypothetical I/O manager you've relied on.

__Alice:__ Quite right! Sounds like a fun problem for me to think more about. If such a thing we possible, that would be quite interesting and useful, wouldn't it...

So it would seem we don't always need to track file or network I/O as an effect, or rather, what we track as an effect is very much dependent on the assumptions that we as programmers choose to make, and on some fundamental pieces of technology like the I/O model and I/O manager used by our language runtime. Just like we don't choose to track memory allocation as an effect (in part due to efficient GC in the runtime), perhaps it's the case that we don't always wish to track 'assumed immutable' file reads as an effect (given a smarter I/O manager).

This was just a thought experiment. I'll leave the design of such an I/O manager as an exercise to the reader.

[Discuss on Twitter](https://twitter.com/pchiusano/status/469131674807455745) or [on Reddit](http://www.reddit.com/r/haskell/comments/265blu/must_file_and_network_io_always_live_in_the_io/).
