# Functions 🧩

!!! abstract "What You'll Learn"
    - ✅ How to define and call functions
    - ✅ Parameters, arguments, and return values
    - ✅ Default, keyword, and arbitrary arguments (`*args`, `**kwargs`)
    - ✅ Scope — local, global, and nonlocal variables
    - ✅ Lambda functions and when to use them
    - ✅ Recursion with real examples
    - ✅ Higher-order functions — `map()`, `filter()`, `sorted()`
    - ✅ Decorators — the Pythonic superpower
    - ✅ Common mistakes to avoid

---

## 📖 Introduction

A **function** is a reusable block of code that performs a specific task. Instead of writing the same code over and over, you define it once and call it whenever you need it.

!!! tip "New to Python?"
    Start with **Basics → Complexity Analysis → Data Structures → Algorithms** before jumping into practice problems.

!!! info "Already know Python?"
    Jump straight to **Data Structures** or **Algorithms** depending on what you're revising.

!!! warning "Keep in mind"
    Every topic includes real code examples, memory diagrams, and practical use cases — read them carefully, don't just skim the code.

---

## 🧩 Defining & Calling Functions

### 1️⃣ Basic Function

```python
def greet():
    print("Hello, World! 👋")

greet()   # call the function
greet()   # call it again
```

**Output:**
```
Hello, World! 👋
Hello, World! 👋
```

**Memory Visualization**

```
def greet():               ← function object created in memory
    print("Hello!")

greet()                    ← Python looks up "greet" → finds function → executes body

Stack Frame:
┌─────────────────────┐
│   greet()  frame    │  ← created when called
│   (executes body)   │
└─────────────────────┘  ← destroyed when function returns
```

!!! info "Real-Life Example"
    A function is like a recipe 📋. You write the recipe once and can cook the dish (call the function) as many times as you want.

---

### 2️⃣ Parameters & Arguments

Pass data **into** a function using parameters.

```python
def greet(name):
    print(f"Hello, {name}! 👋")

greet("Alice")
greet("Bob")
greet("Charlie")
```

**Output:**
```
Hello, Alice! 👋
Hello, Bob! 👋
Hello, Charlie! 👋
```

**Multiple Parameters**

```python
def add(a, b):
    result = a + b
    print(f"{a} + {b} = {result}")

add(3, 5)
add(10, 20)
```

**Output:**
```
3 + 5 = 8
10 + 20 = 30
```

!!! tip "Parameter vs Argument"
    - **Parameter** — variable in the function definition: `def greet(name)`
    - **Argument** — actual value passed when calling: `greet("Alice")`

---

### 3️⃣ Return Values

Use `return` to send a value **back** to the caller.

```python
def add(a, b):
    return a + b

result = add(3, 5)
print(result)
print(add(10, 20))
```

**Output:**
```
8
30
```

**Return Multiple Values**

```python
def min_max(numbers):
    return min(numbers), max(numbers)

low, high = min_max([3, 1, 7, 2, 9, 4])
print(f"Min: {low}, Max: {high}")
```

**Output:**
```
Min: 1, Max: 9
```

**Memory Visualization**

```
result = add(3, 5)

┌────────────────────────┐
│   add() frame          │
│   a = 3, b = 5         │
│   return 8  ───────────┼──► result = 8
└────────────────────────┘
  frame destroyed after return
```

!!! warning "Functions Without return"
    ```python
    def add(a, b):
        a + b           # ❌ computed but not returned!

    result = add(3, 5)
    print(result)       # None  😱

    # ✅ Always use return
    def add(a, b):
        return a + b
    ```

---

## 🎛️ Types of Arguments

### 4️⃣ Default Arguments

Provide a default value when no argument is passed.

```python
def greet(name, message="Hello"):
    print(f"{message}, {name}!")

greet("Alice")               # uses default
greet("Bob", "Good morning") # overrides default
greet("Charlie", "Hey")
```

**Output:**
```
Hello, Alice!
Good morning, Bob!
Hey, Charlie!
```

!!! warning "Default Arguments Must Come Last"
    ```python
    # ❌ SyntaxError — default before non-default
    def greet(message="Hello", name):
        pass

    # ✅ Non-defaults first, defaults last
    def greet(name, message="Hello"):
        pass
    ```

---

### 5️⃣ Keyword Arguments

Pass arguments **by name** — order doesn't matter.

```python
def describe(name, age, city):
    print(f"{name} is {age} years old from {city}.")

# Positional
describe("Alice", 25, "Chennai")

# Keyword — any order
describe(age=25, city="Chennai", name="Alice")
describe(name="Bob", city="Mumbai", age=30)
```

**Output:**
```
Alice is 25 years old from Chennai.
Alice is 25 years old from Chennai.
Bob is 30 years old from Mumbai.
```

---

### 6️⃣ `*args` — Variable Positional Arguments

Accept **any number** of positional arguments as a tuple.

```python
def total(*args):
    print(f"Arguments: {args}")
    return sum(args)

print(total(1, 2, 3))
print(total(10, 20, 30, 40, 50))
```

