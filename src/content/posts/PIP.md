---
title: "PIP (Python Package)"
published: 2026-03-06
description: "Core concept about the PIP and how it works."
# image: "../../assets/images/my_photo.jpg"
tags: ["DSO101 - Containerization", "Python", "Package Manager"]
category: DevOps
draft: false
---

## Definition

**PIP** is a package manager for Python that installs and manages extra Python code. Think of it as Python's app store.

## PIP Full Form

- **P** - PIP (Recursive acronym - PIP inside PIP itself)
- **I** - Install
- **P** - Packages

## Basic Installation

```bash
python3 -m pip install flask
```

### Flag Explanation

The `-m` flag means: "Don't run a file directly; instead, find this module (PIP) inside Python and run it like a program."

If the `-m` flag is not mentioned, all the files will run instead.

## Understanding Flags

A **flag** is an extra instruction added to a command to change how it behaves. It's a special setting that modifies an action. A flag is incomplete without an argument.

### Example Workflow
```
create app → write dockerfile → build image → run container
```

---

## Programming Tasks

### Task 1: Check if Number is Odd or Even

```python
# Program to check if a number is odd or even

num = int(input("Enter a number: "))

if num % 2 == 0:
    print(f"{num} is an even number")
else:
    print(f"{num} is an odd number")
```

### Task 2: Print Numbers from 1 to 100

```python
# Program to print numbers from 1 to 100

print("Numbers from 1 to 100:")
for i in range(1, 101):
    print(i, end=" ")
```

**Alternative using while loop:**

```python
# Using while loop
num = 1
while num <= 100:
    print(num, end=" ")
    num += 1
```

### Task 3: Print Factorial of a Number

```python
# Program to calculate factorial of a number

num = int(input("Enter a number: "))
factorial = 1

if num < 0:
    print("Factorial is not defined for negative numbers")
elif num == 0:
    print(f"Factorial of {num} is 1")
else:
    for i in range(1, num + 1):
        factorial *= i
    print(f"Factorial of {num} is {factorial}")
```

**Alternative using recursion:**

```python
# Using recursion
def factorial(n):
    if n < 0:
        return "Factorial is not defined for negative numbers"
    elif n == 0 or n == 1:
        return 1
    else:
        return n * factorial(n - 1)

num = int(input("Enter a number: "))
print(f"Factorial of {num} is {factorial(num)}")
```

### Task 4: Fibonacci Sequence

```python
# Program to print Fibonacci sequence

n = int(input("How many terms do you want? "))

if n <= 0:
    print("Please enter a positive number")
elif n == 1:
    print(f"Fibonacci sequence up to {n} term: 0")
elif n == 2:
    print(f"Fibonacci sequence up to {n} terms: 0, 1")
else:
    a, b = 0, 1
    fib_sequence = [a, b]
    for i in range(n - 2):
        c = a + b
        fib_sequence.append(c)
        a, b = b, c
    print(f"Fibonacci sequence up to {n} terms: {', '.join(map(str, fib_sequence))}")
```

**Alternative using function:**

```python
# Using function
def fibonacci(n):
    if n <= 0:
        return []
    elif n == 1:
        return [0]
    
    fib_list = [0, 1]
    for i in range(2, n):
        fib_list.append(fib_list[i-1] + fib_list[i-2])
    return fib_list

n = int(input("How many terms do you want? "))
result = fibonacci(n)
print(f"Fibonacci sequence: {result}")
```

---

## Summary

| Topic | Description |
|-------|-------------|
| **PIP** | Python package manager for installing and managing libraries |
| **Module Flag `-m`** | Runs a module as a program instead of a script |
| **Odd/Even Check** | Uses modulo operator (%) to determine divisibility by 2 |
| **Range Iteration** | `range()` function generates sequence of numbers |
| **Factorial** | Product of all positive integers up to n |
| **Fibonacci** | Sequence where each number is sum of previous two |

