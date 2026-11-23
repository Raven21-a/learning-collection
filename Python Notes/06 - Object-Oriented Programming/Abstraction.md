# Abstraction in Python

## Overview

**Abstraction** is an object-oriented programming (OOP) principle that focuses on exposing essential functionality while hiding unnecessary implementation details.

It allows developers to work with objects through well-defined interfaces without needing to understand their internal operations.

For example, a payment system may provide a `pay()` method. The code using this method does not need to know how each payment provider processes transactions internally.

Python supports abstraction through several mechanisms, including ordinary classes, abstract base classes, and protocols.

One important mechanism is the **Abstract Base Class (ABC)**, which allows developers to define abstract methods that subclasses must implement before they can be instantiated.

## Abstract Base Classes

An **Abstract Base Class** defines a common interface or shared behavior for related classes.

Python provides the `abc` module for defining abstract base classes.

```python
from abc import ABC, abstractmethod


class Payment(ABC):
    @abstractmethod
    def pay(self, amount):
        pass
```

In this example:

- `ABC` is a helper class used to define abstract base classes.
- `@abstractmethod` marks `pay()` as an abstract method.
- `pass` is a placeholder that performs no operation.

The `Payment` class establishes a requirement: concrete subclasses must provide an implementation of `pay()`.

Because `Payment` contains an unimplemented abstract method, it cannot be instantiated directly.

```python
payment = Payment()
```

This raises a `TypeError`.

## Implementing Abstract Methods

A subclass becomes instantiable when it provides concrete implementations of all inherited abstract methods.

```python
class CreditCardPayment(Payment):
    def pay(self, amount):
        print(f"Paid {amount} using Credit Card")


class PayPalPayment(Payment):
    def pay(self, amount):
        print(f"Paid {amount} using PayPal")
```

Both subclasses implement the abstract `pay()` method.

They can therefore be instantiated:

```python
credit_card = CreditCardPayment()
paypal = PayPalPayment()

credit_card.pay(100)
paypal.pay(200)
```

Output:

```text
Paid 100 using Credit Card
Paid 200 using PayPal
```

The abstract base class defines the required operation, while each concrete subclass determines how that operation is performed.

These implementations simulate payment processing; they do not perform actual transactions.

## Abstract Methods and Instantiation

A subclass that does not implement all inherited abstract methods remains abstract.

Consider the following example:

```python
from abc import ABC, abstractmethod


class Animal(ABC):
    @abstractmethod
    def speak(self):
        pass


class Dog(Animal):
    def speak(self):
        print("Woof!")


class Cat(Animal):
    pass
```

The `Dog` class provides a concrete implementation of `speak()`.

```python
dog = Dog()
dog.speak()
```

Output:

```text
Woof!
```

However, `Cat` does not implement `speak()`.

```python
cat = Cat()
```

This raises a `TypeError` because `Cat` still has an unimplemented abstract method.

**Important:** Instantiating an object does not automatically invoke its ordinary methods. For example, `Dog()` creates an instance, while `dog.speak()` calls its `speak()` method.

### Multiple Abstract Methods

An abstract base class can define several abstract methods.

```python
class DataSource(ABC):
    @abstractmethod
    def connect(self):
        pass

    @abstractmethod
    def fetch(self):
        pass
```

A concrete subclass must implement both methods before it can be instantiated.

A subclass may also remain abstract intentionally, allowing further subclasses to complete the implementation.

## Abstract Methods Can Have Implementations

Abstract methods are not required to contain only `pass`.

They may provide an implementation that subclasses can reuse.

```python
from abc import ABC, abstractmethod


class Processor(ABC):
    @abstractmethod
    def process(self):
        print("Starting processing")


class FileProcessor(Processor):
    def process(self):
        super().process()
        print("Processing file")


processor = FileProcessor()
processor.process()
```

Output:

```text
Starting processing
Processing file
```

Although the base method provides an implementation, it remains abstract because it is decorated with `@abstractmethod`.

A concrete subclass must still override it.

The subclass can use `super()` to invoke the inherited implementation.

## Combining Abstract and Concrete Methods

An abstract base class can contain both abstract and concrete methods.

Abstract methods define required operations, while concrete methods provide reusable functionality.

```python
from abc import ABC, abstractmethod


class Notification(ABC):
    def log(self):
        print("Notification recorded")

    @abstractmethod
    def send(self, message):
        pass


class EmailNotification(Notification):
    def send(self, message):
        self.log()
        print(f"Email sent: {message}")


notification = EmailNotification()
notification.send("Hello")
```

Output:

```text
Notification recorded
Email sent: Hello
```

Here:

- `send()` is an abstract method that must be implemented.
- `log()` is a concrete method inherited by the subclass.

This design allows an abstract base class to define required behavior while also providing shared implementations.

## Abstraction and Polymorphism

Abstraction and polymorphism are closely related, but they serve different purposes.

**Abstraction** defines the essential operations that an object exposes.

**Polymorphism** allows different objects to respond to those operations through their own implementations.

Consider the payment classes:

