# Easy Practice Problems

!!! abstract "What You'll Learn"
    - ✅ Solve 20 classic easy-level problems across all major data structures
    - ✅ Recognise problem patterns before writing a single line of code
    - ✅ Understand the brute-force approach, then optimise it
    - ✅ Know the time and space complexity of every solution
    - ✅ Build the problem-solving muscle memory needed for interviews

These are the problems that appear in **every** interview loop — at FAANG and startups alike. Nail these before moving to medium. Each problem follows the same structure: pattern recognition → brute force → optimal solution → complexity.

!!! tip "How to use this page"
    Read the problem, **try it yourself first**, then reveal the solution. For each problem, ask: *What data structure does this scream for? Have I seen this pattern before?*

!!! info "Problem-solving framework"
    1. **Understand** — restate the problem, clarify edge cases
    2. **Pattern** — which data structure / technique fits?
    3. **Brute force** — get something working, O(n²) is fine to start
    4. **Optimise** — can a hash map, two pointers, or sliding window help?
    5. **Complexity** — state time and space before the interviewer asks

!!! warning "Keep in mind"
    Easy doesn't mean trivial in an interview. You're expected to produce clean, bug-free code quickly with correct complexity analysis. These problems test your **fluency**, not just your knowledge.

---

## 🗂️ Problem Index

| # | Problem | Topic | Pattern |
|---|---|---|---|
| 1 | Two Sum | Arrays | Hash map |
| 2 | Valid Parentheses | Stack | Stack matching |
| 3 | Reverse a String | Strings | Two pointers |
| 4 | Maximum Subarray | Arrays | Kadane's algorithm |
| 5 | Contains Duplicate | Arrays | Hash set |
| 6 | Merge Two Sorted Lists | Linked Lists | Two pointers |
| 7 | Climbing Stairs | DP | Fibonacci pattern |
| 8 | Binary Search | Arrays | Divide and conquer |
| 9 | Invert Binary Tree | Trees | DFS recursion |
| 10 | Valid Palindrome | Strings | Two pointers |
| 11 | Best Time to Buy/Sell Stock | Arrays | Greedy / sliding min |
| 12 | Linked List Cycle | Linked Lists | Fast & slow pointers |
| 13 | Maximum Depth of Binary Tree | Trees | DFS recursion |
| 14 | First Unique Character | Strings | Hash map |
| 15 | Move Zeroes | Arrays | Two pointers |
| 16 | Symmetric Tree | Trees | DFS recursion |
| 17 | Reverse Linked List | Linked Lists | Iterative pointer swap |
| 18 | Fizz Buzz | Math | Modulo |
| 19 | Number of 1 Bits | Bit manipulation | Bit masking |
| 20 | Flood Fill | Graphs | DFS / BFS |

---

## 1️⃣ Arrays & Hash Maps

### Problem 1 — Two Sum

**Problem:** Given an array `nums` and a target integer, return indices of the two numbers that add up to `target`. Assume exactly one solution exists.

```
Input:  nums = [2, 7, 11, 15], target = 9
Output: [0, 1]   (nums[0] + nums[1] = 2 + 7 = 9)
```

**Pattern:** For every element, you need its complement (`target - num`). A hash map lets you look that up in O(1).

=== "Brute Force — O(n²)"

    ```python
    def two_sum_brute(nums, target):
        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [i, j]
    ```

=== "Optimal — O(n)"

    ```python
    def two_sum(nums, target):
        seen = {}                          # value → index
        for i, num in enumerate(nums):
            complement = target - num
            if complement in seen:
                return [seen[complement], i]
            seen[num] = i
    ```

=== "Walkthrough"

    ```
    nums = [2, 7, 11, 15], target = 9

    i=0  num=2   complement=7   seen={}        → store {2:0}
    i=1  num=7   complement=2   seen={2:0}     → 2 is in seen! return [0, 1] ✅

    Key insight: store what you've SEEN, check if complement exists.
    One pass, no nested loop.
    ```

