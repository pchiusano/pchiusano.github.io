---
layout: post
categories: [fp, programming]
title: The advantages of static typing, simply stated
---

This post summarizes the advantages (and drawbacks) to static typing, as simply as possible. None of these arguments are new or my own, but I wanted to consolidate them in one place.

First, the advantages:

* _A large class of errors are caught, earlier in the development process, closer to the location where they are introduced._ __Example:__ you have a map whose keys are strings and whose values you expect (elsewhere in your program) to be functions from `Int -> Int`, and you accidentally insert a function that returns a _list_ of integers. Oops! With static typing, this error would be caught by the typechecker and pointed out to the programmer at the location the erroneous value is inserted. In a dynamic language, the error could go unnoticed until much later, when that value gets pulled out of the map and the function applied. As another example of this sort of thing, many languages have a concept of `null` (the ['billion dollar mistake'](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare)). Even static languages like Java allow `null` to be used in place of any type, which leads to a situations where unexpected `null` values pop up in your program very far from the place where they were erroneously introduced.

* _The types guide development and can even write code for you---the programmer ends up with less to specify._ __Example:__ Haskell has a feature called typeclasses, which derives aspects of your program, purely based on the types, which are often inferred. The resulting code can be noticeably shorter than dynamic languages which cannot disambiguate programmer intent via types. More advanced dependently typed languages routinely derive large amounts of boring code with guidance from the programmer and the types specified. [Conor McBride](http://strictlypositive.org/) likes to emphasize that types are not merely about preventing errors---they are about declaring more of your intent to the machine so the machine can do more work on your behalf.

* _One can refactor with greater confidence_, since a large class of errors introduced during refactoring will end up as type errors. __Example:__ you have a function that currently returns a single number, and wish to modify it to return a list of numbers. In a static language, updating the declared type signature and fixing any compile errors will catch most, if not all places that need updating.

* _Static types can ease the mental burden of writing programs_, by automatically tracking information the programmer would otherwise have to track mentally in some fashion. __Example:__ you are writing a tricky function, and have two values in scope, `f` and `x`. The `f` you pulled out of a dictionary, the `x` was the result of calling `foo(23)`. Is it safe to call `f(x)`? Types provide an automated, precise answer to this question.

* _Types serve as documentation for yourself and other programmers and provide a 'gradient' that tells you what terms make sense to write._ For someone trained, the types give a sense of what is expressible using any API. Like puzzle pieces with shapes that we can observe fit together, we can think of types as specifying a grammar for programs that 'make sense'. In dynamic languages (what Bob Harber somewhat trollishly refers to as ['singly-typed' languages](https://existentialtype.wordpress.com/2011/03/19/dynamic-languages-are-static-languages/)), information about what programs make sense needs to be communicated in other ways. Example: many libraries have documentation with examples. Great. But if one of the examples has the line `read(message, peer)`, it's often difficult to ascertain whether a related expression, like `read(fileChunkStream(file1), peer)`, is also valid. Types provide immediate answers to these questions, especially for someone trained at reading typeful APIs. 

I haven't mentioned performance, but I'd say it's easier to efficiently execute programs that are statically typed. Dynamic languages executed naively will have much more dynamic dispatch and runtime checks happening. And while it's possible to execute dynamic languages efficiently, it's more complicated. You often need some sort of JIT to get really good performance, whereas static languages can do very well with ahead-of-time compilation. Note that I don't think performance alone is a great reason to prefer static to dynamic---you can get very good performance from dynamic languages with a good JIT (see [LuaJIT](http://luajit.org/)), and that development cost only needs to be paid once. In contrast, the above bullet points affect everyone using the language, pretty much all the time.

Now for some drawbacks to static typing:

* Like any formalism, types require some investment up front to become fluent in.
* Type errors are frequently poor. Thus, even though errors are caught sooner, the way they are reported to the user can be [frustratingly opaque](/2015-03-26/type-errors.html), which isn't good for programmer motivation. In the world of static typing enthusiasts, there's also a segment of people who will state or imply that anyone who gets frustrated by [the user experience of fixing type errors](/2015-03-26/type-errors.html) is dumb, not a "real programmer", etc. There are also [complexity apologists](/2016-02-25/tech-adoption.html) who will defend the "type error user experience". 
* It can be difficult to assign static types to some programs, and learning how best to carve a program up into types is a skill that takes years to master. When programming in a static language, you always have a choice about what information you encode in the types, and how you encode things. For instance, you can create and use a nonempty list type and have the compiler check it statically or you can let nonemptiness be a dynamic property that you have to check for at runtime. You can have lazy, potentially infinite strings be a distinct type from strict, finite strings, or you can lump both concepts into the same type. And so on. In a static language, you do always have the option of building a less typeful API, where less is enforced by the types, but it is often tempting to spend more time encoding things statically (and then proving things to the typechecker) than would be saved by avoidance of potential future bugs. With experience, you develop a good sense for what is worth tracking statically and what to keep dynamic, but newcomers to static languages can make bad tradeoffs here, which in turn contributes to needless complexity in the language's library ecosystem. Haskell has this problem IMO, even though on the whole, I find Haskell has some very high quality libraries.
* To put this last point another way, with static types, you have to make _more choices_, and you may have to make these choices at a point in time where you're unsure how to decide, or when the choice feels distracting. 

I think there is room for reasonable people to differ about the _degree_ to which these advantages and drawbacks matter, and it probably can depend on the person.
It does seem, though, that once you learn typeful programming in a language with a very good type system, it's very unlikely you'll go back to dynamic languages.
Why? With more expertise, the drawbacks are minimized, and you better leverage the advantages. On the other hand, some people never get over the hump. Are there things we can do about that?
More on that in a minute.

### A personal story

I learned programming with C++, in college. Later I learned Java. I didn't have much of an opinion of static vs dynamic typing until I was introduced to Python, which felt like the breath of fresh air I didn't know I needed. Until you are introduced to an alternative, it can be difficult to see the things that are problematic with the status quo. After learning Python, all the _syntax_ devoted to types in C++ and Java seemed superfluous. DHH had some quip about lines like `Employe employee = new Employee("Bob", "Smith");` in Java that made me go "yeah, totally!!!" I was done with static types for a while.

Fast forward a couple years. I'd learned some other languages: Lua, Mathematica, a bit of Scheme, some Prolog (all dynamically typed, notice!). If you'd asked me back then, I'd have said static languages were crap. Java was a punchline. There was a bit of exposure to functional programming via those languages, which led to me hearing about Haskell. 

Haskell, and more generally, typeful languages with good type inference really changed the cost/benefit ratio of static typing.
Most of the dynamic language programs I'd written could be given Haskell types, with zero type annotations if I wanted! This largely eliminates any syntactic advantage to dynamic typing.
Of course, over time, you learn that it's often _helpful_ to declare your types to the compiler, so it can better tell you how you've screwed up.

These days, while I do still think there room for improvement in the 'typeful programming' user experience (which is part of why I'm working on [Unison](http://unisonweb.org)), at this point, I'd never go back to dynamic typing. I'm much more excited to improve the user experience for programming in a statically-typed language, so we get all the great benefits, and less of the drawbacks!

One final observation: when I first started doing statically-typed FP, in Haskell and Scala, it took sometimes great effort to figure out how to express programs in a way that would satisfy the typechecker. Type errors were often bewildering to me. And the act of carving up a program into types took effort. But it felt like the good kind of effort, and I kept at it. 

Now, these things are almost like breathing. I know what is easy to express in the types. I know lots of ways of encoding things and which ones are likely to work nicely for different scenarios. I don't struggle with it. Picking types isn't separate from the act of design. Instead, the design process is intermingled with picking types, and the types help guide the design.

[Rúnar Bjarnason](http://blog.higher-order.com/) made a remark about functional programming that applies pretty well to static vs dynamic typing: he compared functional programming (programming without side effects) to driving on roads. Yes, it can be a constraint. You can't just drive through a river and over a mountain in a straight line from point A to point B. You have to stay on the roads, learn the traffic laws, and so on. But you still get to your destination, and you probably get there faster.

_If you liked this, also check out:_

* [Design for experts; accomodate beginners](/2016-02-25/tech-adoption.html) talks about tech adoption, and how better tech can adapt and thrive in the real world
* [What is usability?](http://pchiusano.github.io/2015-04-23/unison-update7.html#usability) discusses usability vs expressiveness
* [Good teaching makes long-term investments in the learner](/2014-10-23/learning.html) talks about learning and teaching as an investment in future productivity
* [The Unison project](http://unisonweb.org/about): a statically-typed language with a richly-interactive semantic editor
