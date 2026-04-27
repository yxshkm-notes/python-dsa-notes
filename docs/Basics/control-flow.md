# Control Flow 🔀

!!! abstract "What You'll Learn"
    - ✅ How Python makes decisions with `if`, `elif`, `else`
    - ✅ Ternary (conditional) expressions
    - ✅ `match` statement (Python 3.10+)
    - ✅ `for` and `while` loops with real examples
    - ✅ `break`, `continue`, `pass`, and `else` on loops
    - ✅ Nested conditions and common mistakes to avoid

---

## 📖 Introduction

Control flow is how a program decides **what to do next**. By default, Python executes code line by line from top to bottom. Control flow statements let you **branch**, **repeat**, and **skip** code based on conditions.

!!! tip "New to Python?"
    Start with **Basics → Complexity Analysis → Data Structures → Algorithms** before jumping into practice problems.

!!! info "Already know Python?"
    Jump straight to **Data Structures** or **Algorithms** depending on what you're revising.

!!! warning "Keep in mind"
    Every topic includes real code examples, memory diagrams, and practical use cases — read them carefully, don't just skim the code.

---

## 🔀 Conditionals

### 1️⃣ `if` / `elif` / `else`

The most fundamental decision-making structure in Python.

!!! info "Real-Life Example"
    - If it's raining → take an umbrella
    - Elif it's cloudy → maybe take one
    - Else → enjoy the sun ☀️

**Basic Syntax**

```python
age = 20

if age < 13:
    print("Child")
elif age < 18:
    print("Teenager")
elif age < 60:
    print("Adult")
else:
    print("Senior")
```

**Output:**
```
Adult
```

**How Python Evaluates It**

```
age = 20

if age < 13  → False  ✗ skip
elif age < 18 → False  ✗ skip
elif age < 60 → True   ✓ execute → print "Adult"
else          → skipped (a branch already ran)
```

**Practical Example: Grade System**

```python
marks = int(input("Enter your marks (0-100): "))

if marks >= 90:
    grade = "A"
    remark = "🌟 Excellent!"
elif marks >= 80:
    grade = "B"
    remark = "👍 Good!"
elif marks >= 70:
    grade = "C"
    remark = "😊 Average"
elif marks >= 60:
    grade = "D"
    remark = "😐 Below Average"
elif marks >= 0:
    grade = "F"
    remark = "😞 Fail"
else:
    grade = "?"
    remark = "❌ Invalid marks"

print(f"Grade: {grade} — {remark}")
```

**Output:**
```
Enter your marks (0-100): 85
Grade: B — 👍 Good!
```

**Nested `if`**

```python
num = 15

if num > 0:
    print("Positive")
    if num % 2 == 0:
        print("and Even")
    else:
        print("and Odd")
else:
    print("Non-positive")
```

**Output:**
```
Positive
and Odd
```

!!! warning "Indentation is Everything"
    ```python
    # ✅ Correct
    if True:
        print("inside if")

    # ❌ IndentationError
    if True:
    print("no indent")  # IndentationError!
    ```
    Python uses **indentation** (4 spaces) instead of `{}` braces. Getting this wrong crashes your program.

!!! tip "Comparison Operators"
    | Operator | Meaning | Example |
    |----------|---------|---------|
    | `==` | Equal to | `x == 5` |
    | `!=` | Not equal | `x != 5` |
    | `>` | Greater than | `x > 5` |
    | `<` | Less than | `x < 5` |
    | `>=` | Greater or equal | `x >= 5` |
    | `<=` | Less or equal | `x <= 5` |

---

### 2️⃣ Logical Operators (`and`, `or`, `not`)

Combine multiple conditions into one.

```python
age = 25
has_id = True

# and — both must be True
if age >= 18 and has_id:
    print("Entry allowed ✅")

# or — at least one must be True
is_weekend = False
is_holiday = True
if is_weekend or is_holiday:
    print("Day off! 🎉")

# not — flips True/False
is_raining = False
if not is_raining:
    print("Go for a walk 🚶")
```

**Output:**
```
Entry allowed ✅
Day off! 🎉
Go for a walk 🚶
```

**Truth Table**

```
AND:                    OR:                     NOT:
┌───────┬───────┬───┐  ┌───────┬───────┬───┐  ┌───────┬───────┐
│   A   │   B   │A∧B│  │   A   │   B   │A∨B│  │   A   │  ¬A   │
├───────┼───────┼───┤  ├───────┼───────┼───┤  ├───────┼───────┤
│ True  │ True  │ T │  │ True  │ True  │ T │  │ True  │ False │
│ True  │ False │ F │  │ True  │ False │ T │  │ False │ True  │
│ False │ True  │ F │  │ False │ True  │ T │  └───────┴───────┘
│ False │ False │ F │  │ False │ False │ F │
└───────┴───────┴───┘  └───────┴───────┴───┘
```

**Short-Circuit Evaluation**

