---
layout: post
categories: [fp, unison]
title: Unison update 0
---

This was my first week dedicated mostly to working on [Unison](/unison.html). Before getting to my update, I'll give a bit of background on the Unison architecture. There are two pieces:

* The Unison _node_, written in Haskell, which contains an implementation of the Unison language, its typechecker, and any primitive functions. The node exposes an HTTP API designed to facilitate a highly interactive semantic editor.
* The Unison _editor_, written in Elm, runs in the browser and talks to the node over HTTP. It's a semantic editor, and programs constructed in the editor are guaranteed to typecheck _by construction_.

This week (and likely this entire month, if not longer), I'm focused on building out the Unison editor. In terms of the actual coding work, I built out the explorer UI component which will be used to provide type-directed autocomplete when a cell is selected for editing. There are a few libraries out there for doing this sort of thing (like [Twitter typeahead](https://twitter.github.io/typeahead.js/)), but writing custom UI components is pretty easy in Elm and gives me exactly what I want, so that's what I did. Writing a component in a purely functional way forces you to be extremely explicit about what your state is and how it gets updated. For instance:

* If the explorer is open, the user can move the selection with the arrow keys, BUT if the user moves the mouse into the region of the explorer, then the selection resets to whatever is under the mouse.
* If the user presses and holds an arrow key, the event should repeat with some frequency after a period of time. Since we aren't in a text area, we don't just get to piggyback of the standard keyboard repeat rate and have to manage this repetition explicitly!
* If the list of valid selections *changes* (say, because the user typed some more characters in the searchbox), we want to _preserve the previous selection if it still exists_. So if we had selected `"Alice"` after typing `"Ali"`, then we deleted the `"i"`, `"Alice"` should still be selected even if `"Albert"` is now the first element of the list of possibilities.

In any case, I finished an initial version of a component that does all these things and am presently in the process of integrating that into the editor. I'd have loved to be able to show a demo, but that will have to wait until next week.

I've also been thinking through some details of the editor UX. Although I want the Unison editor to be usable by non-expert programmers, first and foremost I want it to be something _I_ would want to use. I currently do all my programming in Vim, which gives me a wonderful sense of directness and control and which is extremely efficient for program entry and editing assuming you know how to use it well. Although a semantic editor has very different constraints, I want to preserve the overall _feeling_ I get when coding in Vim and if possible _improve upon it_. 

I'd like to write more about this in a later post, but my current thinking is that a semantic editor with a well-designed UX can actually be _much more efficient_ than plain text entry. Simply put, the editor has much more information at its disposal, and can therefore disambiguate actions in ways that plain text editors can't. Plain text editors are forced to have much larger command sets, since it has no information (namely type information and information about the language being edited) that it could otherwise use to disambiguate the user intent. Thus, the Unison editor has a very small, simple set of core commands, which is easier for beginners, while retaining the power and expressiveness of raw text editing. Of course, there is some serious work to come up with a thoughtful set of interactions to make the semantic editing experience just as fluid!

I say all this, but another one of my longer-term goals with the Unison editor is for it to be usable to do serious programming on a tablet. I have this dream of sitting under a tree with an iPad, doing real programming on a warm summer day. Doesn't that sound nice? Again, because the editor has much more information, the user has less to specify, and we can avoid having to pop out the keyboard in many cases. This use case isn't a focus for me right now, but I'm keeping it in the back of my head.

That's my update for now, stay tuned until next week!
