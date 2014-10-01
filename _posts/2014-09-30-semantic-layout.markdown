---
layout: post
categories: [fp, unison]
title: A very early demo of semantic program layout
---

Unison programs are viewed and edited in the browser. The frontend (which does things like code layout) is being written in [Elm](http://elm-lang.org) which talks to a backend (which does things like typechecking) written in [Haskell](http://haskell.org). 

I posted <a href="/unison/demo.html" target="_blank">a quick demo</a> of some of the basic technology used for rendering Unison programs. Okay, so it's rather ugly, but try to see past that for a moment. Try resizing the browser window until the expression no longer fits on one line and navigating around using the mouse and/or arrow keys. Unfortunately, there's no editing capabilities just yet (soon!). 

The "raw" source might have looked something like this: 

~~~ Haskell
foo [0,1,2..20] (baz "hello world!") -- omitted full list 
~~~

Although in Unison, there is no "raw source" in textual form, there is only the syntax tree! Moreover, the syntax tree is "nameless"--terms like `foo` and `baz` are uniquely identified by a _nameless hash_ of their contents, and we store metadata (containing human-readable names, among other things) separately. But more on that in a later post.

A couple other things to point out:

* Layout of the code is computed dynamically. As the available width shrinks, the layout engine decides whether to "break" expressions onto multiple lines. This breaking is semantic--we don't just break up expressions wherever they happen to hit the right edge.
* Parenthesization is inserted automatically, only when necessary, using metadata about operator precedence and operator fixity.
* Navigation and selection is also semantic--it's only possible to select a region corresponding to a valid expression. Likewise for expanding, contracting, and moving the selection.
* And in the near future, just about any aspect of this presentation will be customizable. If you want to view that list differently (perhaps using the traditional `[1,2,3..]` syntax), you can do so, and save this view as a first class value in the Unison system. 

What I like about this general approach is how it frees the programmer from having to make decisions about stuff that for the most part doesn't matter (though if it does matter, the programmer still has the _option_ of exercising more control). No more debates about tabs vs spaces or whether to use [K&R indent style](http://en.wikipedia.org/wiki/Indent_style). Unison stores presentation information like this separately, where it belongs--in the layout algorithm selected to display the expression. This is preferable to baking it into the stored form of programs and forcing everyone collaborating on a project to reach some consensus on the One True Indentation Style™.
