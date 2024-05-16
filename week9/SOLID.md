# SOLID

* promoted by Robert C. Martin (Uncle Bob)
* Five design principles intended to make object-oriented designs more understandable, flexible, and maintainable.

1. SRP (Single Responsibility Principle)
1. OCP (Open-Closed Principle)
1. LSP (Liskov Substitution Principle)
1. ISP (Interface Segregation Principle)
1. DIP (Dependency Inversion Principle)

## SRP (Single Responsibility Principle)

* There should never be more than one reason for a class to change.
* A module should be responsible to one, and only one, actor.
* Makes a class more robust to breakage of code parts due to unrelated code changes.
* Results in a smaller and more understandable class that is also testable and reusable.

## OCP (Open-Closed Principle)

* Introduced by Bertrand Meyer
* software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification
  * such an entity can allow its behaviour to be extended without modifying its source code

## LSP (Liskov Substitution Principle)

* Introduced by Barbara Liskov
* If S is a subtype of T, then objects of type T in a program may be replaced with objects of type S without altering any of the desirable properties of that program
  * Preconditions cannot be strengthened in the subtype.
  * Postconditions cannot be weakened in the subtype.
  * Invariant cannot be weakened in the subtype.

## ISP (Interface Segregation Principle)

* No code should be forced to depend on methods it does not use.
* splits interfaces that are very large into smaller and more specific ones so that clients will only have to know about the methods that are of interest to them. (role interfaces)
* keep a system decoupled and thus easier to refactor, change, and redeploy.

## DIP (Dependency Inversion Principle)

1. High-level modules should not import anything from low-level modules. Both should depend on abstractions (e.g., interfaces).
1. Abstractions should not depend on details. Details (concrete implementations) should depend on abstractions.
