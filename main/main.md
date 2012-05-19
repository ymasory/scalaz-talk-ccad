!SLIDE
# Functors, Monads, and Other Scary Words: Programming with Scalaz

- Yuvi Masory
- May 21, 2012
- CCAD

!SLIDE
# Some ground rules ...
- Scalaz 7
-- More modular
-- _Much_ more discoverable
-- Unicode is (mostly) gone
-- Still poorly documented

!SLIDE
# == Considered Harmful
val admin: Option[User] = //...
val curUser: User = //...
if (curUser == admin) { //oops! always false
  //...
}

!SLIDE
def userEqual = equalA[User]
if (curUser === admin) { //doesn't compile!
  //...
}

!SLIDE
# List#head Considered Annoying

//my startup
val payingCustomers: List[Customer] = //...
val customer = payingCustomers.head //throws exception!

Empty lists don't have a head.

!SLIDE
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

Checking for empty lists is unnecessary in many cases. But who knows which ones?

!SLIDE
# scalaz.NonEmptyList
NonEmptyList(1)
NonEmptyList(1, 2, 3)
NonEmptyList(0) //doesn't compile!

NonEmptyList#head //completely safe

!SLIDE
# The Typeclass Pattern

!SLIDE
# Functors

!SLIDE
# Applicatives

!SLIDE
# Monads

!SLIDE
# Monoids

!SLIDE
# Thank you ... questions?
