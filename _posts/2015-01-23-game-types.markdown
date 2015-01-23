---
layout: post
categories: [fp, gaming]
title: Can type systems provide a better way of organizing game interactivity?
---

I've been playing through [Alien: Isolation](https://www.youtube.com/watch?v=trPIYFR-zAk). The game is well-executed, gorgeous, scary, and yet limited in a way that makes it unsatisfying. I'll talk about that, but a bit of context on the game first:

The game takes place 15 years after the events of the first _Alien_ film. You play as Amanda Ripley, daughter of Ellen Ripley and part of a crew sent to recover the flight recorder from the Nostromos (the space station from the first _Alien_ film). After some brief, predictable plot machinations, you end up stranded on board the enormous Sevastopol station, isolated from the rest of your crew. The flight recorder is somewhere aboard the station, but perhaps you have more important things to worry about. You find the station in disarray and suspiciously empty. Various clues---ominous graffiti on the walls, messages and logs on old computer terminals sprinkled throughout the station, and yes, discovery of dismembered dead bodies---lead you to the uncomfortable conclusion that there's _something_ on board the Sevastopol which has killed the majority of the crew. Of course, we knew where this was going all along, but not until a few hours into the gameplay do we see even _a glimpse_ of the Alien.

The gameplay involves a lot of stealth and generally _avoiding_ rather than confronting dangerous situations. You are not in control, and you don't have the resources to attempt any sort of direct confrontation with enemies. The overall experience is effectively frightening. I'll relay just one anecdote from my gameplay. I'd just successfully snuck my way through a treacherous area where some androids-gone-haywire were patrolling and killing any humans they encountered. I exited the area safely and was desperate for a save point. Gameplay fatigue had set in; it was a little over an hour into my session and I wasn't thinking clearly or paying close attention. At this point in the game, I'd seen only glimpses of the Alien and had for no good reason convinced myself that any direct confrontation wasn't something I'd have to worry about for a few more hours. I wandered around somewhat aimlessly looking for a save point (they appear as emergency phone terminals in the game, spaced annoyingly far apart). For no reason that I can explain, I avoided looking at my map, where _where save points are conveniently indicated_. As I wandered, I heard odd noises... perhaps the reverberating denting metal sound that comes with heavy footsteps on a thin metal surface? But I dismissed it---noises that couldn't be fully explained had become a fact of life for me while on board the Sevastopol. The motion sensor I'd been using religiously earlier to avoid androids went unused for several minutes. I finally paused, took it out, and noticed movement on it. Whatever it was moved rapidly out of range, more rapidly than any android. _That's odd._ Without considering the gravity of my situation, I put the sensor away and continued wandering. In retrospect, I was behaving a lot like the clueless expendable crew member in the first Alien movie who has no idea what kind of danger he's in and gets nabbed while searching for the cat that escapes. A minute or two later, I reached a door, and without thinking, walked to it and waited for it to open.

The door opened with a *ka-chunk!* and a satisfying hiss. To my very real horror, this revealed the Alien, _just three feet from where I stood_, directly facing me. I was so mentally unprepared that it took me a second to register what was even happening. In that moment, the full enormity of my own stupidity and carelessness became apparent, and I also understood with certainty that I had no chance of escaping. The Alien took a step forward and quickly dismembered me. Game over.

Taking a step back, yes, the game is effective in providing frightening experiences like this, but there's something unsatisfying about _the limited interactivity of the game world_, and the _predictable orchestration_ that is an inevitable consequence of this limited interactivity. The game is like a beautiful sculpture to explore, but you can't interact with most of it:

* There are rooms with computer terminals. But most of these terminals are just _geometry_. They look pretty, but you can't type on the keyboards or interact with them in any way. In one room, there are computers everywhere, but only _one_ can you interact with to trigger some event. The events are of course highly scripted.
* Supply rooms often contain shelves with various objects on them. Perhaps there's a wrench on a shelf, but you can't pick it up, and you certainly can't use it in some creative way to open a door that's locked.
* There's a door or a table seemingly made of glass, but you can't even make an attempt at smashing it with some item in your inventory (or perhaps your fist!).
* There's a railing, less than your character's height, but you can't just climb over it, you have to walk around it. You can climb up a ladder, but you can't climb up a wall with obvious handholds.

Put another way, most of the entities in the game world are _lifeless objects_. The game world is 99% static geometry. It may be beautiful to look at, but you can't interact with 99% of it, and the 1% you _can_ interact with often feels rather arbitrary---why this thing rather than that? It's understandable why these limitations are in place, since the game creators have to explicitly program any allowable interactivity. And yet these limitations break immersion. When you play the game, you aren't creatively problem solving the way you might in real life. You are merely searching for whatever arbitrary solutions have been orchestrated by the game designers, following along a static set of waypoints.