```python
payments = [
    CreditCardPayment(),
    PayPalPayment()
]

for payment in payments:
    payment.pay(100)
```

Output:

```text
Paid 100 using Credit Card
Paid 100 using PayPal
```

The abstract `Payment` class establishes the `pay()` interface.

Polymorphism allows the calling code to use that interface with different concrete payment objects.

Together, these concepts help separate high-level application logic from implementation details.

## Abstract Base Classes vs. Duck Typing

Python supports behavior-based programming without requiring explicit inheritance.

This approach is commonly known as **Duck Typing**.

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

Both classes work with `notify()` because they provide a compatible `send()` method.

Neither class needs to inherit from an abstract base class.

### Key Differences

| Aspect | Abstract Base Class | Duck Typing |
|---|---|---|
| Main idea | Explicit interface contract | Behavior-based compatibility |
| Inheritance | Usually explicit | Not required |
| Method requirements | Abstract methods are checked during instantiation | Required operations are checked when used |
| Shared implementation | Supported | Not provided automatically |
| Flexibility | More structured | More flexible |
| Typical use | Related implementations with defined contracts | Objects supporting compatible operations |

An ABC can prevent instantiation when required abstract methods are missing.

However, it does not automatically validate method signatures, return types, or business behavior.

Duck Typing and abstract base classes are not mutually exclusive. They can coexist within the same application.

Python also provides `typing.Protocol` for describing structural interfaces, particularly for static type checking.

## Abstraction vs. Encapsulation

Abstraction and encapsulation are related but distinct OOP principles.

### Abstraction

Abstraction focuses on **what an object does**.

It exposes essential operations while reducing the need for callers to understand internal implementation details.

### Encapsulation

Encapsulation focuses on **how data and behavior are organized and how access to internal state is controlled**.

Python commonly uses naming conventions and properties to manage internal implementation details.

For example:

```python
class BankAccount:
    def __init__(self, balance):
        self._balance = balance

    def get_balance(self):
        return self._balance
```

The leading underscore indicates that `_balance` is intended for internal use.

However, this is a convention rather than strict access enforcement.

### Key Distinction

- **Abstraction:** Exposes essential functionality.
- **Encapsulation:** Organizes state and behavior while managing access to implementation details.

Both principles help reduce unnecessary dependencies between software components.

## When to Use Abstract Base Classes

Abstract base classes are useful when multiple related classes must follow a clearly defined interface.

Common applications include:

### Data Processing Systems

Different data sources may implement a common `fetch()` method.

### Notification Systems

Different notification providers may implement a common `send()` method.

### Payment Systems

Different payment methods may implement a common `pay()` method.

### Plugin Architectures

Plugins may be required to implement methods such as `initialize()`, `execute()`, or `close()`.

### Extensible Software Frameworks

A framework may provide shared functionality while requiring subclasses to implement specific operations.

Abstract base classes are particularly useful when interface requirements should be explicit and missing implementations should be detected during instantiation.

However, they are not necessary for every application.

Simple classes, functions, composition, or Duck Typing may provide a clearer solution when formal class hierarchies add little value.

## Common Pitfalls and Best Practices

### Avoid unnecessary abstraction

Do not introduce abstract base classes solely to make a program appear more sophisticated.

Use abstraction when it solves a meaningful design problem.

### Keep interfaces focused

An abstract base class should define a coherent set of operations.

Requiring subclasses to implement many unrelated methods can make implementations difficult to maintain.

### Preserve interface expectations

Concrete implementations should accept compatible arguments and provide behavior consistent with the interface's intended contract.

### Understand the limits of abstract methods

The `@abstractmethod` decorator enforces implementation requirements at instantiation time.

It does not guarantee that an implementation is logically correct.

### Do not confuse abstraction with inheritance

Inheritance establishes a relationship between classes.

Abstraction defines essential behavior while hiding unnecessary details.

An abstract base class uses inheritance as one mechanism for expressing a shared contract, but abstraction is a broader design principle.

### Prefer simpler designs when appropriate

Abstraction should reduce complexity for users of a component, not unnecessarily increase the complexity of the application itself.

## Summary

Abstraction is an OOP principle that separates essential functionality from unnecessary implementation details.

Key concepts include:

- **Abstraction:** Exposes essential operations while hiding implementation complexity.
- **Abstract Base Class (ABC):** Defines shared interfaces and optional reusable behavior.
- **`@abstractmethod`:** Marks methods that must be concretely implemented before instantiation.
- **Concrete subclass:** Implements all remaining abstract methods and can be instantiated.
- **`super()`:** Can reuse implementations provided by abstract base classes.
- **Polymorphism:** Allows different concrete objects to implement a shared operation differently.
- **Duck Typing:** Supports compatible behavior without requiring explicit inheritance.
- **Encapsulation:** Organizes internal state and controls how implementation details are accessed.

Effective abstraction helps developers build software that is easier to extend, test, and maintain.

The goal is not to create as many abstractions as possible, but to define clear and meaningful interfaces where they provide practical value.