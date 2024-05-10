# DDD (Domain Driven Design)

## What is DDD?

* Ways of thinking or set of tools for dealing with domain complexity by ...
  * Separating technology layer from domain layer (Separation of Concern)
  * Building a proper model for domain
* The permise of DDD:
  1. Most softwares focus on domain and domain logic
  1. Complex domain designs build upon models
* DDD is about properly understanding business and integerate the understanding into design and code

## Technical Debt (from [Ward Cunningham](https://wiki.c2.com/?WardExplainsDebtMetaphor))

* Technical debt is not about "writing poor code with the intention of doing a good job later".

* It happends when "people would rush software out the door and learn things but never put that learning back into the program"

* "[I]f you develop a program for a long period of time by only adding features and never reorganizing it to reflect your understanding of those features, then eventually that program simply does not contain any understanding and all efforts to work on it take longer and longer."

## Model

* Model is simplification of interpretation of reality by abstraction
* (Business domain) - (non-essentials)
  * Even though subway maps do not correctly depict the actual subway lanes, they simpify the problem for effective problem-solving.
* Lack of knowledge in business domains leads us astray when abstracting away domains into essentials, possibly resulting in technical debts.
* It is desirable to invent models while communicating with domain expert and to use them for further communication, design and code-writing.
  * ...,which leads to deeper understanding of the domain and better insights for design as you read and write code.

## Strategic Design

* DDD is composed of strategic design and tactical design
* Strategic Design is essential for business success
* Strategic design is a prerequisite for tactical design
* Strategic design concerns the following patterns:
  1. Ubiquitous Language
  1. Bounded Context
  1. Subdomain

### Ubiquitous Language

* Making sure everyone uses the same language(set of vocabulary)
* It is not about blindly following vocabulary used by domain expert
* It is about making terms that deal with business domain together before using them in coding
* Vocabulary system difficult for design or coding needs recalibration.

### Bounded Context

* A bounded context is a specific area within a domain where a specific set of concepts, terms, and rules apply, and where a specific language is used to communicate.
* Building a very large system entails the risk of understanding the meaning of polysemes, which increases the cost of communication.
  * Account -> UserAccount? BankAccount?
* It is able to make one word refer to one single object by limiting the context.

### Subdomains and Bounded Context

* Domains and subdomains are problem space concepts that focus on the specific areas of expertise or knowledge within a business or organization.
* They are defined by the business or organization and are based on their unique needs and requirements.
* On the other hand, bounded contexts are solution space concepts that are used to organize and structure the software system that is being developed to solve the problems in the domain and subdomains.
* They help ensure that the software system is organized in a way that is aligned with the business or organizational requirements, while also being maintainable and adaptable over time.

## Tactical Design

* Given a domain model discoverd through strategic design and ubiquitous language, it is able to incorporate the model into design and code. (Model Driven Design)

* Patterns of domain model:
  1. Entity
  1. Value Object
  1. Aggregate
  1. Repository

### Entity

* In OOP, most entities are defined by continuity and identifiability, not by their properties.
* It is entity if an identifier exists and the identity is verified by the identifier.

### Value Object

* There are cases when continuity and identifiability does not matter
  * For a money laundering tracker, all $1 notes are not the same. They all have to be separate entities
  * For a shopping mall operator, it is safe to assume that all $1 notes are the same. They don't need to identify each note as long as it denotes the same value.
* Value Objects are deemed equal when they have the same properties
  * ..., which means `equals` method in Java has to be implemented according to properties
  * Value Objects need to be immutable in favor of predictability and avoiding potential confusion

### Aggregate

* Just as we drive 'a car' as a whole rather than control each individual part such as tires and an engine,
* Using an aggregate instead of using entities or value objects is advisable
  * Invariants are contraints that need to be met
  * It helps to keep aggregate-wide invariants,maintaining integrity and consistency
  * It helps to maintain an expressive code through the aggregate's interface
* [Aggregate Design Rules](https://www.archi-lab.io/infopages/ddd/aggregate-design-rules-vernon.html) suggested by Vaughn Vernon in IDDD:
  * Rule 1: Model True Invariants in Consistency Boundaries

    > There are different kinds of consistency.  
    > One is transactional consistency, which is considered immediate and atomic.
    > There is also eventual consistency.
    > When discussing invariants, we are referring to transactional consistency.

    > A properly designed Aggregate is one that can be modified in any way required by the business with its invariants completely consistent within a single transaction.

  * Rule 2: Design Small Aggregates
  * Rule 3: Reference Other Aggregates by Identity
  * Rule 4: Use Eventual Consistency Outside the Aggregate Boundary

    > […] if executing a command on one Aggregate instance requires that additional business rules execute on one or more other Aggregates, use eventual consistency.
 
    * Eventual consistuency means that the system will be consistent - at some time in the future ("eventually"). This time can be milliseconds, seconds, or even minutes, hours, or days in the future.

### Respository

* A repository works like a Collection that manages Aggregate
  * Only Aggregates have Repository
  * Repository hides the details and the logistics of persistence
* Spring Data JPA enables focus on business domain, abstracting away the details of technology involved
  * Spring Data JPA's `CascadeType.ALL` and `orphanRemoval=true`options allow for aggregate-repositories-only policy
  * Spring Data JPA's persistence context enables Collection-like usages
  * Spring Data JPA automatically implements the interface
  * Spring's DI allows easy access to repositories

## Layered Architecture and DDD

* Discovering proper Aggregates, configuring Respositories, delegating responsibility to Entities and Value Objects within an Aggregate all enables focus on business domain independent of various techincal issues.
* Domain layer is where domain-centric code lies
* Application layer uses repositories and aggregates
* UI Layer communicates with the user using specific technology such as web
* Containing domain models inside Domain Layer is not possible without the help of layered architecture
