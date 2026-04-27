# Loops 🔁

!!! abstract "What You'll Learn"
    - ✅ How `for` loops work and when to use them
    - ✅ How `while` loops work and when to use them
    - ✅ `range()`, `enumerate()`, `zip()` and more
    - ✅ Nested loops with real examples
    - ✅ `break`, `continue`, `pass`, and `else` on loops
    - ✅ List comprehensions as a loop shortcut
    - ✅ Common mistakes and infinite loop traps

---

## 📖 Introduction

A **loop** lets you repeat a block of code multiple times without writing it over and over. Python has two main loop types — `for` for iterating over sequences, and `while` for repeating based on a condition.

!!! tip "New to Python?"
    Start with **Basics → Complexity Analysis → Data Structures → Algorithms** before jumping into practice problems.

!!! info "Already know Python?"
    Jump straight to **Data Structures** or **Algorithms** depending on what you're revising.

!!! warning "Keep in mind"
    Every topic includes real code examples, memory diagrams, and practical use cases — read them carefully, don't just skim the code.

---

## 🔁 `for` Loop

### 1️⃣ Basic `for` Loop

Iterates over any **iterable** — list, string, tuple, dict, set, range.

!!! info "Real-Life Example"
    - For each student in the class → print their name
    - For each item in the cart → calculate total

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

**Memory Visualization**

```
fruits = ["apple", "banana", "cherry"]

Iteration 1:  fruit ──► "apple"   → body runs
Iteration 2:  fruit ──► "banana"  → body runs
Iteration 3:  fruit ──► "cherry"  → body runs
              no more items       → loop ends
```

---

### 2️⃣ `range()` Function

Generates a sequence of numbers — perfect for index-based loops.

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

=== "Reverse"
    ```python
    for i in range(5, 0, -1):
        print(i, end=" ")
    ```
    **Output:**
    ```
    5 4 3 2 1
    ```

**Memory Visualization**

```
range(1, 6):

┌───┬───┬───┬───┬───┐
│ 1 │ 2 │ 3 │ 4 │ 5 │
└───┴───┴───┴───┴───┘

range() does NOT store all values in memory —
it generates them one by one (lazy evaluation) ✅
```

!!! tip "range() is Memory Efficient"
    `range(1000000)` does not create a million numbers in memory. It generates each number on demand — making it very fast and efficient.

---

### 3️⃣ `enumerate()` — Index + Value

Get both the index and value while looping.

```python
fruits = ["apple", "banana", "cherry"]

# Without enumerate ❌
for i in range(len(fruits)):
    print(i, fruits[i])

# With enumerate ✅
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")

# Start from 1
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

!!! tip "Always use `enumerate()` instead of `range(len(...))`"
    It's cleaner, more Pythonic, and less error-prone.

---

### 4️⃣ `zip()` — Loop Two Lists Together

Iterate over multiple iterables in parallel.

```python
names = ["Alice", "Bob", "Charlie"]
scores = [95, 87, 92]

for name, score in zip(names, scores):
    print(f"{name}: {score}")
```

**Output:**
```
Alice: 95
Bob: 87
Charlie: 92
```

**`zip()` with unequal lengths**

```python
a = [1, 2, 3, 4, 5]
b = ["a", "b", "c"]

for x, y in zip(a, b):
    print(x, y)
```

**Output:**
```
1 a
2 b
3 c
```

!!! warning "zip() stops at the shortest iterable"
    Items 4 and 5 from list `a` are ignored. Use `itertools.zip_longest()` if you need all elements.

---

### 5️⃣ Looping Over Different Iterables

=== "String"
    ```python
    for char in "Python":
        print(char, end="-")
    ```
    **Output:**
    ```
    P-y-t-h-o-n-
    ```

=== "Tuple"
    ```python
    colors = ("red", "green", "blue")
    for color in colors:
        print(color)
    ```
    **Output:**
    ```
    red
    green
    blue
    ```

=== "Dictionary"
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

=== "Set"
    ```python
    unique = {3, 1, 4, 1, 5, 9}
    for num in unique:
        print(num, end=" ")
    ```
    **Output:**
    ```
    1 3 4 5 9
    ```

---

### 6️⃣ Nested `for` Loops

A loop inside another loop.

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

**Star Pattern**

```python
rows = 5
for i in range(1, rows + 1):
    print("* " * i)
```

**Output:**
```
*
* *
* * *
* * * *
* * * * *
```

**Memory Visualization**

```
for i in range(1, 3):       ← outer loop
    for j in range(1, 3):   ← inner loop

i=1: j=1 → (1,1)
     j=2 → (1,2)
i=2: j=1 → (2,1)
     j=2 → (2,2)

Total iterations = outer × inner = 2 × 2 = 4
```

!!! warning "Nested Loop Complexity"
    Every extra level of nesting multiplies the number of iterations. Three nested loops over n items = O(n³) — avoid deep nesting when possible.

---

## 🔄 `while` Loop

### 7️⃣ Basic `while` Loop

Repeats as long as a condition is `True`. Use when you **don't know** how many iterations you need.

!!! info "Real-Life Example"
    - While ATM has balance → allow withdrawals
    - While user hasn't guessed correctly → keep asking

```python
count = 1

while count <= 5:
    print(count, end=" ")
    count += 1

print("\nDone!")
```

**Output:**
```
1 2 3 4 5
Done!
```

**Memory Visualization**

```
count = 1

