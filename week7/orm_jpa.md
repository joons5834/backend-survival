# ORM and JPA

## ORM (Object-Relational Mapping)

* A task, technology, or tool for mapping objects and relational data
* ORM tools in general automatically maps data <-> object and generate SQLs
* It improves maintainability and enables us to focues on business logic.
* It enables persistence of domain objects in object-oriented programming
rather than that of anemic objects dependent on databases.

## JPA(Jakarta Persistence API)

* JPA is an API for managing relational data.
It is a standard for ORM in Java
* EclipseLink and Hibernate are implementations of JPA
* Hibernate is commonly used the most. It also affcted the spec of JPA

## JPA Entity

* (DB perspective) Entities represent persistent data stored in a relational database automatically using container-managed persistence.
* (OOP perspective) An entity models a business entity or multiple actions within a single business process.
* Although focusing solely on DB perspective eases developing simple CRUD apps,
it is easy for apps to satisfy none of the perspectives unless you care about the OOP perspective.
* Properly utilizing relationship mapping and aggregate mapping can reconcile object and relational data.
