---
layout: post
title: When the fancy technique that seems relevant is the wrong tool (a note of caution on zippers)
categories: [fp]
---

__Update:__ I changed the title of this post and toned it down considerably to be less trollish. There was also some confusion / disagreement about the meaning of the term "zipper", so I've tried to clarify that as well.

[Functional zippers](http://www.haskell.org/haskellwiki/Zipper) are an elegant idea. A zipper for a data type, `T`, consists of a new data type representing a "one-hole context" for `T` (the "context"), together with a value of type `T` (the "focus"). The pair can be thought of as a purely functional cursor into a data type. We get functions for making edits at this cursor (by simply replacing the focus), and _relative_ movements (by shifting the focus into the hole of the context, and moving the hole elsewhere), all using pure functions. The name "zipper" comes from the idea that the focus and context interlock much like the teeth on either side of a zipper being pulled apart or coming together. Amazingly, these one-hole contexts for a type `T` can be computed generically by taking the _derivative_ of `T`--a fact that was [worked out by Conor McBride](http://strictlypositive.org/diff.pdf).

Zippers probably popularized (or originated) the more general idea of a "functional cursor", in which we have some notion of a "focus" into a structure and some notion of a "context", with some algebra for moving the focus and making edits to the structure, and eventually commiting these changes, all without side effects. This is indeed a very powerful and useful general idea, but the particular instantiation of that idea which is zippers--a one-hole context for some data type, together with a focus, often isn't the best tool for the job. Every so often, some unsuspecting newcomer to FP gets pointed to zippers (no pun intended) as a possible solution to a problem they are facing, but many of the use cases people propose for zippers are better served by other techniques.

I'll look at two instances of this, each of illustrates a more general situation where zippers aren't really appropriate:

* The first, XML/JSON parsing and serialization, is a case where even the general idea of a functional cursor simply isn't necessary at all.
* The second, using zippers as the model for various editing UIs, is a use case where the general idea of zippers is relevant, but given the particulars we'd be better served by use of a _path_ type or something else more lens-like. That is, the one-hole context model is _too limited_ for these cases.

### XML/JSON parsing and serialization

One of the examples that people sometimes advocate for zippers is parsing and/or serialization of XML/JSON. That is, a JSON parser is something like a `Cursor -> Either Err a`, where the `Cursor` is a zipper giving the parser the ability to move around in the current subtree being parsed (although, nothing stops it from moving up the subtree, gak!). Sounds reasonable, except that we can build a much simpler, likely more performant library from the type `Json -> Either Err a`. We don't need a pseudo-imperative API for navigating around the tree, nor do we need access to our parent. The various "movements" can instead be wrapped up with a few combinators.

~~~ Haskell
data Json =
    N Double | S String | B Bool | Null
  | Array [Json]
  | Object (Map String Json)

type Parser a = Json -> Either Msg a

array :: Parser a -> Parser [a]
array p (Array xs) = traverse p xs
array _ _ = Left "not an array"

object :: Parser v -> Parser (Map String v)
object = ...
~~~

Notice in particular the implementation of `array`. If our model were `Cursor -> Either Err a`, we'd be moving the input zipper to the left (or is it the right? and why should we care about this distinction?) repeatedly and invoking `p` on each subfocus. Here, we just call `traverse`, which encapsulates this general movement pattern. Note that JSON parsing doesn't really require reifying the concept of "the current location" as a data type--using Haskell's regular functions and function calls works fine. To "move" to a different location in the AST we extract a subtree, and call our subparser, which is simpler.

See [this gist](https://gist.github.com/pchiusano/1d7e498063dc1f9f4e24) for a more complete worked example in Scala, with some further comments.

On the serialization side, it's again much nicer to just work with `a -> Json`, rather than `a -> Cursor -> Cursor` or some such. (Actually, for serialization, if you don't care about pretty-printing it is easier/faster to just go directly from `a` to the raw text, there's no need to instantiate the intermediate JSON syntax tree. This is what's done in [Pru](https://github.com/pchiusano/pru), a stupidly simple XML/JSON generating library.)

The general issue here is that neither parsing nor serialization really require the concept of a functional cursor _at all_. _Can_ it be done? Yes, of course. And there are even some nice libraries that happen to use zippers for these tasks. But _should_ it be done? Probably not.

### As a model for some editing UI

Another use case that people tend to suggest for zippers is graphical or command line UIs for editing, say, a file system tree.

The general issue with using zippers here is that the model provided by zippers of a one-hole context which the focus fits into is _too limited_ for the kinds of operations and interactions we need to support. We are generally better off defining some sort of _path_ or lens-like type, which can be used to resolve locations and make edits to a structure.

To make this a little more concrete, let's look at a hypothetical zipper API for a regular list:

~~~ Haskell
data Zipper a = Zipper [a] a [a]

at :: Zipper a -> a
left :: Zipper a -> Zipper a
right :: Zipper a -> Zipper a
replace :: a -> Zipper a -> Zipper a
~~~

If we ignore the representation of `Zipper` for a minute and just focus on its algebra, we can see that the algebra could also be implemented using a `data Loc a = Loc Int (Sequence a)` (as someone also pointed out in the comments on an earlier version of this post). This isn't a zipper--our 'focus' is just a "path" into the context, and our "context" is just our regular sequence type, not a one hole context that the focus fits into, as in zippers.

This sort of `(path, data type)` representation is almost always preferable if we plan on rendering the `Loc` to the user. (Some people might still call this a "zipper", and there's some disagreement about this in the comments, but I prefer to stick to the original, more narrow definition of Huet, McBride, the Haskell wiki, and other sources.) For one, we are probably rendering to the user the "flat" sequence, possibly with the current selected index highlighted in some way. For another, our UI front end will need some way of communicating to our model if the user clicks on a completely different index, and using regular indices into a (functional) sequence type works well for this.

More generally, it is somewhat artifical (and I'd argue bad UX design) to restrict an editing UI of any kind to a single, modal focus--even if this is the default mode, we need to also allow for edits to be more random access. For instance, I should be able to click directly on any node of a tree being edited, not be forced to navigate there via a series of relative movements. (Note: I am not saying that UIs should not support relative movements of the current selection, just that random access is typically a requirement, and random access is a superset of zipper-like access).

For building editable GUIs for some structure, we can build some notion of a _path_ into our structure, a function for looking up the value at a path, editing a value at that path, and a way of resolving screen positions to paths, say. Dealing with paths rather than one-hole contexts just works out nicer for these cases. Paths can come equipped with an algebra for making relative movements, so we don't lose any functionality that zippers provide, but they also give us a story for other things that prove difficult with zippers. In addition to allowing for random access, paths give us a story for concurrent edits, easily talking about multiple locations at once, and batch updates. For instance, if we have two concurrent edits to our structure, at different paths, we can simply apply both edits, or even use [OT](http://en.wikipedia.org/wiki/Operational_transformation) to ensure the edits can be applied in either order to produce the same result. Likewise, we can easily have multiple paths into the same structure, and implement operations like swapping subtrees and so forth quite simply. And if paths are represented as some sort of list of path elements, then for batch updates, we can merge common prefixes into a trie for the batch, and avoid repeated work traversing our structure.

### <a id="remarks"/> Remarks

One of my favorite quotes is Ian Malcolm from Jurassic Park:

> John Hammond: I don't think you're giving us our due credit. Our scientists have done things which nobody's ever done before...

> Dr. Ian Malcolm: Yeah, yeah, but your scientists were so preoccupied with whether or not they could that they didn't stop to think if they should.

There are a lot of things that _can_ be done. But just because something _can_ be done doesn't mean it _should_ be done. _Can_ you write a JSON library that uses zippers? Sure. Is it the best tool for that goal? Probably not. Our industry is full of people wasting time solving hard problems created artificially, which could be solved trivially or sidestepped entirely just by revisiting earlier assumptions.

Research and "just playing" are certainly important (and if no one did that, we wouldn't have all the great ideas and techniques we now have for functional programming!), but I am not a fan of callously pointing newcomers to fancy techniques just because they seem related. That is, I feel it is somewhat irresponsible when people respond to newbies with a "researcher" type answer (go check out `<fancy technique>` for this!!), without communicating clearly that this is what they are doing or even thinking much at all about whether `<fancy technique>` is really the best tool. I don't think this comes from a bad place--FP is a deep, beautiful subject, with a lot of great research that's been done and which is still ongoing. When people see some newcomer asking how to do something, this sometimes provides a good excuse to point them to some aspect of the field that seems relevant.

But as [one commenter remarked](http://www.reddit.com/r/haskell/comments/2deyp2/paul_chiusano_solving_the_wrong_problems_and_why/cjpb29z):

> I can't disagree with "the research should be done", but not every Haskell program should be a research project. I once saw someone give "some kind of 2D zipper" as the answer to "how do I represent a character that can move around a rectangular array of grid cells?" when it was clear that neither party involved had any idea what such a thing could look like. Well okay, if you want to figure out some way to adapt zippers to your particular problem and save a logarithmic factor, that sounds like a fun research project and maybe you can write a functional pearl about it. In the meantime, there's Map (Int,Int) a.

The response I'd like to see in these situations is something like: "a `Map (Int,Int)` a is probably the simplest thing and would definitely work. For fun, your own enrichment, etc, you might want to look at zippers, `<discussion and links to interesting theory, etc.>` And they may even prove useful in your situation, you'd have to play around and see!"

Perhaps it is of theoretical interest that certain problems can be solved with zippers, but zippers _per se_, in their specific instantiation, are rarely (I won't say never) the best solution to a problem. There is a time and a place for research and "just playing", regardless of whether what you are doing seems useful. There is also a time and a place for putting on your "engineer" hat, being very skeptical, and deciding on the right tool for the job.

With all that said, if someone reading this knows of a killer use case for zippers which cannot be solved more simply in another way, I'd very much like to hear about it!
