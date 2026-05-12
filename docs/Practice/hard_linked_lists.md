# 🔗 Hard Practice Problems — Linked Lists

!!! abstract "What You'll Learn"
    - ✅ Solve hard-difficulty linked list problems
    - ✅ Combine reversal, merging, and pointer tricks in complex scenarios
    - ✅ Handle multi-level and arbitrary-pointer structures
    - ✅ Implement O(1) space solutions where possible

Hard linked list problems chain multiple operations together — reverse a segment, detect a cycle, then merge. The challenge is managing multiple pointers simultaneously without losing track of nodes. Drawing the pointer state before coding is non-negotiable at this level.

!!! tip "New to Hard Problems?"
    Revisit Medium Linked Lists and ensure you can reverse a list and detect cycles blindfolded.

!!! info "Strategy"
    Draw every pointer change on paper before writing code. One wrong assignment and the list is lost.

!!! warning "Keep in mind"
    Always use a dummy head node when the head itself might change. Save `next` pointers before overwriting them.

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def build_list(vals):
    dummy = ListNode(0)
    cur = dummy
    for v in vals:
        cur.next = ListNode(v)
        cur = cur.next
    return dummy.next

def print_list(head):
    result = []
    while head:
        result.append(head.val)
        head = head.next
    print(result)
```

---

## 1️⃣ Reverse Nodes in k-Group

**Problem:** Reverse every `k` consecutive nodes in a linked list. If fewer than `k` nodes remain, leave them as-is.

**Example:**
```
Input:  head = [1,2,3,4,5], k = 2
Output: [2,1,4,3,5]

Input:  head = [1,2,3,4,5], k = 3
Output: [3,2,1,4,5]
```

!!! tip "Hint"
    Check if k nodes exist ahead. If yes, reverse that group in-place, connect to the next recursively processed group.

```python
def reverse_k_group(head, k):
    # Check if k nodes exist
    node = head
    for _ in range(k):
        if not node:
            return head
        node = node.next

    # Reverse k nodes
    prev, cur = None, head
    for _ in range(k):
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt

    # head is now the tail of reversed group; connect to next group
    head.next = reverse_k_group(cur, k)
    return prev

head = build_list([1,2,3,4,5])
print_list(reverse_k_group(head, 2))
```
**Output:** `[2, 1, 4, 3, 5]`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n/k) recursion stack |

---

## 2️⃣ Merge K Sorted Lists (Optimal)

**Problem:** Merge `k` sorted linked lists into one sorted list. Aim for O(n log k).

**Example:**
```
Input:  lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
```

!!! tip "Hint"
    Use a min-heap. Push the head of each list. Pop the minimum, push its next. Repeat until heap is empty.

```python
import heapq

def merge_k_lists(lists):
    dummy = ListNode(0)
    cur = dummy
    heap = []

    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))

    while heap:
        val, i, node = heapq.heappop(heap)
        cur.next = node
        cur = cur.next
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))

    return dummy.next
```

| Complexity | Value |
|------------|-------|
| Time       | O(n log k) |
| Space      | O(k)       |

---

## 3️⃣ Flatten a Multilevel Doubly Linked List

**Problem:** A doubly linked list may have nodes with a `child` pointer that leads to another doubly linked list. Flatten it all into a single-level doubly linked list.

**Example:**
```
Input:  1 — 2 — 3 — 4 — 5 — 6
                |
                7 — 8 — 9 — 10
                    |
                    11 — 12
Output: 1 — 2 — 3 — 7 — 8 — 11 — 12 — 9 — 10 — 4 — 5 — 6
```

!!! tip "Hint"
    Use a stack. When a node has a child, push the node's next onto the stack and continue flattening the child. When `next` is None, pop from the stack.

```python
class Node:
    def __init__(self, val, prev=None, next=None, child=None):
        self.val = val
        self.prev = prev
        self.next = next
        self.child = child

def flatten(head):
    if not head:
        return head

    stack = []
    cur = head

    while cur:
        if cur.child:
            if cur.next:
                stack.append(cur.next)
            cur.next = cur.child
            cur.child.prev = cur
            cur.child = None

        if not cur.next and stack:
            nxt = stack.pop()
            cur.next = nxt
            nxt.prev = cur

        cur = cur.next

    return head
```

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(d) — d = depth |

---

## 4️⃣ LRU Cache

**Problem:** Design a data structure supporting `get(key)` and `put(key, value)`, both in O(1). When capacity is exceeded, evict the least recently used item.

!!! tip "Hint"
    Combine a hash map (O(1) lookup) with a doubly linked list (O(1) move-to-front and eviction).

```python
class DLinkedNode:
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.prev = self.next = None

class LRUCache:
    def __init__(self, capacity):
        self.cap = capacity
        self.cache = {}
        self.head = DLinkedNode()  # dummy head (most recent)
        self.tail = DLinkedNode()  # dummy tail (least recent)
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def _insert_front(self, node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key):
        if key not in self.cache:
            return -1
        node = self.cache[key]
        self._remove(node)
        self._insert_front(node)
        return node.val

    def put(self, key, value):
        if key in self.cache:
            self._remove(self.cache[key])
        node = DLinkedNode(key, value)
        self.cache[key] = node
        self._insert_front(node)
        if len(self.cache) > self.cap:
            lru = self.tail.prev
            self._remove(lru)
            del self.cache[lru.key]

