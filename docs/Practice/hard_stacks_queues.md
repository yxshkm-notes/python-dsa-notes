# 📚 Hard Practice Problems — Stacks & Queues

!!! abstract "What You'll Learn"
    - ✅ Solve hard-difficulty stack and queue problems
    - ✅ Apply monotonic stacks to multi-dimensional problems
    - ✅ Design complex data structures using stacks as building blocks
    - ✅ Combine stacks with dynamic programming and greedy patterns

At the hard level, stacks are rarely used alone. They power rectangle problems, expression parsers, and multi-step simulations. Deques become the backbone of O(n) window queries that would otherwise need segment trees.

!!! tip "New to Hard Problems?"
    Master the monotonic stack pattern from Medium Stacks before attempting problems 3, 4, and 7 here.

!!! info "Strategy"
    For each problem, identify: what *invariant* does the stack maintain? Writing this down first makes the code much clearer.

!!! warning "Keep in mind"
    Hard stack problems often require a sentinel (dummy element) at the base to avoid edge-case checks inside tight loops.

---

## 1️⃣ Largest Rectangle in Histogram

**Problem:** Given an array of bar heights, find the area of the largest rectangle that fits within the histogram.

**Example:**
```
Input:  heights = [2,1,5,6,2,3]
Output: 10
```

!!! tip "Hint"
    Monotonic increasing stack. When a shorter bar is encountered, pop and compute the rectangle using the popped height and distance back to the new stack top.

```python
def largest_rectangle_area(heights):
    stack = []  # indices, monotonic increasing
    max_area = 0
    heights = heights + [0]  # sentinel

    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            height = heights[stack.pop()]
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, height * width)
        stack.append(i)

    return max_area

print(largest_rectangle_area([2,1,5,6,2,3]))
```
**Output:** `10`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## 2️⃣ Maximal Rectangle

**Problem:** Given a binary matrix of `'0'`s and `'1'`s, find the largest rectangle containing only `'1'`s and return its area.

**Example:**
```
Input:
  [["1","0","1","0","0"],
   ["1","0","1","1","1"],
   ["1","1","1","1","1"],
   ["1","0","0","1","0"]]
Output: 6
```

!!! tip "Hint"
    Build a running histogram of consecutive `'1'`s for each column, row by row. Apply Largest Rectangle in Histogram on each row's histogram.

```python
def maximal_rectangle(matrix):
    if not matrix or not matrix[0]:
        return 0

    cols = len(matrix[0])
    heights = [0] * cols
    max_area = 0

    def histogram_area(h):
        stack = []
        area = 0
        h = h + [0]
        for i, val in enumerate(h):
            while stack and h[stack[-1]] > val:
                height = h[stack.pop()]
                width = i if not stack else i - stack[-1] - 1
                area = max(area, height * width)
            stack.append(i)
        return area

    for row in matrix:
        for j in range(cols):
            heights[j] = heights[j] + 1 if row[j] == '1' else 0
        max_area = max(max_area, histogram_area(heights[:]))

    return max_area

matrix = [
    ["1","0","1","0","0"],
    ["1","0","1","1","1"],
    ["1","1","1","1","1"],
    ["1","0","0","1","0"]
]
print(maximal_rectangle(matrix))
```
**Output:** `6`

| Complexity | Value |
|------------|-------|
| Time       | O(m × n) |
| Space      | O(n)     |

---

## 3️⃣ Basic Calculator II (Full Expression)

**Problem:** Evaluate a string expression containing `+`, `-`, `*`, `/` (integer division) and spaces. No parentheses.

**Example:**
```
Input:  s = " 3+5 / 2 "
Output: 5
```

!!! tip "Hint"
    Scan with a running `num`. Push to stack on `+`/`-` (with sign); compute immediately for `*`/`/` using the stack top. Sum the stack at the end.

