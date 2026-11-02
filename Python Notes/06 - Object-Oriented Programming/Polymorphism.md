# Polymorphism in Python

## Overview

**Polymorphism** is an object-oriented programming (OOP) concept that allows objects of different types to be used through a common interface while exhibiting different behaviors.

The term *polymorphism* literally means "many forms."

In Python, polymorphism enables the same operation or method call to produce different results depending on the object involved.

It allows developers to write flexible, reusable code without requiring separate handling for every object type.

## Basic Example

Consider several types of vehicles that share a common operation: moving.

```python
class Vehicle:
    def move(self):
        print("Vehicle is moving")


class Car(Vehicle):
    def move(self):
        print("Car is driving")


class Boat(Vehicle):
    def move(self):
        print("Boat is sailing")


class Plane(Vehicle):
    def move(self):
        print("Plane is flying")
```

Each derived class provides its own implementation of the `move()` method.

These objects can be processed through the same interface:

```python
vehicles = [Car(), Boat(), Plane(), Vehicle()]

for vehicle in vehicles:
    vehicle.move()
```

Output:

```text
Car is driving
Boat is sailing
Plane is flying
Vehicle is moving
```

Although the program calls the same method, `move()`, the behavior depends on the actual type of each object.

This is a typical example of polymorphism.

## How Polymorphism Works

In the previous example, every object provides a `move()` method.

When Python executes:

```python
vehicle.move()
```

It looks up the `move` attribute on the object according to Python's attribute lookup rules and method resolution order (MRO).

If the object belongs to `Car`, Python finds and executes the implementation defined in `Car`.

If the object belongs to `Boat`, Python executes the implementation defined in `Boat`.

The calling code does not need to identify the object's specific class before invoking the method.

This separation between the operation being requested and its implementation is a central benefit of polymorphism.

## Polymorphism Through Method Overriding

One common way to achieve polymorphism is through **method overriding**.

Method overriding occurs when a derived class defines a method with the same name as a method inherited from its base class.

```python
class Animal:
    def speak(self):
        print("Some sound")


class Dog(Animal):
    def speak(self):
        print("Woof!")


class Cat(Animal):
    def speak(self):
        print("Meow")
```

A function can accept different objects and invoke the same method:

```python
def make_sound(animal):
    animal.speak()


make_sound(Dog())
make_sound(Cat())
make_sound(Animal())
```

Output:

```text
Woof!
Meow
Some sound
```

The function `make_sound()` does not need separate implementations for `Dog`, `Cat`, and `Animal`.

Instead, each object determines how the requested operation behaves.

**Important:** Method overriding is a mechanism that can enable polymorphism, but the two concepts are not identical. Polymorphism describes the ability to use different objects through a common operation, while overriding describes how a derived class replaces an inherited method implementation.

## Why Polymorphism Is Useful

### Reducing Type-Specific Conditions

Without polymorphism, a program might rely on conditional statements to distinguish object types:

```python
def move_vehicle(vehicle):
    if isinstance(vehicle, Car):
        print("Car is driving")
    elif isinstance(vehicle, Boat):
        print("Boat is sailing")
    elif isinstance(vehicle, Plane):
        print("Plane is flying")
```

This design requires the function to know about individual vehicle classes.

Whenever a new vehicle type is introduced, the function may need to be modified.

With polymorphism, the implementation becomes simpler:

```python
def move_vehicle(vehicle):
    vehicle.move()
```

The function works with any object that provides a compatible `move()` method.

### Improving Extensibility

Suppose a new vehicle type is introduced:

```python
class Train(Vehicle):
    def move(self):
        print("Train is running")
```

The existing processing function does not need to change:

```python
move_vehicle(Train())
```

Output:

```text
Train is running
```

This makes software easier to extend because new implementations can be introduced without modifying code that already uses the common interface.

### Reducing Coupling

Polymorphism allows calling code to depend on a shared operation rather than a particular concrete implementation.

