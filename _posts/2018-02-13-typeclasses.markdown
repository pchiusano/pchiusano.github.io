---
layout: post
categories: [fp, haskell]
title: The trouble with typeclasses
---

Haskell "enforces" typeclass coherence by asking that you define all the instances for a type in the same module (really, file) where you define that type. Whenever you import anything from that module, you bring all these instances into scope. It "works", but it's a kludge - when defining the type `Foo a`, you can't anticipate (nor are you necessarily aware of) all the instances you might wish to give for `Foo`. We want an "open universe" where people can _after the fact_ discover common structure and layer it onto existing data types. I feel this is an important property for a language ecosystem.

You can relax things a bit and give "orphan instances" for a type in other modules, but in so doing, you lose typeclass coherence guarantees, and it's now possible for different parts of your code to use different `Foo` instances, leading to subtle bugs. _Aside:_ I'd say that most working Haskell programmers make their peace with orphan instances in some cases while feeling a bit dirty.

A workaround that people suggest is to create a `newtype` around `Foo` in a separate module, and then give additional instances for this `newtype`, call it `WrappedFoo`. This starts to fall apart anytime the type `Foo` is being embedded in another type. A good example is the class `ToJSON a`, which is used by Aeson to encode values of type `a` to JSON. If we have a type `Bar [Qux (Int, Foo Blah)] String`, and we want to make `Bar` an instance of `ToJSON`, we can't easily use the `ToJSON a => ToJSON (WrappedFoo a)` instance. The creator of `Qux` should not need to be aware of JSON at all.

I also dislike this sort of workaround because it's one of those non-obvious language-specific hacks that I'd be embarrassed to explain to a student. It's magic (in a bad way) and it doesn't even work in general.

First-class representation of dictionaries have their own problems:

* Typeclass coherence issues. I don't really agree that typeclasses solve this problem in general.
* It's tedious to plumb around value-level dictionaries. Scala implicits "solve" this problem in an ad hoc way.
* Related: subtyping of typeclasses (or lack thereof) is another source of plumbing. The lens library would not be usable without subtyping of typeclasses.

It seems like we can do better, but I'm not sure how.
