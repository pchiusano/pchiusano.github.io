---
layout: post
title: Solving the wrong problems (and why zippers are not all that useful)
categories: [fp]
---

[Functional zippers](http://www.haskell.org/haskellwiki/Zipper) are a cute idea. We get a purely functional "pointer" into a data type, and functions for making edits at this pointer and _relative_ movements. When we're done, we can "commit" our changes and get back our edited data type. It's all rather miraculous that it can be done at all (and as an added bonus, we have some deep connections--the type of zippers for a data type arises by taking the _derivative_ of that type). Every so often, some unsuspecting newcomer to FP gets pointed to zippers (no pun intended) as a possible solution to a problem they are facing.

At this point, I like to trot out one of my favorite quotes, Ian Malcolm from Jurassic Park:

> John Hammond: I don't think you're giving us our due credit. Our scientists have done things which nobody's ever done before... 

> Dr. Ian Malcolm: Yeah, yeah, but your scientists were so preoccupied with whether or not they could that they didn't stop to think if they should.

There are a lot of things that _can_ be done. But just because something _can_ be done doesn't mean it _should_ be done. This all sounds obvious, but our industry is full of people wasting time solving hard problems created artificially, which could be solved trivially or sidestepped entirely just by revisiting earlier assumptions.

Getting back to zippers, perhaps it is of theoretical interest that certain problems can be solved with zippers, but zippers are rarely (I won't say never) the best solution to a problem. The reason: it simply isn't all that useful to have an imperative API for navigating around a data type and making edits to it. Yes, it can be done functionally, but we are essentially embedding a little imperative DSL into our purely functional language. Imperative APIs aren't fun, and they don't become fun just because we've embedded them in a functiona language. It's the same reason why working with `IO` directly is something we try to avoid.

One of the examples that people sometimes advocate for zippers is parsing and/or serialization of XML/JSON. That is, a JSON parser is something like a `Cursor -> Either Err a`, with the `Cursor` giving the parser the ability to move around in the current subtree being parsed (although, nothing stops it from moving up the subtree, gak!). Sounds reasonable, except that we can build a much simpler, likely more performant library from the type `Json -> Either Err a`. We don't need a pseudo-imperative API for navigating around the tree, nor do we need access to our parent. The various "movements" can instead be wrapped up with a few combinators.

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

And so on. See [this gist](https://gist.github.com/pchiusano/1d7e498063dc1f9f4e24) for a more complete worked example in Scala.

On the serialization side, it's again much nicer to just work with `a -> Json`, rather than `a -> Cursor -> Cursor` or some such. (Actually, for serialization, if you don't care about pretty-printing it is easier/faster to just go directly from `a` to the raw text, there's no need to instantiate the intermediate JSON syntax tree. This is what's done in [Pru](https://github.com/pchiusano/pru), a stupidly simple XML/JSON generating library.)

Another use case that people tend to suggest for zippers is graphical or command line UIs for editing, say, a file system tree. These use cases aren't very compelling either. It is artificial (and bad UX) to restrict an editing UI to a single, modal focus--it's almost always better to just allow edits to be more random access. For instance, I should be able to click directly on any node of a tree being edited, not be forced to navigate there via a series of relative movements. (Note: I am not saying that UIs should not support relative movements of the current selection, just that random access is a requirement, and random access is a superset of zipper-like access). For building editable GUIs, we generally want some notion of a _path_, a function for looking up the value at a path, editing a value at that path, and a way of resolving screen positions to paths, say. Zippers are just not solving the right problem here.

In summary, don't use zippers when something simpler will do. Which in my experience is most of the time. 

All that said, if someone reading this knows of a killer use case for zippers which cannot be solved more simply in another way, I'd very much like to hear about it!