**Output:**
```
Arguments: (1, 2, 3)
6
Arguments: (10, 20, 30, 40, 50)
150
```

**Memory Visualization**

```
def total(*args):

total(1, 2, 3) called:

args = (1, 2, 3)   ← packed as a tuple automatically

┌─────────────────┐
│ args[0] = 1     │
│ args[1] = 2     │
│ args[2] = 3     │
└─────────────────┘
```

---

### 7️⃣ `**kwargs` — Variable Keyword Arguments

Accept **any number** of keyword arguments as a dictionary.

```python
def profile(**kwargs):
    print(f"Data: {kwargs}")
    for key, value in kwargs.items():
        print(f"  {key}: {value}")

profile(name="Alice", age=25, city="Chennai")
profile(language="Python", level="Beginner")
```

**Output:**
```
Data: {'name': 'Alice', 'age': 25, 'city': 'Chennai'}
  name: Alice
  age: 25
  city: Chennai
Data: {'language': 'Python', 'level': 'Beginner'}
  language: Python
  level: Beginner
```

**Combining All Argument Types**

```python
def demo(a, b, *args, key="default", **kwargs):
    print(f"a={a}, b={b}")
    print(f"args={args}")
    print(f"key={key}")
    print(f"kwargs={kwargs}")

demo(1, 2, 3, 4, 5, key="custom", x=10, y=20)
```

**Output:**
```
a=1, b=2
args=(3, 4, 5)
key=custom
kwargs={'x': 10, 'y': 20}
```

!!! tip "Argument Order Rule"
    ```
    def func(positional, *args, keyword=default, **kwargs)
                  ↑          ↑          ↑              ↑
               regular   variable   keyword-only   variable
               params    positional    with         keyword
                                     default
    ```

---

## 🌍 Scope

### 8️⃣ Local vs Global Scope

```python
x = 10          # global variable

def show():
    x = 99      # local variable — separate from global x
    print(f"Inside: {x}")

show()
print(f"Outside: {x}")
```

**Output:**
```
Inside: 99
Outside: 10
```

**Memory Visualization**

```
Global Scope:
┌──────────────────────────┐
│  x = 10                  │
│  show = <function>       │
└──────────────────────────┘

show() Local Scope:         ← created when show() is called
┌──────────────────────────┐
│  x = 99  (separate!)     │
└──────────────────────────┘  ← destroyed when show() returns
```

### `global` Keyword

Modify a global variable from inside a function.

```python
count = 0

def increment():
    global count
    count += 1

increment()
increment()
increment()
print(count)
```

**Output:**
```
3
```

### `nonlocal` Keyword

Modify a variable from an **enclosing** (but not global) scope.

```python
def outer():
    x = 10

    def inner():
        nonlocal x
        x += 5
        print(f"Inner: {x}")

    inner()
    print(f"Outer: {x}")

outer()
```

**Output:**
```
Inner: 15
Outer: 15
```

!!! warning "Avoid Overusing global"
    Modifying global variables inside functions makes code hard to debug. Pass values as arguments and return results instead.

---

## ⚡ Lambda Functions

### 9️⃣ Lambda (Anonymous Functions)

A one-line function with no name — for simple, short operations.

```python
# Regular function
def square(x):
    return x ** 2

# Equivalent lambda
square = lambda x: x ** 2

print(square(5))    # 25
print(square(10))   # 100
```

**Output:**
```
25
100
```

=== "Regular Function"
    ```python
    def add(a, b):
        return a + b

    print(add(3, 5))
    ```
    **Output:**
    ```
    8
    ```

=== "Lambda ✅"
    ```python
    add = lambda a, b: a + b

    print(add(3, 5))
    ```
    **Output:**
    ```
    8
    ```

**Common Use Cases**

```python
numbers = [5, 2, 8, 1, 9, 3]

# Sort with lambda
sorted_nums = sorted(numbers, key=lambda x: x)
print(sorted_nums)

# Sort list of dicts
students = [
    {"name": "Alice", "gpa": 3.8},
    {"name": "Bob",   "gpa": 3.5},
    {"name": "Charlie", "gpa": 3.9}
]
ranked = sorted(students, key=lambda s: s["gpa"], reverse=True)
for s in ranked:
    print(f"{s['name']}: {s['gpa']}")
```

**Output:**
```
[1, 2, 3, 5, 8, 9]
Charlie: 3.9
Alice: 3.8
Bob: 3.5
```

!!! warning "Don't Overuse Lambdas"
    ```python
    # ❌ Hard to read
    result = lambda x: x**2 if x > 0 else -x**2

    # ✅ Use a regular function for complex logic
    def result(x):
        return x**2 if x > 0 else -x**2
    ```

---

## 🔁 Recursion

### 🔟 Recursive Functions

A function that **calls itself** to solve a smaller version of the same problem.

!!! info "Real-Life Example"
    Russian nesting dolls 🪆 — each doll contains a smaller doll until you reach the smallest one (base case).

**Factorial**

