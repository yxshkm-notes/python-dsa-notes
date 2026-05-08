# Linked Lists

!!! abstract "What You'll Learn"
    - ✅ What a linked list is and how it differs from arrays
    - ✅ Singly vs doubly linked lists
    - ✅ How to implement a linked list in Python from scratch
    - ✅ Core operations: insert, delete, search, traverse
    - ✅ Time and space complexity of each operation
    - ✅ Common interview patterns (two pointers, cycle detection, reversal)

A **linked list** is a linear data structure where elements (called **nodes**) are stored in memory non-contiguously — each node holds a value and a pointer to the next node. Unlike arrays, linked lists don't require a fixed block of memory; they grow and shrink dynamically.

!!! tip "New to Data Structures?"
    Think of a linked list like a **treasure hunt** — each clue (node) tells you where the next clue is. You always start from the first clue (head) and follow the chain. You can't jump to clue #5 directly; you have to walk through 1 → 2 → 3 → 4 → 5.

!!! info "Already know Arrays?"
    The key trade-off is: arrays give O(1) random access but O(n) insert/delete. Linked lists flip this — O(1) insert/delete at the head, but O(n) access by index. Choose based on your usage pattern.

!!! warning "Keep in mind"
    Python doesn't have a built-in linked list node type. You build one using classes. The standard `list` in Python is an array under the hood — **not** a linked list.

---

## Structure in Memory

```
Array (contiguous):
[10 | 20 | 30 | 40]
  0    1    2    3   ← index-based, O(1) access

Linked List (scattered):
[10 | *]──►[20 | *]──►[30 | *]──►[40 | None]
 head                               tail
```

```
Singly Linked Node:
┌──────────┬──────────┐
│  value   │  next ──────► (next node)
└──────────┴──────────┘

Doubly Linked Node:
┌──────┬──────────┬──────┐
│ prev │  value   │ next │
└──────┴──────────┴──────┘
```

---

## 1️⃣ Node & LinkedList Class

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None   # pointer to next node

class LinkedList:
    def __init__(self):
        self.head = None   # entry point to the list
        self.size = 0
```

**Output:**
```
head → None   (empty list)
```

!!! tip "Why a separate Node class?"
    Each node is an **independent object** in memory. The `LinkedList` class just tracks the `head` pointer — the entry point. All traversal happens by following `.next` chains.

---

## 2️⃣ Core Operations

### Traversal

```python
def traverse(self):
    current = self.head
    while current:
        print(current.value, end=" → ")
        current = current.next
    print("None")
```

**Output:**
```
10 → 20 → 30 → None
```

---

### Insert at Head — O(1)

```python
def insert_at_head(self, value):
    new_node = Node(value)
    new_node.next = self.head   # new node points to old head
    self.head = new_node        # head now points to new node
    self.size += 1
```

**Memory diagram:**
```
Before:  head → [10] → [20] → None
After:   head → [5] → [10] → [20] → None
                 ↑ new node
```

---

### Insert at Tail — O(n)

```python
def insert_at_tail(self, value):
    new_node = Node(value)
    if not self.head:
        self.head = new_node
        return
    current = self.head
    while current.next:         # walk to last node
        current = current.next
    current.next = new_node     # attach at end
    self.size += 1
```

**Output:**
```
insert_at_tail(40):  10 → 20 → 30 → 40 → None
```

---

### Insert at Index — O(n)

```python
def insert_at_index(self, index, value):
    if index == 0:
        self.insert_at_head(value)
        return
    new_node = Node(value)
    current = self.head
    for _ in range(index - 1):  # walk to node before target
        if not current:
            raise IndexError("Index out of bounds")
        current = current.next
    new_node.next = current.next
    current.next = new_node
    self.size += 1
```

---

### Delete by Value — O(n)

```python
def delete(self, value):
    if not self.head:
        return False

    # Special case: head is the target
    if self.head.value == value:
        self.head = self.head.next
        self.size -= 1
        return True

    current = self.head
    while current.next:
        if current.next.value == value:
            current.next = current.next.next   # skip over node
            self.size -= 1
            return True
        current = current.next
    return False
```

**Memory diagram (deleting 20):**
```
Before: [10] → [20] → [30] → None
                 ↑ target

After:  [10] ──────► [30] → None
              skipped [20]
```

---

### Search — O(n)

```python
def search(self, value):
    current = self.head
    index = 0
    while current:
        if current.value == value:
            return index
        current = current.next
        index += 1
    return -1   # not found
