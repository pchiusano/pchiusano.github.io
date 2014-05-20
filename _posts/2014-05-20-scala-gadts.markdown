---
layout: post
title: Alternatives to GADTs in Scala
categories: [fp, scala]
---

Scala's support for GADTs, really any existential types in pattern matching, has been poor for a while. I've [talked about this before](https://gist.github.com/pchiusano/1369239) and [submitted a few bug reports](https://issues.scala-lang.org/browse/SI-6680), but at this point I'm not holding my breath that we'll be getting good support for these things in the near future. Instead, I've been focusing my attention on finding workarounds.

Whenever you are tempted to define a data type using case classes, ask yourself whether the interpreter(s) you write for your data type really need to do any interesting, deep pattern matching. Very often they don't, and there are many ways to phrase an interpreter, as a series of stages, such that interesting pattern matching is not needed during any stage. If your interpreter is simply going to be a simple fold over your expression tree, you can often avoid building an expression tree and instead have terms be represented as _functions_ from an algebra to a result. Oleg dubbed this the [finally tagless](http://okmij.org/ftp/tagless-final/) encoding. In Haskell, Oleg took the algebra as a typeclass constraint, which works out pretty nicely, but I'm going to show a slightly different approach using explicit dictionary passing.

_Aside:_ This general technique unfortunately is not as well known as it should be--[even expert Haskellers sometimes miss 'obvious' applications of the technique](http://www.reddit.com/r/haskell/comments/1ohe9m/open_question_help_me_design_a_new_encoding_api/) (see the [apfelmus response](http://www.reddit.com/r/haskell/comments/1ohe9m/open_question_help_me_design_a_new_encoding_api/ccs4g6r)).

Here is one possible encoding, for a simple, typed DSL in which there are only two types of expressions, `readLine` and `printLine`:

~~~ Scala
trait ConsoleAlg[F[_]] {
  def readLine: F[Option[String]]
  def printLine(line: String): F[Unit]
}
~~~

If we were doing this with case classes, we'd have a `Console` type that looked something like:

~~~ Scala
trait Console[+A]
case object ReadLine extends Console[Option[String]]
case class PrintLine(line: String) extends Console[Unit]
~~~

And we might write various interpreters, `Console[A] => IO[A]` (doing actual I/O), a `String => Console[A] => A` (taking 'standard input' as a regular, pure argument), etc. Having the flexibility of being able to choose the interpreter later is what often drives us to this sort of encoding.

But we can achieve the same effect by representing `Console[A]` terms as a function from a `ConsoleAlg[F] => F[A]`:

~~~ Scala
trait Console[+A] {
  def run[F[+_]](F: ConsoleAlg[F]): F[A]
}
 
object Console {
  val readLine: Console[Option[String]] = new Console[Option[String]] {
    def run[F[+_]](F: ConsoleAlg[F]): F[Option[String]] = 
      F.readLine
  }
  def printLine(line: String): Console[Unit] = new Console[Unit] {
    def run[F[+_]](F: ConsoleAlg[F]): F[Unit] = F.printLine(line)
  }
}
~~~

No case classes are involved here. To run a `Console[A]`, we just provide a `ConsoleAlg[F]` for some `F`. The `ConsoleAlg` algebra is pretty impoverished--we lack a way to combine console expressions, but we can make this algebra as expressive as we like (it could extend `Monad[F]`, say).

Generalizing this a bit, we can even abstract over the algebra:

~~~ Scala
// more generally
 
trait Term[Alg[_[_]], +A] {
  def apply[F[_]](A: Alg[F]): F[A]
}
object Term {
  def readLine: Term[ConsoleAlg, Option[String]] = new Term[ConsoleAlg, Option[String]] {
    def apply[F[_]](A: ConsoleAlg[F]): F[Option[String]] = A.readLine
  }
  // etc
}
~~~

Now `Term` is parameterized on the algebra, and we can interpret the same term with different algebra instances to obtain different behaviors.

Next time you're temped to define a new data type with case classes, think about what sort of pattern matching you really have to do in the interpreters. If it's normal, single-level pattern matching (a simple fold), consider using a tagless representation like the above.

[Discuss on Twitter](https://twitter.com/pchiusano/status/468850172462497792)