**Complexity:** Time O(n) · Space O(n)

---

### Problem 4 — Maximum Subarray

**Problem:** Find the contiguous subarray with the largest sum and return its sum.

```
Input:  nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6   (subarray [4, -1, 2, 1])
```

**Pattern:** Kadane's algorithm — at each position, decide whether to extend the current subarray or start fresh.

=== "Brute Force — O(n²)"

    ```python
    def max_subarray_brute(nums):
        max_sum = float('-inf')
        for i in range(len(nums)):
            current = 0
            for j in range(i, len(nums)):
                current += nums[j]
                max_sum = max(max_sum, current)
        return max_sum
    ```

=== "Optimal — O(n)"

    ```python
    def max_subarray(nums):
        current = nums[0]
        best = nums[0]

        for num in nums[1:]:
            current = max(num, current + num)   # extend or restart
            best = max(best, current)

        return best
    ```

=== "Walkthrough"

    ```
    nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]

    num=-2   current=-2   best=-2
    num=1    current=max(1, -2+1)=1    best=1
    num=-3   current=max(-3, 1-3)=-2  best=1
    num=4    current=max(4, -2+4)=4   best=4
    num=-1   current=max(-1, 4-1)=3   best=4
    num=2    current=max(2, 3+2)=5    best=5
    num=1    current=max(1, 5+1)=6    best=6   ✅
    num=-5   current=max(-5, 6-5)=1   best=6
    num=4    current=max(4, 1+4)=5    best=6

    Answer: 6
    ```

**Complexity:** Time O(n) · Space O(1)

---

### Problem 5 — Contains Duplicate

**Problem:** Return `True` if any value appears at least twice in `nums`.

```
Input:  nums = [1, 2, 3, 1]
Output: True
```

=== "Optimal — O(n)"

    ```python
    def contains_duplicate(nums):
        return len(nums) != len(set(nums))

    # Or streaming (stops early):
    def contains_duplicate_early(nums):
        seen = set()
        for num in nums:
            if num in seen:
                return True
            seen.add(num)
        return False
    ```

=== "Walkthrough"

    ```
    A set stores only unique values.
    If len(set) < len(nums) → at least one duplicate exists.

    [1, 2, 3, 1] → set = {1, 2, 3} → len 3 ≠ len 4 → True ✅
    [1, 2, 3, 4] → set = {1, 2, 3, 4} → len 4 = len 4 → False
    ```

**Complexity:** Time O(n) · Space O(n)

---

### Problem 11 — Best Time to Buy and Sell Stock

**Problem:** Given prices on each day, return the maximum profit from one buy and one sell. Must buy before selling.

```
Input:  prices = [7, 1, 5, 3, 6, 4]
Output: 5   (buy at 1, sell at 6)
```

**Pattern:** Track the minimum price seen so far. At each day, check if selling gives a new best profit.

=== "Brute Force — O(n²)"

    ```python
    def max_profit_brute(prices):
        max_p = 0
        for i in range(len(prices)):
            for j in range(i + 1, len(prices)):
                max_p = max(max_p, prices[j] - prices[i])
        return max_p
    ```

=== "Optimal — O(n)"

    ```python
    def max_profit(prices):
        min_price = float('inf')
        max_profit = 0

        for price in prices:
            min_price = min(min_price, price)
            max_profit = max(max_profit, price - min_price)

        return max_profit
    ```

=== "Walkthrough"

    ```
    prices = [7, 1, 5, 3, 6, 4]

    price=7  min=7   profit=max(0, 7-7)=0
    price=1  min=1   profit=max(0, 1-1)=0
    price=5  min=1   profit=max(0, 5-1)=4
    price=3  min=1   profit=max(4, 3-1)=4
    price=6  min=1   profit=max(4, 6-1)=5  ✅
    price=4  min=1   profit=max(5, 4-1)=5

    Answer: 5
    ```

