# 🔍 Hard Practice Problems — Sorting & Searching

!!! abstract "What You'll Learn"
    - ✅ Solve hard-difficulty sorting and searching problems
    - ✅ Apply binary search to complex monotonic conditions
    - ✅ Use advanced sorting as a preprocessing step for harder queries
    - ✅ Combine divide-and-conquer with counting and selection problems

At the hard level, binary search is almost never "find a value in a sorted array." Instead, you're searching on an *answer space* with a custom feasibility check, or running binary search inside another algorithm. Sorting itself becomes a key preprocessing tool that unlocks greedy or two-pointer solutions.

!!! tip "New to Hard Problems?"
    Ensure you are fully comfortable with binary-search-on-answer from Medium Sorting & Searching before starting here.

!!! info "Strategy"
    For every binary search problem: (1) Define the search space. (2) Write the feasibility function. (3) Decide the invariant — when does `left` move? When does `right` move?

!!! warning "Keep in mind"
    Overflow is real in Java/C++. In Python it isn't, but always choose `mid = left + (right - left) // 2` as a habit to carry across languages.

---

## 1️⃣ Count of Range Sum

**Problem:** Given an integer array, count the number of range sums `sum(i, j)` that lie in `[lower, upper]`.

**Example:**
```
Input:  nums = [-2,5,-1], lower = -2, upper = 2
Output: 3  (ranges: [0,0], [2,2], [0,2])
```

!!! tip "Hint"
    Prefix sums + merge sort. During merge, count how many right-half prefix sums fall in `[left_prefix + lower, left_prefix + upper]` for each left-half prefix.

```python
def count_range_sum(nums, lower, upper):
    prefix = [0]
    for n in nums:
        prefix.append(prefix[-1] + n)

    def merge_sort(arr):
        if len(arr) <= 1:
            return arr
        mid = len(arr) // 2
        left  = merge_sort(arr[:mid])
        right = merge_sort(arr[mid:])

        count = 0
        j = k = 0
        for l_val in left:
            while j < len(right) and right[j] - l_val < lower:
                j += 1
            while k < len(right) and right[k] - l_val <= upper:
                k += 1
            count += k - j

        # Standard merge
        merged = []
        i = p = 0
        while i < len(left) and p < len(right):
            if left[i] <= right[p]:
                merged.append(left[i]); i += 1
            else:
                merged.append(right[p]); p += 1
        merged.extend(left[i:])
        merged.extend(right[p:])
        arr[:] = merged
        return arr, count

    _, total = merge_sort(prefix)
    return total

# Direct version that correctly returns count
def count_range_sum(nums, lower, upper):
    prefix = [0]
    for n in nums:
        prefix.append(prefix[-1] + n)

    self_count = [0]

    def merge_sort(arr):
        if len(arr) <= 1:
            return arr
        mid = len(arr) // 2
        left  = merge_sort(arr[:mid])
        right = merge_sort(arr[mid:])

        j = k = 0
        for l_val in left:
            while j < len(right) and right[j] - l_val < lower:
                j += 1
            while k < len(right) and right[k] - l_val <= upper:
                k += 1
            self_count[0] += k - j

        merged = []
        i = p = 0
        while i < len(left) and p < len(right):
            if left[i] <= right[p]:
                merged.append(left[i]); i += 1
            else:
                merged.append(right[p]); p += 1
        return merged + left[i:] + right[p:]

    merge_sort(prefix)
    return self_count[0]

print(count_range_sum([-2,5,-1], -2, 2))
```
**Output:** `3`

| Complexity | Value |
|------------|-------|
| Time       | O(n log n) |
| Space      | O(n)       |

---

## 2️⃣ Reverse Pairs

**Problem:** Count the number of important reverse pairs: `i < j` and `nums[i] > 2 * nums[j]`.

**Example:**
```
Input:  nums = [1,3,2,3,1]
Output: 2
```

!!! tip "Hint"
    Modified merge sort. Before merging two halves, count pairs across left/right using two pointers (since halves are already sorted at that point).

```python
def reverse_pairs(nums):
    count = [0]

    def merge_sort(arr):
        if len(arr) <= 1:
            return arr
        mid = len(arr) // 2
        left  = merge_sort(arr[:mid])
        right = merge_sort(arr[mid:])

        # Count pairs: left[i] > 2 * right[j]
        j = 0
        for l_val in left:
            while j < len(right) and l_val > 2 * right[j]:
                j += 1
            count[0] += j

        # Standard merge
        merged = []
        i = p = 0
        while i < len(left) and p < len(right):
            if left[i] <= right[p]:
                merged.append(left[i]); i += 1
            else:
                merged.append(right[p]); p += 1
        return merged + left[i:] + right[p:]

    merge_sort(nums)
    return count[0]

print(reverse_pairs([1,3,2,3,1]))
print(reverse_pairs([2,4,3,5,1]))
```
**Output:**
```
2
3
```

