---
layout: post
categories: [fp, unison]
title: Unison update 3&colon; connecting the editor to the node (continued)
---

I didn't get a chance to put together a post on Friday, but I made some decent progress. Here's a short recording of an editing session:

<iframe src="/unison/unison-explorer.html" width="530" height="446" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

A few notes about what's happening here:

* This is the same editor we've seen before, but now, a location in the tree can be "opened" for editing. This brings up what I'm calling the "explorer".
* When a location is opened, we get some important information, fetched asynchronously from the Unison backend (you might notice the small blip after the explorer comes up, but before it gets all this information):
  * The _admissible_ type, which at the top of the box in bold in the video. More on this below.
  * The _current_ type, which is simply the type of the node selected. The little grey square is a symbol which represents the current location (we don't repeat the rendering of it in the explorer since it could be a huge tree), so the annotation `□ : Number` indicates that the current location has the type `Number`.
  * The types of any local variables in scope at the edit location.
  * Any well-typed _completions_, which as the video shows can be filtered down by typing in the searchbox. If there are no valid completions that match what the user has typed, the explorer outline turns orange.
* I'm not currently pulling any names in; so the names of type variables aren't very meaningful.

Conceptually, the explorer is just providing a very structured way of selecting a replacement value for the current edit location. But it is restricted to choosing values which ensure the resulting expression is still well-typed. This is where the _admissible_ type comes into play. The admissible type must be the _subtype_ of any chosen replacement, so for instance `forall a . a -> a` is a subtype of `Int -> Int` (any function expecting an `Int -> Int` may safely be given a `forall a . a -> a`). This is not 'subtyping' in the OO sense, it's the subtyping relationship induced by use of quantifiers.

The current type and the admissible type can coincide, though it will always be the case that the admissible type is a subtype of the current type. But, for instance, when replacing the body of the lambda `x -> 42`, since nothing else constrains what the lambda must return, the admissible type is `forall a . a` (or unconstrained). If we had a type annotation that was being pushed down into our expression, or if our lambda were named and used elsewhere in a way that constrained its output type, that information would be reflected in the admissible type.

#### Some notes on global search and import boilerplate

[Last week](/2015-02-13/unison-update2.html) I gave a proposed interaction for how to search for terms at more global scope. I suggested that if the explorer is open, adding a `?` to the front the the search string will increase the search to include global scope. And we could imagine having some other syntax to control the scope of search, to, say, only terms defined in the current _panel_, or constrained to some _package_, where a package is just some metadata which points to a bag of terms.

While it might be nice to have the ability to define the scope more precisely, I actually think it's a better user experience to just make the default scope include _everything_, and simply _display_ any relevant metadata the user might need to make their selection (or further refine the search). The reason I prefer this is that it's less work for the user in the cases where the context is sufficiently discriminatory, which is often the case in typeful programming. That is, if the context is sufficiently type-constrained, we can just give the user all results, from global scope, and free the user from having to decide what rather arbitrary 'bin' to start their search in. And if there are too many results, we can order the results intelligently, indicate there are more results to be had, and put the user in charge of refining their search _now that it has proven necessary_. Of course, the user can still narrow their search to whatever scope up front, it's just this isn't the default.

That's all for now, more updates later this week.
