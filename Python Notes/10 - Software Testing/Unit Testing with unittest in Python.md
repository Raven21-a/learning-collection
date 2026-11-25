# Unit Testing with unittest in Python

## Overview

**Unit testing** is a software testing technique that verifies the behavior of individual units of code, such as functions, methods, or classes.

Python provides the built-in `unittest` module for creating and running automated tests.

Unit tests help developers:

- Verify that code behaves as expected.
- Detect defects during development.
- Prevent regressions when existing code is modified.
- Refactor code with greater confidence.
- Maintain software reliability as projects grow.

A passing test indicates that the code satisfies the specific conditions checked by that test. It does not guarantee that the entire program is free of defects.

## Basic Structure of unittest

A typical `unittest` test class inherits from `unittest.TestCase` and contains methods whose names begin with `test`.

Consider a module named `calculator.py`:

```python
def add(a, b):
    return a + b
```

Create a separate file named `test_calculator.py`:

```python
import unittest
from calculator import add


class TestCalculator(unittest.TestCase):
    def test_add(self):
        result = add(2, 3)
        self.assertEqual(result, 5)


if __name__ == "__main__":
    unittest.main()
```

The main components are:

- `unittest.TestCase`: A base class that provides testing methods and assertions.
- `test_add()`: A test method discovered by the testing framework.
- `assertEqual()`: An assertion that compares the actual and expected results.
- `unittest.main()`: Runs tests when the file is executed directly.

### Running Tests

Run the test file using:

```bash
python -m unittest test_calculator.py
```

Example output:

```text
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

A period (`.`) represents a successful test.

The `OK` message indicates that all executed tests passed.

The reported execution time may vary.

## Assertions

**Assertions** verify that actual results satisfy expected conditions.

The `unittest.TestCase` class provides assertion methods for different types of checks.

### Common Assertion Methods

| Method | Purpose |
|---|---|
| `assertEqual(a, b)` | Checks whether `a == b` |
| `assertNotEqual(a, b)` | Checks whether `a != b` |
| `assertTrue(x)` | Checks whether `bool(x)` is `True` |
| `assertFalse(x)` | Checks whether `bool(x)` is `False` |
| `assertIn(a, b)` | Checks whether `a` is contained in `b` |
| `assertNotIn(a, b)` | Checks whether `a` is not contained in `b` |
| `assertIsNone(x)` | Checks whether `x is None` |
| `assertIsNotNone(x)` | Checks whether `x is not None` |
| `assertIs(a, b)` | Checks whether `a` and `b` reference the same object |
| `assertRaises()` | Checks whether a specified exception is raised |

Example:

```python
import unittest


class TestAssertions(unittest.TestCase):
    def test_values(self):
        self.assertEqual(2 + 3, 5)
        self.assertTrue(10 > 5)
        self.assertIn("Python", ["Python", "Java"])
        self.assertIsNone(None)
```

If an assertion fails, the current test normally stops at that assertion and is recorded as a failure.

### Testing Exceptions with assertRaises()

Tests should verify both normal behavior and expected error conditions.

Consider the following function:

```python
def divide(a, b):
    if b == 0:
        raise ValueError("Divisor cannot be zero")

    return a / b
```

A test can verify that division by zero raises the expected exception:

```python
class TestDivision(unittest.TestCase):
    def test_divide_by_zero(self):
        with self.assertRaises(ValueError):
            divide(10, 0)
```

The test passes if `divide(10, 0)` raises `ValueError`.

If no exception is raised, the assertion fails.

If an unexpected exception type is raised, the test is generally recorded as an error.

The `assertRaises()` method is particularly useful when testing input validation and error handling.

## Organizing Test Cases

Each test should verify a clearly defined behavior.

Consider a multiplication function:

```python
def multiply(a, b):
    return a * b
```

A test class can contain several independent test methods:

```python
import unittest
from calculator import multiply


class TestCalculator(unittest.TestCase):
    def test_multiply_positive_numbers(self):
        self.assertEqual(multiply(3, 4), 12)

    def test_multiply_negative_number(self):
        self.assertEqual(multiply(-2, 5), -10)

    def test_multiply_by_zero(self):
        self.assertEqual(multiply(0, 100), 0)


if __name__ == "__main__":
    unittest.main()
```

These tests cover three different input conditions.

Using separate test methods provides several advantages:

- Each test has a clear purpose.
- Failures are easier to identify.
- Tests can be executed independently.
- A failure in one test does not normally prevent other tests from running.

### Test Naming Conventions

Test names should describe the behavior being verified.

Prefer:

```python
def test_multiply_by_zero(self):
```

Over:

```python
def test_multiply3(self):
```

Descriptive names make test suites easier to understand and maintain.

## Test Fixtures: setUp()

A **test fixture** provides the environment or resources needed to execute a test.

In `unittest`, the `setUp()` method prepares resources before each test method.

Consider a class named `Counter`:

```python
class Counter:
    def __init__(self):
        self.count = 0

    def increment(self):
        self.count += 1

    def reset(self):
        self.count = 0
```

The test class can use `setUp()` to create a fresh object for every test:

```python
import unittest
from counter import Counter


