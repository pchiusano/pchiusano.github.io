---
layout: post
categories: [fp, unison]
title: Unison update 1&colon; support for asynchronous server requests and a basic editing layer
---

I spent this week working on a very basic editing layer for the Unison editor. Previously, I'd implemented logic for [how to display a Unison panel](/2014-11-13/program-as-ui.html), including embedded graphical views, but had no way of actually editing these panels. Time to do something about that!

[DEMO][] (explanation further down)

__Aside:__ A Unison _panel_ is what I am calling the editable rendering of a Unison term. It may contain both "source" views and embedded graphics, and also _reactivity_, where parts of the panel are updated instantly as other parts of the panel are edited (I'll talk more about this in a later post).

My goal here was to get the basics of this editing story worked out, and also do a bit of tinkering to get a sense of how fluid the semantic editing UX could be. I mentioned [last week][] that I'd like to preserve the same _feeling_ of directness and control I have when using a raw text editor like Vim, even if the particulars are going to be quite different. And though I wasn't expecting to get all the way there in one week, I did want to get to a point where I felt comfortable with moving on to other things.

### The problems with 'What You Type Is What You Get' (WYTIWYG)

In a regular text editor or even the fanciest IDE, the editing cursor is positioned directly in the document, and whatever keys you press (sans modifier keys) get interpreted and inserted directly into the document. For instance, if I want to write the expression:

    foo (grr "Alice" 42) (prob qux razzle)

... I just place the insertion cursor wherever I want in the document and start typing away. I call this the 'What You Type Is What You Get' model. This model of editing is so ingrained in most people's minds that it might not be apparent that _it is a choice_. In the physical world, if we're writing on a piece of paper with a pen, well then yes, our "edits" (handwriting) must be directly applied to the document. In the software world, it is a _choice_ whether to mimic this experience for an editor.

And there are [a number of problems with using the WYTIWYG editing model for programming][punchcard]:

* WYTIWYG _overspecifies_. The user is put in charge of things like inserting parentheses and typing out full identifier names, when _in context_ they should only have to disambiguate among _valid possibilities_. Even in an IDE, in the WYTIWYG model, the user must distinguish between finishing typing an identifier (which might refer to a nonexistent symbol) and _accepting the current completion_.
* Programs are highly structured. Raw text is completely unstructured. Thus, we have an impedence mismatch---our way of specifying programs contains a lot of junk. Most raw text is not well-formed (does not even parse) and certainly is not well-typed (it makes no sense and is not executable).
* This mismatch generates some very hairy problems for compiler authors. When the user enters a program that is junk, we need to provide the user with feedback in the form of _parse errors_ and _type errors_. Generating good error messages is difficult, tedious, a significant amount of work, and worst of all _uninteresting_. Of course, it can be improved, but is that really the [right problem to solve?][jurassic] If the existing error messages of most compilers are any indication, it's a problem people aren't particularly interested in solving well, either, even if it is possible.
* The user experience for editing raw text programs is awful. Tiny changes can generate screenfuls of errors, which must be deciphered by the user. Type errors even in nice compilers like GHC are [like little riddles][Lennart]---"X is different than Y. Now where did the typechecker come up with X and Y? Have fun tracking that down!". I've been doing FP for a while now, in Haskell, Scala, and now Elm. About 95% of type errors I understand and fix in under 30 seconds. About 4% take me a minute or two. And every 1%, I stare at it for way too long and start losing flow. I think I understand the problem, and make a change. Another screenful of errors. Flow is now officially broken, and demoralization starts setting in. The other day I actually gave up after staring at an error for close to 15 minutes and [_asked on the Elm mailing list if anyone could explain the error I was seeing_][compile-error]! If we want to teach children and newcomers about programming, can we really expect them to deal with arcane nonsense like this? Of course not!

Our industry is full of apologists for the status quo who wear their ability to deal with this nonsense as some kind of badge of honor or proof of their intelligence. "You n00b, you just have to learn to read type errors better, obviously!" Well, there is essential complexity, and there is accidental complexity caused by making silly assumptions. We are better off applying our intelligence and creativity to dealing with problems that are _essentially challenging_, not problems that just happen to be so.

### Progress on fluid semantic editing

Let's go back to our example:

    foo (grr "Alice" 42) (prob qux razzle)

In a semantic editor, here's a possible keysequence which reproduces this:

    f (grr "Alice" 42) (p q r

Notice that it looks the same, but we need not spell out all our identifiers, and we don't bother closing the final paren. If `foo` is the only valid completion, then `<space>` can accept this completion, add another argument, and begin editing that argument. Unlike raw text editing, where typing `f` may refer to some as yet undeclared identifier (which would not typecheck!), in a semantic editor the user simply cannot reference a nonexistent identifier, so there is no need to force the user to disambiguate whether they meant to do that or not.

In this model, parentheses are more like navigational cues---they tell the editor where to apply edits but aren't part of the document. They are displayed only if actually necessary, based on precedence information stored as separate metadata.

The other model I've experimented with is simply getting rid of parentheses to control editing entirely. Instead, one uses the arrow keys or `<hjkl>` to navigate, and there are two actions to accept and continue, one indicated by `<space>` and another indicated by `<shift+space>`. Say `<space>` accepts the current completion and advances to the next _sibling_ of the current node, and `<shift+space>` accepts the current completion and adds a new _child_ to the current node and moves the editing cursor to _that_.

That might not make much sense, so see [this demo][DEMO]. Here are some notes:

* The layout is much like we've seen before---layout is computed dynamically based on available width, parens are inserted automatically. Try shrinking the browser window until the expression no longer fits.
* There's a selection model which can be manipulated using the mouse and or arrow keys. Try hovering over any node and then pressing the up arrow.
* There's also now an _editing layer_. It's not hooked up to the typechecker (that comes next week), but it gives the flavor of the interaction. Click on an entry in the list of names `["Alice", "Bob"...]` (or hit 'enter' while a name is selected) and start typing in the box. The box turns red if there's no matching completions. Hitting space accepts the current completion if the box is non-red _and advances to the next item in the list_. Hitting shift+space accepts the completion, adds a new argument, and moves to editing that argument. No mousing around needed, and with a bit more thought we can add support for insertion (via `,`) and infix operators, all in a fluid fashion.

Another bit I found interesting about this model is that the question of whether Unison is case-sensitive ceases to have real meaning. When editing a node of the panel, the editor can do the search and matching however it chooses, and the resulting selection can be rendered however you choose. In this example, I chose to do case-insensitive search in [the demo][DEMO]. Put another way, case-sensitivity becomes a property of the _search functionality_ rather than the language itself. Different programmers might reasonable configure or toggle between different search modes, even in the middle of editing their programs!

I haven't gone too far down the path of exploring how to make the semantic editing experience feel completely fluid, and there are a lot of little details, but I've gone far enough that I feel relatively confident that it's possible and am ready to move onto other things for now. I'll circle back to this a bit later on.

### Detour to allow asynchronous loops in Elm

I spent Monday and Tuesday on a minor yak shaving detour to allow definition of cycles in Elm's [FRP library](http://package.elm-lang.org/packages/elm-lang/core/1.1.0/Signal). [Unison's architecture][last week] means that there is a lot of back and forth between the editor and the Unison node. Many interactions with the UI have the possibility of generating a web request to fetch information from the node. The result of such requests can affect how subsequent UI interactions are interpreted (for instance, the list of valid completions shown when a node is selected for editing affects how mouse positions and clicks are interpreted) Of course, it is sensible to try to arrange things such that the relevant information is prefetched, but the mere possibility of cycles needs to be accounted for.

Unfortunately, [this use case cannot currently be expressed in Elm](https://groups.google.com/forum/#!searchin/elm-discuss/http/elm-discuss/hMQTNHVoMeE/klNvRcY_oRMJ) without major contortions that I wasn't willing to adopt. There is a new feature planned for Elm 0.15 that looked like it would address my use case, but I didn't want to block until that was ready, and I also didn't want to bank on it being exactly what I needed. I also didn't like the idea of being at the mercy of someone else's timeline. These three months are extremely valuable to me, and I want to make the most of them! After taking a brief look at what would be involved in writing the feature I needed, it seemed straightforward, so I decided to just go for it and told myself I'd bail and find some other path in 2-3 days. It went... surprisingly well and I packaged up the result as [a new library](https://github.com/pchiusano/elm-execute).

With that in hand, I implemented the following generic function:

```Elm
asyncUpdate : (Signal req -> Signal (model -> model))
           -> Signal (model -> (Maybe req, model))
           -> req
           -> model
           -> Signal model
asyncUpdate eval actions req0 model0 = ...
```

It feels a bit ad hoc, but it wraps up what I suspect is a pretty common pattern of interpreting a signal of actions which will be used to update some model, some of which may "on the side" generate requests which must be run asynchronously while other actions come in. The first argument, `eval`, is the signal transformer which (may) issue asynchronous requests and use the results to update the model. But the function has no opinion on whether these are actually web requests, or something faked with a time delay and some pure logic locally. The current demo is running using the following function as the `eval` argument:

```Elm
search : Sink Field.Content -> Signal Request -> Signal (Model -> Model)
search searchbox reqs =
  let containsNocase sub overall = String.contains (String.toLower sub) (String.toLower overall)
      possible = ["Alice", "Alicia", "Bob", "Burt", "Carol", "Carolina", "Dave", "Don", "Eve"]
      matches query = List.filter (containsNocase query) possible
      go _ model = -- our logic is pure, ignore the request
        let possible = matches (Explorer.getInputOr Field.noContent model.explorer).string
        in updateExplorerValues searchbox (List.map Terms.str possible) model
  in Time.delay (200 * Time.millisecond) (Signal.map go reqs)
```

Not very exciting, but I can easily swap in something that actually contacts the real Unison node over HTTP. I just have to swap out this one function!

### Next week

This post is getting pretty long, so I'll call it quits here. Next week I'll be hooking this editing layer up to the real Unison node and typechecker. Stay tuned! As always I welcome your comments.

[DEMO]: /unison/editor0.html
[jurassic]: http://www.imdb.com/title/tt0107290/quotes?item=qt1464414
[last week]: /2015-01-30/unison-update0.html
[punchcard]: /2014-09-30/punchcard-era.html
[Lennart]: https://www.youtube.com/watch?v=rdVqQUOvxSU
[compile-error]: https://groups.google.com/forum/#!searchin/elm-discuss/staring$20at$20this$20type$20error/elm-discuss/3qAG7SLOFK0/9Hssqqk9vZoJ

