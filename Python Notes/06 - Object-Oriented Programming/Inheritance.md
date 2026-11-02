# Inheritance in Python

## Overview

**Inheritance** is an object-oriented programming (OOP) mechanism that allows a class to inherit attributes and methods from another class.

It enables developers to reuse existing implementations, extend functionality, and establish relationships between related classes.

In an inheritance relationship:

- **Base class (parent class):** The class whose functionality is inherited.
- **Derived class (child class):** The class that inherits from another class.

A derived class can use inherited methods, define additional methods and attributes, or override inherited behavior.

## Basic Syntax

Inheritance is declared by specifying a base class in parentheses after the derived class name.

```python
class Animal:
    def speak(self):
        print("Some sound")


class Dog(Animal):
    pass


dog = Dog()
dog.speak()
```

Output:

```text
Some sound
```

In this example, `Dog` inherits from `Animal`. Although `Dog` does not define `speak()`, its instances can access the method inherited from `Animal`.

The `pass` statement is a placeholder that performs no operation.

## Inheriting Attributes and Methods

A derived class can inherit an initializer and other methods from its base class.

```python
class Vehicle:
    def __init__(self, brand):
        self.brand = brand

    def show_info(self):
        print(f"Brand: {self.brand}")


class Car(Vehicle):
    pass


car = Car("Toyota")
car.show_info()
```

Output:

```text
Brand: Toyota
```

When `Car("Toyota")` is executed, Python uses the inherited initializer because `Car` does not define its own `__init__()` method.

The initializer assigns the `brand` attribute to the new instance.

**Note:** Instance attributes such as `self.brand` are created during initialization. They are not copied from the base class as class attributes.

## Method Overriding

**Method overriding** occurs when a derived class defines a method with the same name as an inherited method.

The derived implementation takes precedence when that method is called on an instance of the derived class.

```python
class Animal:
    def speak(self):
        print("Some sound")


class Dog(Animal):
    def speak(self):
        print("Woof!")


animal = Animal()
dog = Dog()

animal.speak()
dog.speak()
```

Output:

```text
Some sound
Woof!
```

The `Dog` class overrides `speak()` without modifying the original method defined in `Animal`.

Method overriding allows related classes to provide specialized behavior through a common method name.

## Extending Functionality with `super()`

The built-in `super()` function provides access to methods resolved after the current class in the method resolution order (MRO).

In a simple single-inheritance relationship, it is commonly used to call a method defined in the base class.

### Extending an Initializer

```python
class Vehicle:
    def __init__(self, brand):
        self.brand = brand


class Car(Vehicle):
    def __init__(self, brand, model):
        super().__init__(brand)
        self.model = model


car = Car("Toyota", "Corolla")

print(car.brand)
print(car.model)
```

Output:

```text
Toyota
Corolla
```

Here, `super().__init__(brand)` reuses the initialization logic inherited from `Vehicle`.

The derived class then initializes its additional `model` attribute.

This avoids duplicating initialization code.

### Extending an Inherited Method

`super()` can also be used with ordinary methods.

```python
class Vehicle:
    def show_info(self):
        print("Vehicle information")


class Car(Vehicle):
    def show_info(self):
        super().show_info()
        print("Type: Car")


car = Car()
car.show_info()
```

Output:

```text
Vehicle information
Type: Car
```

The derived method retains the base implementation and adds specialized behavior.

**Important:** `super()` does not always refer directly to the immediate parent class. Its behavior follows Python's MRO, which is particularly important in multiple inheritance.

## Types of Inheritance

Python supports several inheritance structures.

### Single Inheritance

A derived class inherits from one base class.

```python
class Animal:
    pass


class Dog(Animal):
    pass
```

### Multilevel Inheritance

A class inherits from another derived class.

```python
class Animal:
    pass


class Mammal(Animal):
    pass


class Dog(Mammal):
    pass
```

### Multiple Inheritance

A class inherits from more than one base class.

```python
class Printer:
    def print_document(self):
        print("Printing")


class Scanner:
    def scan_document(self):
        print("Scanning")


class MultiFunctionDevice(Printer, Scanner):
    pass
```

Python uses the **Method Resolution Order (MRO)** to determine where inherited methods are found.

The MRO can be inspected using:

```python
print(MultiFunctionDevice.mro())
```

Multiple inheritance offers flexibility but can increase complexity. It should be used carefully, particularly when base classes define overlapping methods.

## When to Use Inheritance

Inheritance is most appropriate when classes share a meaningful **is-a relationship**.

For example:

- A `Car` is a `Vehicle`.
- A `Dog` is an `Animal`.
- An `EmailNotification` is a `Notification`.

Inheritance is useful when:

1. Related classes share common behavior.
2. Derived classes need to specialize existing functionality.
3. A clear class hierarchy improves software organization.
4. A shared interface is useful for polymorphic behavior.

However, inheritance is not automatically the best solution for eliminating duplicate code.

### Inheritance vs. Composition

**Inheritance** models an *is-a* relationship, while **composition** models a *has-a* relationship.

For example, a car has an engine. This relationship is generally better represented through composition.

```python
class Engine:
    def start(self):
        print("Engine started")


class Car:
    def __init__(self):
        self.engine = Engine()

    def start(self):
        self.engine.start()


car = Car()
car.start()
```

Composition allows functionality to be assembled from independent objects without establishing an inheritance hierarchy.

In many software designs, composition provides greater flexibility and reduces coupling between classes.

## Common Pitfalls and Best Practices

### Avoid unnecessary inheritance

Do not create inheritance hierarchies solely because two classes have similar code. Consider whether they represent a meaningful conceptual relationship.

### Avoid excessive hierarchy depth

Deep inheritance hierarchies can make programs difficult to understand, modify, and debug.

### Preserve expected behavior

When overriding methods, maintain the behavioral expectations established by the base class. A derived class should generally remain usable wherever its base class is expected.

### Use `super()` appropriately

Use `super()` when extending cooperative inherited behavior, particularly initialization logic. It is not necessary when a method intentionally provides a complete replacement.

### Prefer composition when appropriate

If one object uses or contains another object rather than representing a specialized version of it, composition is often a better design choice.

## Summary

Inheritance is a fundamental OOP mechanism for organizing related classes and reusing existing implementations.

Key concepts include:

- **Base class:** Provides functionality that can be inherited.
- **Derived class:** Inherits and optionally extends or overrides functionality.
- **Method overriding:** Replaces inherited behavior for a derived class.
- **`super()`:** Provides access to the next implementation in the method resolution order.
- **MRO:** Determines the order in which Python searches classes for methods.
- **Composition:** An alternative design mechanism based on combining objects.

Effective inheritance requires more than understanding syntax. It requires choosing meaningful class relationships and keeping software structures understandable and maintainable.