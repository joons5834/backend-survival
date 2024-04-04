# Serialization

## What is serialization?

* The process of translating a data structure or object state into a format that can be stored or transmitted and reconstructed later

* Deserialization: extracting a data structure from a series of bytes

* Marshalling: the process of transforming the memory representation of an object into a data format suitable for storage or transmission, especially between different runtimes

  * The point with marshalling an object is to have that object that is present in one **running** program be present in another **running** program

  * Serialization is only concerned about transforming data to generate that intermediate, "dry" representation of the object (for example, into a stream of bytes) which could then be either reified in a different runtime, or simply stored in a database, a file or in memory.

## JSON (JavaScript Object Notation)

* An open standard file format and data interchange format that uses human-readable text to store and transmit data objects consisting of attribute–value pairs and arrays (or other serializable values).

* In Java, DTO is preferred to Map for JSON schema management and type-safety.

* In Java, Jackson is a well-known tool for transforming DTOs to JSON and vice versa.

* Spring Boot takes care of converting DTO <-> JSON with out-of-the-box integeration with Jackson

```java
 @GetMapping("/{id}")
 public PostDto detail(@PathVariable String id) throws JacksonException {
    PostDto postDto = new PostDto(id, "title", "content");

    return postDto;
 }
```