| Complexity | Value |
|------------|-------|
| Time       | O(n log n) |
| Space      | O(n)       |

---

## 3️⃣ Split Array Largest Sum

**Problem:** Split an array into `m` non-empty continuous subarrays to minimise the largest subarray sum. Return that minimum largest sum.

**Example:**
```
Input:  nums = [7,2,5,10,8], m = 2
Output: 18  (split [7,2,5] | [10,8])
```

!!! tip "Hint"
    Binary search on the answer. Search space: `[max(nums), sum(nums)]`. Check feasibility: can we split with largest sum ≤ mid in at most m parts?

```python
def split_array(nums, m):
    def can_split(limit):
        parts = 1
        total = 0
        for n in nums:
            if total + n > limit:
                parts += 1
                total = 0
            total += n
        return parts <= m

    left, right = max(nums), sum(nums)
    while left < right:
        mid = (left + right) // 2
        if can_split(mid):
            right = mid
        else:
            left = mid + 1
    return left

print(split_array([7,2,5,10,8], 2))
print(split_array([1,2,3,4,5], 2))
```
**Output:**
```
18
9
```

| Complexity | Value |
|------------|-------|
| Time       | O(n log(sum − max)) |
| Space      | O(1)                |

---

## 4️⃣ Smallest Range Covering Elements from K Lists

**Problem:** Given `k` sorted integer lists, find the smallest range `[a, b]` such that there is at least one number from each list in `[a, b]`.

**Example:**
```
Input:  nums = [[4,10,15,24,26],[0,9,12,20],[5,18,22,30]]
Output: [20,24]
```

!!! tip "Hint"
    Min-heap of `(value, list_index, element_index)`. Track the current max across the heap. The range is `[heap_min, current_max]`. Pop min and push the next element from that list.

```python
import heapq

def smallest_range(nums):
    heap = []
    current_max = float('-inf')

    for i, lst in enumerate(nums):
        heapq.heappush(heap, (lst[0], i, 0))
        current_max = max(current_max, lst[0])

    best = [float('-inf'), float('inf')]

    while heap:
        current_min, i, j = heapq.heappop(heap)

        if current_max - current_min < best[1] - best[0]:
            best = [current_min, current_max]

        if j + 1 == len(nums[i]):
            break

        next_val = nums[i][j+1]
        heapq.heappush(heap, (next_val, i, j+1))
        current_max = max(current_max, next_val)

    return best

print(smallest_range([[4,10,15,24,26],[0,9,12,20],[5,18,22,30]]))
```
**Output:** `[20, 24]`

| Complexity | Value |
|------------|-------|
| Time       | O(n log k) — n = total elements |
| Space      | O(k)                            |

---

## 5️⃣ Find K-th Smallest Pair Distance

**Problem:** Given an integer array, find the kth smallest absolute difference among all pairs.

**Example:**
```
Input:  nums = [1,3,1], k = 1
Output: 0
```

!!! tip "Hint"
    Binary search on the answer (distance). Count pairs with distance ≤ mid using sliding window on a sorted array.

```python
def smallest_distance_pair(nums, k):
    nums.sort()
    n = len(nums)

    def count_pairs(mid):
        count = left = 0
        for right in range(n):
            while nums[right] - nums[left] > mid:
                left += 1
            count += right - left
        return count

    left, right = 0, nums[-1] - nums[0]
    while left < right:
        mid = (left + right) // 2
        if count_pairs(mid) >= k:
            right = mid
        else:
            left = mid + 1
    return left

print(smallest_distance_pair([1,3,1], 1))
print(smallest_distance_pair([1,6,1], 3))
```
**Output:**
```
0
5
```

| Complexity | Value |
|------------|-------|
| Time       | O(n log n + n log W) — W = max diff |
| Space      | O(1) |

---

## 6️⃣ Maximum Gap

**Problem:** Given an unsorted array, find the maximum difference between successive elements in its sorted form. Must run in O(n) time and O(n) space.

**Example:**
```
Input:  nums = [3,6,9,1]
Output: 3
```

!!! tip "Hint"
    Bucket sort (pigeonhole). With n elements, by pigeonhole the max gap ≥ `(max−min)/(n−1)`. Create buckets of this size; max gap must be between buckets, not within them.