```python
# 'and' stops at first False
x = 0
if x != 0 and 10 / x > 1:   # safe! — stops at x != 0
    print("big")

# 'or' stops at first True
name = "Alice"
if name or input("Enter name: "):  # stops at name — no input needed
    print(f"Hello, {name}")
```

---

### 3️⃣ Ternary Expression

A one-line `if-else` for simple conditions.

```python
# Syntax: value_if_true if condition else value_if_false

age = 20
status = "Adult" if age >= 18 else "Minor"
print(status)

# Nested ternary (use sparingly!)
score = 75
grade = "A" if score >= 90 else "B" if score >= 80 else "C"
print(grade)
```

**Output:**
```
Adult
C
```

=== "Traditional if-else"
    ```python
    age = 20
    if age >= 18:
        status = "Adult"
    else:
        status = "Minor"
    print(status)
    ```

=== "Ternary Expression ✅"
    ```python
    age = 20
    status = "Adult" if age >= 18 else "Minor"
    print(status)
    ```

!!! warning "Don't Overuse Ternary"
    Keep ternary expressions simple. If you need more than one `else`, use a regular `if-elif-else` for readability.

---

### 4️⃣ `match` Statement (Python 3.10+)

Python's version of `switch-case` — cleaner than long `elif` chains.

```python
command = "quit"

match command:
    case "start":
        print("Starting the engine 🚀")
    case "stop":
        print("Stopping... 🛑")
    case "pause":
        print("Paused ⏸️")
    case "quit" | "exit":
        print("Goodbye! 👋")
    case _:
        print(f"Unknown command: {command}")
```

**Output:**
```
Goodbye! 👋
```

**Match with Values**

```python
point = (0, 1)

match point:
    case (0, 0):
        print("Origin")
    case (x, 0):
        print(f"On X-axis at {x}")
    case (0, y):
        print(f"On Y-axis at {y}")
    case (x, y):
        print(f"Point at ({x}, {y})")
```

**Output:**
```
On Y-axis at 1
```

!!! info "match vs if-elif"
    | | `match` | `if-elif` |
    |-|---------|-----------|
    | Python version | 3.10+ only | All versions |
    | Pattern matching | ✅ Powerful | ❌ Basic |
    | Readability | ✅ Cleaner | Verbose for many cases |
    | Use case | Multiple exact values | Complex conditions |

---

## 🔁 Loops

### 5️⃣ `for` Loop

Iterates over any **iterable** — list, string, range, dict, etc.

!!! info "Real-Life Example"
    - For each student in the class → print their name
    - For each item in cart → calculate total price

**Basic Syntax**

```python
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(fruit)
```

**Output:**
```
apple
banana
cherry
```

**`range()` Function**

=== "range(stop)"
    ```python
    for i in range(5):
        print(i, end=" ")
    ```
    **Output:**
    ```
    0 1 2 3 4
    ```

=== "range(start, stop)"
    ```python
    for i in range(1, 6):
        print(i, end=" ")
    ```
    **Output:**
    ```
    1 2 3 4 5
    ```

=== "range(start, stop, step)"
    ```python
    for i in range(0, 10, 2):
        print(i, end=" ")
    ```
    **Output:**
    ```
    0 2 4 6 8
    ```

=== "Reverse range"
    ```python
    for i in range(5, 0, -1):
        print(i, end=" ")
    ```
    **Output:**
    ```
    5 4 3 2 1
    ```

**`enumerate()` — Index + Value**

```python
fruits = ["apple", "banana", "cherry"]

for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")

# Start index from 1
for index, fruit in enumerate(fruits, start=1):
    print(f"{index}. {fruit}")
```

**Output:**
```
0: apple
1: banana
2: cherry
1. apple
2. banana
3. cherry
```

**Iterating Over Strings**

```python
for char in "Python":
    print(char, end="-")
```

**Output:**
```
P-y-t-h-o-n-
```

**Iterating Over Dictionaries**

```python
student = {"name": "Alice", "age": 20, "gpa": 3.8}

for key in student:
    print(key)

for key, value in student.items():
    print(f"{key}: {value}")
```

**Output:**
```
name
age
gpa
name: Alice
age: 20
gpa: 3.8
```

**Nested `for` Loops**

```python
# Multiplication table
for i in range(1, 4):
    for j in range(1, 4):
        print(f"{i}×{j}={i*j}", end="  ")
    print()
```

**Output:**
```
1×1=1  1×2=2  1×3=3
2×1=2  2×2=4  2×3=6
3×1=3  3×2=6  3×3=9
```

**Memory Visualization**

```
for fruit in ["apple", "banana", "cherry"]:

Iteration 1:  fruit → "apple"   → body executes
Iteration 2:  fruit → "banana"  → body executes
Iteration 3:  fruit → "cherry"  → body executes
              (no more items)   → loop ends
```

---

### 6️⃣ `while` Loop

Repeats as long as a condition is `True`. Use when you **don't know** how many times to loop.

!!! info "Real-Life Example"
    - While the ATM has balance → allow withdrawals
    - While user hasn't guessed correctly → keep asking

