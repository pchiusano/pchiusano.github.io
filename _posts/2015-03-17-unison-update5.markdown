---
layout: post
categories: [fp, unison]
title: Unison update 5&#58; a better spreadsheet
---

Here's a video of the latest progress. In this video, I create the term `view reactive (1 + 1)`, which displays in the editor as `2`. This might not seem like much, but the ability to define reactive views like this is the first step in allowing Unison panels to be used much like a spreadsheet, where the user fills in values and other parts of the panel are updated in response. There's also a few other things shown which I'll talk through below:

<iframe src="/unison/unison-update5-movie.html" width="450" height="500" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

### What's going on?

I've added a number of builtin functions to the editor. The video makes use of the following functions:

```Haskell
data View a -- builtin data type

view : View a -> a -> a
source : View a
reactive : View a
-- of course, there can be many other View values
```

The `view` function evaluates to its second argument at runtime, but its presence can be used to annotate the syntax tree to override how subtrees are rendered. The first argument is of type `View a` (capital 'V'), and the second argument is of type `a`. You'll notice in the video that after the `1 + 1` has been filled in for the second argument, the admissible type for the first argument is now `View Number`.

The editor interprets `view source blah` as the usual source rendering of `blah`. It interprets `view reactive blah` in the same way, but first evaluates `blah` to normal form. Also shown in the video---we can toggle between the 'raw' and interpreted view by typing 'v' when a node is highlighted.

### A better spreadsheet, and a better application framework

Now I'll make a controversial claim, which is that the ability to define 'reactive' values like this puts us on our way to making the Unison editor a richer, more powerful replacement for spreadsheets. We're missing a few things, namely a declaration layer, which lets us define and edit more than just a single expression, and also a richer layout library. But with these things in place, this reactivity gives us many of the tools we need to reproduce modern 'applications' with a Unison panel. Obviously, at this early stage, the Excel and Google sheets developers shouldn't be too worried, but with time, the ability of Unison to replicate functionality of these applications will grow quite rapidly.

Programmers like to scoff at spreadsheets, but they're extremely popular among nonprogrammers. Why is that? Here are just a few reasons:

* Spreadsheets have a largely nonsymbolic representation of programs. The only symbolic representations is the simple expression language, which people can easily learn as consists only of constants and function application. Of course, every spreadsheet program has some ad hoc language for defining new formula, but that's outside the usual spreadsheet model and gets used very rarely.
* We can think of the spreadsheet as a limited kind of application-building toolkit. It supports a form of interactivity, in that we may select a location for editing, and updates occur in response to these edits (conceptually no different than any other application, be it Photoshop or Twitter). There's no separate step of 'running' the program and we're encouraged to view the UI as just a way of viewing a program, rather than a separate artifact _produced by_ a program. See [viewing a program as a UI](/2014-11-13/program-as-ui.html).
* The user can easily construct functions that manipulate sequences in an inductive fashion, considering only concrete inputs. (For instance, the cell A2 takes the value `= A1 * A3 + B$7`, and the user drags to extend this definition in order to build a whole sequence. Of course, a programmer has no trouble generalizing this sort of thing and just writing a step function that uses symbolic inputs, but it requires less learning to do things the spreadsheet way.
* Lastly, and this might seem dumb, but a 2D grid as a template is actually surprisingly nice starting point for a lot of computing tasks.

In the finance industry, where I used to work, spreadsheets get used for _everything_ and often grow horribly complex. The problem is that while spreadsheets are approachable and require very little learning curve, they're also quite crippled:

* They are entirely first-order. There is basically no support for abstraction (though all spreadsheet programs have some ad hoc language for defining new formula), and certainly no ability to define higher-order functions. Without the ability to define new abstractions, it's impossible to manage complexity. The way people reuse spreadsheets is largely by copying, pasting, and modifying. Naturally, spreadsheet apps have some grown hoc ways of linking between spreadsheets, but all this is a very very poor substitute for a real programming language.
* The forms of interactivity spreadsheets do support is too limited. Why can't I render a numeric cell as a slider that I can move back and forth to see things update instantly, for instance? And even when some form of interactivity is supported, it's usually in some ad hoc, unguessable way, rather than something unified and obvious.
* The 2D grid which seems like a helpful starting point actually becomes quite annoying for more complex spreadsheets. I'm just declaring a collection of variables, why the heck do I have to worry about where they are positioned? Why the heck am I refering to values by _position_ rather than by some meaningful name?? Also, the use of a grid often leads to lots of futzing around to avoid unwanted interaction between row heights and and column widths.

With the Unison editor model, we can cover all the advantages of spreadsheets while providing a much more powerful programming model, and a much richer layout library. I'll talk more about where this is going in a later post.

### Technical note: avoiding the need for impredicative instantiation when searching for functions

As I mentioned in [update 3](/2015-02-23-unison-update3), the type shown in bold when the explorer is opened is the _admissible type_, which must be a subtype of whatever value the user fills in the explorer. So of course `Number` is a subtype of `Number`, and `forall a . a -> [a]` is a subtype of `forall a . a -> [a]`, but also `forall a . a` is a subtype of `Number`. When the explorer pops up, and in response to the user typing, we need to find all terms in the store which are a supertype of the admissible type, and it's actually important that type-based search be _perfectly accurate_. Unlike Hoogle, we can't get away with returning some ill-typed results that match some processed version of the admissible type (this would allow creation of ill-typed expressions), and we also can't get away with an overly strict matcher that elides some results that do match (this could result in the user getting stuck, unable to fill in a value that should typecheck).

The problem is that naively doing a subtyping check is going to be overly strict. The admissible type `forall a . a` (shown in the editor as just `a`, implicitly quantified) is only a subtype of `forall a . a -> a` (the type of the identity function) if we allow impedicative instantiation of the type variable `a`. Yikes! Rather than open that can of worms, I chose to sidestep the problem. Conceptually, the admissible type is quite useful to display to the user as it lets them know what sort of terms are valid replacements for the node they've selected. So the explorer can work in two phases---first, we use a processed version of it as a search query, with the constraint that the search algorithm must _at least_ return any terms that could be supertypes of the admissible type, even at some impredicative instantiation. This is very easy.

Once we have this conservative upper bound on the set of possible matches, we then try replacing each result in the expression being edited, ensuring that the result is well-typed. The replacement in context doesn't require any impredicativity, it's just normal typechecking. Anything passing this second level of checks is a valid replacement, and can be shown to the user as a completion in the explorer. So long as the first check phase cuts down the search space to a reasonable number of possible matches, these checks can be done very quickly.
