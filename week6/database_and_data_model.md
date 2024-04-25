# Databases and Data models

## Databases

* A database is an organized collection of data stored and accessed
* It often refers to DBMS instead.

## DBMS (Database Management System)

* A DBMS manages a database. It allows for the following:
  * Easier access to data
  * Efficient manipulation of data
  * Management of access rights, security issues
* RDBMS(Relational Database Management System) is the most popular kind
  * Colloquially, DB commonly refers to RDBMS(DB -> DBMS -> RDBMS)
* DBMS provides database language called SQL
  * DDL (Data Definition Language) → Schema
  * DML (Data Manipulation Language) → Query & Command
  * DCL (Data Control Language) → Grant, Revoke, Commit, Rollback

## Data Model

* An abstract model that organizes elements of data and standardizes how they relate to one another and to the properties of real-world entities
* Sometimes it refers to an abstract formalization of the objects and relationships found in a particular application domain
* At other times it refers to the set of concepts used in defining such formalizations

### Types of Data Models

1. Conceptual Data Model: Describes the semantics of a domain. 
e.g. entity relationship model
1. Logical Data Model : Describes the semantics, as represented by a particular data manipulation technology.
e.g. relational data models, network data models and hierarchical data models
1. Physical Data Model: Describes the physical means by which data are stored
e.g. partitions, CPUs, tablespaces

### Relational Model

#### Attribute

* consists of name and type
* its name should be unique within a set
* examples:
  * name/string
  * age/int
  * sex/char
* mostly implemented as columns

#### Tuple

* A set of (attribute, value) pairs
  * {(name.string, John), (age/int, 13), (sex/char, M)}
* mostly implemented as rows(records)
* RDBMS allows `null` values

#### Relation

* A pair of (a set of attributes, a set of tuples)
  * A set of attributes is called 'heading'
  * A set of tuples is called 'body'
* Example

```
(
  // Heading
  { name/string, age/int, sex/char },

  // Body
  {
    { (name/string, John), (age/int, 13), (sex/char, M) },
    { (name/string, Melissa), (age/int, 12), (sex/char, F) }
  }
)
```

* Mostly implemented as tables
* The set of attribute is represented as schema

#### operations

* Just as more than one numbers results in a new number with operations
(1 + 1 = 2), multiple relational operators can generate new relations 
from one or more relations

##### Projection

* Construct a set tuples of selected attributes
* Represented as a list of column names after `SELECT` keyword in SQL

```sql
SELECT name, age, gender
FROM people;
```

* Use wildcard (*) for an unfiltered list of attributes

```sql
SELECT *
FROM people;
```

##### Selection

* Select tuples that satify conditions
* Represented as `WHERE` clause in SQL 

```sql
SELECT name, age, gender
FROM people
WHERE age < 13;
```

##### Cartesian Product

* The Cartesian product of two relations A and B, denoted A × B, is a relation where ..
  * Heading: a set of a merged pair {(a<sub>0</sub>,a<sub>1</sub>..., b<sub>0</sub>, b<sub>1</sub>...)} where a is in A<sub>header</sub> and b is in B<sub>header</sub>
  * Body: a set of all merged pairs {(a<sub>00</sub>,a<sub>01</sub>..., b<sub>00</sub>, b<sub>01</sub>...),(a<sub>00</sub>,a<sub>01</sub>...,b<sub>10</sub>, b<sub>11</sub>...),...} where a is in A<sub>body</sub> and b is in B<sub>body</sub>.


```sql
SELECT *
FROM r1, r2;
```

##### join

* selection among cartesian product
* σ<sub>people.name = items.person_name</sub> (people × items)
  * σ = Selection
  * × = Cartesian Product

Qualify column names with {table name}.{column name} in SELECT clause if the column names are present in both tables. Use `AS` keyword to change column name.

```sql
SELECT people.name, age, gender, items.name AS item_name, usage
FROM people, items
WHERE people.name = items.person_name;
```

`FROM a JOIN b ON a.x=b.x` is used in place of `FROM a,b.. WHERE a.x=b.x`
```sql
SELECT people.name, age, gender, items.name AS item_name, usage
FROM people
JOIN items ON people.name = items.person_name;
```

Use OUTER JOIN to include people without any items in the result relation of the following query.

```sql
SELECT people.name, age, gender, items.name AS item_name, usage
FROM people
LEFT OUTER JOIN items ON people.name = items.person_name;
```

### Entity-Relationship Model

* commonly formed to represent things a business needs to remember in order to perform business processes
* Entities : tables that hold specific information (data)
* Relationships : the associations or interactions between entities
* visualized as ERD (Entity-Relationship Diagram)