```python
def maximum_gap(nums):
    if len(nums) < 2:
        return 0

    min_val, max_val = min(nums), max(nums)
    if min_val == max_val:
        return 0

    n = len(nums)
    bucket_size = max(1, (max_val - min_val) // (n - 1))
    bucket_count = (max_val - min_val) // bucket_size + 1

    buckets = [[float('inf'), float('-inf')] for _ in range(bucket_count)]

    for num in nums:
        idx = (num - min_val) // bucket_size
        buckets[idx][0] = min(buckets[idx][0], num)
        buckets[idx][1] = max(buckets[idx][1], num)

    max_gap = 0
    prev_max = min_val

    for b_min, b_max in buckets:
        if b_min == float('inf'):
            continue
        max_gap = max(max_gap, b_min - prev_max)
        prev_max = b_max

    return max_gap

print(maximum_gap([3,6,9,1]))
print(maximum_gap([10]))
```
**Output:**
```
3
0
```

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## 7️⃣ Median from Data Stream

**Problem:** Design a data structure to efficiently insert numbers and find the median at any time.

!!! tip "Hint"
    Two heaps: a max-heap for the lower half and a min-heap for the upper half. Balance them after each insert so their sizes differ by at most 1.

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.lo = []  # max-heap (negate values)
        self.hi = []  # min-heap

    def addNum(self, num):
        heapq.heappush(self.lo, -num)
        # Balance: lo top must be ≤ hi top
        if self.hi and -self.lo[0] > self.hi[0]:
            heapq.heappush(self.hi, -heapq.heappop(self.lo))
        # Size balance: lo can be at most 1 larger
        if len(self.lo) > len(self.hi) + 1:
            heapq.heappush(self.hi, -heapq.heappop(self.lo))
        elif len(self.hi) > len(self.lo):
            heapq.heappush(self.lo, -heapq.heappop(self.hi))

    def findMedian(self):
        if len(self.lo) > len(self.hi):
            return float(-self.lo[0])
        return (-self.lo[0] + self.hi[0]) / 2.0

mf = MedianFinder()
mf.addNum(1); mf.addNum(2)
print(mf.findMedian())   # 1.5
mf.addNum(3)
print(mf.findMedian())   # 2.0
```
**Output:**
```
1.5
2.0
```

| Complexity | Value |
|------------|-------|
| Time       | O(log n) per add, O(1) median |
| Space      | O(n)                          |

---

## 8️⃣ Russian Doll Envelopes

**Problem:** Given envelopes `(width, height)`, find the maximum number you can nest (each dimension must be strictly larger).

**Example:**
```
Input:  envelopes = [[5,4],[6,4],[6,7],[2,3]]
Output: 3  (2,3) → (5,4) → (6,7)
```

!!! tip "Hint"
    Sort by width ascending; break ties by height *descending*. Then find the Longest Increasing Subsequence on heights only — the descending tie-break prevents using two envelopes with the same width.

```python
import bisect

def max_envelopes(envelopes):
    envelopes.sort(key=lambda x: (x[0], -x[1]))
    heights = [e[1] for e in envelopes]

    # LIS via patience sorting
    tails = []
    for h in heights:
        pos = bisect.bisect_left(tails, h)
        if pos == len(tails):
            tails.append(h)
        else:
            tails[pos] = h

    return len(tails)

print(max_envelopes([[5,4],[6,4],[6,7],[2,3]]))
print(max_envelopes([[1,1],[1,1],[1,1]]))
```
**Output:**
```
3
1
```

| Complexity | Value |
|------------|-------|
| Time       | O(n log n) |
| Space      | O(n)       |

---

## ✅ Quick Reference Summary

| # | Problem | Key Technique | Time | Space |
|---|---------|---------------|------|-------|
| 1 | Count of Range Sum | Merge Sort + Prefix Sum | O(n log n) | O(n) |
| 2 | Reverse Pairs | Modified Merge Sort | O(n log n) | O(n) |
| 3 | Split Array Largest Sum | Binary Search on Answer | O(n log S) | O(1) |
| 4 | Smallest Range from K Lists | Min-Heap + Sliding Max | O(n log k) | O(k) |
| 5 | Kth Smallest Pair Distance | Binary Search + Sliding Window | O(n log W) | O(1) |
| 6 | Maximum Gap | Bucket Sort (Pigeonhole) | O(n) | O(n) |
| 7 | Median from Data Stream | Two Heaps | O(log n) add | O(n) |
| 8 | Russian Doll Envelopes | Sort + LIS (Patience) | O(n log n) | O(n) |