**Complexity:** Time O(n) · Space O(1)

---

### Problem 15 — Move Zeroes

**Problem:** Move all `0`s in `nums` to the end while maintaining the relative order of non-zero elements. Do it **in-place**.

```
Input:  [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]
```

**Pattern:** Two pointers — one scans, one marks the next write position.

```python
def move_zeroes(nums):
    write = 0                           # next position for a non-zero

    for read in range(len(nums)):
        if nums[read] != 0:
            nums[write] = nums[read]
            write += 1

    while write < len(nums):            # fill rest with 0s
        nums[write] = 0
        write += 1
```

**Walkthrough:**
```
[0, 1, 0, 3, 12]
 r
 w

read=0  nums[0]=0  skip
read=1  nums[1]=1  write→ [1, 1, 0, 3, 12]  write=1
read=2  nums[2]=0  skip
read=3  nums[3]=3  write→ [1, 3, 0, 3, 12]  write=2
read=4  nums[4]=12 write→ [1, 3, 12, 3, 12] write=3
fill 0s →           [1, 3, 12, 0, 0] ✅
```

**Complexity:** Time O(n) · Space O(1)

---

## 2️⃣ Strings

### Problem 3 — Reverse a String

**Problem:** Reverse a list of characters in-place.

```
Input:  ['h', 'e', 'l', 'l', 'o']
Output: ['o', 'l', 'l', 'e', 'h']
```

```python
def reverse_string(s):
    left, right = 0, len(s) - 1
    while left < right:
        s[left], s[right] = s[right], s[left]
        left += 1
        right -= 1
```

**Walkthrough:**
```
['h','e','l','l','o']
  L               R     swap → ['o','e','l','l','h']
      L       R         swap → ['o','l','l','e','h']
          L=R           stop ✅
```

**Complexity:** Time O(n) · Space O(1)

---

### Problem 10 — Valid Palindrome

**Problem:** A string is a palindrome if it reads the same forwards and backwards (ignoring non-alphanumeric characters and case).

```
Input:  "A man, a plan, a canal: Panama"
Output: True
```

=== "Clean approach"

    ```python
    def is_palindrome(s):
        cleaned = [c.lower() for c in s if c.isalnum()]
        return cleaned == cleaned[::-1]
    ```

=== "Two pointers — O(1) space"

    ```python
    def is_palindrome(s):
        left, right = 0, len(s) - 1
        while left < right:
            while left < right and not s[left].isalnum():
                left += 1
            while left < right and not s[right].isalnum():
                right -= 1
            if s[left].lower() != s[right].lower():
                return False
            left += 1
            right -= 1
        return True
    ```

=== "Walkthrough"

    ```
    "A man, a plan, a canal: Panama"
     L                            R

    Skip non-alnum, compare A == a (case-insensitive) ✅
    Move inward... n == n ✅ ... a == a ✅ ... all match → True
    ```

**Complexity:** Time O(n) · Space O(1) for two pointers

---

### Problem 14 — First Unique Character

**Problem:** Given a string, return the index of the first non-repeating character. Return `-1` if none exists.

```
Input:  "leetcode"
Output: 0   ('l' appears once)

Input:  "aabb"
Output: -1
```

```python
from collections import Counter

def first_unique_char(s):
    count = Counter(s)                  # O(n) — frequency of each char
    for i, c in enumerate(s):
        if count[c] == 1:
            return i
    return -1
```

**Walkthrough:**
```
s = "leetcode"
count = {'l':1, 'e':3, 't':1, 'c':1, 'o':1, 'd':1}

i=0  'l' → count=1 → return 0 ✅
```

**Complexity:** Time O(n) · Space O(1) (at most 26 keys for lowercase letters)

---

## 3️⃣ Stacks

### Problem 2 — Valid Parentheses