```

**Output:**
```python
ll.search(30)   # → 2
ll.search(99)   # → -1
```

---

## 3️⃣ Singly vs Doubly Linked List

=== "Singly Linked"

    ```python
    class Node:
        def __init__(self, value):
            self.value = value
            self.next = None        # one pointer

    # Traverse: forward only
    # Delete: need previous node reference
    # Space: O(n) — 1 pointer per node
    ```

    ```
    head → [A|→] → [B|→] → [C|None]
    ```

=== "Doubly Linked"

    ```python
    class Node:
        def __init__(self, value):
            self.value = value
            self.next = None        # forward pointer
            self.prev = None        # backward pointer

    # Traverse: both directions
    # Delete: O(1) if you have the node reference
    # Space: O(n) — 2 pointers per node
    ```

    ```
    head → [None|A|→] ⇄ [←|B|→] ⇄ [←|C|None] ← tail
    ```

=== "When to use which"

    | Feature | Singly | Doubly |
    |---|---|---|
    | Memory | Less (1 ptr/node) | More (2 ptrs/node) |
    | Reverse traversal | ❌ | ✅ |
    | Delete with node ref | O(n) | O(1) |
    | Implementation | Simpler | More complex |
    | Use case | Stack, simple queue | Deque, browser history, LRU cache |

---

## 4️⃣ Classic Interview Patterns

### Reverse a Linked List — O(n) time, O(1) space

```python
def reverse(self):
    prev = None
    current = self.head
    while current:
        next_node = current.next   # save next
        current.next = prev        # reverse the pointer
        prev = current             # move prev forward
        current = next_node        # move current forward
    self.head = prev
```

**Step-by-step visualization:**
```
Initial:  None ← ? | [1]→[2]→[3]→None

Step 1:   None ← [1] | [2]→[3]→None
Step 2:   None ← [1]←[2] | [3]→None
Step 3:   None ← [1]←[2]←[3] |

Result:   head → [3]→[2]→[1]→None
```

---

### Detect a Cycle — Floyd's Algorithm

```python
def has_cycle(self):
    slow = self.head
    fast = self.head
    while fast and fast.next:
        slow = slow.next          # moves 1 step
        fast = fast.next.next     # moves 2 steps
        if slow == fast:
            return True           # they met — cycle exists
    return False
```

!!! info "Why does this work?"
    If there's a cycle, the fast pointer laps the slow pointer and they meet. If no cycle, fast reaches `None`. This is called **Floyd's Tortoise and Hare** algorithm — O(n) time, O(1) space.

---

### Find Middle Node — Two Pointers

```python
def find_middle(self):
    slow = self.head
    fast = self.head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow.value   # slow is at middle when fast hits end
```

**Output:**
```
List: 1 → 2 → 3 → 4 → 5
Middle: 3

List: 1 → 2 → 3 → 4
Middle: 3  (second middle for even length)
```

---

### Merge Two Sorted Lists

```python
def merge_sorted(l1, l2):
    dummy = Node(0)          # placeholder head
    current = dummy
    while l1 and l2:
        if l1.value <= l2.value:
            current.next = l1
            l1 = l1.next
        else:
            current.next = l2
            l2 = l2.next
        current = current.next
    current.next = l1 or l2  # attach remaining
    return dummy.next
```

---

## 5️⃣ Python's `collections.deque`

For most practical use cases, use Python's built-in `deque` (doubly linked list under the hood):

```python
from collections import deque

dq = deque([1, 2, 3])
dq.appendleft(0)    # O(1) insert at head → deque([0,1,2,3])
dq.append(4)        # O(1) insert at tail → deque([0,1,2,3,4])
dq.popleft()        # O(1) remove from head → 0
dq.pop()            # O(1) remove from tail → 4
```

!!! tip "Use `deque` in interviews unless asked to implement from scratch"
    `deque` gives you O(1) operations on both ends. It's the right tool for queues, sliding windows, and BFS.

---

## ✅ Quick Reference Summary

| Operation | Singly LL | Doubly LL | Python `list` | `deque` |
|---|---|---|---|---|
| Access by index | O(n) | O(n) | **O(1)** | O(n) |
| Insert at head | **O(1)** | **O(1)** | O(n) | **O(1)** |
| Insert at tail | O(n) | **O(1)** | **O(1)** amortized | **O(1)** |
| Insert at index | O(n) | O(n) | O(n) | O(n) |
| Delete at head | **O(1)** | **O(1)** | O(n) | **O(1)** |
| Delete by value | O(n) | O(n) | O(n) | O(n) |
| Search | O(n) | O(n) | O(n) | O(n) |
| Space | O(n) | O(2n) | O(n) | O(n) |

!!! warning "Key interview gotchas"
    - Always handle the **empty list** and **single-node** edge cases
    - When deleting, track the **previous node** (or use a dummy head trick)
    - Two-pointer (slow/fast) patterns solve many linked list problems in O(1) space
    - Reversing in-place requires **3 pointers**: `prev`, `current`, `next_node`