---
layout: post
title: Is tech adoption really that random? A VHS vs Betamax redux!
categories: [tech]
---

I worry a bit that people are adopting an overly fatalist perspective on tech adoption. This perspective has become almost a reflex--we can't possibly know or predict what technology will end up being widely adopted and often the "worse" technology wins, so just don't even try!

I acknowledge there is some underlying randomness at work here but it would be a shame if it were totally random, right? Why would you bother trying to create any new technology if its fate will be decided by coin flip? But if it's not _totally_ random then there must be some underlying principles, so what are they? I've started looking at a few examples of this (and would love to hear others in the comments) and I hypothesize a simple rule: if a technology is at least an order of magnitude better _for some key factor(s) evaluated by market participants_, and not substantially worse for other factors, it either finds a niche or takes over the market. And the corollary of this claim is that if a technology _isn't_ substantially better for key factors, there's a certain degree of randomness and [path dependence](https://en.wikipedia.org/wiki/Path_dependence) that determines what technologies get adopted.

Let's look at some examples.

### VHS vs Betamax

The way this is usually presented is that Betamax was the better technology (it had higher picture quality, supposedly), but VHS won out, because 'worse is better'. Geez, I'm disappointed that people are so readily able to accept such a shallow explanation. Implicit assumption: the adoption game is totally random in who it rewards with victory, and maybe it even actively rewards bad quality! But let's look closer:

* The picture quality wasn't better, at least in any obvious way, here's a quote from [Why VHS was better than Betamax][vhs]: "Standing in a shop at the time, there was absolutely no visible difference in picture quality, and some reviews had found that VHS's quality was superior."
* Betamax was more expensive, at least at first
* And the kicker: VHS had a 2 hour recording time, long enough to record a movie, while Betamax opted for a smaller, sleeker form-factor that could only record 1 hour!
* It's very much a 'winner take all' market due to positive feedback loops (e.g. the more people who own VHS players, the more likely video rental stores are to stock VHS, which leads to more people wanting to own VHS), so the difficulty of catching up increases with time.

[vhs]: https://www.theguardian.com/technology/2003/jan/25/comment.comment?INTCMP=SRCH

There are always multiple factors that go into comparing the utility of products, and different buyers in a market will weight those factors differently, which is why markets can have different niches. In the format wars, we have price, recording time, picture quality, and availability of content in that format, among others. But both VHS and Betamax were in the same ballpark on all these key factors, so they faced direct competition in a market with network effects and VHS had the edge in the factors that mattered to most people. But, as a thought experiment, I hypothesize:

* If Betamax picture quality had been miraculously 100x, equivalent to a modern 4k TV, it would have found a niche, even if it were more expensive than VHS and had the same limited recording time. There is always a segment of the market that will pay a premium for obvious quality for some key factors. And most likely, the obvious picture quality would spur huge investment in compensating for the other factors that VHS had an edge in.
* If Betamax had a 5 year head start, it might have reached the tipping point where VHS recording time superiority wasn't enough. (More likely, Betamax could have developed a longer recording time format before VHS had a chance to catch up.)

Consistent with my hypothesis: the market later moved on to DVDs (superior picture and sound quality, no degradation over time, and random access / interactivity), at around the time where these improvements in key factors were sufficient to overcome the inertia of VHS.

Other format wars have worked similarly---formats roughly equivalent in key factors were introduced around the same time, and faced direct competition in a winner-take-all market.

### QWERTY vs other keyboard layouts

The QWERTY keyboard layout was a relic from the typewriter era, designed to actively prevent higher typing speeds that would jam the typewriter, and it got carried over to computer keyboards. The trouble is that none of the alternate layouts have been able to show a big enough improvement in typing speed or any other factor. Really, how much better is some super-custom keyboard layout, once you invest all the time in getting fluent with it? _Maybe 2x_, if that? But you could also improve your typing speed just by learning to touch-type better, which also would carry over to other keyboards you have to use!

On the other hand, as a thought experiment, what if DVORAK were 10x faster than QWERTY? How about 100x faster? At a certain point, the improvement in a key factor becomes sufficient to overcome momentum of the existing technology and find a niche or take over the market. And we have some evidence for this: stenography has existed in parallel alongside QWERTY, because its speed over QWERTY made it more useful than QWERTY for some segment of the market.

### RethinkDB vs MongoDB

[RethinkDB vs MongoDB](http://www.defstartup.org/2017/01/18/why-rethinkdb-failed.html) is an interesting case. MongoDB beat RethinkDB to market and was faster at first, while RethinkDB took their time and focused on correctness and safety (which many using NoSQL cared less about). Both technologies were in the same ballpark for all factors and faced direct competition.

### Time for some wild speculation into human psychology

A funny story is that I myself accepted the usual explanation about VHS vs Betamax without much thought. Then after I started working on [Unison](https://unisonweb.org) people often asked me questions about programming language adoption and I realized how depressing it was for me to accept the premise that it was really just a harsh, cruel, and totally random world out there. According to this premise there was an excellent chance that nothing I worked on would be picked up by anyone else while other technologies I liked a lot less became wildly popular for no apparent reason. While feeling somewhat down in the dumps after one of these conversations I got a bit more curious about VHS vs Betamax... a couple hours of feverish internet research and hope was restored. The world actually makes sense, kinda!!!

The question I asked myself after: why did I so readily accept the earlier explanation of the "harsh cruel and random world"? Well, like all myths it has an element of truth. In the small, there is a lot of randomness at play, and we have to acknowledge this reality. But I think also that there is something _comforting_ about the notion that it's a harsh, cruel, random world out there. If the world is really that way, you aren't really responsible for how things play out and you might as well not bother doing anything, right? Just collect your fat paycheck, Netflix and chill! The people trying to build actual new things are just suckers who are wasting their time.

When you are actually working on a big project, though, this kind of mentality just doesn't work very well and is super unmotivating, so it's worth trying to understand whether this mentality reflects reality or if it's something your brain has just accepted without much examination.

There's hope!