**Problem:** Given a string of brackets, return `True` if every opening bracket is closed in the correct order.

```
Input:  "()[]{}"   Output: True
Input:  "([)]"     Output: False
Input:  "{[]}"     Output: True
```

**Pattern:** Use a stack — push opening brackets, pop and match when you see a closing bracket.

```python
def is_valid(s):
    stack = []
    mapping = {')': '(', '}': '{', ']': '['}

    for char in s:
        if char in mapping:                         # closing bracket
            top = stack.pop() if stack else '#'
            if mapping[char] != top:
                return False
        else:
            stack.append(char)                      # opening bracket

    return not stack                                # stack must be empty
```

**Walkthrough:**
```
s = "{[]}"

char='{'  opening → stack=['{'
char='['  opening → stack=['{','['
char=']'  closing → pop '[', mapping[']']='[' ✅  stack=['{']
char='}'  closing → pop '{', mapping['}']='{'  ✅  stack=[]

stack empty → True ✅

s = "([)]"
char='('  stack=['(']
char='['  stack=['(','[']
char=')'  closing → pop '[', mapping[')']]='(' ≠ '[' → False ✅
```

**Complexity:** Time O(n) · Space O(n)

---

## 4️⃣ Linked Lists

### Problem 6 — Merge Two Sorted Lists

**Problem:** Merge two sorted linked lists and return the merged list (also sorted).

```
Input:  1→2→4  and  1→3→4
Output: 1→1→2→3→4→4
```

**Pattern:** Dummy head + two pointers — compare heads, attach smaller one.

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def merge_two_lists(l1, l2):
    dummy = ListNode(0)             # placeholder to simplify edge cases
    current = dummy

    while l1 and l2:
        if l1.val <= l2.val:
            current.next = l1
            l1 = l1.next
        else:
            current.next = l2
            l2 = l2.next
        current = current.next

    current.next = l1 or l2         # attach remaining nodes

    return dummy.next
```

**Walkthrough:**
```
l1: 1→2→4    l2: 1→3→4

Compare 1,1 → tie, take l1(1)  → merged: 1
Compare 2,1 → l2 wins(1)       → merged: 1→1
Compare 2,3 → l1 wins(2)       → merged: 1→1→2
Compare 4,3 → l2 wins(3)       → merged: 1→1→2→3
Compare 4,4 → tie, take l1(4)  → merged: 1→1→2→3→4
l1 done, attach l2 tail(4)     → merged: 1→1→2→3→4→4 ✅
```

**Complexity:** Time O(n + m) · Space O(1)

---

### Problem 12 — Linked List Cycle

**Problem:** Return `True` if the linked list has a cycle.

**Pattern:** Floyd's fast & slow pointers — if there's a cycle, fast will lap slow and they'll meet.

```python
def has_cycle(head):
    slow = head
    fast = head

    while fast and fast.next:
        slow = slow.next            # 1 step
        fast = fast.next.next       # 2 steps
        if slow == fast:
            return True             # met → cycle exists

    return False
```

**Walkthrough:**
```
List: 3 → 2 → 0 → -4
              ↑_______↑  (cycle back to 2)

