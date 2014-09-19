---
layout: post
categories: [fp, gaming]
title: The limits of procedural generation and lazy simulation in games
---

I was watching [this video](http://www.gamespot.com/videos/reality-check-how-does-no-mans-sky-actually-work/2300-6420148/) about [No Man's Sky](http://no-mans-sky.com/), a game that's being advertised as "a science fiction game set in an infinite, procedurally generated universe". One of the creators [mentions here](http://blog.us.playstation.com/2014/08/26/no-mans-sky-a-whole-universe-to-explore/) that each planet in the universe of the game is fully determined by a single 64 bit seed. From this one seed, every "every blade of grass, tree, flower, creature" is generated, and it's all done lazily, as the world is observed by the player.

Fascinating!

This got me thinking about the limits of lazy generation and lazy _simulation_ of a game universe. It's "easy" to imagine procedurally generating a static landscape and perhaps a set of parameters for certain dynamical systems that will be simulated in a game world. What isn't clear is how to "lazily simulate" the evolution of the world itself, on demand.

As an example, perhaps we are wandering around a planet in our game and notice a creature with distinctive red spots drinking water from a stream. If we leave and come back an hour or two later, the creature's new location should be at least plausible--regardless of how things are implemented, we should not be able to observe any discontinuities.

For an "infinite universe", it clearly won't scale to naively simulate the actions of every entity the player observes, for an indefinite period of time. Yes, the  game *does* need to do fine grained simulation of any entities being directly observed, but if we travel to another planet on the other side of the galaxy and come back a few *years* later (in game time), the game could at least in principle get away with a more coarse-grained simulation. This problem reminds me a lot of _level of detail_ (LOD) rendering, where objects further away are rendered at lower levels of detail (which are not observable anyway). What I am pondering is something like LOD _simulation_.

This seems like a natural idea, but what could it mean?

Here's another example, from the system we call "Reality". I have a clock on the wall in my living room. _There_, I'm looking at it right now, watching the second hand move. When I go to sleep tonight, of course I presume it keeps ticking, but epistemologically speaking, I can't really be certain. All I can say for sure is that when I wake up in the morning, I'll observe the clock in a state that is _consistent with it having been ticking at a continuous rate all night_. Just as the world could have been created five minutes ago (not possible to disprove!), I also have no way of knowing if the clock stayed frozen until the split second before I first observed it the next morning!

In a game world, LOD simulation seems to require the ability to "fast forward" the entities being simulated (or at least step through the simulation at different levels of granularity). The clock example is easy because the clock state is a deterministic function of time so we can fast forward on demand to when the state is being observed. But what would it mean to fast forward the simulation of the red-spotted creature we noticed? I can certainly imagine some _ad hoc_ way of doing this for that particular entity, but I wonder if there is some _general_, _compositional_ way of describing behaviors such that we get the ability to fast forward an arbitrary composition of different entities. Compositionality is important--we want the coarse grained simulation of an entire planet to be composed from the LOD simulations of the entities on the planet. It won't scale to have to define these more coarse grained simulations *manually*, for each and every combination of entities in the game world.

_Aside:_ I'm reminded a bit of [some of Conal's observations about FRP](http://conal.net/blog/posts/garbage-collecting-the-semantics-of-frp)

There's one more difficulty: interactivity. Suppose we interact with the red-spotted creature in some way - perhaps strapping a heavy weight to its back which slows it down. This should in turn affect the LOD simulation for that creature, and it should bubble up in some way to the LOD simulation for the planet, and the game universe as a whole even.

I kind of wonder whether No Man's Sky (and other open-world games like _Spore_) have developed a principled, scalable, compositional solution to these problems. My suspicion is probably not. It seems like these games make the problems tractable by limiting richness of entity behaviors and placing very strong limits on interactivity.

I suspect FP has some interesting things to say about how to structure simulations like this.

What do you think?
