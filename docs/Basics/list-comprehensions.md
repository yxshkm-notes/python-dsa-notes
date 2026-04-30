# List Comprehensions 🔥

!!! abstract "What You'll Learn"
    - ✅ What list comprehensions are and why they matter
    - ✅ Basic syntax and how it maps to a `for` loop
    - ✅ Filtering with conditions (`if`)
    - ✅ `if-else` inside comprehensions
    - ✅ Nested list comprehensions
    - ✅ Dictionary, set, and generator comprehensions
    - ✅ When to use and when NOT to use comprehensions
    - ✅ Common mistakes to avoid

---

## 📖 Introduction

A **list comprehension** is a concise, Pythonic way to create a new list by transforming or filtering items from an existing iterable — all in a single line.

!!! tip "New to Python?"
    Start with **Basics → Complexity Analysis → Data Structures → Algorithms** before jumping into practice problems.

!!! info "Already know Python?"
    Jump straight to **Data Structures** or **Algorithms** depending on what you're revising.

!!! warning "Keep in mind"
    Every topic includes real code examples, memory diagrams, and practical use cases — read them carefully, don't just skim the code.

---

## 🧠 The Big Idea

### 1️⃣ Loop → Comprehension

Every list comprehension is a shorthand for a `for` loop that builds a list.

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

**Syntax Breakdown**

```
[ expression   for   item   in   iterable ]
      ↑           ↑    ↑    ↑       ↑
  what to        loop  var  keyword source
  produce
```

**Memory Visualization**

```
squares = [i ** 2 for i in range(1, 6)]

Step by step:
i=1 → 1**2  = 1
i=2 → 2**2  = 4
i=3 → 3**2  = 9
i=4 → 4**2  = 16
i=5 → 5**2  = 25

Result: [1, 4, 9, 16, 25]
```

---

## 🔢 Basic Comprehensions

### 2️⃣ Transform Items

Apply an operation to every item in a list.

```python
numbers = [1, 2, 3, 4, 5]

# Double each number
doubled = [n * 2 for n in numbers]
print(doubled)

# Convert to string
as_strings = [str(n) for n in numbers]
print(as_strings)

# Uppercase each word
words = ["hello", "world", "python"]
uppercased = [w.upper() for w in words]
print(uppercased)

# Get lengths
lengths = [len(w) for w in words]
print(lengths)
```

**Output:**
```
[2, 4, 6, 8, 10]
['1', '2', '3', '4', '5']
['HELLO', 'WORLD', 'PYTHON']
[5, 5, 6]
```

---

### 3️⃣ Iterating Over a String

```python
# Characters of a word
chars = [c for c in "Python"]
print(chars)

# Only vowels
vowels = [c for c in "Hello World" if c in "aeiouAEIOU"]
print(vowels)

# ASCII values
ascii_vals = [ord(c) for c in "ABC"]
print(ascii_vals)
```

**Output:**
```
['P', 'y', 't', 'h', 'o', 'n']
['e', 'o', 'o']
[65, 66, 67]
```

---

## 🔍 Filtering with Conditions

### 4️⃣ `if` Filter

Add a condition to **include only matching items**.

```python
[ expression for item in iterable if condition ]
```

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Even numbers only
evens = [n for n in numbers if n % 2 == 0]
print(evens)

# Numbers greater than 5
big = [n for n in numbers if n > 5]
print(big)

# Both conditions
even_and_big = [n for n in numbers if n % 2 == 0 and n > 5]
print(even_and_big)
```

**Output:**
```
[2, 4, 6, 8, 10]
[6, 7, 8, 9, 10]
[6, 8, 10]
```

**Practical: Filter a List of Words**

```python
words = ["apple", "hi", "banana", "ok", "cherry", "Python", "AI"]

# Words longer than 4 characters
long_words = [w for w in words if len(w) > 4]
print(long_words)

# Words that start with a vowel
vowel_words = [w for w in words if w[0].lower() in "aeiou"]
print(vowel_words)

# Non-empty strings
data = ["Alice", "", "Bob", "", "Charlie"]
cleaned = [s for s in data if s]
print(cleaned)
```

**Output:**
```
['apple', 'banana', 'cherry', 'Python']
['apple', 'ok']
['Alice', 'Bob', 'Charlie']
```

---

### 5️⃣ `if-else` Expression

Transform items differently based on a condition — note the `if-else` goes **before** the `for`.

```python
[ value_if_true if condition else value_if_false for item in iterable ]
```

```python
numbers = [1, 2, 3, 4, 5, 6]