```python
def calculate(s):
    stack = []
    num = 0
    op = '+'

    for i, ch in enumerate(s):
        if ch.isdigit():
            num = num * 10 + int(ch)
        if (not ch.isdigit() and ch != ' ') or i == len(s) - 1:
            if op == '+':
                stack.append(num)
            elif op == '-':
                stack.append(-num)
            elif op == '*':
                stack.append(stack.pop() * num)
            elif op == '/':
                stack.append(int(stack.pop() / num))  # truncate toward zero
            op = ch
            num = 0

    return sum(stack)

print(calculate("3+2*2"))
print(calculate(" 3+5 / 2 "))
```
**Output:**
```
7
5
```

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## 4️⃣ Basic Calculator (With Parentheses)

**Problem:** Evaluate a string with `+`, `-`, and `()`. No `*` or `/`.

**Example:**
```
Input:  s = "(1+(4+5+2)-3)+(6+8)"
Output: 23
```

!!! tip "Hint"
    Stack stores `(result_so_far, sign_before_paren)`. On `(`, push state and reset. On `)`, pop and combine with sign.

```python
def calculate_with_parens(s):
    stack = []
    result = 0
    num = 0
    sign = 1

    for ch in s:
        if ch.isdigit():
            num = num * 10 + int(ch)
        elif ch == '+':
            result += sign * num
            num = 0
            sign = 1
        elif ch == '-':
            result += sign * num
            num = 0
            sign = -1
        elif ch == '(':
            stack.append(result)
            stack.append(sign)
            result = 0
            sign = 1
        elif ch == ')':
            result += sign * num
            num = 0
            result *= stack.pop()   # sign before '('
            result += stack.pop()   # result before '('

    return result + sign * num

print(calculate_with_parens("(1+(4+5+2)-3)+(6+8)"))
```
**Output:** `23`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## 5️⃣ Remove K Digits

**Problem:** Given a non-negative integer string `num` and integer `k`, remove `k` digits to produce the smallest possible number.

**Example:**
```
Input:  num = "1432219", k = 3
Output: "1219"
```

!!! tip "Hint"
    Monotonic increasing stack. Pop larger digits from the stack top when the next digit is smaller and `k > 0`. Strip leading zeros.

```python
def remove_k_digits(num, k):
    stack = []
    for digit in num:
        while stack and k and stack[-1] > digit:
            stack.pop()
            k -= 1
        stack.append(digit)

    # Remove remaining k digits from the end
    if k:
        stack = stack[:-k]

    # Strip leading zeros
    return ''.join(stack).lstrip('0') or '0'

print(remove_k_digits("1432219", 3))
print(remove_k_digits("10200", 1))
print(remove_k_digits("10", 2))
```
**Output:**
```
1219
200
0
```

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## 6️⃣ Trapping Rain Water II (3D)

**Problem:** Given an `m×n` height matrix, compute the total water that can be trapped after rain (3D version).

**Example:**
```
Input:  heightMap = [[1,4,3,1,3,2],[3,2,1,3,2,4],[2,3,3,2,3,1]]
Output: 4
```

!!! tip "Hint"
    Min-heap BFS from the boundary inward. The water level at any interior cell is determined by the minimum boundary height seen so far.

```python
import heapq

def trap_rain_water(heightMap):
    if not heightMap or len(heightMap) < 3 or len(heightMap[0]) < 3:
        return 0

    rows, cols = len(heightMap), len(heightMap[0])
    visited = [[False] * cols for _ in range(rows)]
    heap = []

    # Push all boundary cells
    for r in range(rows):
        for c in range(cols):
            if r == 0 or r == rows-1 or c == 0 or c == cols-1:
                heapq.heappush(heap, (heightMap[r][c], r, c))
                visited[r][c] = True

    water = 0
    max_height = 0

    while heap:
        h, r, c = heapq.heappop(heap)
        max_height = max(max_height, h)
        for dr, dc in [(1,0),(-1,0),(0,1),(0,-1)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and not visited[nr][nc]:
                visited[nr][nc] = True
                water += max(0, max_height - heightMap[nr][nc])
                heapq.heappush(heap, (heightMap[nr][nc], nr, nc))

    return water

print(trap_rain_water([[1,4,3,1,3,2],[3,2,1,3,2,4],[2,3,3,2,3,1]]))
```
**Output:** `4`

