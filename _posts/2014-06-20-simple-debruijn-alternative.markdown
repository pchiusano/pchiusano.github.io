---
layout: post
title: A simple alternative to De Bruijn indexing, from ICFP 2013
categories: [fp]
---

There was a [functional pearl at ICFP 2013][the paper] by Axelsson and Claessen that introduced a deceptively simple, useful technique for dealing with variable bindings in DSLs. Without any heavy machinery, it gives us HOAS-like syntax for constructing terms of the DSL, while retaining a simple first-order representation. The 'library' they developed is about 15 lines of code. When it was presented at ICFP, the audience was stunned. IIRC, there were no questions, as everyone was clearly wondering _how the @^#$!! didn't I think of that?_ I waited for someone in the audience to point out during the Q&A that this technique was FP folklore discovered by Alonzo Church in the 30s, but not a peep.

The question posed by the paper: is it possible to provide a HOAS-like syntax, `lam :: Expr -> Expr` for creating binding forms in a DSL, without introducing heavy machinery like a global name supply, all while retaining a simple first-order ADT:

~~~ Haskell
type Name = Integer -- anything ordered will do

data Expr 
  = Var Name
  | App Expr Expr
  | Lam Name Expr

lam :: (Expr -> Expr) -> Expr
lam f = ???
~~~

[The paper][the paper] does a nice job of working up to the solution iteratively, but I'll jump right to the punchline. In `lam`, we have to somehow pick a fresh `Name` that isn't already used in the body of `f`. The solution is a circular program---we call `f` with a name that isn't used in its body, which we inspect after calling `f`. But how do we ensure productivity? Here is the complete code:

~~~ Haskell
lam :: (Expr -> Expr) -> Expr
lam f = Lam n body
  where body = f (Var n)
        n = maxBV body + 1

bot :: Name
bot = 0

-- Computes the maximum bound variable in the given expression
maxBV :: Expr -> Name
maxBV (Var _) = bot
maxBV (App f a) = maxBV f `max` maxBV a
maxBV (Lam n _) = n
~~~~

Epic! Go [read the paper][the paper] for full details. 

Now for some remarks. One can view this scheme as an alternative to De Bruijn indexing. In De Bruijn, variables are named according to the number of lambdas one must go out to find the point where they are bound. This is nicely nameless, but it also means that a variable's meaning is context dependent--we need to look at the number of surrounding lambdas. For instance:

~~~ Haskell
\x -> (\y -> f x y) x
(Lam (Lam (f v2 v1)) v1) -- De Bruijn representation
~~~

Notice that the symbol `v1` does not have a consistent meaning within a particular lexical scope. In this scheme (Axelsson-Claessen naming, shall we call it?), a variable gets a name based on the _depth_ of lambdas in lexical scope underneath the lambda we are introducing. This has the advantage that names have a consistent meaning in each lexical scope, which I find easier to read (and think about):

~~~ Haskell
\x -> (\y -> f x y) x
\v2 -> (\v1 -> f v2 v1) v2 -- Axelsson-Claessen naming
~~~

Thus, 'leaf' level lambda parameters get assigned a name of `1`, parents get assigned a parameter name of `2`, and so on. This way of naming has the added bonus of ensuring the productivity of the knot-tying program for picking a fresh variable in `lam`. We don't need to examine the value of each variable in the body to find a variable that is free. (Furthermore, as the code for `maxBV` above shows, we do not need to descend into the body of a lambda either---the lambda constructor effectively caches the computed lambda-depth of the body.)

I don't think this replaces all the use cases of more heavyweight techniques for managing variable bindings, like using polymorphic recursion to ensure well-formedness, à la [bound](http://hackage.haskell.org/package/bound), but it's such a useful, simple idea that I'm already finding uses for it.

[Discuss on Reddit](http://www.reddit.com/r/haskell/comments/28vfd0/a_simple_alternative_to_de_bruijn_indexing_from/)

[the paper]: http://www.cse.chalmers.se/~emax/documents/axelsson2013using.pdf
