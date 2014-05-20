---
template: post
title: Alternatives to GADTs in Scala
categories: [fp, scala]
---

Scala's support for GADTs is known to be poor. I've [talked about this before](https://gist.github.com/pchiusano/1369239) and [submitted a few bug reports](https://issues.scala-lang.org/browse/SI-6680), but at this point I've given up hope that they'll be well supported in the near forseeable future. Instead, I've been focusing my attention on finding workarounds.

~~~ Scala
trait ConsoleAlg[F[_]] {
  def readLine: F[Option[String]]
  def printLine(line: String): F[Unit]
}
 
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
