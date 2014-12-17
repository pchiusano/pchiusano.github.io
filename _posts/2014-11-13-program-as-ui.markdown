---
layout: post
title: Viewing a program as a UI&#58; an early demo
categories: [fp, unison]
---

In [an earlier post](/2014-09-30/semantic-layout), I showed a demo of semantic program layout in Unison. Here, I'd like to demonstrate the ability of Unison program rendering to *embed* graphical views. This will seem like just a neat trick at first, but like the "finger pointing at the moon", it's leading to a deeper shift in how we think about programming. I hope to convey some of the gravity of this perspective.

We typically think of programs as being represented by their textual form. If we want to produce a UI, we write a program *whose output* is some sort of renderable graphical object---perhaps a pile of HTML/CSS/JS. Let's consider this a form of compilation from our program to our graphical object, `G`. Like most forms of compilation, changes to the compiled output can't be trivially reflected back into our source program. As a result, we tend to think of edits and interactivity on `G` as _being distinct from the activity of editing our program_. Put another way, we think of interaction with a UI as being a fundamentally different activity than programming.

That is how we typically think of things, but there's another perspective---the UI *is* a program. We don't write a program to produce a UI, we write a program that *is* a UI. That is, the UI is simply a specific kind of rendering of a program, and interacting with the UI is, quite literally, programming. I don't mean that it's analogous to programming, I mean that it *is* programming, in the sense that the user's interaction with the UI is directly mapped to edits of an underlying program.

This probably doesn't make much sense, so I'm hoping a few demonstrations will clarify. Please excuse the ugliness of these UIs (which is "easy" to fix) and try to see through to the underlying ideas.

__Aside:__ This perspective, of the program as UI, is something Conal Elliott also talks about in [his work on Tangible Functional Programming](http://www.youtube.com/watch?v=faJ8N0giqzw).

First, let's look at [this page](/unison/demo-swatch-raw.html). This is a semantic layout of a Unison expression. As before, try shrinking the width of the page down until the expression no longer fits horizontally---the expression will automatically rewrap. Parentheses are inserted automatically where needed, and you can also manipulate the selection with the mouse or the arrow keys. Notice the text below, which shows a representation of the current selected _path_. That is a path into a Unison expression. The exact representation of paths isn't important; what is important is that these paths give us all we need to be able to make edits to our expression.

Something that's rather unfortunate about this view is that we have a portion of our program which describes a color, `Color.rgba 230 126 34 1`, but we can't actually see what that color is! We could write a separate program to render this color for us, but it would be nice to just _change how the expression of type `Color` is rendered_. Take a look at [this page](/unison/demo-swatch.html), which renders the color as a little square swatch of the specified color. Notice that the path information is still tracked as before.

So far, this is only a little interesting. For any _closed_ expression (one without any free variables), it's easy to imagine how we can select an alternate rendering that interprets the expression in some graphical way. Being able to embed these graphical views in our program rendering while retaining editability is snazzy, and sometimes helpful, but it still feels like regular programming.

Going a step further, we can control not just how closed expressions are rendered, but also how _function application_ is rendered. Have a look at [this page](/unison/demo-42.html). What are we looking at? Well, it's a normal (albeit ugly) web page. It's also a rendering of the expression `answer 42`, where answer is perhaps defined as:

```Haskell
answer 42 = True
answer _ = False
```

Rather than rendering `answer 42` in its _source_ form, we have annotated our syntax tree to indicate that we'd like the function `answer` to render itself differently. Specifically, we've supplied a function `Panel -> Panel`, which tells where the argument to the function `answer` should be placed, and what its surrounding visual context should be in the layout. Notice again that we still have the path information needed to support editing.

Here's an excerpt of the API used:

```Haskell
cell : View a -> a -> a -- special form which controls how the value is rendered, ignored at runtime
panel : View a -> a -> Panel -- function which renders a term as a "Panel", which is a first-class value
text : Style -> View String
vertical : View [Panel] -- view a list of panels, vertically stacked, as in `panel vertical [...]`
source : View a -- the "source" view of a value
function1 : (Panel -> Panel) -> View (a -> b) -- override how function application is rendered
h1 : Style
...
```

And here's roughly how this API is used to produce [the page linked above](/unison/demo-42.html).

```Haskell
answer : Int -> Bool
answer 42 = True
answer _ = False

visualAnswer : Int -> Bool
visualAnswer =
  let msg = "The Answer to The Ultimate Question of Life, the Universe, and Everything..."
  in cell
    (function1 (arg -> vertical [ panel (text h1) msg, arg])) 
    answer

visualAnswer 42 -- renders as /unison/demo-42.html  
```

The `cell` special form, together with `function1`, give us a way of annotating the syntax tree to control how function application is rendered. This means, for instance, that the user can be presented with a normal looking web form, which, in fact, as it is filled in by the user (with type-directed editing support), is filling in values of a Unison expression. The user of such a form is quite literally programming, they just don't need to know it! What they do notice is that the page provides extremely helpful autocomplete whenever they encounter a field that needs filling in.

There's much more work to do, but the basic ideas here will enable creation of extremely rich, interactive renderings of programs. In this way of working, there is no longer a clear distinction between "programming" and "interacting with a UI". Interacting with a Unison program rendering (which I call a _panel_) edits an underlying Unison program, and we retain all the good aspects of textual program representations---the UI is not just an endpoint, it's a basis for further composition, just like the usual textual representation of programs. But unlike raw text, we are free to embed whatever graphical views are useful or helpful for programming.

### Remarks ###

When any new technology is invented, old ways of thinking get carried forward. My father (a retired photographer) always brings up the development of motion picture. After its invention, some of the first use of motion picture was in recording regular live theatre. The idea that that one could make cuts and splice together a sequence of different shots to tell a story was something that came later. People needed to discover that film was its own distinct medium of expression with its own powerful language.

So it is with computation. Since the discovery of computation and software, over and over again, people have used software to produce the virtual equivalents of pre-software technology. We don't need to keep doing that. Using software to produce a UI separate from our program means our applications are [fixed appliances](/2013-05-22/future-of-software.html) that support only the finite set of views and interactions its creators have explicitly allowed. Software is a new medium, we don't need to use it to reproduce old media. If instead we reimagine the UI as simply a way of rendering and interacting with an underlying program, our software artifacts can be flexible, composable, _and_ easy to use.