```python
def factorial(n):
    if n == 0 or n == 1:   # base case
        return 1
    return n * factorial(n - 1)   # recursive case

print(factorial(5))
print(factorial(0))
```

**Output:**
```
120
1
```

**Memory Visualization**

```
factorial(5)
  └─ 5 × factorial(4)
         └─ 4 × factorial(3)
                └─ 3 × factorial(2)
                       └─ 2 × factorial(1)
                              └─ returns 1   ← base case

Unwinding:
  2 × 1 = 2
  3 × 2 = 6
  4 × 6 = 24
  5 × 24 = 120  ✅
```

**Fibonacci**

```python
def fibonacci(n):
    if n <= 1:              # base case
        return n
    return fibonacci(n-1) + fibonacci(n-2)

for i in range(8):
    print(fibonacci(i), end=" ")
```

**Output:**
```
0 1 1 2 3 5 8 13
```

!!! warning "Recursion Limit"
    ```python
    import sys
    print(sys.getrecursionlimit())   # 1000 (default)

    # Deep recursion causes RecursionError!
    # For large inputs, use iteration or memoization instead.
    ```

!!! tip "Every Recursive Function Needs"
    1. **Base case** — stops the recursion
    2. **Recursive case** — calls itself with a smaller input
    Without a base case → infinite recursion → `RecursionError`!

---

## 🏗️ Higher-Order Functions

### 1️⃣1️⃣ `map()`, `filter()`, `sorted()`

Functions that take other functions as arguments.

=== "map()"
    ```python
    # Apply a function to every item
    numbers = [1, 2, 3, 4, 5]

    squares = list(map(lambda x: x**2, numbers))
    print(squares)

    # With regular function
    def double(x):
        return x * 2

    doubled = list(map(double, numbers))
    print(doubled)
    ```
    **Output:**
    ```
    [1, 4, 9, 16, 25]
    [2, 4, 6, 8, 10]
    ```

=== "filter()"
    ```python
    # Keep items where function returns True
    numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

    evens = list(filter(lambda x: x % 2 == 0, numbers))
    print(evens)

    # Filter names longer than 4 chars
    names = ["Al", "Alice", "Bob", "Charlie", "Eve"]
    long_names = list(filter(lambda n: len(n) > 4, names))
    print(long_names)
    ```
    **Output:**
    ```
    [2, 4, 6, 8, 10]
    ['Alice', 'Charlie']
    ```

=== "sorted()"
    ```python
    # Sort with custom key
    words = ["banana", "apple", "cherry", "kiwi"]

    # By length
    by_length = sorted(words, key=len)
    print(by_length)

    # Reverse alphabetical
    reverse_alpha = sorted(words, reverse=True)
    print(reverse_alpha)
    ```
    **Output:**
    ```
    ['kiwi', 'apple', 'banana', 'cherry']
    ['kiwi', 'cherry', 'banana', 'apple']
    ```

---

## 🎨 Decorators

### 1️⃣2️⃣ Function Decorators

A decorator **wraps** a function to add extra behaviour without modifying it.

```python
def shout(func):
    def wrapper():
        result = func()
        return result.upper()
    return wrapper

@shout
def greet():
    return "hello, world"

print(greet())
```

**Output:**
```
HELLO, WORLD
```

**Practical: Timer Decorator**

```python
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"⏱️ {func.__name__} took {end - start:.4f}s")
        return result
    return wrapper

@timer
def slow_add(a, b):
    time.sleep(0.1)
    return a + b

print(slow_add(3, 5))
```

**Output:**
```
⏱️ slow_add took 0.1002s
8
```

**Memory Visualization**

```
@shout
def greet(): ...

is equivalent to:

def greet(): ...
greet = shout(greet)   ← greet is replaced by wrapper

greet()  →  wrapper()  →  original greet()  →  .upper()
```

!!! tip "Common Built-in Decorators"
    | Decorator | Use |
    |-----------|-----|
    | `@staticmethod` | Method that doesn't need `self` |
    | `@classmethod` | Method that receives the class |
    | `@property` | Access method like an attribute |
    | `@functools.lru_cache` | Cache function results (memoization) |

---

## ✅ Quick Reference Summary

| Concept | Syntax | Use When |
|---------|--------|----------|
| Basic function | `def func():` | Reusable block of code |
| Parameters | `def func(a, b):` | Pass data into function |
| Return value | `return value` | Send result back to caller |
| Default args | `def func(a, b=10):` | Optional parameters |
| Keyword args | `func(a=1, b=2)` | Pass by name, any order |
| `*args` | `def func(*args):` | Unknown number of positional args |
| `**kwargs` | `def func(**kwargs):` | Unknown number of keyword args |
| Lambda | `lambda x: x**2` | Short one-line functions |
| Recursion | `def f(n): return f(n-1)` | Problem that reduces to itself |
| `global` | `global x` | Modify global var inside function |
| `nonlocal` | `nonlocal x` | Modify enclosing scope var |
| Decorator | `@decorator` | Wrap function with extra behaviour |