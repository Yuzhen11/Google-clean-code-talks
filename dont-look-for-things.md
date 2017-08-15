# Ask for Things! Don't look for Things!

### Starting the Obvious

- To test a method you first need to instantiate an object
- - Specifically the SUT = System Under Test
- - And also the SUT's collaborators or Test-Double collaborators = Fakes, Dummies,
Mocks, Stubs, Spys, etc.

People put crazy things in constructor making code hard to test.

### Cost of Construction

How would I test this?

```java
class Document {
  String html;
  Document(String url) {
    HtmlClient client = new HtmlClient();
    html = client.get(url);
  }
}
```
Problems: Mixing graph construction with work. Doing work in construction.


```java
class Document {
  String html;
  Document(HtmlClient client, String url) {
    html = client.get(url);
  }
}
```

Law of Demeter: Asking for something you don't need directly only to get to what
you really want.

A better design:

```java
class Document {
  String html;
  Document(String html) {
    this.html = html;
  }
}

class Printer {
  void print(Document html) {
    // do some work here
  }
}

class DocumentFactory {
  HtmlClient client;
  DocumentFactory(HtmlClient client) {
    this.client = client;
  }
  Document build(string url) {
    return new Document(client.get(url));
  }
}
```
Easy to test since Document is easy to construct.

- Test has to successfully navigate the constructor each time instance is needed
- Objects require construction
- - Hard to construct objects are a real pain to test!

### Service Locator
Problems it attempts to solve / what it tried to do:
- Resolution of dependencies without calling new in your production code
- Inversion of Control
- Allow for looser coupling
- Interchange wiring for tests

Shortcomings:
- Service Locator encourges ambiguous APIs
- Difficulties wiring the locator up for tests
- Often uses global mutable state
- Violates the spirit of the Law of Demeter

a.k.a Context
Better than a Singleton
- If you had static look up of services this is an improvement. It is testable but
it is not pretty.
Hide dependency

```java
class House {
  House(Locator locator) {
  }
}
```
Don't know what the House needs.

```java
class House {
  House(Door d, Window w, Roof w) {
  }
}
```
This time the House has a clear API shown via the constructor.

Service Locator
- Mixing Responsibilities
- - Lookup
- - Factory
- Need to have an interface for testing
- Anything which depends on Service locator now depends on everything else

Similar Partners-in-Crime (Other names):
Registry, Locator, Context, Manager, Handler, Environment, Principle.

### Law of Demeter

Imagine you are in a store and the item you are purchasing is $25. 
- Do you give the clerk $25?
- Or do you give the clerk your wallet and let him/her retrieve $25?

- You only ask for objects which you directly need (operate on)
- a.getX().getY()... is a dead givaway.
- serviceLocator.getService() is breaking the Law of Demeter
- Dependency Injection of the specific object you need. Hollywood Principle

### Object Lifetime
- Constructor Injection:
- - Only inject objects whose lifetime is equal to or greater than the "injectee"
- Pass in as method parametes at the time of exection:
- - Objects whose lifetime is shorter than the "injectee"

## Always Ask for Things
- Abandon the new opertor (in 99% of your classes)
- - All of the new operators end up with application configuration objects (which
either works or doesn't -- integration tested)
- - Exceptions for hashmap, etc. Basic data structure.
- Since you do not construct collaborators, there is no need to know about objects
you don't directly use
- - Construction is done by DI Container, or Factories and Builders.

## Two piles

Pile of Objects
- Business logic
- This is why you're writing code
- Gets the collaborator needed

Pile of New Keywords
- Provider objects
- Factores, Builders
- This is how you get the code you write to work together
- Dependency Injection managed

When you separate your "Two Piles"
- Small tests are very easy to write and understand
- - Happen naturally when using DI
- When everything works in integration tests
- - Great!
- When the wiring is wrong
- - Failure (It often blows up)
- - Scenario tests are needed to ensure that objects are wired together properly
- When the system fails in some small way
- - Bug with specific class, missing unit test
