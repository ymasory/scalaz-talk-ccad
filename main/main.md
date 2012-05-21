!SLIDE
# Functors, Monads, and Other Scary Words
# Programming with Scalaz

- Yuvi Masory
- May 21, 2012
- CCAD

![pic](main/scary.png "scalaz code")

!SLIDE
# Scalaz 7
- More modular
- _Much_ more discoverable
- Unicode is (mostly) gone
- Still poorly documented

!SLIDE
# I. You don't need to use functional programming to benefit from Scalaz!

!SLIDE
# 1. Scalaz has lots of random utilities (although could use some more ...)

!SLIDE
# Random utilities
```
"1".toInt
"foo".toInt //uh-oh

val iOpt: Option[Int] = "foo".parseInt
println(iOpt.isDefined ? "parsed" | "unparseable")
```

!SLIDE
# 2. Scalaz has _better_ alternatives to Java legacies.

!SLIDE
# `==` Considered Harmful

```
val admin: Option[User] = //...
val curUser: User = //...
if (curUser == admin) { //oops! always false
  //...
}
```

!SLIDE
# `===` Considered Awesome
```
implicit def userEqual = equalA[User]
if (curUser === admin) { //doesn't compile!
  //...
}
```

!SLIDE
# 3. Scalaz has _safer_ alternatives to many Scala constructions.

!SLIDE
# `List#head` Considered Annoying

```
//my startup
val payingCustomers: List[Customer] = //...
val customer = payingCustomers.head //throws exception!
```

Empty lists don't have a head.

!SLIDE
```
//my business
class Customer {
  def creditCards: List[String]
}
object Customer {
  def mkCustomer(name: String, creditCard: String, creditCards: String*): Customer = ...
}
someCustomer.creditCards.headOption match {
  case Some(creditCard) =>  //now that's annoying
  case None
}
```

Checking for empty lists is unnecessary in many cases. But who knows which ones?

!SLIDE
# `scalaz.NonEmptyList`
```
NonEmptyList(1)
NonEmptyList(1, 2, 3)
NonEmptyList(0) //doesn't compile!

val lst: NonEmptyList[Int] = ...
lst.head //completely safe
```


!SLIDE
# II. The Typeclass Pattern
You may have noticed this strange bit

```
implicit def userEqual = equalA[User]
```

!SLIDE
# What are types?
A simple definition: types are collections of _expressions_.

## Polymophism
Because we want our functions to work on many types.

## Ad-hoc polymorphism
Polymorphic functions may do something different depending on its input type.

!SLIDE
# Ad-hoc polymorphism using inheritance & subtyping

```
trait Equal[A] {
  def ===(a: A): Boolean
}
case class Person(name: String, zip: Int) extends Equal[Person] {
  def ===(that: Person) = //...
}
Person("yuvi", 19104) === Person("colleen", 12345)
```

!SLIDE
# Not bad. But what if ...
- ... you don't control the `Person` source code?
- ... you want more than one equality notion?
- ... you want your domain objects "light" and free of behavior?
- ... you want method implementations to be fully known at compile time?
- ... you want to leave the interface "open" so you can add new implementations later without re-jiggering a type hierarchy

!SLIDE
# Ad-hoc polymorphism using typeclasses
```
trait Equal[A] {
  def(a1: Equal, a2: Equal): Boolean
}
object PersonEqual extends Equal[Person] {
  def equals(p1: Person, p2: Person): Boolean = //...
}
PersonEqual equals (Person("yuvi", 19104), Person("colleen", 12345))
```

!SLIDE
# Now with some sugar
```
trait Equal[A] {
  def(lhs: A, rhs: A): Boolean
}
object Equal {
  implicit def addEqualOps[A:Equal](lhs: A) = new EqualOps(lhs)
}
class EqualOps[A](lhs: A)(implicit ev: Eq[A]) {
  def ===(rhs: A) ev.eq(lhs, rhs)
}
implicit object PersonEqual extends Equal[Person] {
  def equals(p1: Person, p2: Person): Boolean = //...
}

import Equal._
p1 === p2
```

