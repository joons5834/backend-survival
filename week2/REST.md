# What Is REST API?

## What is API(Application Programming Interface)?

* It is a contract between a server and a client for communication
  * API specification lays out how a certain request is called and how its response is formatted.
* By exposing only those parts that a user will find useful, API hides implementation details from users, which allows them to still use APIs with a change of implementation detail hidden behind the consistent,  exposed parts. (Information Hiding)

### Uniform Resource Identifier

* Web API is about requesting operations (create, read, update, delete) on resources.
* URI is a way to identify resources
* There are largely two types of URI: URL and URN
  * URL(Uniform Resource Locator) → location of a resource. Vulnerable to location change.
  * URN(Uniform Resource Name) → Unique name of a resource. Rarely used.

### MIME Type(Content-Type, Media Type)

* Denotes a type of content.
* `<type>/<subtype>`
* `Content-Type` in HTTP headers
* Examples
  * text/plain
  * text/html
  * text/css
  * text/javascript
  * application/xml
  * application/json

## What is REST(REpresentational State Transfer)?

* "Architectural Styles and the Design of Network-based Software Architectures" - by Roy Fielding
* A software architectural style that was created to guide the design and development of the architecture for the World Wide Web

### Constraints

* Client/Server - Clients are separated from servers by a well-defined interface
* Stateless - each request from a client to a server should contain all necessary information
* Cache - Responses indicate their cacheability
* Uniform Interface
  * Resource identification in requests: Individual resources are identified in requests, for example using URIs in RESTful Web services.
  * Resource manipulation through representations: When a client holds a representation of a resource, including any metadata attached, it has enough information to modify or delete the resource's state.
  * Self-descriptive messages: Each message includes enough information to describe how to process the message. For example, which parser to invoke can be specified by a media type. Media types such as "application/json" might be too broad to be self-descriptive.
  * Hypermedia as the engine of application state (HATEOAS): Having accessed an initial URI for the REST application, a REST client should then be able to use server-provided links dynamically to discover all the available resources it needs.
* Layered System: Adding independent intermediaries like proxies, firewalls, and gateways between the client and server to improve security, simplicity, and scalability
* Code on Demand (optional): Servers can temporarily extend or customize the functionality of a client by transferring logic to the client that can be executed within a standard virtual machine