Step 1: slow=2, fast=0
Step 2: slow=0, fast=2   (fast looped)
Step 3: slow=-4, fast=-4 → MEET → True ✅
```

**Complexity:** Time O(n) · Space O(1)

---

### Problem 17 — Reverse Linked List

**Problem:** Reverse a singly linked list in-place.

```
Input:  1 → 2 → 3 → 4 → 5
Output: 5 → 4 → 3 → 2 → 1
```

=== "Iterative — O(1) space"

    ```python
    def reverse_list(head):
        prev = None
        current = head

        while current:
            next_node = current.next    # save next
            current.next = prev         # reverse pointer
            prev = current              # advance prev
            current = next_node         # advance current

        return prev                     # new head
    ```

=== "Recursive"

    ```python
    def reverse_list_recursive(head):
        if not head or not head.next:
            return head
        new_head = reverse_list_recursive(head.next)
        head.next.next = head           # reverse the link
        head.next = None
        return new_head
    ```

=== "Walkthrough"

    ```
    1 → 2 → 3 → None

    prev=None  cur=1
    Step 1: next=2, 1→None, prev=1, cur=2     None←1  2→3
    Step 2: next=3, 2→1,    prev=2, cur=3     None←1←2  3
    Step 3: next=None, 3→2, prev=3, cur=None  None←1←2←3

    return prev=3  →  3→2→1→None ✅
    ```

**Complexity:** Time O(n) · Space O(1) iterative · O(n) recursive (call stack)

---

## 5️⃣ Trees

### Problem 9 — Invert Binary Tree

**Problem:** Invert (mirror) a binary tree.

```
Input:       [4]           Output:      [4]
            /   \                      /   \
          [2]   [7]                  [7]   [2]
          / \   / \                  / \   / \
         [1][3][6][9]               [9][6][3][1]
```

```python
def invert_tree(root):
    if not root:
        return None
    root.left, root.right = invert_tree(root.right), invert_tree(root.left)
    return root
```

**Walkthrough:**
```
At every node: swap left and right children, then recurse.

invert(4):
  left  = invert(7) → returns inverted [7] with [9],[6]
  right = invert(2) → returns inverted [2] with [3],[1]
  swap: [4].left=[7-inverted], [4].right=[2-inverted] ✅
```

**Complexity:** Time O(n) · Space O(h) where h = tree height

---

### Problem 13 — Maximum Depth of Binary Tree

**Problem:** Return the maximum depth (number of nodes along the longest root-to-leaf path).

```
Input:   [3]
        /   \
      [9]   [20]
            /  \
          [15]  [7]

Output: 3
```

```python
def max_depth(root):
    if not root:
        return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))
```

**Walkthrough:**
```
max_depth(3)
  = 1 + max(max_depth(9), max_depth(20))
  = 1 + max(1, 1 + max(max_depth(15), max_depth(7)))
  = 1 + max(1, 1 + max(1, 1))
  = 1 + max(1, 2)
  = 1 + 2 = 3 ✅
```

**Complexity:** Time O(n) · Space O(h)

---

### Problem 16 — Symmetric Tree

**Problem:** Return `True` if a binary tree is a mirror of itself.

```
Symmetric:         Not symmetric:
     [1]                [1]
    /   \              /   \
  [2]   [2]          [2]   [2]
  / \   / \            \     \
[3][4][4][3]           [3]   [3]
```

```python
def is_symmetric(root):
    def is_mirror(left, right):
        if not left and not right:
            return True             # both None → symmetric
        if not left or not right:
            return False            # one None → not symmetric
        return (left.val == right.val
                and is_mirror(left.left, right.right)
                and is_mirror(left.right, right.left))

    return is_mirror(root.left, root.right)
```

**Walkthrough:**
```
is_mirror(2, 2):
  vals match ✅
  is_mirror(3, 3) → vals match, both leaves ✅
  is_mirror(4, 4) → vals match, both leaves ✅
