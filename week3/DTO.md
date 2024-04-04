# DTO

## What is DTO(Data Transfer Object)?

* An object that carries data between (remote) processes.

* It does not have any behavior except for sotrage and retrieval

* Latest versions of Java(since 14) support Record. (Not supported in old Java Bean related libraries )

## Why use DTO?

* It aggregates the data that would have been transferred by the several calls, but that is served by one call only.

* It shifts data in expensive remote calls.
  * REST is about handling representations, which necessiates the need for special-purpose objects without any behaviors except storage and retrieval.

* If the format of incoming data is fixed, DTOs are preferred (over Map in Java) since it allows to easily check for errors (typos in field names etc.)