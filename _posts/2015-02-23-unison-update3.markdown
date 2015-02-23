---
layout: post
categories: [fp, unison]
title: Unison update 3&colon; connecting the editor to the node (continued)
---

I didn't get a chance to put together a post on Friday, but I made some decent progress. Here's a short recording of an editing session:

<video id="explorer-video" width="640" height="360" preload controls>
	<source src="unison/unison-explorer.mov" />
	<source src="unison/unison-explorer.ogg" />
	<source src="unison/unison-explorer.m4v" />
</video>

A few notes about what's happening here:

* This is the same editor we've seen before, but now, a location in the tree can be "opened" for editing. This brings up what I'm calling the "explorer".
* When a location is opened, we get some important information, fetched asynchronously from the Unison backend (you might notice the small blip when the explorer comes up, but before it gets all this information):
  * The _admissible_ type, which at the top of the box in bold in the video. More on this below.
  * The _current_ type, which is simply the type of the node selected. The little grey square is a symbol which represents the current location (we don't repeat the rendering of it in the explorer since it could be a huge tree), so the annotation `□ : Number` indicates that the current location has the type `Number`.
  * The types of any local variables in scope at the edit location.
  * Any well-typed _completions_, which as the video shows can be filtered down by typing in the searchbox. If there are no valid completions that match what the user has typed, then the explorer is in  the list of completions is empty and the explorer outline turns orange.

Conceptually, the explorer is just providing a very structured way of selecting a replacement value for the current edit location. But it is restricted to choosing values which ensure the resulting expression is still well-typed. This is where the _admissible_ type comes into play. The admissible type must be the _subtype_ of any chosen replacement, so for instance `forall a . a -> a` is a subtype of `Int -> Int` (any function expecting an `Int -> Int` may safely be given a `forall a . a -> a`). This is not 'subtyping' in the OO sense, it's the subtyping relationship induced by use of quantifiers.

The current type and the admissible type can coincide, though it will always be the case that the admissible type is a subtype of the current type. But, for instance, when replacing the body of the lambda `x -> 42`, since nothing else constrains what the lambda must return, the admissible type is `forall a . a`, essentially unconstrained. If we had a type annotation that was being pushed down into our expression, or if our lambda were named and used elsewhere in a way that constrained its output type, that information would be reflected in the admissible type.

