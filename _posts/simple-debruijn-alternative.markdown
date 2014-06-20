---
layout: post
title: A simple alternative to De Bruijn indexing, from ICFP 2013
categories: [fp]
---

There was a [functional pearl at ICFP 2013][the paper] that demonstrated an amazingly simple technique for dealing with variable bindings in DSLs. 

~~~ Haskell
data Expr = Var Name
          | App Expr Expr
          | Lam Name Expr
~~~

The question they posed is, can we provide a HOAS-like syntax, `lam :: Expr -> Expr` for creating `Lam` constructors, without introducing heavy machinery like a global name supply?

[the paper]: http://www.cse.chalmers.se/~emax/documents/axelsson2013using.pdf