Check: count <= 5 → 1 <= 5 → True  → print 1, count = 2
Check: count <= 5 → 2 <= 5 → True  → print 2, count = 3
Check: count <= 5 → 3 <= 5 → True  → print 3, count = 4
Check: count <= 5 → 4 <= 5 → True  → print 4, count = 5
Check: count <= 5 → 5 <= 5 → True  → print 5, count = 6
Check: count <= 5 → 6 <= 5 → False → loop ends ✅
```

---

### 8️⃣ `while True` + `break`

An intentional infinite loop that exits on a condition — common for menus and input validation.

```python
while True:
    command = input("Enter command (quit to exit): ")
    if command == "quit":
        print("Goodbye! 👋")
        break
    print(f"Running: {command}")
```

**Output:**
```
Enter command (quit to exit): hello
Running: hello
Enter command (quit to exit): quit
Goodbye! 👋
```

**Practical: Input Validation**

```python
while True:
    age = int(input("Enter your age (1-120): "))
    if 1 <= age <= 120:
        print(f"Valid age: {age} ✅")
        break
    print("Invalid! Try again ❌")
```

**Output:**
```
Enter your age (1-120): -5
Invalid! Try again ❌
Enter your age (1-120): 200
Invalid! Try again ❌
Enter your age (1-120): 25
Valid age: 25 ✅
```

!!! warning "Infinite Loop Trap"
    ```python
    # ❌ This runs forever — forgot to update count!
    count = 1
    while count <= 5:
        print(count)
        # count += 1  ← missing!

    # ✅ Always update the variable that controls the condition
    count = 1
    while count <= 5:
        print(count)
        count += 1
    ```

=== "for loop"
    ```python
    # Known number of iterations
    for i in range(10):
        print(i)
    ```

=== "while loop"
    ```python
    # Condition-based — unknown iterations
    user_input = ""
    while user_input != "quit":
        user_input = input("Enter: ")
    ```

---

## 🎛️ Loop Control

### 9️⃣ `break` — Exit Loop Early

Stops the loop immediately and jumps out.

```python
numbers = [1, 3, 7, 2, 9, 4]

for num in numbers:
    if num == 2:
        print("Found 2! Stopping.")
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
for num in [1, 3, 7, 2, 9, 4]:

  num=1 → print 1
  num=3 → print 3
  num=7 → print 7
  num=2 → BREAK ──────────────────────────┐
  num=9 → never reached                   │
  num=4 → never reached                   ▼
                                 continues after loop
```

---

### 🔟 `continue` — Skip Current Iteration

Skips the rest of the current iteration and moves to the next one.

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

**Practical: Filter Invalid Data**

```python
data = [10, -5, 20, -3, 15, 0, 8]
total = 0

for num in data:
    if num <= 0:
        continue
    total += num

print(f"Sum of positives: {total}")
```

**Output:**
```
Sum of positives: 53
```

---

### 1️⃣1️⃣ `pass` — Do Nothing

A placeholder for empty blocks. Python requires at least one statement in a block.

```python
for i in range(5):
    pass    # TODO: add logic later

print("No errors! ✅")
```

**Output:**
```
No errors! ✅
```

---

### 1️⃣2️⃣ `else` on Loops

The `else` block runs **only if the loop finished without hitting `break`**.

```python
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

**With `break` — `else` is skipped**

```python
numbers = [1, 3, 5, 6, 9]
target = 6

for num in numbers:
    if num == target:
        print(f"Found {target}!")
        break
else:
    print(f"{target} not found.")
```

**Output:**
```
Found 6!
```

!!! tip "Loop else — Perfect for Search"
    Avoids the need for a `found = False` flag variable. Clean and Pythonic.

---

## ⚡ List Comprehensions

A compact, Pythonic way to build lists using a single line instead of a loop.

### Basic Comprehension

=== "Traditional Loop"
    ```python
    squares = []
    for i in range(1, 6):
        squares.append(i ** 2)
    print(squares)
    ```
    **Output:**
    ```
    [1, 4, 9, 16, 25]
    ```

=== "List Comprehension ✅"
    ```python
    squares = [i ** 2 for i in range(1, 6)]
    print(squares)
    ```
    **Output:**
    ```
    [1, 4, 9, 16, 25]
    ```

### With Condition

```python
# Even numbers only
evens = [i for i in range(1, 11) if i % 2 == 0]
print(evens)

# Words longer than 4 chars
words = ["hi", "hello", "Python", "AI", "code"]
long_words = [w for w in words if len(w) > 4]
print(long_words)
```

**Output:**
```
[2, 4, 6, 8, 10]
['hello', 'Python']
```

### Nested Comprehension

```python
# Flatten a 2D list
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [num for row in matrix for num in row]
print(flat)
```

**Output:**
```
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

!!! warning "Don't Overuse Comprehensions"
    If your comprehension is hard to read in one line — use a regular loop. Readability > cleverness.

---

## ✅ Quick Reference Summary

| Loop / Statement | Syntax | Use When |
|-----------------|--------|----------|
| `for` | `for x in iterable:` | Known sequence to iterate |
| `while` | `while condition:` | Condition-based repetition |
| `range()` | `range(start, stop, step)` | Numeric loops |
| `enumerate()` | `for i, v in enumerate(lst):` | Need index + value |
| `zip()` | `for a, b in zip(x, y):` | Parallel iteration |
| `break` | `break` | Exit loop early |
| `continue` | `continue` | Skip current iteration |
| `pass` | `pass` | Empty block placeholder |
| `for-else` | `for ... else:` | Detect if break was hit |
| List comp | `[x for x in lst if cond]` | Build lists concisely |