→ True ✅
```

**Complexity:** Time O(n) · Space O(h)

---

## 6️⃣ Dynamic Programming

### Problem 7 — Climbing Stairs

**Problem:** You can climb 1 or 2 steps at a time. How many distinct ways can you climb `n` stairs?

```
n=2 → 2 ways  (1+1, 2)
n=3 → 3 ways  (1+1+1, 1+2, 2+1)
n=4 → 5 ways
```

**Pattern:** The number of ways to reach step `n` = ways to reach `n-1` + ways to reach `n-2`. This is exactly Fibonacci.

=== "Recursive with memo — O(n)"

    ```python
    from functools import lru_cache

    def climb_stairs(n):
        @lru_cache(maxsize=None)
        def dp(i):
            if i <= 1:
                return 1
            return dp(i - 1) + dp(i - 2)
        return dp(n)
    ```

=== "Bottom-up DP — O(1) space"

    ```python
    def climb_stairs(n):
        if n <= 1:
            return 1
        prev2, prev1 = 1, 1
        for _ in range(2, n + 1):
            prev2, prev1 = prev1, prev1 + prev2
        return prev1
    ```

=== "Walkthrough"

    ```
    n=5

    step 2: prev2=1, prev1=1+1=2   (ways: 2)
    step 3: prev2=2, prev1=2+1=3   (ways: 3)
    step 4: prev2=3, prev1=3+2=5   (ways: 5)
    step 5: prev2=5, prev1=5+3=8   (ways: 8) ✅

    It's Fibonacci! f(1)=1, f(2)=2, f(3)=3, f(4)=5, f(5)=8
    ```

**Complexity:** Time O(n) · Space O(1)

---

## 7️⃣ Binary Search

### Problem 8 — Binary Search

**Problem:** Given a sorted array and a target, return the index of target or `-1` if not found.

```
Input:  nums = [-1, 0, 3, 5, 9, 12], target = 9
Output: 4
```

```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2   # avoids integer overflow

        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1                  # target is in right half
        else:
            right = mid - 1                 # target is in left half

    return -1
```

**Walkthrough:**
```
nums = [-1, 0, 3, 5, 9, 12], target = 9
left=0, right=5

Round 1: mid=2  nums[2]=3  < 9  → left=3
Round 2: mid=4  nums[4]=9  = 9  → return 4 ✅
```

!!! warning "The classic binary search bug"
    Use `mid = left + (right - left) // 2` not `(left + right) // 2`. The latter can overflow in languages with fixed int sizes. Good habit even in Python.

**Complexity:** Time O(log n) · Space O(1)

---

## 8️⃣ Miscellaneous

### Problem 18 — Fizz Buzz

**Problem:** For integers 1 to n: print "FizzBuzz" if divisible by both 3 and 5, "Fizz" if by 3, "Buzz" if by 5, else the number.

```python
def fizz_buzz(n):
    result = []
    for i in range(1, n + 1):
        if i % 15 == 0:
            result.append("FizzBuzz")
        elif i % 3 == 0:
            result.append("Fizz")
        elif i % 5 == 0:
            result.append("Buzz")
        else:
            result.append(str(i))
    return result
```

!!! tip "Check 15 first"
    Always check `% 15` before `% 3` and `% 5`. If you check `% 3` first, multiples of 15 will match "Fizz" and never reach "FizzBuzz".

**Complexity:** Time O(n) · Space O(n)

---

### Problem 19 — Number of 1 Bits

**Problem:** Return the number of `1` bits in the binary representation of an integer (Hamming weight).

```
Input:  n = 11  (binary: 1011)
Output: 3
```

=== "Built-in"

    ```python
    def hamming_weight(n):
        return bin(n).count('1')
    ```

=== "Bit trick — Brian Kernighan"

    ```python
    def hamming_weight(n):
        count = 0
        while n:
            n &= n - 1      # clears the lowest set bit
            count += 1
        return count
    ```

=== "Walkthrough"

    ```
    n = 11  →  binary: 1011

    n=1011  n-1=1010  n & (n-1) = 1010   count=1
    n=1010  n-1=1001  n & (n-1) = 1000   count=2
    n=1000  n-1=0111  n & (n-1) = 0000   count=3
    n=0     → stop

    Answer: 3 ✅

    Key trick: n & (n-1) always clears the rightmost 1 bit.
    So we loop exactly as many times as there are 1 bits.
    ```

**Complexity:** Time O(k) where k = number of 1 bits · Space O(1)

---

### Problem 20 — Flood Fill