| Complexity | Value |
|------------|-------|
| Time       | O(m × n × log(m×n)) |
| Space      | O(m × n)            |

---

## 7️⃣ Sum of Subarray Minimums

**Problem:** Given an array, return the sum of `min(subarray)` for every contiguous subarray. Answer modulo 10^9 + 7.

**Example:**
```
Input:  arr = [3,1,2,4]
Output: 17
```

!!! tip "Hint"
    For each element, find how many subarrays it is the minimum of: `left[i]` = distance to previous smaller, `right[i]` = distance to next smaller or equal. Contribution = `arr[i] * left[i] * right[i]`.

```python
def sum_subarray_mins(arr):
    MOD = 10**9 + 7
    n = len(arr)
    left = [0] * n   # number of subarrays where arr[i] is min, extending left
    right = [0] * n  # extending right

    stack = []
    for i in range(n):
        while stack and arr[stack[-1]] >= arr[i]:
            stack.pop()
        left[i] = i - stack[-1] if stack else i + 1
        stack.append(i)

    stack = []
    for i in range(n-1, -1, -1):
        while stack and arr[stack[-1]] > arr[i]:
            stack.pop()
        right[i] = stack[-1] - i if stack else n - i
        stack.append(i)

    return sum(arr[i] * left[i] * right[i] for i in range(n)) % MOD

print(sum_subarray_mins([3,1,2,4]))
```
**Output:** `17`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## 8️⃣ Maximum Frequency Stack

**Problem:** Design a `FreqStack` that supports:
- `push(val)` — push an integer
- `pop()` — remove and return the most frequent element; if tie, the most recently pushed

!!! tip "Hint"
    Two maps: `freq[val]` = count, `group[freq]` = stack of elements at that frequency. Track `max_freq` globally.

```python
from collections import defaultdict

class FreqStack:
    def __init__(self):
        self.freq = defaultdict(int)
        self.group = defaultdict(list)
        self.max_freq = 0

    def push(self, val):
        self.freq[val] += 1
        f = self.freq[val]
        self.max_freq = max(self.max_freq, f)
        self.group[f].append(val)

    def pop(self):
        val = self.group[self.max_freq].pop()
        self.freq[val] -= 1
        if not self.group[self.max_freq]:
            self.max_freq -= 1
        return val

fs = FreqStack()
fs.push(5); fs.push(7); fs.push(5); fs.push(7); fs.push(4); fs.push(5)
print(fs.pop())  # 5  (freq 3)
print(fs.pop())  # 7  (freq 2, most recent)
print(fs.pop())  # 5  (freq 2)
print(fs.pop())  # 4  (freq 1, most recent)
```
**Output:**
```
5
7
5
4
```

| Complexity | Value |
|------------|-------|
| Time       | O(1) push and pop |
| Space      | O(n)              |

---

## ✅ Quick Reference Summary

| # | Problem | Key Technique | Time | Space |
|---|---------|---------------|------|-------|
| 1 | Largest Rectangle in Histogram | Monotonic Increasing Stack | O(n) | O(n) |
| 2 | Maximal Rectangle | Histogram per Row + Stack | O(m×n) | O(n) |
| 3 | Basic Calculator II | Stack + Running Operator | O(n) | O(n) |
| 4 | Basic Calculator (Parens) | Stack for State | O(n) | O(n) |
| 5 | Remove K Digits | Monotonic Increasing Stack | O(n) | O(n) |
| 6 | Trapping Rain Water II | Min-Heap BFS | O(mn log mn) | O(mn) |
| 7 | Sum of Subarray Minimums | Contribution via Two Stacks | O(n) | O(n) |
| 8 | Maximum Frequency Stack | Freq Map + Group Stacks | O(1) | O(n) |
