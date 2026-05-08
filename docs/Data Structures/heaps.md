# Heaps

!!! abstract "What You'll Learn"
    - ✅ What a heap is and the heap property (min-heap vs max-heap)
    - ✅ How heaps are stored as arrays — no pointers needed
    - ✅ Core operations: insert, extract-min, heapify
    - ✅ Python's `heapq` module — the practical tool for interviews
    - ✅ Time and space complexity of every operation
    - ✅ Classic interview patterns: K largest, K smallest, merge K lists, median stream

A **heap** is a complete binary tree that satisfies the **heap property** — every parent node is ordered relative to its children. Heaps are the go-to structure whenever you need fast access to the **minimum or maximum** element, without keeping the whole collection sorted.

!!! tip "New to Heaps?"
    Think of a heap like a **priority queue at a hospital** — the most critical patient (min/max) is always at the front. When a new patient arrives or one is treated, the queue reorganises to keep the most critical at the top. You never care about full sorted order — just who's next.

!!! info "Coming from Trees?"
    A heap *is* a binary tree, but stored cleverly as a flat array. There are no `left`/`right` pointers — index arithmetic replaces them. This makes heaps cache-friendly and memory-efficient.

!!! warning "Keep in mind"
    Python's `heapq` is a **min-heap only**. To simulate a max-heap, insert negated values (`-x`). A heap does **not** support O(log n) search by value — it only guarantees fast access to the root.

---

## The Heap Property

=== "Min-Heap"

    ```
    Every parent ≤ its children.
    The SMALLEST element is always at the root.

             [1]
            /    \
          [3]    [5]
         /   \   /
       [4]  [8][7]

    Root = 1 → always the minimum ✅
    ```

=== "Max-Heap"

    ```
    Every parent ≥ its children.
    The LARGEST element is always at the root.

             [9]
            /    \
          [7]    [6]
         /   \   /
       [4]  [3][2]

    Root = 9 → always the maximum ✅
    ```

=== "NOT a Heap"

    ```
             [3]
            /    \
          [1]    [5]       ✗ parent [3] > child [1]
         /   \             → violates min-heap property
       [4]  [8]
    ```

---

## 1️⃣ Heap as an Array

A heap doesn't use node objects. It maps a complete binary tree to a flat list using index math:

```
Heap array:  [1, 3, 5, 4, 8, 7]
              0  1  2  3  4  5

              index 0  →  [1]         root
              index 1  →  [3]         left child of 0
              index 2  →  [5]         right child of 0
              index 3  →  [4]         left child of 1
              index 4  →  [8]         right child of 1
              index 5  →  [7]         left child of 2
```

**Index formulas:**

```python
parent(i)      = (i - 1) // 2
left_child(i)  = 2 * i + 1
right_child(i) = 2 * i + 2
```

**Diagram:**
```
Array: [ 1  | 3  | 5  | 4  | 8  | 7  ]
Index:   0    1    2    3    4    5

              [1]  ← index 0
             /    \
           [3]    [5]   ← index 1, 2
          /   \   /
        [4]  [8][7]     ← index 3, 4, 5

parent(3) = (3-1)//2 = 1  → [3] ✅
left_child(1) = 2*1+1 = 3 → [4] ✅
```

---

## 2️⃣ Core Operations

### Insert (Sift Up) — O(log n)

Add the new element at the end of the array, then bubble it up until the heap property is restored.

```python
def insert(heap, value):
    heap.append(value)              # add at end
    i = len(heap) - 1
    while i > 0:
        parent = (i - 1) // 2
        if heap[parent] > heap[i]:  # min-heap: parent should be smaller
            heap[parent], heap[i] = heap[i], heap[parent]  # swap
            i = parent
        else:
            break                   # heap property restored
```

**Step-by-step (inserting 2 into `[1, 3, 5, 4, 8, 7]`):**
```
Step 1 — append 2:
[ 1  | 3  | 5  | 4  | 8  | 7  | 2 ]
                                  ↑ i=6, parent=2 → heap[2]=5 > 2, swap

Step 2 — sift up:
[ 1  | 3  | 2  | 4  | 8  | 7  | 5 ]
              ↑ i=2, parent=0 → heap[0]=1 < 2, stop

Final heap:
         [1]
        /    \
      [3]    [2]
     /   \   / \
   [4]  [8][7] [5]
```

---

### Extract Min (Sift Down) — O(log n)

Swap the root with the last element, remove the last, then push the new root down until heap property is restored.

```python
def extract_min(heap):
    if not heap:
        return None
    if len(heap) == 1:
        return heap.pop()

    min_val = heap[0]
    heap[0] = heap.pop()           # move last element to root
    i = 0
    n = len(heap)

    while True:
        left  = 2 * i + 1
        right = 2 * i + 2
        smallest = i

        if left < n and heap[left] < heap[smallest]:
            smallest = left
        if right < n and heap[right] < heap[smallest]:
            smallest = right

        if smallest == i:
            break                  # heap property restored
        heap[i], heap[smallest] = heap[smallest], heap[i]
        i = smallest

    return min_val
```