class TestCounter(unittest.TestCase):
    def setUp(self):
        self.counter = Counter()

    def test_initial_count(self):
        self.assertEqual(self.counter.count, 0)

    def test_increment(self):
        self.counter.increment()
        self.assertEqual(self.counter.count, 1)

    def test_reset(self):
        self.counter.increment()
        self.counter.reset()
        self.assertEqual(self.counter.count, 0)
```

The framework calls `setUp()` before each individual test method.

Therefore, the tests do not share the same `Counter` instance.

This helps prevent one test from affecting another through shared mutable state.

### setUp() vs. __init__()

The two methods serve different purposes:

- `__init__()` initializes an object when it is created.
- `setUp()` prepares test-specific state before each test method.

Test classes should generally use `setUp()` rather than overriding `TestCase.__init__()` for ordinary test preparation.

## Cleaning Up with tearDown()

The `tearDown()` method performs cleanup after each test method.

It is commonly used to release resources created during testing.

```python
import unittest


class TestExample(unittest.TestCase):
    def setUp(self):
        print("Setting up")

    def tearDown(self):
        print("Cleaning up")

    def test_example(self):
        print("Running test")
```

The execution sequence is:

```text
setUp()
   |
   v
test_example()
   |
   v
tearDown()
```

The framework normally calls `tearDown()` even when an assertion in the test method fails.

However, if `setUp()` raises an exception, `tearDown()` is not called for that test.

For resources requiring guaranteed cleanup after successful setup steps, `unittest` also provides cleanup registration methods such as `addCleanup()`.

### Common Cleanup Tasks

- Closing open files.
- Disconnecting from databases.
- Removing temporary resources.
- Releasing acquired system resources.

Not every test requires `tearDown()`. It should be used when explicit cleanup is necessary.

## Test Failures vs. Test Errors

The `unittest` framework distinguishes between failures and errors.

### Test Failure

A **test failure** occurs when an assertion does not match the expected result.

Example:

```python
def test_add(self):
    result = 2 - 3
    self.assertEqual(result, 5)
```

The actual result is `-1`, not `5`.

The test is recorded as a failure because the assertion is false.

### Test Error

A **test error** occurs when an unexpected exception prevents the test from completing normally.

Example:

```python
def test_division(self):
    result = 10 / 0
    self.assertEqual(result, 5)
```

Python raises a `ZeroDivisionError` before the assertion is reached.

The test is recorded as an error.

### Key Difference

- **Failure:** The executed assertion did not satisfy the expected condition.
- **Error:** An unexpected exception interrupted the test.

Both indicate that the test suite requires investigation.

## Running and Discovering Tests

The `unittest` module provides several ways to execute tests.

### Run a Specific Test File

```bash
python -m unittest test_calculator.py
```

### Run a Specific Test Class

```bash
python -m unittest test_calculator.TestCalculator
```

### Run a Specific Test Method

```bash
python -m unittest test_calculator.TestCalculator.test_multiply_by_zero
```

### Discover Tests Automatically

```bash
python -m unittest discover
```

By default, test discovery searches for Python files matching the pattern:

```text
test*.py
```

For a project with a dedicated `tests` directory, discovery can be configured explicitly:

```bash
python -m unittest discover -s tests -p "test_*.py"
```

The selected tests must be importable in the configured project environment.

## Common Testing Principles

### Test Expected Behavior

Tests should verify the externally observable behavior of a function or class rather than unnecessarily depending on its internal implementation.

### Keep Tests Independent

Tests should not rely on other tests having executed first.

Each test should prepare its own required state.

### Use Descriptive Names

Test names should clearly communicate which behavior or condition is being verified.

### Cover Edge Cases

In addition to typical inputs, consider boundary values, empty inputs, invalid values, and other relevant conditions.

### Avoid Unnecessary Complexity

A test should be easier to understand than the behavior it verifies whenever practical.

### Keep Tests Deterministic

Given the same controlled conditions, a test should consistently produce the same result.

Avoid unnecessary dependencies on time, external services, shared files, or unpredictable state.

### Do Not Modify Production Code Merely to Pass a Test

When a test fails, investigate whether the implementation, the test expectation, or both are incorrect.

The goal is correct behavior, not simply a passing test suite.

## Limitations of Unit Testing

Unit testing is an important part of software quality assurance, but it has limitations.

A test suite can only verify the scenarios and expectations represented by its tests.

Unit tests may not detect:

- Problems involving interactions between multiple components.
- Incorrect assumptions in test specifications.
- Failures caused by external systems.
- Performance bottlenecks under real workloads.
- Security vulnerabilities outside the tested scenarios.

Other forms of testing, including integration testing and end-to-end testing, are often necessary for larger applications.

Automated unit testing provides confidence, but it does not constitute a complete proof of correctness.

## Summary

Python's `unittest` module provides a structured framework for automated unit testing.

Key concepts include:

- **Unit testing:** Verifies individual functions, methods, or classes.
- **`unittest.TestCase`:** Provides the foundation for organizing tests.
- **Assertions:** Compare actual results with expected conditions.
- **`assertRaises()`:** Verifies expected exceptions.
- **`setUp()`:** Prepares test resources before each test method.
- **`tearDown()`:** Cleans up resources after a test method.
- **Test failure:** An assertion produces an unexpected result.
- **Test error:** An unexpected exception interrupts the test.
- **Test discovery:** Automatically identifies and executes eligible tests.

Effective unit testing helps developers detect defects, prevent regressions, and maintain reliable software as applications evolve.