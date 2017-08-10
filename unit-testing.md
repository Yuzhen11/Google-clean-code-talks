# Unit Testing

### How do you write hard to test code?

Hard to answer but easy to do. Do it intrinsically.

Most people say:
- Make things private
- Using final keyword
- Long methods
- ...

Real issues:
- Mixing new with logic
- looking for things
- Work in constructor
- Global state
- Singleton
- Static methods (No seam)
- Deep inheritance

Writing testable code
- Good OO
- Dependency Injection
- Test Driven Development
- A whole lot about un-testable code

There is no secret to writing test... there are only secrets to writing testable code!

Different kind of tesets

- Scenario/Large tests
- - Test whole application by pretending to be a user
- - Slow/Flaky/Mostly happy paths
- Functional/Medium Tests
- - External dependencies simulated
- - Test class interaction
-Unit/Small tests
- - Focus on application logic
- - Very Fast/ No I/O / No need for a debugger

Hard to test code: Mix object creation code and business logic.

This will assure that a test can never construct a graph of objects different from production.
Hence nothing can be tested in isolation.

### Take Away

- Unit tests are a preferred way of testing
- Unit tests require separation of instantiation of object graph from business logic under test