Is there anything that can be done about this?

### Enter type systems ###

I'm going to make some informal arguments for why type systems and programming languages provide a partial answer to the question of how to organize a much richer degree of game interactivity. Let's consider the system we call "reality". In the real world, there are a combinatorial number of possible interactions between objects in the world. For any combination of objects, there are some interactions that are physically allowed, for instance:

* Given a mug and a faucet, I can fill the mug with water. If I have some plastic wrap also, I can stretch it over the mug tightly to seal the water inside. If I throw the mug against a wall it will likely shatter and spray the water.
* Hmm, that gives me an idea. What if I filled the mug with _gasoline_ then covered it with plastic wrap? If I chucked this at a wall, would the shattering of the ceramic generate a spark that ignited the gasoline?
* This mug seems pretty sturdy. I bet I could stand on top of it and reach an extra 5 inches or so higher. I wonder if that would ever prove useful?

I just made these up, and could come up with hundreds of other interesting interactions that are allowed by the system we call reality. Beside these interesting, off-label usages of the objects in reality, there are a whole lot of predictable interactions allowed as well, simply using objects "as intended" to interact with the world. In our day-to-day life, we generally only consider a much smaller subset of possible interactions, but if actually placed in extraordinary circumstances (like surviving the Sevastopol), people can be quite resourceful and will use whatever interactions are available to them.

The basic problem with achieving this richness is of course that naively programming each of these allowed interactions doesn't scale. Even just considering pairs of entities in the game, the amount of programming grows quadratically---how can a faucet interact with a mug? How about with a wrench? (Perhaps it increases the rate at which the wrench becomes rusty and hence breakable.) How about with the player? (Perhaps increases weight, which affects how high player can jump. Or affects player's temperature, which affects...) Naively considering each of these possible interactions and writing code to simulate them explicitly is impossible, and so not surprisingly games just settle for exposing a very small number of interactions to make the problem tractable. Some games go further than others, but even so-called sandbox games are extremely limited in what interactivity they allow. This has the unfortunate consequence of eliminating any hope of truly creative and personal gameplay, in which the player solves environmental and situational challenges in whatever ways they choose, not necessarily in ways anticipated by the game designer. (For a game like _Alien: Isolation_, survival via creative thinking and personal choices would be both compelling and _empowering_---it's not _I beat the game_, it's _I figured out how to survive, using my own wits and creativity._)

What is needed is a _compositional language_ for describing possible game interactions. Some people would point to physics engines as providing some of this, and indeed they do! With a physics engine, the consequences of particular interactions with the game world are _computed dynamically_. This is obviously sensible. But a physics engine is too low-level to describe all the high level interactions that are of interest for gameplay.

We need something else, but since we can't describe all possible interactions explicitly, the goal of this compositional language must be to let us dynamically compute the consequences of putting together various entities in the world, like a physics engine does for certain types of physical interactions. Here is where type systems and programming languages provide a partial answer. We just need to consider how to apply them in their full power to the problem of organizing game interactivity.

To see how type systems solve and programming languages solve a similar problem, consider the rather mundane question: what is the consequence of applying the `squareRoot` function to the string `"hello"`? This probably seems trivial, but I find it rather profound that no programming language has this situation as a special case. Instead the typechecker provides a compositional language and a fully generic program for determining whether any action (be it `squareRoot` or anything else) is _applicable_ to an entity. Since `squareRoot` takes a number, and `"hello"` is not a number, this action is not allowed by the typechecker. If on the other hand our function application does typecheck, say `squareRoot 2.0`, then the _computational rules_ of the language provide a way of dynamically computing the consequences of applying our `squareRoot` action. No explicit programming for each of these cases is needed; the typechecker and general rules of computation provide us with all the information we need to determine whether actions are applicable, and if so, what the consequences are of applying these actions. So while we have a combinatorial number of possible interactions, we have a _constant amount of programming_ in the language and typechecker implementation to decide which interactions are supported and what the consequences are.

That's a pretty powerful idea. Let's think about how this concept could apply to the problem of game interactivity. I'll sketch out some ideas, but perhaps this post might spark others to start thinking along similar lines:

In this approach, entities in the world, be they players, coffee mugs, or doors, are no longer just geometry, or geometry that interacts with the physics engine. Instead, each entity is a value of some type, `s`, equipped with a _view_ which converts `s` to some sort of object that can be rendered by the graphics engine. And we also have associated with the entity a number of functions that take `s` as a parameter. These will be used for interacting with the entity. This probably sounds a bit abstract, so let's look at our coffee mug. I'll use Haskell syntax, but that's not essential:

```Haskell
module CoffeeMug where

  data CoffeeMug a = CoffeeMug { liquid :: Maybe a }
  data Empty -- no values of this type

  -- empty the mug, and get its contents and a (now-empty) mug
  pour :: CoffeeMug a -> (CoffeeMug Empty, Maybe a)

  -- fill a mug, must be with a value of type liquid
  fill :: Liquid a => CoffeeMug Empty -> a -> CoffeeMug a

  -- smash the mug (against a hard surface), obtaining shards,
  -- the contents, and a short burst of energy (the `Watts`)
  smash :: Hard surface => surface -> CoffeeMug a -> (Shards, Watts, Maybe a)
```

So our coffee mug can be filled with liquid, emptied, or smashed. Each of these produce values _which have types_. We rely on the the type system to handle in a _fully generic way_ how these entities can interact.

Continuing on, we can add gasoline and faucets to our world.

```Haskell
module Gasoline where
  data Gasoline = Gasoline
  instance Liquid Gasoline where ...

  ignite :: Watts -> Gasoline -> Either Gasoline Explosion
```

So gasoline can be ignited given sufficent input energy. And a faucet can be used to transfer water to some other entity:

```Haskell
module Water where
  data Water = Water
  instance Liquid Gasoline where ...

module Faucet where
  import Water

  data Faucet = Faucet
  data Water = Water

  fill :: Faucet -> (Water -> r) -> (Faucet, r)
  fill f container = (f, container Water)
```

Notice that the expression `Faucet.fill faucet (CoffeeMug.fill mug)` typechecks, and returns a filled `CoffeeMug Water`!

You can see where this is going. What is a gas station? A gas station provides a similar interface as a faucet, except it dispenses `Gasoline`. And perhaps I have to supply currency of some sort as an argument to the `GasStation.fill` function? I can now go to a gas station and fill up my coffee mug with gasoline. Of course, with a nice programming language, any similarities in the interactivity of two entities in the world can be factored out into generic functions used by both!

With a constant amount of work defining primitive interactions, we get an extremely rich and interactive world. In fact, modeling interactivity in this way is probably much _less_ work than all the time and talent that goes into modeling the _visual appearance_ of entities in the world. If we are going to have an artist spend time making a realistic-looking wrench to sit statically on a shelf, why not have a programmer spend a few minutes thinking about some interactions to expose for that wrench? Of course, there are lots of modeling choices to make, but the point is we have a cohesive framework in which to make such choices.

Here are some issues I haven't addressed, but which might be fun to think about:

* Game interactions are _spacial_ and _temporal_, it isn't _just_ the logical and computational bits that are relevant. For example, we need to be _near_ a faucet and we need to have taken the coffee cup out of our inventory in order to fill it up with water. And if we want to smash the coffee cup, we need to be near to a hard surface, and the `Watts` value it produces doesn't hang around indefinitely. How to handle these concerns?
* Arguments to these interaction functions may need to be emitted from things like the physics engine, or even applied automatically by the physics engine. For instance, how do I model my use case from earlier, of _throwing_ a mug against a distant wall?
* How do we render the interactions themselves? It's all well and good to have an ignite function on `Gasoline`, but how do we show this? And how do we define these animations in a modular way?
* How do we represent the player's inventory, and constrain it appropriately?
* How do we efficiently _look up_ what actions are available to us at each location in the game?

But I view these as all [fun problems to solve](/2014-08-27/naysaying.html) rather than insurmountable difficulties. I have ideas about how to address concerns like this which perhaps I'll discuss in a later post.

In closing, I want lay out a vision for the experience of creating and then _playing_ richly interactive games organized using a framework like I what I've described:

* Every entity in the game world is interactive. There is no static geometry, but the amount of programming work needed to facilitate this is _constant_ for each entity. Adding interactivity to entities is done alongside the development of their geometry, visual appearance, and physics, and all entities get added to a vast library that can be used by level designers.
* Because all entities are interactive, there are a combinatorial number of possible interactions supported. A level designer no longer can predict, up front, what interactions are possible. Level design is a process of _discovery_, where the environment of the level is refined based on the emergent properties of the interactions supported by entities in the environment. As in reality, there may be hundreds or thousands of ways of accomplishing something in the game, and consequences all _follow logically_.
* When playing a game, there are situational and environmental challenges, _not arbitrary puzzles_ which can be solved in one way. The player uses their own creativity and wits to deal with challenges and feels empowered to do so. People leave a gameplay experience feeling enriched and having learned something about themselves and the world.

Up until now, games have focused a huge amount of resources on visual appearance and on low-level gameplay mechanics. I consider these areas very well explored at this point. We now have the tools to develop games that are much more richly interactive, and it's been sitting under our noses for a long time. I'm excited to see what games can become in the coming years.
