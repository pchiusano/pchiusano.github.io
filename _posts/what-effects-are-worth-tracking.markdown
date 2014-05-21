---
layout: post
title: Is network I/O always an effect worth tracking?
categories: [fp]
---

The usual definition of functional programming is in terms of referential transparency. There are various ways of stating the definition of RT, but it often goes something like: 

An expression, `e` is referentially transparent ('pure') if it may be _replaced with its result_, `e'`, in all programs `p`, _without affecting the meaning_ of `p`. The phrases 'replaced with its result' and 'without affecting the meaning' are ambiguous, and I don't love this definition for another reason--it implies that 'results' have the same type as terms. But let's leave that issue aside. The key ambiguity is this phrase _without affecting the meaning_ of `p`. Just what is the meaning of a program `p`? And how can we say if two meanings are equivalent?

These are somewhat philosophical questions. What doesn't get emphasized often enough is that it is a _choice_ how we assign meaning to our programs. And our choice of 'meaning assignment' determines what expressions are considered pure, and what expressions have a typed effect if we are sticking to the discpline of pure FP. There are standard choices: we don't typically track memory allocation as a typed effect (because we have GC), nor partiality (because this is unavoidable in a Turing-complete language), but we do track file and network I/O (using the `IO` monad or perhaps something more fine-grained). Chapter 13 of [FP in Scala](manning.com/bjarnason) discusses some of these issues in more detail, and [Runar also has a nice post](http://blog.higher-order.com/blog/2012/09/13/what-purity-is-and-isnt/) that formed the starting point for that chapter.

In general, the advantage to _not_ tracking an effect is that, without any sort of explicit effect polymorphism (for instance being parametric in the choice of monad), functions are more general, in particular higher order functions, which work for all possible combinations of the presence or abscence of an effect. For instance, Haskell does not track evaluation as an effect--both thunks and forced values have the same type in Haskell (ignoring `Int#` and the like). The advantage to this is we avoid a combinatorial explosion of different functions (or lots of explicit effect polymorphism) for dealing with strictness and evaluation. My favorite example of this is the `map` function:

~~~ Haskell
map :: (a -> b) -> [a] -> [b]
~~~

In Haskell, we may pass an `a -> b` which is lazy, and avoids evaluating the `a` it is given, or a strict `a -> b`. Both variants are useful, and in a strict language, we could in theory define two versions of `map`, one which takes a strict function, and another which takes a non-strict function. (In practice, no one bothers with this sort thing in the standard libraries, so strict languages are often unnecessarily so, at a loss to modularity)

### Should we always track file and network I/O as an effect? ###

So now I want to ask a question--should we always track file and network I/O with typed effects? Might there be situations where we want to allow pure code to perform file and/or network I/O? Treat this as more of a thought experiment; I am not actually advocating for any of this for real code.

Suppose I am writing a piece of code that fetches data from the file `/foo/bar/baz.bin`, and I am prepared to assume that the contents of that file will never change over the course of my running application. Perhaps I can't truly guarantee that this assumption will hold, but so what? A gamma ray could flip some bits in the region of memory corresponding to my purely functional data structure, and I can't guarantee this won't occur either! Even if you consider the possibility of gamma ray corruption to be a lower than the possibility of some external process mucking with the file system, there is no _fundamental_ distinction. Honestly, what is the difference between fetching data from a (assumed to be immutable) region of memory and an (assumed to be immutable) region on disk? At the end of the day, we as programmers get to decide what possibilities we want to account for and model explicitly in our programs.

__Bob:__ Aha! But you've forgotten that reading from a file uses a file handle, which are in limited in number by the OS, and therefore this resource acquisition and freeing is worth tracking as an effect. 

But actually, this constraint is artificial--one can imagine an OS in which file handles were no more scarce than memory. Or we could simply imagine a more sophisticated I/O manager which makes the underlying OS file handle scarcity irrelevant to the vast majority of programs. For the sake of this thought experiment, let's imagine such a world. Do we still need to track reading the file `/foo/bar/baz.bin` as an effect? Not necessarily. From the perspective of our program's meaning, we might not care whether the contents of the file are fetched during the execution of the program, or whether the file contents were fetched ahead of time and we are just reading from an in-memory literal. The only way we can distinguish between these two programs (the one which fetches ahead of time, and the one which fetches during the running of the program) is that the one which fetches the data during the running of the program may have greater running time and/or possibility of failure. But we already quotient running time and possibility of failure out of our program's meaning, so the two programs can be considered semantically identical!

Now that we've come this far, suppose we modify our program to read instead from `http://example.com/foo/bar/baz.bin`. Once again, suppose I am prepared to assume that the data available at that URL will never change over the course of my running application. Perhaps I can't truly guarantee that assumption will hold, but again so what? Our gamma ray argument still carries. If we are being precise, we might say that there is some probability distribution which models the likelihood of the data changing out from under us, and depending on this distribution we make a decision about whether to model the effect in our programs. But there is no One True way of making such a decision.

Once again, one might object--fetching data from a URL involves lots of 
