[![Build Status](https://travis-ci.org/jooby-project/requery-starter.svg?branch=master)](https://travis-ci.org/jooby-project/requery-starter)
# requery

Starter kit for [Requery](https://github.com/requery/requery).

## quick preview

This project contains a simple application that:

* Use a memory database
* Insert a database record on application startup time
* Expose data via `JSON` routes.

[App.java](https://github.com/jooby-project/requery-starter/blob/master/src/main/java/starter/requery/App.java):

```java
public class App extends Jooby {

  /** The logging system. */
  private final Logger log = LoggerFactory.getLogger(getClass());

  {
    /** Connection pool: */
    use(new Jdbc());

    /** JSON: */
    use(new Jackson());

    /** Requery: */
    use(new Requery(Models.DEFAULT)
        .schema(TableCreationMode.DROP_CREATE));

    /** Save a new person on startup: */
    onStart(registry -> {
      Person person = new Person();
      person.setName("Pedro Picapiedra");
      person.setAge(42);
      person.setEmail("pedrookok@picapiedra.com");

      log.info("Saving {}", person);

      EntityStore<Persistable, Person> store = require(EntityStore.class);
      store.insert(person);
    });

    /**
     * List persons:
     */
    get("/", req -> {
      EntityStore<Persistable, Person> store = require(EntityStore.class);
      return store.select(Person.class)
          .get()
          .toList();
    });

    /**
     * Get a person by ID:
     */
    get("/:id", req -> {
      EntityStore<Persistable, Person> data = require(EntityStore.class);
      return data.select(Person.class)
          .where(Person.ID.eq(req.param("id").intValue()))
          .get()
          .first();
    });
  }

  public static void main(final String[] args) {
    run(App::new, args);
  }
}

```

## run

    mvn jooby:run

Database source code will be generated by Maven.

## help

* Read the [module documentation](http://jooby.org/doc/requery)
* Join the [channel](https://gitter.im/jooby-project/jooby)
* Join the [group](https://groups.google.com/forum/#!forum/jooby-project)