**Step-by-step (extract min from `[1, 3, 2, 4, 8, 7]`):**
```
Step 1 — swap root with last, pop:
[ 7  | 3  | 2  | 4  | 8 ]    removed 1, 7 is now root
  ↑ sift down

Step 2 — compare 7 with children 3 and 2, smallest=2, swap:
[ 2  | 3  | 7  | 4  | 8 ]
              ↑ i=2, children: none smaller → stop

Result: extracted 1, heap = [2, 3, 7, 4, 8] ✅
```

---

### Heapify (Build Heap from List) — O(n)

Convert an arbitrary list into a heap in-place. Start from the last non-leaf and sift down each node.

```python
def heapify(arr):
    n = len(arr)
    # last non-leaf is at index n//2 - 1
    for i in range(n // 2 - 1, -1, -1):
        sift_down(arr, i, n)

def sift_down(arr, i, n):
    while True:
        left, right, smallest = 2*i+1, 2*i+2, i
        if left < n and arr[left] < arr[smallest]:
            smallest = left
        if right < n and arr[right] < arr[smallest]:
            smallest = right
        if smallest == i:
            break
        arr[i], arr[smallest] = arr[smallest], arr[i]
        i = smallest
```

**Output:**
```python
arr = [9, 4, 7, 1, 8, 3]
heapify(arr)
print(arr)   # → [1, 4, 3, 9, 8, 7]
```

!!! info "Why O(n) and not O(n log n)?"
    Intuitively: most nodes are near the bottom and travel a short distance. The mathematical sum of work across all levels converges to O(n). This is why `heapq.heapify()` is O(n), not O(n log n).

---

## 3️⃣ Python's `heapq` — The Practical Tool

```python
import heapq

# Build a min-heap
nums = [5, 1, 8, 3, 2]
heapq.heapify(nums)              # O(n), in-place
print(nums)                      # → [1, 3, 8, 5, 2]  (heap-ordered)

# Push
heapq.heappush(nums, 0)          # O(log n)
print(nums[0])                   # → 0  (always the min)

# Pop min
print(heapq.heappop(nums))       # → 0, O(log n)

# Peek min without removing
print(nums[0])                   # → 1

# Push and pop in one step (more efficient)
heapq.heapreplace(nums, 4)       # pop min, push 4 — O(log n)
heapq.heappushpop(nums, 0)       # push 0, pop min — O(log n)

# N largest / N smallest
print(heapq.nlargest(3, nums))   # → [8, 5, 4]
print(heapq.nsmallest(3, nums))  # → [1, 2, 3]
```

---

### Max-Heap in Python (Negate Trick)

```python
import heapq

nums = [5, 1, 8, 3, 2]
max_heap = [-x for x in nums]
heapq.heapify(max_heap)

# Push
heapq.heappush(max_heap, -9)

# Pop max
max_val = -heapq.heappop(max_heap)
print(max_val)   # → 9
```

**Output:**
```
max_heap (stored):  [-9, -5, -8, -1, -2, -3]
max_heap (actual):  [ 9,  5,  8,  1,  2,  3]
                     ↑ root = largest ✅
```

---

### Heap of Tuples (Priority + Value)

```python
import heapq

# heapq compares tuples element by element
# use (priority, value) for a priority queue
tasks = []
heapq.heappush(tasks, (2, "medium task"))
heapq.heappush(tasks, (1, "urgent task"))
heapq.heappush(tasks, (3, "low priority"))

while tasks:
    priority, task = heapq.heappop(tasks)
    print(f"[{priority}] {task}")
```

**Output:**
```
[1] urgent task
[2] medium task
[3] low priority
```

!!! tip "Tuple trick for max-heap with data"
    Store `(-priority, value)` to get max-heap ordering while keeping the associated data.

---

## 4️⃣ Classic Interview Patterns

### K Smallest Elements — O(n log k)

```python
import heapq

def k_smallest(nums, k):
    return heapq.nsmallest(k, nums)

# Manual approach using a max-heap of size k:
def k_smallest_manual(nums, k):
    max_heap = []
    for num in nums:
        heapq.heappush(max_heap, -num)
        if len(max_heap) > k:
            heapq.heappop(max_heap)         # remove the largest
    return sorted(-x for x in max_heap)
```

**Output:**
```python
k_smallest([3, 1, 4, 1, 5, 9, 2, 6], 3)
# → [1, 1, 2]
```

!!! info "Why a max-heap for K smallest?"
    Keep a max-heap of size k. If a new element is smaller than the current max, it evicts it. The heap always holds the k smallest seen so far.

---

### K Largest Elements — O(n log k)

```python
import heapq

def k_largest(nums, k):
    return heapq.nlargest(k, nums)

# Manual approach using a min-heap of size k:
def k_largest_manual(nums, k):
    min_heap = []
    for num in nums:
        heapq.heappush(min_heap, num)
        if len(min_heap) > k:
            heapq.heappop(min_heap)         # remove the smallest
    return sorted(min_heap, reverse=True)
```

