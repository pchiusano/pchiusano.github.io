---
layout: post
title: In search of a better abstract machine for nonstrict languages
categories: [fp]
---

For a while, I've been curious if it's possible to develop a better runtime for nonstrict languages. Despite the [advantages of pervasive nonstrictness](http://augustss.blogspot.com.br/2011/05/more-points-for-lazy-evaluation-in.html) in a language, a drawback is that nonstrict languages make it more difficult to reason about space usage. Much of this is people being unfamiliar with nonstrictness and surprised when it performs differently than a strict language, but some of it is the fact that the normal order evaluation doesn't provide an obvious _compositional_ means of assigning space usage to programs. Reasoning about space usage in a nonstrict language is nonlocal.

As an optimization, Haskell does _static_ strictness analysis. Strictness analsysis has always struck me as a rather kludgy, incomplete solution. Because it's a static analysis, it depends on what functions get inlined. Thus, depending on this unrelated axis of optimization (inlining), an expression may have completely different asymptotic space usage, at the whim of the inliner. A good example of this is `foldl`, which, if run with optimizations turned on, does the right thing for `foldl (+) 0 [1..1000000]`, running in constant space and reducing the thunk as it goes, and with optimizations turned off, builds up a huge, left-leaning tower thunks that takes linear space. Yikes! As far as I am concerned, inlining and any other optimization should never do anything other than improve constant factors. Anything affecting asymptotics should be under explicit programmer control. (There's also _optimistic evaluation_, which I also dislike, as it depends on _timing information_. Thus, a `foldl` might use constant space or linear space depending on how long the folding function takes to run. Gak!)

Enough complaining about Haskell, though, here's a simple idea for a nonstrict runtime that I tried playing with recently. Note that my goal is to preserve the same termination properties of normal order evaluation, while making reasoning about space usage simpler by propagating stricness information at runtime. I do not just want a strict language, as that would be throwing the baby out with the bathwater. Here's my sketch:

```Haskell
-- HOAS encoding, for simplicity
data Runtime
  = Prim !Int
  | Await !Strictness !(Runtime -> Runtime)
  | App !Runtime !Runtime

data Strictness = Strict | Nonstrict

whnf :: Runtime -> Runtime
whnf i@(Prim _) = i
whnf a@(Await _ _) = a
whnf (App f x) = case whnf f of
  Await Strict body -> case whnf x of x -> whnf (body x)
  Await Nonstrict body -> whnf (body x)
  _ -> error "cannot apply a non-function"

```

So, notice that lambdas (the `Await` constructor), when taking an argument, _indicate to the caller whether the argument should be passed strictly or nonstrictly_. The idea being that if a function is going to evaluate an argument anyway, it might as well have the caller evaluate that argument. In Haskell, callers always (modulo static stricness analysis) pass arguments thunked. Here, arguments are received one at a time and we are propagating strictness information to the caller (and possibly to their caller). This is nice for higher-order functions, because they can propagate this stricness also. I hypothesized that the problem with `foldl` was that it was simply not propagating the strictness of the binary folding function it receives.

Here's an implementation of `foldl` using `Runtime` ([full gist][]). Obviously, it's pretty ugly writing out `Runtime` values directly, rather than compiling to `Runtime` but stick with me for now:

[full gist]: https://gist.github.com/pchiusano/9047a14b53b49b10a010

```Haskell
-- order of arguments shuffled to maximize specialization
-- foldl :: [a] -> (b -> a -> b) -> b -> b
-- foldl [] !f z = z
-- foldl (h:t) !f z = foldl t f (f z h)

foldlRuntime :: Runtime
foldlRuntime = Await Strict $ \list ->
  list `App` (Await Strict (\_ -> Await Strict id)) -- scott-encoded lists, pass an expression when list is nil
       `App` (Await Nonstrict (\h -> Await Nonstrict (\t -> -- and a function accepting the head and tail when cons
         Await Strict (\f@(Await strictness _) ->
           Await strictness (\z -> -- note that we request the `z` using the
             foldlRuntime `App` t
                          `App` f
                          `App` (f `App` z `App` h))))))
```

The interesting bit is where we propagate the strictness of `f` to how we request `z`. If `f` is strict in its first argument, then this argument is requested strictly. However, even though this implementation works in constant space, it's actually not the same as the usual `foldl` (nor is it `foldl'`). In the branch where the list is nonempty, the `z` argument strictness always matches the strictness of `f`, but since `f z h` is passed as the new `z` parameter, we should technically respect the strictness of the final argument to `foldl`. We could do this, by asking for the strictness of the partially applied `foldl t f`, and respecting that strictness, but it would require keeping a linear call stack.

I'm not sure that explanation makes sense, but the bottom line is that we need some static information about strictness---we don't want the runtime to have to look ahead to the end of the loop in order to determine strictness of an argument, as that entails linear space usage. Thus a more clever runtime isn't sufficient. We need to know that `fold` is polymorphic in its strictness, statically. [Stefan Holdermans showed an example of how to embed this in Haskell](http://markmail.org/message/yqpwhlaybyu6tf4c#query:+page:1+mid:lqaqjsg2awh3d3yg+state:results). Imagine if `foldl` had a richer type, something like:

```Haskell
foldl :: ![] -> !(s b -> s2 a -> s b) -> s b -> !b
```

Where `!` indicates the type is strict, the `s` in `s b` is a strictness type parameter, that is, strictness is tracked like a regular effect, and it is possible to write code which is polymorphic in strictness. Many HOFs will have this property--we want them to propagate the strictness of the function arguments. The `foldlRuntime` implementation I gave above actually implements this type. What is nice is that we have a single runtime value for implementing all the different instantiations of the strictness tags `s` and `s2`.

Note that this basic idea can I believe be implemented efficiently with two mutable stacks. And it's interesting to think about extending it to handle concerns like unboxing. That is, when awaiting an argument, we can tell the caller whether to accept that argument strictly, and, if strict, whether to accept the argument unboxed.
