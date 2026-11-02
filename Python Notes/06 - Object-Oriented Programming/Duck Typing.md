# Duck Typing in Python

## Overview

**Duck Typing** is a programming approach in which an object's suitability for an operation is determined by the behavior it supports rather than its explicit class or inheritance relationship.

The concept is commonly illustrated by the expression:

> If it walks like a duck and quacks like a duck, it can be treated as a duck.

In Python, this means that an object does not need to inherit from a particular class to be used in a function. It only needs to support the operations that the function requires.

Duck Typing is closely associated with Python's dynamic type system and is an important mechanism for writing flexible, reusable code.

## Basic Example

Consider two unrelated classes:

```python
class Dog:
    def speak(self):
        print("Woof!")


class Cat:
    def speak(self):
        print("Meow")
```

Although `Dog` and `Cat` do not inherit from a common user-defined base class, both provide a `speak()` method.

A single function can operate on either object:

```python
def make_sound(animal):
    animal.speak()


make_sound(Dog())
make_sound(Cat())
```

Output:

```text
Woof!
Meow
```

The function does not check whether `animal` is an instance of `Dog` or `Cat`.

Instead, it calls `speak()` directly.

As long as the supplied object provides a compatible `speak()` method, the operation can succeed.

This is the fundamental principle of Duck Typing.

## How Duck Typing Works

Python performs attribute lookup at runtime.

When the following statement is executed:

```python
animal.speak()
```

Python attempts to retrieve the `speak` attribute from the object and call it.

The result depends on whether the object supports the requested operation.

Duck Typing does not require:

- A shared user-defined base class.
- An explicit interface declaration.
- A check for a particular concrete class.

Instead, the calling code relies on the object's supported behavior.

However, a method having the correct name does not automatically guarantee correct behavior. It must also accept the required arguments and satisfy the expectations of the calling code.

## Duck Typing and Dynamic Typing

Duck Typing is closely related to **Dynamic Typing**, but the two concepts are not identical.

Dynamic Typing means that Python determines and checks the types of values during program execution rather than requiring variables to have fixed declared types.

For example:

```python
value = 10
value = "Hello"
```

The variable `value` can refer to objects of different types at different times.

Duck Typing is a design approach that takes advantage of Python's ability to work with objects based on the operations they support.

For example:

```python
def display_length(item):
    print(len(item))


display_length("Python")
display_length([1, 2, 3])
display_length({"a": 1, "b": 2})
```

Output:

```text
6
3
2
```

The function works with strings, lists, and dictionaries because all three support the `len()` operation.

It does not need to check their concrete types.

**Important:** Dynamic Typing describes how types are handled by the language, while Duck Typing describes how code can be designed around supported behavior.

## Duck Typing Without Inheritance

One of the main advantages of Duck Typing is that unrelated classes can provide compatible interfaces.

Consider two document exporters:

```python
class PDFExporter:
    def export(self, content):
        print(f"Exporting PDF: {content}")


class CSVExporter:
    def export(self, content):
        print(f"Exporting CSV: {content}")
```

A common function can work with both exporters:

```python
def save_document(exporter, content):
    exporter.export(content)


save_document(PDFExporter(), "Report")
save_document(CSVExporter(), "Report")
```

Output:

```text
Exporting PDF: Report
Exporting CSV: Report
```

Neither exporter needs to inherit from a common user-defined class.

The function simply expects an object that supports `export(content)`.

This approach can make software easier to extend because additional exporter implementations can be introduced without modifying the calling function.

## Duck Typing vs. Inheritance

Duck Typing and Inheritance can both support polymorphic behavior, but they establish compatibility in different ways.

### Inheritance

Inheritance defines an explicit relationship between classes.

```python
class Animal:
    def speak(self):
        print("Some sound")


class Dog(Animal):
    def speak(self):
        print("Woof!")
```

Here, `Dog` is explicitly derived from `Animal`.

Inheritance can provide shared implementations and organize related classes into a hierarchy.

### Duck Typing

Duck Typing focuses on whether an object supports the required behavior.

```python
class Dog:
    def speak(self):
        print("Woof!")


class Robot:
    def speak(self):
        print("Hello!")
```

Although `Dog` and `Robot` are conceptually unrelated, both can be used by code that calls `speak()`.

### Key Differences

| Aspect | Inheritance | Duck Typing |
|---|---|---|
| Primary focus | Class relationships | Supported behavior |
| Explicit parent class | Required for the inheritance relationship | Not required |
| Shared implementation | Can be inherited | Not provided automatically |
| Flexibility | Organized around class hierarchies | Organized around compatible operations |
| Typical use | Related types sharing behavior | Different types supporting a common operation |

