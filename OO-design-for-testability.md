# OO Design for Testability

Software engineers should be responsible for writing tests. But not Quality assurance and Test engineers.

## How do you write HARD TO TEST code?

- Location of new operators
- Work in constructor
- Global state
- Law of Demeter violation (least knowledge, loose coupling)

Seam: Make coupling objects testable. (Using interface)

Separate:
- Object construction and lookup.
- Business Logic.

```java
class House {
  Kitchen kitchen = new Kitchen();
  Bedroom bedroom;
  
  House() {
    bedroom = new Bedroom();
  }
}
```
Above code is hard to test. 

Use dependency injection:

```java
class House {
  Kitchen kitchen;
  Bedroom bedroom;
  
  House(Kitchen k, Bedroom b) {
    kitchen = k;
    bedroom = b;
  }
}
```

### Cost of Construction

To test a method you first need to instantiate an object:
- Work inside constructor has no seems
- - Can't override
- - Your test must successfully navigate the constructor maze.
Do as little work in constructor as possible!

```java
class Car {
  Engine engine;
  Car(File file) {
    String model = readEngineModel(file);
    engine = new EngineFactory().create(model);
  }
}
```
```java
class Car {
  Engine engine;
  
  #Inject
  Car(Engine engine) {
    this.engine = engine;
  }
}

@provides
Engine getEngine(
    EngineFactory engineFactory,
    @EngineModel String model) {
  return engineFactory.create(model);
}
```

### Global State

Insantiy: repeating the same thing and expecting a different result.

```java
class X {
  X() {...}
  public int doSomething() {...}
}

int a = new X().doSomething();
int b = new X().doesomething();

// a == b ?
```

- Multiple Executions can produce different results
- - Flakiness
- - Order of tests matters
- - Cannot run test in parallel
- Unbounded location of state

- Hidden Global State in JVM
- - System.currentTime()
- - new Data()
- - Math.random()
- Testing above code is hard

### Deceptive API

- API that lies about what it needs
- Spooky action at a distance

```java
testCharge() {
  CreditCard cc;
  cc = new CreditCard("1232343241");
  cc.charge(100).
}
```

Spooky action at a distance! Never passed in isolation

NullPointerException...

```java
testCharge() {
  Database.connect(...);
  OfflineQueue.start();
  CreditCardProcessor.init(...);
  CreditCard cc;
  cc = new CreditCard("1232343241");
  cc.charge(100).
}
```

The code doesn't imply the order, e.g., can we call `OfflineQueue.start()` first?

Better API
```java
testCharge() {
  db = new DataBase(...);
  queue = new OfflineQueue(db);
  ccProc = new CCProcessor(queue);
  CreditCard cc;
  cc = new CreditCard("1232343241");
  cc.charge(100).
}
```

### Service Locator

- aka Context
- Better than a Singleton
- - If you had stataic look up of services this is an improvment. It is testable but it is not pretty.
- Hides true dependencies

```java
class House {
  Door door;
  Window windoe;
  Roof roof;
  
  House(Locator locator) {
    door = locator.getDoor();
    window = locator.getWindow();
    roof = locator.getRoof();
  }
}
```

```java
class House {
  Door door;
  Window windoe;
  Roof roof;
  
  House(Door d, Window w, Roof r) {
    door = d;
    window = w;
    roof = r;
  }
}
```

- Mixing Reponsibilities
- - Lookup
- - Factory
- Need to have an interface for testing
- Anything which depends on Service locator now depends on everything else.
