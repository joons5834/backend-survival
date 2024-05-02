# Hibernate and Spring Data JPA

## Hibernate Core Usage

### import

```gradle
implementation 'org.hibernate.orm:hibernate-core:6.1.7.Final'
implementation 'org.postgresql:postgresql:42.5.4'
```

### Annotate a class

* write a class and annotate the class with `@Entity` and `@Table`
  * annotate the id field(column) with `@Id`
  * Other fields must be annotated with `@Column(name = '')` if their names differ from column names

```java
@Entity
@Table(name = "people")
public class Person {
  @Id
  @Column(name = "name")
  private String name;

  @Column(name = "age")
  private Integer age;

  @Column(name = "gender")
  private String gender;

 //......
}
```

### Define a persistence unit

`src/main/resources/META-INF/persistence.xml`

```xml
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" version="2.1">
  <persistence-unit name="demo">
    <class>com.example.demo.models.Person</class>
    <properties>
      <property name="jakarta.persistence.jdbc.url"
        value="jdbc:postgresql://localhost:5432/postgres"/>
      <property name="jakarta.persistence.jdbc.user"
        value="postgres"/>
      <property name="jakarta.persistence.jdbc.password"
        value="password"/>
      </properties>
    </persistence-unit>
</persistence>
```

### Set up `EntityManagerFactory` and `EntityManager`

```java
public class JpaTest {
  private EntityManagerFactory entityManagerFactory;

  private EntityManager entityManager;

  @BeforeEach
  void setUp() {
    entityManagerFactory = Persistence.createEntityManagerFactory("demo");
    entityManager = entityManagerFactory.createEntityManager();
  }

  @AfterEach
  void tearDown() {
    entityManager.close();
    entityManagerFactory.close();
  }

  @Test
  void query() {
    Person person = entityManager.find(Person.class, "John");

    System.out.println("*".repeat(80));
    System.out.println(person);
    System.out.println("*".repeat(80));
  }
}
```

### Create and change objects

```java
Person person = new Person("Mr.Big", 35, "M");
entityManager.persist(person);
person.changeAge(30); // automatically do UPDATE upon commit
...
```

### Use JPQL and `createQuery` to get multiple entities

* JPQL is a SQL-like query that runs against JPA entities

```java
String jpql = "SELECT person FROM Person person";

List<Person> people = entityManager
  .createQuery(jpql, Person.class) // Explicit typing for TypedQuery is preferred to Query without typing
  .getResultList();

assertEquals(2, people.size());
```

### Use `Embeddable` to utilize Value Object

* OOP prefers value objects as field's types to primitive type for easier meaning identification
  * `name` and `gender` are harder to make out since both are `String`
  * You may want to clearly define type for `gender`
* JPA's aggregate mapping support value objects as field type
* Annotate the value object's class with `@Embeddable` and
annotate the field in the parent entity with `@Embedded`

```java
@Embeddable
public class Gender {
  @Column(name = "gender")
  private String value;

  private Gender() {
  }

  // ....
```

```java
@Embedded
private Gender gender;
```

### Relationship Mapping

* Easily tap into entity relationship with relationship mapping
* To prevent side effects of misusage,
it is recommended to use with `CascadeType.ALL`and `orphanRemoval=true`.

```java
@Entity
@Table(name = "items")
public class Item {
  @Id
  @Column(name = "name")
  private String name;
  //...
```

`src/main/resources/META-INF/persistence.xml`

```xml
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" version="2.1">
  <persistence-unit name="demo">
    <class>com.example.demo.models.Person</class>
    <class>com.example.demo.models.Item</class>
```

`Person.java`

```java
@OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
@JoinColumn(name = "person_name")
@OrderBy("name")
private List<Item> items = new ArrayList<>();

//  ...

public void addItem(String name, String usage) {
  Item item = new Item(name, usage, this.name);

  items.add(item);
}

public void removeItem(String name) {
  Optional<Item> item = items.stream()
    .filter(i -> i.name().equals(name))
    .findFirst();

  if (item.isEmpty()) {
    return;
  }

  items.remove(item.get());
}

public List<Item> items() {
  return new ArrayList<>(items);
}
```

## Spring Data JPA

Spring Data JPA does away the following hassles:

* new Entity has to be included in `persistence.xml`
* Getting `EntityManager`
* Managing Transactions

### Set up dependencies

* Spring Boot DevTools
* Spring Web
* Spring Data JPA
* PostgreSQL Driver

`src/main/resources/application.yml`.

```yml
spring:
 datasource:
 url: jdbc:postgresql://localhost:5432/postgres
 username: postgres
 password: password
 ```

### Set up an interface

 ```java
 public interface PersonRepository extends CrudRepository<Person, String> {
  List<Person> findAll();
  Optional<Person> findByName(String name); // add method findByXXX for query
  Person save(Person person);
  void delete(Person person);
}
 ```

### Using `@Transactional`

* You can delineate transaction using `@Transactional` annotation, which is implemented using Spring AOP
* Application layer generally correponds to transaction area.
* In DDD(2003, Eric Evans), application layer coordinates and delegates tasks to domain objects on the layer underneath.
* Tests annoatated with `@Transactional` rollbacaks after test

```java
@Service
@Transactional
public class CreateItemService {
  private PersonRepository personRepository;

  public CreateItemService(PersonRepository personRepository) {
    this.personRepository = personRepository;
  }

  void createItem(String personName, String name, String usage) {
    Person person = personRepository.findByName(personName)
      .orElseThrow(() -> new PersonNotFound(personName));

    person.addItem(name, usage);
  }
}
```
