---
layout: post
title: A nice, purely functional alternative to sinks and wormholes in UI programming
categories: [fp, unison]
---

There's a common antipattern used in UI frameworks that are otherwise quite nice---the use of sinks or "wormholes" for interactive UI elements rather than explicit control flow. Sinks and wormholes are close cousins of explicit callbacks, and are bad for many of the same reasons, even when they don't break referential transparency. I'll explain the problematic pattern and outline a simple, purely functional solution with nicer properties.

As a running example, let's imagine building a UI for selecting an element from a list of names. So, we have a list of names like `["Alice", "Bob", "Carol", "Dave", "Eve"]`, and we want to render it to the user in some pretty way. Besides the static view, we'd like it to be possible for users to move the selection using the arrow keys or mouse. And we'd like for the selected value to be available to us in some way, so that other parts of our UI can react to the current selection. How can we do this?

Many UI frameworks answer this question by having some notion of a sink or wormhole. When creating the UI element (the list view), we pass it some sort of sink to which changes in the selection will be sent. Here's an example from Elm:

```Elm
dropDown : (a -> Message) -> List (String, a) -> Element -- returns an opaque UI element

channel : a -> Channel a -- create a wormhole, side effect!
send : Channel a -> a -> Message -- produce a package to be shipped through a wormhole
subscribe : Channel a -> Signal a -- obtain the signal of values sent through the wormhole
```

In `dropDown`, the `a -> Message` is our sink. Each time the user changes the selected item, that function will be called to produce a `Message`, which is conceptually an `(a, Channel a)`. Producing such a value has no side effects much like producing `IO` actions in Haskell, but when the UI is actually running, Elm will handle actually sending the `a` to its destination. The value sent appears as if by magic on the other side of the channel for anyone who calls `subscribe`. This is what I call the wormhole antipattern. I prefer the term "wormhole" to "channel", as it's suggestive of the discontinuity in control flow that comes with using this technique to structure UI updates.

__Update:__ A reader on reddit [pointed me to some FRP literature in which 'wormhole' has a specific technical meaning](http://www.reddit.com/r/haskell/comments/2ozi90/paul_chiusano_a_nice_purely_functional/cms9d07). I wasn't aware of it when writing the post, which just uses the term 'wormhole' colloquially.

The pattern is bad for several reasons:

* Using wormholes/sinks means giving up on the straightforward control flow of the functional style. Instead, we have the same sort of "spooky action at a distance" seen in imperative programming. Values sent to a wormhole pop out in a completely different part of the program, without the programmer having to make dependencies explicit. This makes reasoning about code extremely difficult, for the same reason that programming with explicit callbacks or gotos is difficult.
* It's inflexible---we need to decide, when constructing our view, what sort of events we are going to expose (hence fixing the allowable interactivity). And even if we abstract over the wormholes used by the view, having to decide on their shape _at all_ means nailing down something we may want to leave until later.
* It gets in the way of code reuse. Anytime you are specifying a _destination_ for a result, rather than just returning a result and using regular pure functions to decide where and how that result gets propagated, you are making it difficult to reuse whatever logic which produced that result. It's similar to why actors, based around the type `A => Unit`, aren't composable. We have to decide what the actor does with its 'result' of processing each `A`, as opposed to a pure function `A => B` where the caller gets to decide what to do with the resulting `B`.
* Creating these wormholes is inherently imperative. Unlike, say, the value `41 + 1`, whose meaning is defined in terms of its constituent parts, creating a wormhole results in a new thing, with a new identity. Of course, we can banish wormhole creation to some sort of typed effect (Elm doesn't bother with this, which I also dislike), but this doesn't change the model.

To see how these problems manifest in practice, consider our running example. Suppose we decide, after we've created our nice view, that we want to allow the user to _edit_ individual names in the list, say by clicking on them. Or suppose we want to allow reordering of the list via dragging and dropping. It seems like we should be able to reuse all the logic for assembling our static view, since, after all, if the user _isn't_ interacting with the component, the static rendering will be the same as before. But when using the wormhole antipattern, without heroic efforts at abstraction our view construction will include a mess of attaching connections to these wormholes and assuming some particular way of interacting with the view. All of this code will need to be revised or rewritten, and code reuse is nowhere in sight.

Now that you're hopefully convinced this is a bad idea, what can we do about it?

The key observation, which is not new, is that a view should be solely responsible for rendering itself statically, while retaining enough information to resolve mouse or touch positions to meaningful values and doing the reverse lookup of resolving values to regions. This lets us add any interactivity we want _after the fact, without having to modify the view_. The view and controller are entirely decoupled, and we can take the same view and layer on different types of interactivity after the fact. How awesome is that?

Here's a sketch of an API ([full gist](https://gist.github.com/pchiusano/ba958c70ebaf535aa805)):

```
embed : a -> Element -> Layout a

-- create a list view, specifying a view and a value for each element
list : a -> List (Layout a) -> Layout a

-- resolve a layout to its rendered form
render : Layout a -> Element

-- for a given screen location and layout, find the list of
at : (Int,Int) -> Layout a -> List a

-- find all regions and values corresponding to the given key
region : (k -> k -> Bool) -> (a -> k) -> Layout a -> k -> [(a, Region)]
```

__Aside:__ Elm's type system cannot represent the type `Cofree f a`, but if you're familiar with that type, you'll notice that `Layout` is just a specialized version of `Cofree`, with an `f` base functor that specifies the primitive layout functions.

A `Layout a` is a tree of opaque UI components (`Element` values), but we retain information about the relative position of these components and how they map to meaningful values of type `a`. Thus, we can resolve mouse clicks to values and map values back to rectangular regions on screen for doing things like highlighting of selections and so forth. The `at` function takes time linear in the depth of the layout tree, which is usually logarithmic, and the `region` function is efficient so long as we have an ordering on `k` which lets us prune searching subtrees.

There are no magic wormholes anywhere, we just produce `Layout a` values and resolve things like mouseclicks and so forth using regular pure functions. Control flow remains completely explicit, with all the enormous benefits that come with that!

Although I am still experimenting with this approach, I'm finding it's quite nice in many cases and I'm using it heavily in the Unison editor. I strongly suspect there's another abstraction layer to be discovered in wrapping up a `Layout a` with some sort of state evolution function. I imagine having a list view with a 'current selection' that can be manipulated, all as a reusable widget, _without_ having to give up the explicit control flow of the functional style.
