---
title: Why are we still programming like it's the punchcard era?
layout: post
categories: [fp, unison]
---

Here's a scenario that's familiar to most programmers: after making a seemingly minor program change in your text editor / IDE of choice, the compiler spews back at you tens or even hundreds of baffling compile errors. Even if you've gotten used to this sort of thing, there's something a little demoralizing about it. I don't particularly enjoy sleuthing around to figure out the root cause when the compiler is giving me a trail of seemingly unrelated clues. Compilers are often pretty bad at reporting the root cause of the actual errors.

This basic workflow - make changes, submit them to the compiler, and interpret any errors that occur - has remained largely unchanged since the punchcard era. Here's another way of putting it: we start with completely unconstrained input in the form of _raw text_. We then submit a blob of this raw text to the compiler, which "parses" the text, and then typechecks it. Of course, most raw text strings are not valid programs -- they don't even parse, let alone typecheck! Thus, over the years, compilers and editors have become increasingly sophisticated at reporting back to the user where and how the user went wrong in this raw text, unconstrained input. Modern IDEs have tightened the loop, but the basic model is the same!

I am sure that error reporting will continue to improve along these lines, but here, I want to talk about a different model. Rather than starting with completely unconstrained input and having to decipher the inevitable parse and compile errors, let's instead _constrain the editing UI to only allow creation of programs that typecheck._ I'll call this the _semantic editor_ model.

For example, if I would like to call the function `squareRoot` (which accepts numbers), and I am filling in the argument, a semantic editor would somehow prevent me from providing the string `"hello!"`. This constraint raises some UX challenges:

* In a regular text editor, the cursor is placed at a location in the document, and text is inserted directly at the cursor. In a semantic editor, we never insert directly into the document. Instead, we select an editable node, and make a _suggestion_ for a replacement. How we do this is a UX question. The editor can provide autocomplete, of course, but only valid suggestions will be accepted. Now how to make this fluid and useable...?
* If the user enters something that is nonsense (like passing "hello!" to `squareRoot`), the editor should provide some feedback--perhaps the autocomplete box has the goal type `Number` at the top, and the string `"hello!"` is greyed out and unselectable, with its type (of `String` displayed).
* If the user can't just stick the cursor anywhere in the program and start typing, how do they navigate around the program and make well-formed and well-typed edits?
* Plain text program editing gives users a feeling of directness and control. It's easy to temporarily "break" the structure of the code and repair it later. In a semantic editor, we don't want the user to get into a state where they are stuck and unable to make progress, because the UI is too constrained!

With a little thought and imagination, all these fun problems are solvable. Moreover, these are the sort of [fun problems I _want_ to solve](/2014-08-27/naysaying), because a semantic editor is a much nicer user experience, and potentially a _vastly more powerful_ environment than plain text editing. Here are just a few other advantages:

* In some ways, writing a semantic editor is _simpler_. There is no parser to write! Nor are there concerns about accurately tracking locations everywhere to be able to report errors back to the user! In fact, concerns about error reporting are largely eliminated. The editor can trivially ensure well-formedness (it will not even be possible to have mismatched parentheses), and type errors are largely restricted to telling the user that the "goal type" at the current cursor is `X`, which differs from the type `Y` of the value they've entered.
* In a semantic editor, there's no need to restrict the presentation of code to plain text, if other ways of rendering the code are more appropriate.
* In a semantic editor, large scale edits can be described using a set of _refactoring combinators_, rather than using manual (and often tedious) text munging.
* A semantic editor can be usable by children and newcomers to programming. Compiler errors (and runtime errors, due to programs being ill-typed) are a usability catastrophe. An editor which only allows creation of well-typed programs is like having bumpers on the bowling lanes! (Of course, understanding types and having a good mental model of how programs are evaluated is always going to be helpful if you are creating programs of any complexity)

I'll say more about how the challenges of a semantic editor are being addressed in Unison in a later post.

_Update:_ See [here](/2014-09-30/semantic-layout) for a very early demo of semantic program layout, and some further discussion.
