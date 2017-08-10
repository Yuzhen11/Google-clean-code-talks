# Inheritance, Polymorphism and Testing

Premise: Most *if*s can be replaced by polymorphism

Why?

- Functions without ifs are easier to read
- Functions without ifs are easier to test
- Polymorphic systems are easier to maintain

Use polymorphism:

- If an object should behave differently based on its state
- If you have to check the same condition in multiple places

Use conditionals:

Mainly to do comparisons of primitive objects: >, < ==, !=
There other uses, but today we focus on avoiding if.

## State Based Behavior

Replace conditionals with polymorphism

You have a conditional that chooses different behaviors depending on the type of an object.

Move each leg of the conditional to an overriding method in a subclass. Make the original method abstract.

## Model this: 1 + 2 * 3

evaluate() computes the result of an expression

```java
class Node {
  char operator;
  double value;
  Node left;
  Node right;
  double evaluate() {
    switch (operator) {
      case '#': return value;
      case '+': return left.evaluate() + right evaluate();
      case '*': return left.evaluate() * right.evaluate();
      case ... // edit this for each new operator
    }
  }
}
```

Node: ValueNode, OpNode

```java
abstract class Node {
  abstract double evaluate();
}
class ValueNode extends Node {
  double value;
  double evaluate() {
    return value;
  }
}
class abstract OpNode extends Node {
  Node left;
  Node right;
  abstract double evaluate();
}
class AdditionNode extends OpNode {
  double evaluate() {
    return left.evaluate() + right.evaluate();
  }
}
class MultiplicationNode extends OpNode {
  double evaluate() {
    return left.evaluate() * right.evaluate();
  }
}
```

## Summary

A polymorphic solution is often better because: 

- new behavior can be added without having the original source code, and
- each opeation/concern is separated in a separate file which makes it easy to test/understand.

Prefer polymorphism over conditionals:

- switch almost always means you should use polymorphism
- *if* is more subtle.... sometimes an if is just an if

## Repeated Condition

```java
class Update {
  execute() {
    if (FLAG_i18n_ENABLED) {
      // Do A;
    } else {
      // Do B;
    }
  }
  render() {
    if (FLAG_i18n_ENABLED) {
      // Render A;
    } else {
      // Render B;
    }
  }
}
```

### Replace conditional with polymorphism

You have a conditional that chooses different behavior depending on the type of an object.

Move each leg of the conditional to an overriding method in a subclass. Make the original method abstract.

```java
abstract class Update {
  // ...
}
class I18Update entends Update {
  execute() {
    // Do A;
  }
  render() {
    // Render A;
  }
}
```

Easier for testing.

### Where did the ifs go?

```java
class Consumer {
  Consumer(update u) {...}
}
class Factory {
  Consumer build() {
    Update u = FLAG_i18n_ENABLED ?
                 new I18NUpdate()
                 : new NonI18NUpdate();
    return new Consumer(u);
  }
}
```
Dependency injection. 

# Two piles

Responsible for business logic, domain abstraction. Given the collaborators needed.

Pile of Objects:
- business logic
- the fun stuff

Responsible for building object graphs. Creates and provides collaborators (Dependency Injection).

Pile of Construction
- factories
- builders
- Provider<T>

### Benefits

- Conditional is localized in one place
- No more duplication
- Separate of responsibilities, and global state
- Common code is in one location
- Testing independently easily, and in parallel
- Looking at the subclasses makes it clear what the differences are

When to use polymorphism
- Behavior changes based on state
- Parallel conditionals are in multiple places in code