**Problem:** Given an image (2D grid), a starting pixel `(sr, sc)`, and a new colour, flood-fill the region connected to the starting pixel with the new colour.

```
Input:  image = [[1,1,1],    sr=1, sc=1, color=2
                 [1,1,0],
                 [1,0,1]]

Output:          [[2,2,2],
                  [2,2,0],
                  [2,0,1]]
```

**Pattern:** DFS from starting pixel — colour connected same-colour neighbours.

```python
def flood_fill(image, sr, sc, color):
    original = image[sr][sc]
    if original == color:
        return image                    # no change needed

    rows, cols = len(image), len(image[0])

    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols:
            return
        if image[r][c] != original:     # different colour — stop
            return
        image[r][c] = color             # paint
        dfs(r+1, c)
        dfs(r-1, c)
        dfs(r, c+1)
        dfs(r, c-1)

    dfs(sr, sc)
    return image
```

**Walkthrough:**
```
Start at (1,1), original=1, new_color=2

Paint (1,1)=2 → recurse up(0,1), down(2,1), left(1,0), right(1,2)
  (0,1)=1 → paint 2 → recurse → (0,0)=1→2, (0,2)=1→2
  (2,1)=0 → stop
  (1,0)=1 → paint 2 → (2,0)=1→2, (0,0) already 2
  (1,2)=0 → stop

Result: [[2,2,2],[2,2,0],[2,0,1]] ✅
```

**Complexity:** Time O(m × n) · Space O(m × n) call stack

---

## ✅ Quick Reference Summary

| Problem | Key insight | Time | Space |
|---|---|---|---|
| Two Sum | Hash map for complement lookup | O(n) | O(n) |
| Valid Parentheses | Stack — push open, match close | O(n) | O(n) |
| Reverse String | Two pointers from both ends | O(n) | O(1) |
| Maximum Subarray | Kadane's — extend or restart | O(n) | O(1) |
| Contains Duplicate | Set deduplication | O(n) | O(n) |
| Merge Sorted Lists | Dummy head + two pointers | O(n+m) | O(1) |
| Climbing Stairs | Fibonacci / DP rolling window | O(n) | O(1) |
| Binary Search | Halve search space each step | O(log n) | O(1) |
| Invert Binary Tree | Swap children, recurse | O(n) | O(h) |
| Valid Palindrome | Two pointers, skip non-alnum | O(n) | O(1) |
| Buy/Sell Stock | Track running minimum | O(n) | O(1) |
| Linked List Cycle | Fast & slow pointer meet | O(n) | O(1) |
| Max Depth Tree | `1 + max(left, right)` | O(n) | O(h) |
| First Unique Char | Counter + first count==1 | O(n) | O(1) |
| Move Zeroes | Write pointer skips zeros | O(n) | O(1) |
| Symmetric Tree | Mirror check — outer vs inner | O(n) | O(h) |
| Reverse Linked List | Three-pointer iterative swap | O(n) | O(1) |
| Fizz Buzz | Check 15 before 3 and 5 | O(n) | O(n) |
| Number of 1 Bits | `n & (n-1)` clears lowest bit | O(k) | O(1) |
| Flood Fill | DFS from start, paint connected | O(m×n) | O(m×n) |

!!! warning "Key interview gotchas"
    - **Two Sum**: store indices in the map, not just values
    - **Valid Parentheses**: stack must be **empty** at the end — a single `(` left over is invalid
    - **Binary Search**: use `left + (right - left) // 2` to prevent overflow
    - **Linked List Cycle**: check `fast and fast.next` before moving — null pointer otherwise
    - **Flood Fill**: always check if `original == color` first, or you'll recurse infinitely
    - **Climbing Stairs**: it's Fibonacci — `dp(n) = dp(n-1) + dp(n-2)`, base cases are both `1`
    - **Kadane's**: initialise `current` and `best` with `nums[0]`, not `0` — handles all-negative arrays