These approaches are not mutually exclusive. Python programs frequently use both.

## Duck Typing and Polymorphism

**Polymorphism** allows different types of objects to respond to the same operation with different implementations.

Duck Typing is one way to achieve polymorphic behavior without requiring an explicit inheritance relationship.

For example:

```python
class EmailSender:
    def send(self, message):
        print(f"Email: {message}")


class SMSSender:
    def send(self, message):
        print(f"SMS: {message}")


def notify(sender, message):
    sender.send(message)
```

Both classes can be used with the same function:

```python
notify(EmailSender(), "Hello")
notify(SMSSender(), "Hello")
```

Output:

```text
Email: Hello
SMS: Hello
```

The function `notify()` depends on the `send()` operation rather than a specific sender class.

Duck Typing describes this behavior-based approach to compatibility, while polymorphism describes the ability to invoke a common operation on different object types.

## Handling Unsupported Operations

Duck Typing relies on objects supporting the requested operations.

If an object does not provide a required method, the program may raise an exception.

For example:

```python
class Printer:
    def print_document(self):
        print("Printing")


printer = Printer()
printer.send("Hello")
```

This raises an `AttributeError` because `Printer` does not provide a `send` attribute.

Even when an attribute exists, calling it may fail if it is not callable or does not accept the supplied arguments.

### Using Exception Handling

In situations where unsupported operations are expected and recoverable, exception handling may be appropriate.

```python
def notify(sender, message):
    try:
        sender.send(message)
    except AttributeError:
        print("The sender does not support this operation.")
```

However, this broad handler can also catch an `AttributeError` raised inside a valid `send()` implementation.

Therefore, exceptions should be handled at an appropriate level, with care not to conceal programming errors.

### Using `hasattr()`

Python provides the built-in `hasattr()` function to check whether an object exposes an attribute.

```python
if hasattr(sender, "send"):
    sender.send("Hello")
```

However, `hasattr()` does not guarantee that an attribute is callable, accepts the expected arguments, or behaves correctly.

In many cases, directly attempting the required operation and allowing genuine programming errors to propagate is preferable to adding unnecessary checks.

## When to Use Duck Typing

Duck Typing is useful when code should work with multiple types that provide a common behavior.

Typical applications include:

- **Document processing:** Objects providing `read()` or `write()` methods.
- **Notification systems:** Objects providing a `send()` method.
- **Export systems:** Objects providing an `export()` method.
- **Data processing:** Objects supporting iteration.
- **Plugin architectures:** Components exposing compatible operations.

Duck Typing is particularly suitable when a formal inheritance hierarchy would introduce unnecessary coupling.

However, more explicit contracts may be beneficial in larger systems where interface requirements must be documented and checked systematically.

Python provides tools such as Abstract Base Classes and typing protocols for expressing such contracts.

## Common Pitfalls and Best Practices

### Design around meaningful behavior

Functions should depend on operations that have clear and consistent purposes.

A method name alone is not a complete interface contract.

### Keep calling conventions compatible

Objects intended to support the same operation should accept compatible arguments and provide results that satisfy the caller's expectations.

### Avoid unnecessary type checks

Do not require an object to belong to a specific class when its behavior is sufficient for the operation.

However, explicit type checks remain appropriate when the program genuinely depends on type identity.

### Do not suppress unexpected errors

Avoid broad exception handling that hides failures inside otherwise valid implementations.

Errors should remain visible unless they represent expected and recoverable conditions.

### Consider explicit interfaces when needed

For larger codebases, Abstract Base Classes or typing protocols can help document and check expected behavior.

These mechanisms can complement Duck Typing rather than replace it.

## Summary

Duck Typing is a behavior-oriented approach to programming in which objects are used according to the operations they support rather than their explicit class relationships.

Key concepts include:

- **Behavior-based compatibility:** Objects are suitable when they provide the required operations.
- **Dynamic Typing:** Python checks types and resolves operations at runtime.
- **No required inheritance:** Compatible objects do not need a shared user-defined base class.
- **Polymorphism:** Different objects can respond to a common operation through their own implementations.
- **Flexible interfaces:** Calling code can depend on behavior instead of concrete types.
- **Runtime errors:** Missing or incompatible operations may raise exceptions.
- **Explicit contracts:** Abstract Base Classes and typing protocols can provide additional structure when appropriate.

Duck Typing encourages flexible software design by focusing on what objects can do rather than what classes they belong to.