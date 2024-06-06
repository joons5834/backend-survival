# CQRS

## CQS (Command-query separation)

* every method should either be a *command* that performs an action, or a *query* that returns data to the caller, but not both.
  * asking a question should not change the answer.
* Makes its states (via queries) and state changes (via commands) more comprehensible.

## CQRS(Command and Query Responsibility Segregation)

* a pattern that separates read and update operations for a data store.

* Command and Query Responsibility Segregation uses the same definition of Commands and Queries
that Meyer used and maintains the viewpoint that they should be pure.

* The fundamental difference is
that in CQRS objects are split into two objects, one containing the Commands one containing the
Queries.

* Benefits of CQRS include:
  * Independent scaling. CQRS allows the read and write workloads to scale independently, and may result in fewer lock contentions.
  * Optimized data schemas. The read side can use a schema that is optimized for queries, while the write side uses a schema that is optimized for updates.
  * Security. It's easier to ensure that only the right domain entities are performing writes on the data.
  * Separation of concerns. Segregating the read and write sides can result in models that are more maintainable and flexible. Most of the complex business logic goes into the write model. The read model can be relatively simple.
  * Simpler queries. By storing a materialized view in the read database, the application can avoid complex joins when querying.

## Materialized view

* To enhance query(read) performance
* Unlike Views, materialized views are saved and periodically synced
* Useful for adoption of CQRS without complex infrastructure configuration

## OLTP & OLAP

* OLTP(Online Transaction Processing): a type of data processing that consists of executing a number of transactions occurring concurrently—online banking, shopping, order entry, or sending text messages, for example

* OLAP(online analytical processing): an approach to quickly answer multi-dimensional analytical (MDA) queries. Read model is not only for UI but for data analysis.

## Event Sourcing

* Capture all changes to an application state as a sequence of events.
* Event Sourcing is frequently used with CQRS since event sourcing makes it harder to quickly get/analyze current state
