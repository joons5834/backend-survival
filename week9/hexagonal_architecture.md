# Hexagonal Architecture

* Also called "Ports & Adapters"

## Motivation

* Allow an application to equally be driven by users, programs, automated test or batch scripts, and to be developed and tested in isolation from its eventual run-time devices and databases.

* Solves the issue of  entanglement between the business logic and the interaction with external entities.

## The solution

* The application communicates over "ports" to external agencies.
* There will typically be multiple adapters for any one port, for various technologies that may plug into that port.
  * graphical human interface, a test harness, a batch driver, an http interface, a direct program-to-program interface, a mock (in-memory) database, a real database ...
* In Java, ports are defined in interfaces and adapters implement them.

```java
//port
public interface NameRepository {
    String name();
}
```

```java
//adapter
public class InMemoryNameRepository implements NameRepository {
    private String name;

    public InMemoryNameRepository() {
        this.name = "Tester";
    }

    public void name() {
        return name;
    }
}
```
