---
title: Are monads really more expressive than applicatives?
layout: post
categories: [fp]
---

The party line about `Applicative` vs `Monad` is that monads provide additional power. Of course that's true in some sense. With `Applicative`, the structure of the computation is defined entirely by pure code; whereas with `Monad`, the value _inside_ an effect can be used to determine the subsequent structure of the computation. Algebraically, a `Monad f` lets us remove layers of `f`, while `Applicative f` only lets us introduce (via `pure`) and combine layers (via `<*>`).

But for _particular effects_, it's often the case that computations that seem to require `Monad` can be phrased using only `Applicative`. Moreover, the `Applicative` phrasing is often an _improvement_, making the code more testable, better factored, and facilitating reuse. Let's look at some concrete examples, then we'll ponder the general principle. 

#### Example 1: Parsing ####

Consider writing a parser for a language in which users can declare their own operators of arbitrary precedence and fixity:

~~~
infixl 7 <+> 
...
-- later
a <+> f <|> 8 &&& xyz
~~~

Sounds like a case for monadic parsers, doesn't it? We parse the `infixl 7 <+>` and then use this to generate a parser to use for the rest of the file.

Or do we need monads for this? Another way of phrasing this sort of parsing task is to separate the parsing into two phases. Our parser is just responsible for aggregating all the operator declarations, but chains of operators are left as a flat list, which we then group according to precedence in _pure_ code. So we have something like this:

~~~ Haskell
data Delimited d a = Delimited a [(d,a)]
instance Functor (Delimited d) where ...

data Fixity = L | R | None
data Op a = Op Fixity Int

precedentize :: (d -> (Op, a -> a -> a)) -> Delimited d a -> a
~~~

With this formulation, we'll assume the parser is now only responsible for building up a `Map` of the operator declarations, and `Delimited op expr` for some `op` and `expr` types. We then call `precedentize` _in pure code_, using `fmap` or `liftA2`. Essentially, chains of operators are left 'unparsed', and interpreted later. What I like about this formulation is that `precedentize` is now a regular pure function, it's trivial to test, and it's not entangled with parsing, which makes it more reusable.

Now that we've spotted this factoring, we can see that using monadic parsing led us to an overly sequential factoring of this computation. When we took a step back, we saw that there need not be a dependency between parsing the `infixl 7 <+>` and parsing the later `a <+> f <|> ..`, but monads make it very easy to fall back on this overly sequential formulation. 

I have some general remarks on this that I'll save for later, but let's look at a couple other examples first. Suppose we are writing a `Gen (Int, Int)` which generates _ordered_ pairs drawn from `(0..10, 0..10)`. We could write this monadically, something like:

~~~ Haskell
do 
  x <- choose 0 10
  y <- choose x 10
  return (x,y)
~~~

But we could also do it just by generating two values, and then _sorting_ the values to produce a pair, which only requires `Applicative`. Once again, the code is slightly improved--the core logic is a pure function, `orderedPair`, which is easy to test and which is not entangled with test case generation:

~~~ Haskell
orderedPair :: Ord a => a -> a -> (a,a)
orderedPair a a2 | a <= a2 = (a, a2)
orderedPair a a2           = (a2, a)
~~~

Obviously, this is a trivial example, but perhaps you're starting to get the idea. Let's look at one more example, which was [posed as a challenge](http://www.reddit.com/r/programming/comments/t6p0a/elm_a_new_language_for_functionalreactive_web/c4k4pnw) for any 'reasonable' FRP framework:

> Start with a simple counter widget: a label displaying the current count, and a pair of buttons for incrementing and decrementing the counter. Then combine two counters on a single page. Then implement a variable number of counters: there is a "add counter" button which adds a new counter, and with each counter goes a button "delete" to delete that counter. In addition, display the sum of the values of all the counters.

