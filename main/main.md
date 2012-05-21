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
  def(a1: Equal, a2: Equal): Boolean
}
implicit object PersonEqual extends Equal[Person] {
  def equals(p1: Person, p2: Person): Boolean = //...
}
class EqualOps(a: Any) {
  def ===(that: a)
}
implicit def addEqual(a: Any) = 
PersonEqual equals (Person("yuvi", 19104), Person("colleen", 12345))
```


!SLIDE
- No dynamic dispatch, methods known by compiler.
- Behavior is de-coupled from domain object, AND
- Behavior is de-coupled from trait
- Multiple equality notions can exist side-by-side

!SLIDE
# III. Let's get to some real functional programming ...
![pic](main/dontpanic.jpg "don't panic")

!SLIDE
# Functors

!SLIDE
# Applicatives
No time for this ...

!SLIDE
# Monads
![pic](main/monads.jpg "monads")

!SLIDE
# Monoids

!SLIDE
# Thank you ... questions?
