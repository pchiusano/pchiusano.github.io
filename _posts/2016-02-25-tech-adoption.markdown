---
layout: post
categories: [econ, fp, tech]
title: Design for experts; accomodate beginners
---

Technologists like to think about tools from the persepective of technical merits ("Lisp is better than C---it can do XYZ and C can't!"). Lots of arguments take place at this level. Here, I want to consider another perspective. Let's think of new technology like we would a new species entering an ecosystem. From this perspective, what matters is whether the species has attributes that allow it to survive and propagate itself. For new "technology species", surviving means attracting and retaining development resources (people, money, time, etc), and propagating means increasing adoption.

It's a harsh, cruel world. A world where technological species emerge and die off, seemingly at random---technical features are relevant only insofar as they help with survival. A world where [worse is worse](/2014-10-13/worseisworse.html) but "worse" (in terms of technical capabilities) can often survive better than better. A world where ["the market can remain irrational far longer than you can remain solvent"](http://www.quotationspage.com/quote/38206.html).

So for now let us put aside whatever personal feelings we might have about what technology is better (in our imaginary world where everyone adopted the tech we think is best) and consider this real world. It's ugly and brutal. But it's reality. How can new technology (and we _would_ really prefer it be good technology) adapt and thrive in this reality?

This post looks at the question from just one angle. Is it better for survival that a technology be designed for beginners or experts? Note that I am _not_ asking "do you personally prefer tools designed for experts or for beginners?" I am asking what is better for _survival and propagation_.

The situation is pretty nuanced. Let's put aside any talk of beginners or experts for a moment and think in terms of _learning curves_. Every technology has a learning curve. The x-axis is "time spent learning". Let's call the y-axis "productivity", basically tracking how much you can accomplish with the tool. Disclaimer: I won't claim any of these are new ideas, this is more an exercise in clarifying how we think about things.

A tool you already know how to use has a learning curve which is a flat line. You already know anything about the tool, and your productivity with the tool doesn't go up with time: (DANGER: bad ascii art graphs to follow!!)

                 |
                 |
    productivity |_____________ 20p
                 |
                 |
                  -------------
                      time

The `20p` is short for "20 productivity units". Yes, that sounds silly. Stay with me. A tool which is _limited in its capabilities_ might have a very short period of learning, then a flat curve:

                    limited-tech

                 |
                 |
    productivity |
                 | ____________ 10p
                 |/
                  -------------
                      time

When the line slopes upward, you are learning.

A tool which is very powerful may have a very long, steep learning curve. People use the sloppy phrase "steep learning curve" a lot, but that's not a bad thing at all. Steepness in the curve could mean you are learning a lot very quickly:

                    powerful-tech

                 |        _ 100p
                 |       /
                 |      /
                 |     /
                 |    /
                 |   /
    productivity |  /
                 | /
                 |/
                  -------------
                      time

Notice that productivity of `powerful-tech` matches `limited-tech` at each point in time, then blows past it. The "final" productivity is 10x more. Yay! Since there is never a point in time where one is more productive with `limited-tech`, all else being equal, from the perspective of adoptability, `powerful-tech` completely dominates `limited-tech`.

What's bad for adoption is not steepness, it's _nonlinearity in the learning curve_. Here's a hypothetical learning curve for some powerful tool:

           difficult-powerful-tech

    |                            _ 1000p
    |                           /
    |                          /
    |                         /
    |                    ... /
    |                   /
    |_                _/
    | \              /
    |  \____________/
     ----------------------------------
                  time

Obviously, these numbers and even the shape of the curve are completely made up. Again, bear with me. With a curve like this, your productivity might go _down_ compared to the usual way you might do things. You then do lots of learning for a while, but the learning doesn't manifest as increased productivity. You're laying the foundations of a huge skyscraper and the work is mostly invisible. Eventually, you reach a point where your foundation is complete, and you can actually start building. Your productivity rises rapidly and you also have the mental tools needed to absorb new concepts very easily so the slope of your learning increases as well. You zip past your old productivity before you started learning the tech and eventually reach a point where you're 10 or 100x more productive than you were previously. Awesome! Unfortunately, it's taken a long time to get there. And in "microbenchmarks" or toy problems of the sort that are easy to discuss, the less productive tool seems to win out, break even, or be only marginally worse, so you have a hard time concocting simple, compelling examples to convince anyone else that this `difficult-powerful-tech` is really worth learning. People on the outside start to think of you as some sort of irrational zealot, weirdly attached to your pet technology. Sounding familiar?

Lots of technologies have learning curves like this and they often don't amount to more than a niche technology unless the final productivity multiplier is really huge. There are lots of factors in play here, and how they interact is interesting.