# Label even/odd
labels = ["even" if n % 2 == 0 else "odd" for n in numbers]
print(labels)

# Replace negatives with 0
data = [5, -3, 8, -1, 0, 7, -2]
cleaned = [n if n > 0 else 0 for n in data]
print(cleaned)

# Absolute values
absolutes = [n if n >= 0 else -n for n in data]
print(absolutes)
```

**Output:**
```
['odd', 'even', 'odd', 'even', 'odd', 'even']
[5, 0, 8, 0, 0, 7, 0]
[5, 3, 8, 1, 0, 7, 2]
```

!!! warning "`if` position matters!"
    ```python
    # Filter (if AFTER for) — keeps matching items only
    evens = [n for n in range(10) if n % 2 == 0]

    # Transform (if-else BEFORE for) — processes ALL items
    labels = ["even" if n % 2 == 0 else "odd" for n in range(10)]
    ```

---

## 🔲 Nested Comprehensions

### 6️⃣ Nested Loops in Comprehension

```python
[ expression for outer in iterable for inner in iterable ]
```

**Flatten a 2D List**

=== "Traditional Loop"
    ```python
    matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
    flat = []
    for row in matrix:
        for num in row:
            flat.append(num)
    print(flat)
    ```
    **Output:**
    ```
    [1, 2, 3, 4, 5, 6, 7, 8, 9]
    ```

=== "Nested Comprehension ✅"
    ```python
    matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
    flat = [num for row in matrix for num in row]
    print(flat)
    ```
    **Output:**
    ```
    [1, 2, 3, 4, 5, 6, 7, 8, 9]
    ```

**All Combinations (Cartesian Product)**

```python
colors = ["red", "blue"]
sizes  = ["S", "M", "L"]

combos = [(color, size) for color in colors for size in sizes]
for c in combos:
    print(c)
```

**Output:**
```
('red', 'S')
('red', 'M')
('red', 'L')
('blue', 'S')
('blue', 'M')
('blue', 'L')
```

**Memory Visualization**

```
[num for row in matrix for num in row]

outer loop: row = [1,2,3] → inner: num=1,2,3
outer loop: row = [4,5,6] → inner: num=4,5,6
outer loop: row = [7,8,9] → inner: num=7,8,9

