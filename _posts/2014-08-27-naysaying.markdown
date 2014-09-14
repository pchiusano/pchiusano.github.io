---
layout: post
title: Unimaginative naysaying in tech discussions
categories: [tech, fp]
---

> It's the [early days of man](https://www.youtube.com/watch?v=U2iiPpcwfCA), a chilly day, and Alice and Bob are attempting to start a fire by rubbing sticks together. It's a struggle, on account of some freezing rain earlier in the day that's left the sticks damp. Suddenly, there is a brilliant flash of light, and a mysterious portal opens up next to Alice and Bob. A Stranger steps through carrying a __blowtorch__.
> 
> The Stranger explains he is from the Future and has come to speed along Alice and Bob's technological evolution. He demonstrates use of the blowtorch and has a roaring fire going within a minute or two. He explains how the torch works by burning a substance called __butane__.
>
> Bob's immediate response: "Look Stranger, I don't know who you are, but I don't like your elitist tone, and __I'm__ going to keep starting my fires by rubbing sticks together. If I start using your so-called 'blowtorch', it's going to be a real problem finding 'butane' fuel for it."

New technologies can be vast improvements along one axis (starting fires efficiently), while simultaneously generating new _fun problems to solve_ along another axis (obtaining butane). Being optimistic and willing to solve these fun problems is how progress is made. Unimaginative naysaying like Bob focuses entirely on perceived negatives of new technology, taking the current state of the world as a given.

Time and time again in discussions of technology, I see unimaginative naysaying:

* "You know, isn't it kind of archaic that we're storing programs as text files and running compilers in batch mode like it's still the punch card era? I think we could do better than that." __Response:__ "Text is KING. I'm not giving up Vim / Eclipse, and plus look at all the great tooling, like Git, that can be written in a language agnostic way! Plus, Smalltalk tried this, like 20 years ago, and it never took off."
* "You know, if we don't have rampant side effects, it's easier to compose programs by sticking together smaller pieces. That seems like a good idea." __Response:__ "But I need fast, mutable data structures in like 2% of my code, therefore this justifies sprinkling side effects willy nilly throughout! Be pragmatic! The Real World™ has mutation!!! Also, databases."
* "Static typing seems to help me catch mistakes while I'm writing the program. That seems useful, we should do more of that." __Response:__ "Oh gawd, you sound like one of those brainwashed J2EE developers. Sorry, I'm not writing `ArrayList<String> myList = new ArrayList<String>();`. Dynamic typing, DHH, and Agile FTW!!1!"

Developing new technology means being open to seeing potential, and working to achieve it. _Don't be **that** guy!_

And in closing:

> The person who says it cannot be done should not interrupt the person doing it.
> 
> -- Chinese Proverb

By the way, I will not be at all surprised when some commenter shows up to pick apart my analogy or one of my ha ha only serious examples.