lru = LRUCache(2)
lru.put(1, 1); lru.put(2, 2)
print(lru.get(1))    # 1
lru.put(3, 3)        # evicts key 2
print(lru.get(2))    # -1
```
**Output:**
```
1
-1
```

| Complexity | Value |
|------------|-------|
| Time       | O(1) all ops |
| Space      | O(capacity)  |

---

## 5️⃣ Palindrome Linked List (O(1) Space)

**Problem:** Determine if a linked list is a palindrome in O(n) time and O(1) space.

**Example:**
```
Input:  head = [1,2,2,1]
Output: True
```

!!! tip "Hint"
    Find the middle, reverse the second half in-place, compare both halves, then restore (optional).

```python
def is_palindrome(head):
    # Find middle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    # Reverse second half
    prev, cur = None, slow
    while cur:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt

    # Compare
    left, right = head, prev
    while right:
        if left.val != right.val:
            return False
        left = left.next
        right = right.next
    return True

head = build_list([1,2,2,1])
print(is_palindrome(head))
```
**Output:** `True`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(1)  |

---

## 6️⃣ Swap Nodes in Pairs (Generalised to k)

**Problem:** Given a linked list, swap every two adjacent nodes and return the head. Do not modify values.

**Example:**
```
Input:  head = [1,2,3,4]
Output: [2,1,4,3]
```

!!! tip "Hint"
    Iterative approach with a dummy node. For each pair: rewire four pointers — prev → second → first → third.

```python
def swap_pairs(head):
    dummy = ListNode(0)
    dummy.next = head
    prev = dummy

    while prev.next and prev.next.next:
        first = prev.next
        second = prev.next.next

        prev.next = second
        first.next = second.next
        second.next = first

        prev = first

    return dummy.next

head = build_list([1,2,3,4])
print_list(swap_pairs(head))
```
**Output:** `[2, 1, 4, 3]`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(1)  |

---

## 7️⃣ Find Duplicate Number (Linked List Cycle)

**Problem:** Given an array of `n+1` integers where each value is in `[1, n]`, find the duplicate. Must not modify the array; O(1) space.

**Example:**
```
Input:  nums = [1,3,4,2,2]
Output: 2
```

!!! tip "Hint"
    Treat the array as a linked list where `nums[i]` is the next pointer. A duplicate creates a cycle. Use Floyd's algorithm.

```python
def find_duplicate(nums):
    slow = fast = nums[0]

    # Phase 1: detect cycle
    while True:
        slow = nums[slow]
        fast = nums[nums[fast]]
        if slow == fast:
            break

    # Phase 2: find entry point
    slow = nums[0]
    while slow != fast:
        slow = nums[slow]
        fast = nums[fast]

    return slow

print(find_duplicate([1,3,4,2,2]))
print(find_duplicate([3,1,3,4,2]))
```
**Output:**
```
2
3
```

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(1)  |

---

## 8️⃣ Design Skiplist

**Problem:** Design a skip list that supports `search`, `add`, and `erase` operations, all in O(log n) average time.

!!! tip "Hint"
    Each node has a random tower of forward pointers at multiple levels. Use a max level (e.g. 16) and promote nodes with probability 0.5 per level.

```python
import random

class SkiplistNode:
    def __init__(self, val, level):
        self.val = val
        self.forward = [None] * (level + 1)

class Skiplist:
    MAX_LEVEL = 16
    P = 0.5

    def __init__(self):
        self.head = SkiplistNode(-1, self.MAX_LEVEL)
        self.level = 0

    def _random_level(self):
        lvl = 0
        while random.random() < self.P and lvl < self.MAX_LEVEL:
            lvl += 1
        return lvl

    def _find_predecessors(self, target):
        update = [None] * (self.MAX_LEVEL + 1)
        cur = self.head
        for i in range(self.level, -1, -1):
            while cur.forward[i] and cur.forward[i].val < target:
                cur = cur.forward[i]
            update[i] = cur
        return update

    def search(self, target):
        update = self._find_predecessors(target)
        node = update[0].forward[0]
        return node is not None and node.val == target

    def add(self, num):
        update = self._find_predecessors(num)
        new_level = self._random_level()
        if new_level > self.level:
            for i in range(self.level + 1, new_level + 1):
                update[i] = self.head
            self.level = new_level
        node = SkiplistNode(num, new_level)
        for i in range(new_level + 1):
            node.forward[i] = update[i].forward[i]
            update[i].forward[i] = node

    def erase(self, num):
        update = self._find_predecessors(num)
        node = update[0].forward[0]
        if not node or node.val != num:
            return False
        for i in range(self.level + 1):
            if update[i].forward[i] != node:
                break
            update[i].forward[i] = node.forward[i]
        while self.level > 0 and not self.head.forward[self.level]:
            self.level -= 1
        return True

sl = Skiplist()
sl.add(1); sl.add(2); sl.add(3)
print(sl.search(0))   # False
sl.add(4)
print(sl.search(1))   # True
print(sl.erase(0))    # False
print(sl.erase(1))    # True
print(sl.search(1))   # False
```
**Output:**
```
False
True
False
True
False
```

| Complexity | Value |
|------------|-------|
| Time       | O(log n) avg |
| Space      | O(n log n)   |

---

## ✅ Quick Reference Summary

| # | Problem | Key Technique | Time | Space |
|---|---------|---------------|------|-------|
| 1 | Reverse Nodes in k-Group | Recursive Group Reversal | O(n) | O(n/k) |
| 2 | Merge K Sorted Lists | Min-Heap | O(n log k) | O(k) |
| 3 | Flatten Multilevel DLL | Stack-based Flattening | O(n) | O(d) |
| 4 | LRU Cache | HashMap + Doubly Linked List | O(1) | O(cap) |
| 5 | Palindrome Linked List | Reverse Half In-Place | O(n) | O(1) |
| 6 | Swap Nodes in Pairs | Iterative Pointer Rewiring | O(n) | O(1) |
| 7 | Find Duplicate Number | Floyd's Cycle Detection | O(n) | O(1) |
| 8 | Design Skiplist | Probabilistic Skip Levels | O(log n) avg | O(n log n) |