**Output:**
```python
k_largest([3, 1, 4, 1, 5, 9, 2, 6], 3)
# → [9, 6, 5]
```

---

### Kth Largest Element — O(n log k)

```python
import heapq

def kth_largest(nums, k):
    min_heap = heapq.nsmallest(k, nums)   # keep k largest
    return min_heap[0]                     # smallest of those = kth largest

# Or streaming version:
def kth_largest_stream(nums, k):
    heap = nums[:k]
    heapq.heapify(heap)
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heapreplace(heap, num)
    return heap[0]
```

**Output:**
```python
kth_largest([3, 1, 4, 1, 5, 9, 2, 6], 2)
# → 6  (2nd largest)
```

---

### Merge K Sorted Lists — O(n log k)

```python
import heapq

def merge_k_sorted(lists):
    result = []
    heap = []

    # Push (value, list_index, element_index) for each list's first element
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))

    while heap:
        val, i, j = heapq.heappop(heap)
        result.append(val)
        if j + 1 < len(lists[i]):
            heapq.heappush(heap, (lists[i][j+1], i, j+1))

    return result
```

**Output:**
```python
lists = [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
merge_k_sorted(lists)
# → [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

---

### Median of a Data Stream — O(log n) insert, O(1) median

Use two heaps: a max-heap for the lower half, a min-heap for the upper half. Keep them balanced.

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.lo = []   # max-heap (lower half) — store negated
        self.hi = []   # min-heap (upper half)

    def add_num(self, num):
        heapq.heappush(self.lo, -num)           # push to lower half

        # Balance: ensure lo max ≤ hi min
        if self.hi and -self.lo[0] > self.hi[0]:
            heapq.heappush(self.hi, -heapq.heappop(self.lo))

        # Balance sizes: lo can have at most 1 more than hi
        if len(self.lo) > len(self.hi) + 1:
            heapq.heappush(self.hi, -heapq.heappop(self.lo))
        elif len(self.hi) > len(self.lo):
            heapq.heappush(self.lo, -heapq.heappop(self.hi))

    def find_median(self):
        if len(self.lo) > len(self.hi):
            return -self.lo[0]
        return (-self.lo[0] + self.hi[0]) / 2
```

**Visualization:**
```
Stream: 1, 7, 3, 5

After 1:   lo=[1]         hi=[]          median = 1
After 7:   lo=[1]         hi=[7]         median = (1+7)/2 = 4.0
After 3:   lo=[3,1]       hi=[7]         median = 3
After 5:   lo=[3,1]       hi=[5,7]       median = (3+5)/2 = 4.0
            ↑ max-heap     ↑ min-heap
```

---

## 5️⃣ Heap Sort — O(n log n)

```python
def heap_sort(arr):
    heapq.heapify(arr)                        # O(n)
    return [heapq.heappop(arr) for _ in arr]  # O(n log n)

# In-place version (max-heap, no extra space):
def heap_sort_inplace(arr):
    n = len(arr)
    # Build max-heap
    for i in range(n // 2 - 1, -1, -1):
        sift_down_max(arr, i, n)
    # Extract elements
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]       # move max to end
        sift_down_max(arr, 0, i)              # restore heap on reduced range
```

**Output:**
```python
arr = [5, 1, 8, 3, 2]
print(heap_sort(arr))   # → [1, 2, 3, 5, 8]
```

---

## ✅ Quick Reference Summary

| Operation | Time | Notes |
|---|---|---|
| Build heap (`heapify`) | O(n) | Not O(n log n) — bottom-up is smarter |
| Insert (`heappush`) | O(log n) | Sift up from last position |
| Extract min (`heappop`) | O(log n) | Sift down from root |
| Peek min | O(1) | `heap[0]` — no removal |
| Search by value | O(n) | Heap gives no search shortcut |
| K smallest/largest | O(n log k) | Maintain a heap of size k |
| Heap sort | O(n log n) | In-place, not stable |
| Merge K sorted lists | O(n log k) | k = number of lists |
| Median stream insert | O(log n) | Two-heap technique |
| Median stream query | O(1) | Always at the heap tops |

| Pattern | Heap type | Size |
|---|---|---|
| K smallest | Max-heap | k |
| K largest | Min-heap | k |
| Kth largest | Min-heap | k |
| Kth smallest | Max-heap | k |
| Median stream | Min + Max | n/2 each |
| Merge K lists | Min-heap | k (one per list) |

!!! warning "Key interview gotchas"
    - Python `heapq` is **min-heap only** — negate values for max-heap behaviour
    - Heap does **not** sort the full array — `heap[1]` is not necessarily the 2nd smallest
    - For "K largest" use a **min**-heap of size k (counterintuitive but correct)
    - For "K smallest" use a **max**-heap of size k
    - `heapq.nlargest(k, arr)` and `heapq.nsmallest(k, arr)` are convenient but O(n log k) — not O(k log n)
    - Always use `(priority, value)` tuples when heaping objects to avoid comparison errors