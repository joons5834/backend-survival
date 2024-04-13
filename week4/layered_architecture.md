# Layered Archtecture

## Separation of Concerns

* Large programs are hard to maintain
* It is necessary to properly separate and group parts of a program
* Folders in file systems and Java packages are examples

### Cohesion

* The degree to which the elements inside a module bleong together
* A measure of the strength of relationship between the methods and data of a class and some unifying purpose or concept served by that class
* A measure of the strength of relationship between the class's methods and data themselves
* Modules with high cohesion tend to be preferable for robustness, reliability, reusability and understandability

### Coupling

* The degree to interdependence between software modules
* A measure of how closely connected two routines or modules are
* Low coupling is often thought to be a sign of a well-structured computer system and a good design

## Layered Architecture in web app

* Functions(Business logic) can be separated from web(UI)
* Web deals with UI Layers which is implemented in Spring Web MVC's Controller
* A layer underneath it deals with business logic

## Refactoring into Pesentation-Domain-Data layering

* Design refers to putting codes in place while refeactoring is improving design
* Presentation layer concerns about handling HTTP requests and rendering HTML
* Domain layer is a business(domain) logic layer that contains validations and calculations
* Data access layer sorts out how to manage persistent data in a database or remote services.

### Service

* In a board web page example, functions are separated into individual methods which are aggregated into `PostService`, which in turn belongs to `application` package

### Data access

* Continuing with the board web page example,
there are several ways of managing data
  * List
  * Map
* Create an interface `PostDAO` and implement one for each.(`PostListDAO`,`PostMapDAO`)

## Application & Domain Layer (From Domain-Driven Design by Eric Evans)

### Application layer

* Defines the jobs the software is supposed to do and directs the expressive domain objects to work out problems. The tasks this layer is responsible for are meaningful to the business or necessary for interaction with the application layers of other systems.

* This layer is kept thin. It does not contain business rules or knowledge, but only coordinates tasks and delegates work to collaborations of domain objects in the next layer down.

* It does not have state reflecting the business situation, but it can have state that reflects the progress of a task for the user or the program.

### Domain Layer (Model Layer)

* Responsible for representing concepts of the business, information about the business situation, and business rules.

* State that reflects the business situation is controlled and used here, even though the technical details of storing it are delegated to the infrastructure. This layer is the heart of business software.

### Repository

* Mediates between domain and data mapping layers using a collection-like interface for accessing domain objects
