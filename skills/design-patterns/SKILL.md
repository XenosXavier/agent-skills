---
name: design-patterns
description: Design pattern selection and implementation knowledge for a developer agent. Use when facing a recurring structural problem in class design, object creation, or code architecture.
metadata:
  author: Bug.J
  version: "3.0"
---

# Design Patterns

Design patterns are proven solutions to recurring structural problems in software. They are not templates to apply by default — each pattern solves a specific problem and carries trade-offs. Use this skill to identify whether a pattern fits the current task, select the right one, and implement it correctly.

---

## Instructions

1. Identify the structural problem from the requirements before selecting any pattern
2. Scan the Pattern List below — match the "Use when" condition against the actual problem
3. For each candidate, load its reference file and read the Trade-offs and Related sections — Related is for comparison context only, do not load additional files based on it
4. Select the best-fit pattern from the candidates and implement it directly in code

---

## Misuse Signals

- MUST NOT apply a pattern when no "Use when" condition matches the actual problem
- MUST NOT apply a pattern speculatively ("we might need this later")
- MUST NOT apply more than 2 patterns in a single task
- MUST NOT use Singleton when dependency injection is available
- MUST NOT use Decorator if it breaks the interface contract of the wrapped object
- MUST NOT layer patterns on simple code that a plain class would solve

---

## Pattern List

### Creational

| Pattern | Use when | Reference |
|---------|----------|-----------|
| **Factory Method** | subclass must choose which concrete type to instantiate | [→](references/factory-method.md) |
| **Abstract Factory** | system must create families of related objects without specifying concrete classes | [→](references/abstract-factory.md) |
| **Builder** | object requires complex step-by-step construction with varying configurations | [→](references/builder.md) |
| **Prototype** | creating a new object is expensive and a similar object already exists | [→](references/prototype.md) |
| **Singleton** | exactly one instance must coordinate actions system-wide | [→](references/singleton.md) |

### Structural

| Pattern | Use when | Reference |
|---------|----------|-----------|
| **Adapter** | existing class has the right logic but the wrong interface | [→](references/adapter.md) |
| **Bridge** | abstraction and implementation must vary independently | [→](references/bridge.md) |
| **Composite** | individual objects and groups of objects must be treated uniformly | [→](references/composite.md) |
| **Decorator** | behavior must be added to individual objects dynamically without subclassing | [→](references/decorator.md) |
| **Facade** | a complex subsystem needs a single simplified entry point | [→](references/facade.md) |
| **Flyweight** | large number of similar objects are consuming too much memory | [→](references/flyweight.md) |
| **Proxy** | access to an object must be controlled, deferred, or wrapped | [→](references/proxy.md) |

### Behavioral

| Pattern | Use when | Reference |
|---------|----------|-----------|
| **Chain of Responsibility** | a request must pass through multiple handlers and the handler isn't known upfront | [→](references/chain-of-responsibility.md) |
| **Command** | actions must support undo/redo, queuing, or be passed as parameters | [→](references/command.md) |
| **Interpreter** | input follows a grammar that must be parsed and evaluated at runtime | [→](references/interpreter.md) |
| **Iterator** | elements of a collection must be traversed without exposing its internal structure | [→](references/iterator.md) |
| **Mediator** | many objects communicate directly causing tight coupling | [→](references/mediator.md) |
| **Memento** | object state must be captured and restored without exposing internals | [→](references/memento.md) |
| **Null Object** | null checks for the same object are repeated throughout the codebase | [→](references/null-object.md) |
| **Observer** | state change in one object must automatically notify an unknown number of dependents | [→](references/observer.md) |
| **State** | object behavior must change based on its internal state at runtime | [→](references/state.md) |
| **Strategy** | algorithm or behavior must be swappable at runtime | [→](references/strategy.md) |
| **Template Method** | algorithm has fixed steps but specific steps must be customizable by subclasses | [→](references/template-method.md) |
| **Visitor** | new operations must be added to a class hierarchy without modifying it | [→](references/visitor.md) |

### Architectural

| Pattern | Use when | Reference |
|---------|----------|-----------|
| **Repository** | domain logic is mixed with database queries or file I/O | [→](references/repository.md) |
| **Unit of Work** | multiple repository operations must succeed or fail as one transaction | [→](references/unit-of-work.md) |
| **CQRS** | read and write models have different performance or complexity requirements | [→](references/cqrs.md) |
| **Event Sourcing** | full history, temporal queries, or state rebuilt from events is required | [→](references/event-sourcing.md) |
| **Specification** | business rules must be composed, reused, and tested independently | [→](references/specification.md) |
