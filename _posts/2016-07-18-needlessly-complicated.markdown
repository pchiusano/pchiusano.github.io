---
layout: post
categories: [fp, tech]
title: Needlessly confusing names 
---

Names and the little details of an API matter. I'm going to pick on one small example I came across recently. In [unagi-chan](https://hackage.haskell.org/package/unagi-chan-0.4.0.0/docs/Control-Concurrent-Chan-Unagi.html), a very fast lock-free queue implementation, we have the following API:

```Haskell
newChan :: IO (InChan a, OutChan a)
```

Here's me: 

_Huh, okay, they split the queue into separate read and write access. Geez, was that really necessary? If I wanted to partially apply `readChan` or `tryReadChan` and pass around an `IO a` or `IO (Maybe a)`, I could do that, you know. Now if I need both read and write access, you've forced me to pass around a pair... ugly. But okay, okay, fine, I can deal with this, this thing's supposed to be screaming fast, I can put up with a few admittedly subjective API warts, and honestly I'm being pretty ungrateful that the people wrote this nice code and made it freely available._

Two minutes later: a type error when trying to read from an `InChan`. _Wait, what? Let me re-check hackage docs here... WAT? `readChan` takes an `OutChan`??_

```Haskell
readChan :: OutChan a -> IO a
```

_WTF!!?! What kind of idiot would---oh, wait, maybe that does make sense. I'm probably just being dense here. Calm down, Paul. Let's think about this, do you read from the `InChan` or the `OutChan`? Obviously I should read from the `InChan`... but AHA! From the perspective of the producer, the `InChan` IS the `OutChan`. But from the perspective of the consumer, it's the reverse! This is so fucking confusing! What the hell was I even doing? I've forgotten by now..._ 

_(After a moment of realization). You know, it's completely arbitrary whether the `InChan` is from the perspective of a queue writer or a queue reader, so these names are just confusing as hell. Calling these two types `WootChan` and `Woot2Chan` would have been MORE INFORMATIVE, since it would not have tempted me to start writing code based on my assumed understanding of the meaning of `In` and `Out` without checking the type signatures!_ 

_Not to mention, while I'm trashing this code, who decided to call those functions `readChan` and `writeChan`? Where exactly are we writing to / reading from? Is this a stack or a queue? How about `enqueue` and `dequeue`, which is more appropriately suggestive of what's happening?_

Five minutes later, I'd settled on this: if you really insist on splitting up the queue into two types, how about these names:

```Haskell
newQueue :: IO (Enqueue a, Dequeue a)

enqueue :: Enqueue a -> a -> IO ()
dequeue :: Dequeue a -> IO a
```

Now THIS is the sort of API I like. I can use it without switching on random parts of my brain that have nothing to do with what I'm trying to accomplish. 

Summary:

* _Don't make me think (about the wrong stuff)._
* Don't make things needlessly complicated or confusing.
* Don't tolerate the complexity apologists who claim these mental speed bumps are "no big deal", or who say things like:
  * "just RTFM"
  * "fool, I just read the type signatures, ignore all names, and am enlightened"
  * "we're all professionals here; stop whining and get on with writing MOAR codez!!1"

Also see this section of [design for experts; accomodate beginners](http://pchiusano.github.io/2016-02-25/tech-adoption.html#alternatives). 
