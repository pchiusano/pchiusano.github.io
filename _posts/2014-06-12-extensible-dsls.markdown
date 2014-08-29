---
layout: post
title: A very simple technique for making DSLs extensible
categories: [fp]
---

The heavyweight techniques for 'solving' the Expression Problem (Data types a la Carte ([PDF](http://www.cs.ru.nl/~W.Swierstra/Publications/DataTypesALaCarte.pdf)) and some of the syntactically lighter weight extensible effect variants that [popped up recently](http://www.cs.indiana.edu/~sabry/papers/exteff.pdf)) are sometimes way overkill. Using the usual tools of abstraction, we can often make our DSLs extensible without any fuss.

For illustration of this very simple technique (I hesitate to even call it that), I'll use an untyped lambda calculus with well-formedness of scoping enforced:

~~~ Haskell
data Slot a = Bound | Free a
type Prim = Int

data Expr a
  = Unit Prim
  | Ap (Expr a) (Expr a)
  | Lam (Expr (Slot a))
  | Var a

instance Monad Expr where ... -- left as an exercise
~~~

It would be nice if our language supported, say, addition and multiplication. We could certainly add these as constructors to `Expr`:

~~~ Haskell
data Expr a
  = Unit Prim
  | Plus (Expr a) (Expr a)
  | Times (Expr a) (Expr a)
  | Ap (Expr a) (Expr a)
  | Lam (Expr (Slot a))
  | Var a
~~~

But we can see that's not going to scale. We don't want to have to add a new constructor for every single primitive we wish to add to our language. Moreover, it would be nice if our interpreter of this DSL could abstract over the set of primitives, and care only about the language's overall lexical structure given by `Ap`, `Lam`, and `Var`. One is tempted to introduce a more general `Ext` constructor which lets us import any Haskell function, say:

~~~ Haskell
data Expr a
  = Unit Prim
  | Ext (Expr a -> Expr a)
  | Ap (Expr a) (Expr a)
  | Lam (Expr (Slot a))
  | Var a
~~~

But this HOAS encoding loses us our `Monad Expr` (which gives us capture-avoiding subsitution) and the type becomes rather difficult to work with. We could solve this with some more restricted `Ext` constructor that only works on primitives:

~~~ Haskell
data Expr a
  = Unit Prim
  | Ext (Prim -> Expr a)
  | Ap (Expr a) (Expr a)
  | Lam (Expr (Slot a))
  | Var a

plus = Ext (\x -> Ext (\y -> Unit (x+y)))

-- Possible once again, now that `a` is strictly in positive position
instance Monad Expr where ... 
~~~

But this isn't ideal either, as we are being forced to commit to some set of effects for `Ext` when defining our data type, rather than deferring this decision to the interpreter. Let's look at some examples of such interpreters. They might have signatures like:

~~~ Haskell
-- We take a closed expression
eval :: (forall a . Expr a) -> R
eval2 :: (forall a . Expr a) -> R2
~~~

for various choices of `R`, `R2`, or whatever other type we wish to interpret to.

The problem becomes more apparent when we consider the possibility of effectful primitives. Here it becomes clear that we _don't_ want `Expr` to be the one choosing what effects are present in `eval`. But if we write

~~~ Haskell
data Expr f a
  = Unit Prim
  | Ext (Prim -> f (Expr f a)) -- note the `f` here
  | Ap (Expr f a) (Expr f a)
  | Lam (Expr f (Slot a))
  | Var a
~~~

... then we now have the creator of `Expr` values being forced to choose what effects are allowed in `Ext`, rather than deferring that decision. Back when we had a `Plus` constructor for our primitives, such an interpreter was free to interpret those subterms into the effect of _its_ choosing.

Another thing lost is that our `Expr` data type is no longer a fully defunctionalized syntax tree---it contains Haskell functions which means we've lost the ability to do things like serialize `Expr` values and send them over a network, compile our DSL to run on the GPU, and so on.

At this point, people usually start talking about 'The Expression Problem' and considering some pretty ugly, heavyweight solutions. Do we really need these pyrotechnics? Let's take a step back. Our original goal here was a lambda calculus with an extensible set of primitives, and an interpreter which could be defined without caring about the set of primitives chosen. All we have to do is simply have `Expr` abstract over the type of these primitives:

~~~ Haskell
data Expr e a
  = Unit Prim
  | Ext e -- GENIUS!!!
  | Ap (Expr a) (Expr a)
  | Lam (Expr (Slot a))
  | Var a

instance Monad (Expr e) where ...
~~~

We don't want `Expr` to have to commit to a particular set of primitives, nor a particular set of effects when interpreting these primitives, so we simply abstract over the choice, using the `Ext e` constructor. An evaluator now becomes

~~~ Haskell
eval :: (e -> R) -> (forall a . Expr e a) -> R
~~~

for some particular `R`. We've just deferred the question of how to interpret these `Ext` constructors to the caller of this `eval`. Code which builds up `Expr` can use normal abstraction to be polymorphic in the choice of `Ext` primitives, and it's `eval` and its caller who get to decide how these `Ext e` constructors get interpreted. The type `e` could be some initial encoding like `data Ext = Plus | Times` or some final encoding closer to the effect type used in `eval`, or anything in between. We're done.

If we wish to share code in the different evaluators, we just use the normal tools of abstraction to factor out common structure. The `Expr` algebra is simple enough, for instance, that we could write a more polymorphic evaluator, perhaps something like:

~~~ Haskell
eval :: Monad f => (e -> f r) -> (a -> f r) -> (r -> r -> f r) -> Expr e a -> f r
~~~

The `r -> r -> f r` tells the interpreter how to interpret function application in the `r` domain, an we can go ahead and allow non-closed `Expr` values if we like.

#### <a id="remarks"/> Final remarks ####

There is a place for fancy techniques, but ye good ol' abstraction and parametric polymorphism are often all that's needed. These basic tools, available to us all, provide the ability to arbitrarily shuffle around _where_ various decisions are made.

I'd like to pontificate a bit about the [Expression Problem](http://en.wikipedia.org/wiki/Expression_problem), which Wadler described:

> The Expression Problem is a new name for an old problem. The goal is to define a datatype by cases, where one can add new cases to the datatype and new functions over the datatype, without recompiling existing code, and while retaining static type safety (e.g., no casts).

A bit of history is in order. The Expression Problem was first described during the heyday of OO, in 1998. Functional programming was still a young discipline then (recall that Applicative functors and other important abstractions and tools of FP were discovered in the past 10 or even 5 years!), and in my opinion, it wasn't properly understood that FP indeed subsumed all the techniques considered to be OO. OO and FP were instead seen as being in conflict--FP provided closed ADTs which let us add new functions, but not new cases, while OO let us add new cases but not new functions. This characterization has persisted even today.

Rather than talking about OO vs FP, I prefer to talk about initial vs final encodings. Initial encodings (using a closed ADT) give us the utmost flexibility in the interpreter, while in some sense fixing the constructors, while final encodings (what people called OO) let us add arbitrary constructors, while in some sense fixing the interpreter. But both initial and final encodings and everything in between are possible in FP! See for instance [Oleg's writeup in Haskell](http://okmij.org/ftp/tagless-final/). The little trick I gave in this post can be viewed as sitting somewhere between a pure initial encoding and a pure final encoding.

Going just a bit further, let's examine the above characterization a bit more. Are ADTs truly closed in any meaningful sense? They are closed in the sense that one cannot add cases to an ADT without recompiling the code, and updating all the code which pattern matches on that ADT. But why is that necessary? If we wish to 'add' cases to a data type, `E`, there is _often no need to update the `E` data type in place_. We can simply write a function from `E -> E2`, where `E2` can contain whatever additional cases or functionality we want. All the functions which were defined over `E` still work, without recompilation. If we notice common patterns between code that works with `E` and code that works with `E2`, we can abstract over these similarities using the normal tools of FP.

Once again, some history is order. During the heyday of OO, when the Expression Problem seemed like a bigger deal, abstraction in FP was not as well-understood and some of the basic tools of abstraction that functional programmers take for granted were yet to be discovered. Thus, preserving the exact functions monomorphic on the original type `E` was seen as being Rather Important--data types were generally thought of as having bespoke APIs, and defining a new data type would require duplicating large swaths of this bespoke API. The more modern view is that there's very little new under the sun--data types generally fit into a handful of abstraction bins that functional programmers have discovered (or rediscovered from even earlier work in mathematics), and most of the surface area of a data type's API we now get for free by simply implementing the appropriate abstractions for our data types. Moreover, I think people tend to write more abstract functional code these days, remaining ignorant of concrete details until it is convenient to specify them (often at the outer layers of the program). These factors have conspired to make the Expression Problem increasingly a non-problem for working functional programmers. I cannot think of the last time I've truly been bitten by the Expression Problem in a way that didn't have a trivial 'solution' like what I gave above.

That being said, a language with convenient support for anonymous union types with the natural subtyping relationship might make for an interesting type system. But practically speaking, this isn't holding FP back.
