!SLIDE
# Functors, Monads, and Other Scary Words
# Programming with Scalaz

- Yuvi Masory
- May 21, 2012
- CCAD

!SLIDE
# Scalaz 7
- More modular
- _Much_ more discoverable
- Unicode is (mostly) gone
- Still poorly documented

!SLIDE
# You don't need to use functional programming to benefit from Scalaz!

!SLIDE
# Random utilities
```
"1".toInt
"foo".toInt //uh-oh

val iOpt: Option[Int] = "foo".parseInt
println(iOpt.isDefined ? "parsed" | "unparseable")
```

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
# The Typeclass Pattern
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

!SLIDE
# Let's get to some real functional programming ...
![pic](main/dontpanic.jpg "don't panic")

!SLIDE
# Functors

!SLIDE
# Applicatives

!SLIDE
# Monads
![pic](main/monads.jpg "monads")

!SLIDE
# Monoids

!SLIDE
# Thank you ... questions?