**Basic Syntax**

```python
count = 1

while count <= 5:
    print(count, end=" ")
    count += 1

print("Done!")
```

**Output:**
```
1 2 3 4 5 Done!
```

**Practical Example: Number Guessing Game**

```python
import random

secret = random.randint(1, 10)
attempts = 0

while True:
    guess = int(input("Guess (1-10): "))
    attempts += 1

    if guess < secret:
        print("Too low! 📉")
    elif guess > secret:
        print("Too high! 📈")
    else:
        print(f"🎉 Correct! Got it in {attempts} attempts!")
        break
```

**Output:**
```
Guess (1-10): 5
Too low! 📉
Guess (1-10): 8
Too high! 📈
Guess (1-10): 6
🎉 Correct! Got it in 3 attempts!
```

!!! warning "Infinite Loop"
    ```python
    # ❌ This runs forever — forgot to update count!
    count = 1
    while count <= 5:
        print(count)
        # count += 1  ← missing!

    # ✅ Always make sure the condition eventually becomes False
    count = 1
    while count <= 5:
        print(count)
        count += 1
    ```

=== "for loop"
    ```python
    # Use when you know how many times
    for i in range(10):
        print(i)
    ```

=== "while loop"
    ```python
    # Use when condition-based
    user_input = ""
    while user_input != "quit":
        user_input = input("Enter command: ")
    ```

---

## 🎛️ Loop Control Statements

### 7️⃣ `break`

Exits the loop **immediately**, regardless of the condition.

```python
numbers = [1, 3, 7, 2, 9, 4, 6]

for num in numbers:
    if num == 2:
        print(f"Found 2! Stopping.")
        break
    print(num)
```

**Output:**
```
1
3
7
Found 2! Stopping.
```

**Memory Visualization**

```
for num in [1, 3, 7, 2, 9, 4, 6]:

  num=1 → print(1)
  num=3 → print(3)
  num=7 → print(7)
  num=2 → condition True → BREAK ──────────────────┐
  num=9 → never reached                             │
  num=4 → never reached                             │
  num=6 → never reached                             │
                                                    ▼
                                          continues after loop
```

---

### 8️⃣ `continue`

Skips the **current iteration** and jumps to the next one.

```python
for i in range(1, 11):
    if i % 2 == 0:
        continue        # skip even numbers
    print(i, end=" ")
```

**Output:**
```
1 3 5 7 9
```

**Practical: Skip Invalid Input**

```python
data = [10, -5, 20, -3, 15, 0, 8]

total = 0
for num in data:
    if num <= 0:
        continue        # skip non-positive
    total += num

print(f"Sum of positives: {total}")
```

**Output:**
```
Sum of positives: 53
```

---

### 9️⃣ `pass`

Does absolutely nothing — a **placeholder** for empty blocks.

```python
# Placeholder for future code
for i in range(5):
    pass    # TODO: add logic later

# Empty function body
def my_function():
    pass

# Empty class
class MyClass:
    pass

print("No errors! ✅")
```

**Output:**
```
No errors! ✅
```

!!! info "Why `pass`?"
    Python doesn't allow empty blocks — an `if`, `for`, `def`, or `class` with no body causes a `SyntaxError`. `pass` is the fix.

---

### 🔟 `else` on Loops

Python has a unique feature — `else` on loops. The `else` block runs **only if the loop completed without `break`**.

```python
# Search for a number
numbers = [1, 3, 5, 7, 9]
target = 6

for num in numbers:
    if num == target:
        print(f"Found {target}!")
        break
else:
    print(f"{target} not found in list.")
```

**Output:**
```
6 not found in list.
```

**With `break` (else skipped)**

```python
numbers = [1, 3, 5, 6, 9]
target = 6

for num in numbers:
    if num == target:
        print(f"Found {target}!")
        break
else:
    print(f"{target} not found.")   # skipped because break ran
```

**Output:**
```
Found 6!
```

**`while-else`**

```python
attempts = 0
max_attempts = 3

while attempts < max_attempts:
    password = input("Enter password: ")
    if password == "secret123":
        print("✅ Access granted!")
        break
    attempts += 1
else:
    print("❌ Too many failed attempts. Locked out!")
```

!!! tip "Loop else — When to Use"
    Perfect for **search loops** where you want to know if something was found or not, without using a flag variable.

---

## ✅ Quick Reference Summary

| Statement | Purpose | Runs when |
|-----------|---------|-----------|
| `if` | Check condition | condition is `True` |
| `elif` | Alternative condition | previous was `False`, this is `True` |
| `else` | Fallback | all above were `False` |
| `match` | Pattern matching (3.10+) | value matches a `case` |
| `for` | Iterate over iterable | each item in sequence |
| `while` | Repeat while condition | condition is `True` |
| `break` | Exit loop early | immediately on execution |
| `continue` | Skip current iteration | immediately on execution |
| `pass` | Do nothing (placeholder) | immediately, no effect |
| `for-else` | Run after loop | loop finished without `break` |