!SLIDE
# Note this is a brand new type relationship
```
//"is-a"
def mycompare[A <: Comparable](lhs: A, rhs: A) = lhs compare rhs

//"has-a"
def myequal[A:Equal](lhs: A, rhs: A) = lhs equals rhs

//"has-a", de-sugared, note "ev" is never used
def myequal[A](lhs: A, rhs: A)(ev: Equal[A]) = lhs equals rhs
```

!SLIDE
# Advantages of typeclasses
- No dynamic dispatch, implementations known to compiler.
- Behavior is de-coupled from domain object, AND
- Behavior is de-coupled from inheritance, leaving the trait "open"
- Multiple typeclass instances can exist side-by-side

# Disadvantages of typeclasses
- Boilerplate (for now)
- Run-time overhead of wrapper object creation (for now)

!SLIDE
# More on typeclasses
- Seth Tisue's NE Scala Talk (video): http://bit.ly/He7rgq
- Erik Osheim's PHASE talk (slides): http://bit.ly/pbsukl

!SLIDE
# III. Let's get to some real functional programming ...
![pic](main/dontpanic.jpg "don't panic")

# You already do all these things ...

!SLIDE
# Functors
Let's keep it simple: if you can map over it, it's a functor.

```
trait Functor[F[_]] {
  def map[A, B](r: F[A], f: A => B): F[B]
}
```

!SLIDE
# `Option` is a `Functor`
```
object OptionIsFunctor: Functor[Option] = new Functor[Option] {
  def map[A, B](r: Option[A], f: A => B) = r match {
    case None => None
    case Some(a) => Some(f(a))
  }
}
```

!SLIDE
# `List` is a `Functor`
```
object ListIsFunctor: Functor[List] = new Functor[List] {
  def map[A, B](as: List[A], f: A => B) = as match {
    case Nil => Nil
    case h :: t => f(h) :: map(t, f)
  }
}
```

!SLIDE
# Applicatives (really, Applicative Functors)
No time for this ... and not so natural in Scala since functions are not curried by default.

```
for {
  url   <- urlOpt
  pw    <- passwordOpt
  uname <- usernameOpt
} yield DriverManager getConnection (url, pw, uname)

(url |@| pw |@| uname) { Driver.getConnection }
```

!SLIDE
# Aside: This is what Scala already does, although oddly without types or a representation of `Functor`

```
for (el <- List(1, 2)) yield el + 10

List(1, 2) map { _ + 10 }
```

!SLIDE
# Monads
`Functors` that can `flatMap`

![pic](main/monads.jpg "monads")

!SLIDE
# If we didn't have monads
```
def perfectRoot(i: Int): Option[Int] = //...
val opt = perfectRoot(10000)
opt map { perfectRoot(_) }
//Some(Some(10))

opt flatMap { perfectRoot(_) }
//Some(10)
```

!SLIDE
# Monad
```
trait Monad[M[_]] extends Applicative[M] {
  def pure[A](a: => A): M[A]
  def flatMap[A, B](a: M[A], f: A => M[B]): M[B]
}
```

!SLIDE
# Monads you know
- `List`
- `Option`
- `Function1`

!SLIDE
# Aside: This is what Scala already does, although oddly without types or a representation of `Monad`

```
for {
  a <- aOpt
  b <- bOpt
  c <- cOpt
} yield a + b + c

aOpt.flatMap { a =>
  bOpt.flatMap { b =>
    cOpt.flatMap { c =>
      a + b + c
    }
  }
}
```


!SLIDE
# Monoids
Things you can add.

!SLIDE
# Technically, Monoids
- A monoid is a semigroup with a zero element ...
- A semigroup is a set of elements with the binary operation "plus"
- Zero is the left and right additive identity ...
- The set is closed over plus.

!SLIDE
# What have we gained through a formal representation of functors, monads, and monoids? After all these are ideas already present in Scala and almost every programming language.

!SLIDE
# Thank you ... questions?