Result: [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

---

### 7️⃣ Generate a Matrix (Nested List)

Build a 2D list using a nested comprehension.

```python
# 3×3 matrix of zeros
matrix = [[0 for _ in range(3)] for _ in range(3)]
print(matrix)

# Multiplication table
table = [[i * j for j in range(1, 6)] for i in range(1, 6)]
for row in table:
    print(row)
```

**Output:**
```
[[0, 0, 0], [0, 0, 0], [0, 0, 0]]
[1, 2, 3, 4, 5]
[2, 4, 6, 8, 10]
[3, 6, 9, 12, 15]
[4, 8, 12, 16, 20]
[5, 10, 15, 20, 25]
```

!!! warning "Don't Create 2D Lists Like This"
    ```python
    # ❌ All rows share the SAME list object!
    matrix = [[0] * 3] * 3
    matrix[0][0] = 99
    print(matrix)  # [[99, 0, 0], [99, 0, 0], [99, 0, 0]] 😱

    # ✅ Use comprehension — each row is independent
    matrix = [[0] * 3 for _ in range(3)]
    matrix[0][0] = 99
    print(matrix)  # [[99, 0, 0], [0, 0, 0], [0, 0, 0]] ✅
    ```

---

## 🗂️ Other Comprehension Types

### 8️⃣ Dictionary Comprehension

```python
{ key_expr: value_expr for item in iterable }
```

```python
# Square mapping
squares = {n: n**2 for n in range(1, 6)}
print(squares)

# Flip keys and values
original = {"a": 1, "b": 2, "c": 3}
flipped = {v: k for k, v in original.items()}
print(flipped)

# Filter a dict — keep only passing grades
grades = {"Alice": 85, "Bob": 42, "Charlie": 91, "Dave": 55}
passing = {name: score for name, score in grades.items() if score >= 60}
print(passing)
```

**Output:**
```
{1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
{1: 'a', 2: 'b', 3: 'c'}
{'Alice': 85, 'Charlie': 91}
```

---

### 9️⃣ Set Comprehension

```python
{ expression for item in iterable }
```

```python
# Unique squares
numbers = [1, -1, 2, -2, 3, 3, 4]
unique_squares = {n**2 for n in numbers}
print(unique_squares)

# Unique first letters
words = ["apple", "avocado", "banana", "blueberry", "cherry"]
first_letters = {w[0] for w in words}
print(first_letters)
```

**Output:**
```
{1, 4, 9, 16}
{'a', 'b', 'c'}
```

---

### 🔟 Generator Expression

Like a list comprehension but **lazy** — generates items one at a time without storing them all in memory.

=== "List Comprehension"
    ```python
    # Stores ALL values in memory at once
    squares = [n**2 for n in range(1_000_000)]
    print(type(squares))   # <class 'list'>
    ```

=== "Generator Expression ✅"
    ```python
    # Generates values on demand — memory efficient
    squares = (n**2 for n in range(1_000_000))
    print(type(squares))   # <class 'generator'>

    print(next(squares))   # 0
    print(next(squares))   # 1
    print(next(squares))   # 4
    ```

**Memory Visualization**

```
List comprehension:     Generator expression:
[n**2 for n in range(5)]    (n**2 for n in range(5))

Memory:                     Memory:
┌───┬───┬────┬────┬────┐    ┌──────────────────┐
│ 0 │ 1 │  4 │  9 │ 16 │    │ state: i=0       │ ← only current state
└───┴───┴────┴────┴────┘    └──────────────────┘
  All 5 values stored          One value at a time
```

!!! tip "Use Generator When"
    - Processing large datasets
    - You only need to iterate once
    - Memory efficiency matters (e.g. reading huge files)

---

## ⚠️ When NOT to Use Comprehensions

### 1️⃣1️⃣ Readability First

!!! warning "Don't Sacrifice Readability"
    ```python
    # ❌ Too complex — hard to read
    result = [x**2 for x in range(100) if x % 2 == 0 if x % 3 == 0]

    # ✅ Regular loop is clearer here
    result = []
    for x in range(100):
        if x % 2 == 0 and x % 3 == 0:
            result.append(x**2)

    # ❌ Deeply nested — nightmare to read
    flat = [val for sublist in [[[1,2],[3,4]],[[5,6]]] for inner in sublist for val in inner]

    # ✅ Use regular loops for complex nesting
    ```

**Rule of Thumb:**

```
One condition  → Comprehension ✅
Two conditions → Maybe ⚠️
Three or more  → Regular loop ✅
```

---

## 🛠️ Practical Examples

### 1️⃣2️⃣ Real-World Use Cases

**Remove Duplicates (preserving order)**

```python
seen = set()
data = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3]
unique = [x for x in data if not (x in seen or seen.add(x))]
print(unique)
```

**Output:**
```
[3, 1, 4, 5, 9, 2, 6]
```

**Parse CSV Row**

```python
row = "Alice,25,Chennai,3.8"
fields = [f.strip() for f in row.split(",")]
print(fields)
```

**Output:**
```
['Alice', '25', 'Chennai', '3.8']
```

**Find Common Elements**

```python
a = [1, 2, 3, 4, 5]
b = [3, 4, 5, 6, 7]

common = [x for x in a if x in b]
print(common)
```

**Output:**
```
[3, 4, 5]
```

**Transpose a Matrix**

```python
matrix = [[1, 2, 3],
          [4, 5, 6],
          [7, 8, 9]]

transposed = [[row[i] for row in matrix] for i in range(3)]
for row in transposed:
    print(row)
```

**Output:**
```
[1, 4, 7]
[2, 5, 8]
[3, 6, 9]
```

---

## ✅ Quick Reference Summary

| Type | Syntax | Output |
|------|--------|--------|
| Basic | `[expr for x in iterable]` | `list` |
| With filter | `[expr for x in iterable if cond]` | `list` |
| With transform | `[a if cond else b for x in iterable]` | `list` |
| Nested loop | `[expr for x in a for y in b]` | `list` |
| Nested list | `[[expr for y in b] for x in a]` | `list of lists` |
| Dict comprehension | `{k: v for x in iterable}` | `dict` |
| Set comprehension | `{expr for x in iterable}` | `set` |
| Generator | `(expr for x in iterable)` | `generator` |