For example, a function that calls `send()` does not necessarily need to know whether it is working with an email notification, a text message, or another notification system.

This can reduce dependencies between software components and improve maintainability.

## Polymorphism Without Inheritance

In Python, polymorphism does not always require a shared user-defined base class.

Consider two independent classes:

```python
class PDFExporter:
    def export(self):
        print("Exporting PDF")


class CSVExporter:
    def export(self):
        print("Exporting CSV")
```

A function can work with either object:

```python
def export_document(exporter):
    exporter.export()


export_document(PDFExporter())
export_document(CSVExporter())
```

Output:

```text
Exporting PDF
Exporting CSV
```

Neither class inherits from the other, and they do not need a common user-defined base class.

The function works because both objects provide a compatible `export()` method.

This behavior is closely related to **Duck Typing**, a characteristic Python programming technique in which an object's supported behavior matters more than its explicit class hierarchy.

Duck Typing will be discussed separately in the next article.

## Polymorphism in Built-in Operations

Polymorphism is not limited to methods defined by developers.

Python's built-in operations can also behave differently depending on the objects involved.

For example, the `+` operator supports different operations for different types:

```python
print(5 + 3)
print("Hello" + " World")
print([1, 2] + [3, 4])
```

Output:

```text
8
Hello World
[1, 2, 3, 4]
```

In these examples:

- Integers use `+` for arithmetic addition.
- Strings use `+` for concatenation.
- Lists use `+` for concatenation.

The operator is the same, but its behavior depends on the operand types.

Python implements many such operations through special methods. For example, `__add__()` participates in implementing the `+` operator.

This is another form of polymorphic behavior.

However, not every operator supports every type. The operation must be supported by the objects involved.

## When to Use Polymorphism

Polymorphism is especially useful when multiple types of objects need to support a common operation but implement it differently.

Typical applications include:

- **Notification systems:** Different notification types provide a `send()` method.
- **File processing:** Different file handlers provide a `process()` method.
- **Data sources:** Different data providers expose a `get_data()` method.
- **Export systems:** Different exporters provide an `export()` method.
- **Software plugins:** Different plugins implement a common set of operations.

Polymorphism is particularly valuable when new implementations are likely to be added over time.

However, developers should avoid creating unnecessary classes or abstractions when a simpler design is sufficient.

## Common Pitfalls and Best Practices

### Maintain consistent interfaces

Methods intended to be used polymorphically should have compatible calling conventions.

For example, if one implementation requires additional arguments that the calling code does not provide, the common operation may fail.

### Preserve expected behavior

Implementations should satisfy the expectations established by the shared interface.

Providing a method with the correct name is not sufficient if its behavior violates the intended contract.

### Avoid unnecessary type checks

When objects already support a common operation, direct method calls are often preferable to repeated `isinstance()` checks.

However, type checks remain appropriate when behavior genuinely depends on type information.

### Handle unsupported operations appropriately

Python does not automatically guarantee that every object has a requested method.

Attempting to call a missing method may raise an `AttributeError`.

### Keep abstractions meaningful

A shared method should represent a coherent operation across different implementations.

Using the same method name for unrelated behaviors can make code confusing rather than flexible.

## Summary

Polymorphism allows different types of objects to respond to a common operation through their own implementations.

Key concepts include:

- **Common interface:** Different objects support the same operation.
- **Different implementations:** Each type can provide its own behavior.
- **Method overriding:** A derived class replaces an inherited method implementation.
- **Extensibility:** New object types can often be introduced without changing existing calling code.
- **Reduced coupling:** Calling code can depend on shared behavior rather than concrete types.
- **Duck Typing:** Python can support polymorphic behavior without an explicit shared class hierarchy.
- **Operator polymorphism:** Built-in operations may behave differently for different operand types.

Polymorphism is an important foundation for designing flexible, extensible, and maintainable software.