First, people like getting _feedback_ when they are learning. With a highly nonlinear learning curve, feedback is much more indirect. Someone who is willing to deal with a highly nonlinear learning curve copes by A) believing strongly in the end results they'll eventually be able to achieve and B) enjoying learning for its own sake, even when it doesn't immediately lead to enhanced productivity. Let's face it---such people are a minority. Perhaps this is a sad failure of our education system, but it's also the current reality.

Next, if the final multiplier is huge, then we might be tempted to conclude that regardless of whether a technology is difficult to learn, people and businesses who wish to remain competitive will learn it. But, maybe not. Why is that? Well, a business can often achieve the same net productivity by employing _more people using less powerful tools_. Each individual is less productive, but throw enough people at the problem and stuff gets accomplished! Alan Kay quipped that ["most software today is very much like an Egyptian pyramid with millions of bricks piled on top of each other, with no structural integrity, but just done by brute force and thousands of slaves."](https://en.wikiquote.org/wiki/Alan_Kay#A_Conversation_with_Alan_Kay.2C_2004-05)

Look at companies like Google and Facebook. They are building software systems largely using tools that were or could have been written 30 years ago (PHP, C++, Java, Python, Go). Are they just acting irrationally? Why don't they get with the program and use some modern tech?? But it's more complicated than that. Even if we ignore the massive switching costs they'd face in migrating to some alternatives, it isn't even clear that these companies should just use the tech that is "the most powerful" (after acquiring deep expertise):

* On the one hand, using less powerful technology means they have to hire more programmers to accomplish the same tasks. There's additional communication overhead to having more programmers, and more technical debt (technical debt is much easier to create with more limited technology, has a higher "interest rate", and is harder to pay down). Score one point in favor of more powerful tools.
* But in using less powerful tools with a "better" learning curve (in the sense of at least having higher _short-term_ productivity and less nonlinearity, as discussed above), they also have less training costs, a much larger (and basically fungible) pool of qualified workers, and probably get away with paying less in salary than they might have to otherwise. This is mostly a function of that nonlinear learning curve.
* They also might have more concurrency in development. Perhaps there is more duplication and technical debt by hiring more programmers to accomplish the same thing, but how does overall productivity compare?
* That is, the objective of companies like Google and Facebook is not to maximize what individual programmers can accomplish, it's closer to: maximize overall productivity of the organization in comparison to the competition, while keeping costs low enough to pay expenses and/or raise money.

To make this a bit more concrete, consider two businesses, Business A and Business B, both competing to build some massive software system for a new niche. Business A decides to use Tech A with a highly nonlinear learning curve which we'll assume to be more productive given sufficient expertise but _less_ productive at first, while Business B decides to use Java. Due to the nonlinear learning curve, there's a much smaller pool of existing experts whose productivity with Tech A exceeds average productivity with Java. So Business A either needs to allocate a lot more capital to attracting such candidates, or they need to invest a lot of time in training programmers whose productivity is initially less than Java. So Business A has more up-front capital requirements and/or less velocity in the short term as they invest in training non-experts.

Here are some scenarios:

* If the project can be done at maximal velocity by 5 people using the best tools, the winning strategy is Business A. Even if the pool of experts is small, as long as Business A has sufficient capital to get just 5 of these experts to join, they start with higher productivity and continue to have higher productivity. In fact, this is probably among the best uses of capital for the business.
* If the project can't be done at maximal velocity given the number of people Business A can feasibly hire, then Business A is trading off short-term velocity (they have to train) in exchange for future productivity. Whether this is a winning strategy depends on whether the market being targeted has network effects and switching costs. If it has network effects and switching costs, being the initial leader is an advantage. Even if Business A later catches up to Business B in terms of functionality, if everyone is locked into Business B (whether explicitly or just via network effects), Business A can still lose!

Bottom line is that even a business which is being purely rational about tech decisions has a lot of difficult-to-estimate factors to consider. How much communication overhead will there be with more programmers and more limited tech? How much worse will the technical debt be? How much are training costs? Rather than a clear victory strategy, we have difficult tradeoffs. Are companies like Google and Facebook making the right choice? Does anyone really know?

This leads me to a design principle which might be summarized:

> Design for experts; accomodate beginners

That is, design powerful tools, but make the learning curve as linear as possible, and try to match or exceed the productivity of less powerful alternatives as soon as possible in the learning timeline. That is, it shouldn't take 6 months to match the productivity of more limited alternatives. The goal here is to eliminate a situation in which we are tempted to settle for more limited technology as a hack to (possibly!) improve adoption. For example:

       easy-powerful-tech-1

    |        /
    |       /
    |      /
    |     /
    |    /
    |   /
    |  / <- 10p
    | /
    |/
     ----------------------
             time

        easy-limited-tech

    |
    |
    |
    |
    |
    |
    |  ______ 10 p
    |_/
    |
     ---------
       time

Here, `easy-powerful-tech-1` starts out with lower productivity than `easy-limited-tech`, but quickly exceeds it through linear productivity growth.

Another strategy that can work is to start out with _higher productivity_ (often in the form of powerful features that can be used without deep understanding), and hit any nonlinear portion of the learning curve _after_ already eclipsing more limited tech:

       easy-powerful-tech-2

    |
    |                     /
    |                    /
    |                   /
    |    ____20p_______/
    |   /
    |__/ 10p
    |
    |
     ----------------------
             time

Notice that we have nonlinearity, but it's past the point where we're more productive than `easy-limited-tech`, so we're still better suited for adoption!

## Alternatives

With this in mind, I'm going to give names to some alternative adoption strategies one sees in the wild:

_Design for beginners (alienate experts):_ Build technology that's as approachable as possible for beginners, at the cost of _alienating experts_. While you win on adoption in the short term, in the long term, your beginners become experts and start to grow frustrated with your tool. Mindshare of experts starts migrating elsewhere, which is not good for competitiveness of your technology. At this point, it's only switching costs and network effects, and the gradual influx of new learners that keep the technology around. Is this enough to ensure survival? Maybe, maybe not. As an example, consider _spreadsheets_, [a limited technology that is easy to learn](/2015-03-17/unison-update5.html#why-spreadsheets). Spreadsheets are a programming environment with poor capacity for abstraction or really any of the other tools programmers use to manage complexity (the ability to define new types, for instance). In the finance industry, spreadsheets get used pervasively for lots of interactive programming tasks and there are _expert beginners_ who create extremely complex spreadsheets. In one sense, the results are impressive. And yet... it's a bit like building a skyscraper out of toothpicks and marshmallows. Can it be done? Yes, perhaps. Is it impressive? Yes, in a way. But investing in learning and using better technology (like, say, steel) would have paid for itself many times over.

We're now in a state where lots of people have recognized the problems with spreadsheets and there's a cottage industry of companies competing to replace or augment spreadsheets with more powerful alternatives. It's a difficult market though, because of just how deeply embedded spreadsheets are into organization workflows, and how high the switching costs and network effects now are.

Next up is _design for experts (alienate beginners):_ Build technology that's extremely powerful, giving no thought to how newcomers to the technology might become interested in it and come up to speed. On the one hand, the experts who bother to learn it are quite productive. On the other hand, the technology lacks attributes that facilitate anything other than niche following.

And there's something else, a bit more subtle. This strategy tends to attract the wrong sort of people. People who are tacitly okay with technology being _needlessly difficult_ for newcomers can tend to give off an unwelcoming vibe. Perhaps they view it as a sort of badge of honor or a proof of how smart they are that they're able to deal with this difficulty and now get to use a technology that's more productive. Perhaps they are even actively hostile to beginners. They make it personal and either directly or indirectly suggest people are stupid for not using their "expert-level" technology. It's often a RTFM, "toughen up", hazing culture, not a helpful one. Maybe the community leaders are unpleasant or rude, and no one seems to have much of a problem with it. The community starts to take a kind of pride in its niche status and acts more like a secret club. Members start to _like_ being small and different from the mainstream. Any of this sounding familiar?

Ironically, these factors can actually _get in the way of building better technology_. When part of the appeal of using some technology is getting to feel like you're part of your own little tribe, there's often a tacit (or explicit) rejection or disinterest among community members in making technical changes that could eliminate needless difficulty and make the technology more accessible to "outsiders". I'm not going to name names but you can probably think of lots of examples of this phenomenon...

## Conclusion

With new tools, adoption matters. And what drives adoption and flourishing isn't always technical quality. If we want high-quality, powerful tech to flourish in the world, we can start by _hacking the learning curve_. Design for experts, but accomodate beginners by eliminating needless difficulty and incidental complexity. Provide _easy wins_ that people can see and use without requiring deep understanding, but also provide hooks in the right places to guide beginners toward further learning.

This isn't compromising on power or principles. And many experts can appreciate and get on board with these changes too. We _can_ have it both ways. We just need to know how the world really works, and adapt our survival strategy accordingly.

Also see:

* [What is usability?](/2015-04-23/unison-update7.html#usability) dives into the concept of usability and usability comparisons.
* Want specifics? The [Unison project](http://unisonweb.org) is a new programming platform I'm working on that adopts this philosophy. Powerful programming models can be made much more accessible with a richer, more structured editor, and some radical assumptions that lets us solve [big problems once, at the level of the whole platform](http://unisonweb.org/2015-06-02/distributed-evaluation.html).
* [Good teaching makes long-term investments in the learner](/2014-10-23/learning.html), talks about the importance of investing in future productivity, not just the immediate task facing a learner.
* [Why spreadsheets](http://unisonweb.org/2015-06-02/distributed-evaluation.html#post-start) explains why spreadsheets are so popular, despite being much-maligned by programmers.
