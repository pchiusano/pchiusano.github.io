---
layout: post
categories: [fp]
title: An interesting variation on a strict by default language
---

In [this old but relatively unknown paper by Tim Sheard](http://web.cecs.pdx.edu/~sheard/papers/ExplicitLazy.ps), he describes a pure language which is strict by default but optionally lazy. Sounds boring, but there's a twist: strictness is not tracked in the types, and there are a few annotations which make it possible to write code which is polymorphic in its strictness. This post is an exploration to see to what extent this addresses the problems with strict by default evaluation ([nicely covered by Lennart Augustsson](http://augustss.blogspot.com.br/2011/05/more-points-for-lazy-evaluation-in.html)). Summary: it helps a bit in some cases, but laziness still wins.

Here's the entirety of Sheard's API:

~~~ Haskell
-- suspend a computation - a language primitive, not first class
lazy :: a -> a -- conceptual type
lazy = <magic>

-- unwraps any laziness, first class
strict :: a -> a
strict (lazy a) = strict a -- conceptual implementation
strict a = a

-- first class function, does application, preserving "thunkiness" of argument
mimic :: (a -> b) -> a -> b
mimic f (lazy a) = lazy (f a) -- conceptual implementation
mimic f a = f a

-- example
ones = 1 : (lazy ones)
~~~

Here, `ones` is an infinite list. Thunks in the list will be forced as usual. `mimic` is the most interesting function--if the argument to the function is `lazy`, the result is `lazy`, otherwise, we do normal strict function application. Notice that `lazy` just returns an `a`, and `mimic` likewise accepts a regular `a`. Whether a value is thunked or not is not tracked in the types.

At first glance, this seems like it could be a promising direction. One of the problems with strict by default is that if thunks have a different type than non-thunks, people don't typically bother to make their code polymorphic in strictness (even if that is possible in the language) so we see functions unnecessarily specialized to strict arguments when they could be usefully nonstrict. And strictness can't be _undone_--if `fromMaybe :: a -> Maybe a -> a` is strict in its first argument (whoops!), we can't use it and have to resort to explicit pattern matching in situations where computing the first argument would be expensive.

Sheard's proposal addresses this to some extent. `fromMaybe` can be written as usual:

~~~ Haskell
fromMaybe :: a -> Maybe a -> Maybe a
fromMaybe a Nothing = a
fromMaybe _ (Just a) = a
~~~

And now the onus is on _callers_ to decide whether to pay for thunking the first argument:

~~~
-- blah :: Maybe Int
fromMaybe 1 blah -- don't bother thunking `1`, not worth it
fromMaybe (lazy (reallyExpensiveFn x y z)) blah -- this time, we do care
~~~
 
Importantly, `fromMaybe` does not need rewriting for callers to be able to make this decision after the fact. Not tracking strictness in the type essentially lets `fromMaybe` be polymorphic in its strictness, with the decision left up to the caller. This is an improvement over the usual situation in strict by default languages.

Let's look at another example, `foldr`:

~~~ Haskell
foldr :: (a -> b -> b) -> b -> [a] -> b
foldr f z [] = z
foldr f z (h:t) = f h (lazy (foldr f z t))
~~~

This isn't quite as nice a situation as `fromMaybe`. As library writer, we need to remember to supply the `lazy` annotation for the second argument of `f`, just in case `f` benefits from it. 

***
__Aside:__ It would be interesting if we could somehow inspect _at runtime_ whether the _function_ is strict or lazy, and do a "thunkiness preserving" function application: 

~~~ Haskell
foldr :: (a -> b -> b) -> b -> [a] -> b
foldr f z [] = z
foldr f z (h:t) = f h `mimicf` foldr f z t

-- not first class
mimicf : (a -> b) -> a -> b
mimicf = <magic>
~~~

Although it seems like we would want to make most function calls use `mimicf`!

***

One advantage to our definition of `foldr` is we don't need to _propagate_ the change out to functions like `mconcat` that might use `foldr`:

~~~ Haskell
mconcat :: Monoid a => [a] -> a
mconcat = foldr mappend mempty 
~~~

This is an improvement in the sense that the signature of `mappend` does not need to anticipate whether monoids will be strict or lazy. Anyone can supply a `Monoid` after the fact with a nonstrict `mappend`, and usefully use that monoid in `mconcat`. If strict and lazy values had different types, we necessarily need to correctly anticipate when laziness might be useful at _all_ places in the call graph. If even one function is overly strict, we can't reuse it!

***
__Aside:__ On the other hand, looking at `mconcat`, I think it is a little unfortunate that the strictness is not tracked at all in the types. The `foldr`-based implementation is only really likely to be appropriate for monoids which are lazy in their second parameter to `mappend`. For strict monoids, we probably want a version of `mconcat` based on `foldl'`.

***

Let's now look at another example where things break down, `liftA2` specialized to `Maybe`:

~~~ Haskell
lift2 :: (a -> b -> c) -> Maybe a -> Maybe b -> Maybe c
lift2 f Nothing _ = Nothing
lift2 f _ Nothing = Nothing
lift2 f (Just a) (Just b) = Just (f `mimic` a `mimic` b) -- don't force `a` and `b` unnecessarily!
~~~

Note that the order of the pattern matching means we are possibly non-strict in the second `Maybe`. So we can call `lift2 a (lazy reallyExpensive)` and avoid some useless work if `a` is `Nothing`. The use of `mimic` ensures we don't force `a` and `b` if they aren't already forced.

So far this looks promising. But let's think about it some more. What if `f` "wants to be" nonstrict in its second argument? For instance, suppose `f` is the `Cons` constructor in the type of infinite streams:

~~~ Haskell
data Stream a = Cons a (lazy (Stream a)) -- hypothetical syntax for declaring a new lazy data constructor
~~~

The problem is the code which produces the second `Maybe` value to pass to `lift2` will not _necessarily_ know that the value inside any `Just` can be usefully thunked. For example:

~~~ Haskell
blah :: Maybe (Stream Int)
blah = if x then Nothing else Just (p [1,2,3])

foo = lift2 Cons Nothing blah
~~~

Notice that `blah` has no way of knowing that it will be used by `lift2` with an argument that could be nonstrict in its second parameter. It has no information by which to make a decision about whether `p [1,2,3]` should be strict or lazy. It has to make an arbitrary decision.

Laziness by default puts callees in charge of deciding whether arguments should be strict or lazy, rather than forcing _callers_ to make a decision about this without sufficient information.

### Are we done?

It seems like we're done--laziness once again comes out on top. But is there anything we can do to improve Sheard's proposal? What if we could _abstract_ over the decision of whether to produce a value strictly or lazily? Here's one rather kludgy way:

~~~ Haskell
data Strict = Strict | Lazy

blah :: Strict -> Maybe (Stream Int)
blah s = if x then Nothing else Just (pr s)
  where pr Lazy   = lazy (p [1,2,3])
        pr Strict = p [1,2,3]

foo = lift2 Cons x (blah Lazy) -- `Cons` benefits from laziness, so we produce lazily
foo' = lift2 (\_ a -> head a) h (blah Strict) -- `head a` does not benefit, so we produce strictly
~~~

We are just deferring the decision to the caller of `blah`. This is good, but now the writer of `blah` is in charge of abstracting `blah` in such a way that it can be used by different types of callers. This almost certainly won't happen as often as would be useful, and we get unnecessary strictness and loss of modularity as the result. Note that pervasive laziness gives us what we want for free.

Any replacement for pervasive laziness needs a full story for:

* How callees will defer to their (multiple callers) the decision of whether to produce a result (or any subexpressions of their result) strictly or lazily. 
* How function application within a HOF can _respect the desired laziness_ of the function being called, without necessarily having to anticipate that it may receive a function which "wants to be" nonstrict.

This sort of polymorphism in strictness needs to be something that happens by default, without the programmer having to anticipate the need or write special code with this in mind. Lacking this, we've lost something substantial in terms of modularity by using a strict by default language.
