# 🔍 Medium Practice Problems — Sorting & Searching

!!! abstract "What You'll Learn"
    - ✅ Solve medium-difficulty sorting and searching problems
    - ✅ Apply binary search to non-obvious search spaces
    - ✅ Implement and adapt classic sorting algorithms
    - ✅ Use binary search on the answer (search on value, not index)

Sorting and searching problems reward clean thinking about invariants. Binary search especially is misunderstood — it's not just for sorted arrays. Many medium problems require you to binary search on a *range of possible answers*. That pattern alone unlocks dozens of problems.

!!! tip "New to Sorting & Searching?"
    Make sure you know O(n log n) sorts (merge sort, quicksort) and standard binary search before starting.

!!! info "Already know the basics?"
    For every binary search problem, explicitly define: what is the search space? What is the condition? What does it mean to go left vs right?

!!! warning "Keep in mind"
    Off-by-one errors are the #1 bug in binary search. Always trace through small examples to verify your `left`, `right`, and termination condition.

---

## 1️⃣ Search in Rotated Sorted Array

**Problem:** A sorted array has been rotated at an unknown pivot. Search for a target and return its index, or `-1` if not found. Must be O(log n).

**Example:**
```
Input:  nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```

!!! tip "Hint"
    One half of the array is always sorted. Check which half, then decide if target falls there; otherwise search the other.

```python
def search_rotated(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] == target:
            return mid
        # Left half is sorted
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        # Right half is sorted
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    return -1

print(search_rotated([4,5,6,7,0,1,2], 0))
print(search_rotated([4,5,6,7,0,1,2], 3))
```
**Output:**
```
4
-1
```

| Complexity | Value |
|------------|-------|
| Time       | O(log n) |
| Space      | O(1)     |

---

## 2️⃣ Find Minimum in Rotated Sorted Array

**Problem:** Find the minimum element in a rotated sorted array with unique elements.

**Example:**
```
Input:  nums = [3,4,5,1,2]
Output: 1
```

!!! tip "Hint"
    Binary search. The minimum is in the unsorted half. If `nums[mid] > nums[right]`, min is to the right.

```python
def find_min(nums):
    left, right = 0, len(nums) - 1
    while left < right:
        mid = (left + right) // 2
        if nums[mid] > nums[right]:
            left = mid + 1
        else:
            right = mid
    return nums[left]

print(find_min([3,4,5,1,2]))
print(find_min([4,5,6,7,0,1,2]))
```
**Output:**
```
1
0
```

| Complexity | Value |
|------------|-------|
| Time       | O(log n) |
| Space      | O(1)     |

---

## 3️⃣ Kth Largest Element in an Array

**Problem:** Find the kth largest element in an unsorted array. Not the kth distinct element.

**Example:**
```
Input:  nums = [3,2,1,5,6,4], k = 2
Output: 5
```

!!! tip "Hint"
    QuickSelect gives O(n) average. Alternatively, use a min-heap of size k: O(n log k).

=== "QuickSelect (O(n) avg)"
    ```python
    import random

    def find_kth_largest(nums, k):
        target = len(nums) - k  # kth largest = (n-k)th smallest

        def quickselect(l, r):
            pivot = nums[r]
            p = l
            for i in range(l, r):
                if nums[i] <= pivot:
                    nums[i], nums[p] = nums[p], nums[i]
                    p += 1
            nums[p], nums[r] = nums[r], nums[p]
            if p == target:
                return nums[p]
            elif p < target:
                return quickselect(p + 1, r)
            else:
                return quickselect(l, p - 1)

        return quickselect(0, len(nums) - 1)

    print(find_kth_largest([3,2,1,5,6,4], 2))
    ```

=== "Min-Heap (O(n log k))"
    ```python
    import heapq

    def find_kth_largest_heap(nums, k):
        heap = []
        for num in nums:
            heapq.heappush(heap, num)
            if len(heap) > k:
                heapq.heappop(heap)
        return heap[0]

    print(find_kth_largest_heap([3,2,1,5,6,4], 2))
    ```

**Output:** `5`

| Approach | Time | Space |
|----------|------|-------|
| QuickSelect | O(n) avg | O(1) |
| Min-Heap | O(n log k) | O(k) |

---

## 4️⃣ Merge Intervals

**Problem:** Given an array of intervals, merge all overlapping intervals.

**Example:**
```
Input:  intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
```

!!! tip "Hint"
    Sort by start time. Iterate: if current interval's start ≤ last merged interval's end, merge. Otherwise, push as new interval.

```python
def merge_intervals(intervals):
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    return merged

print(merge_intervals([[1,3],[2,6],[8,10],[15,18]]))
```
**Output:** `[[1, 6], [8, 10], [15, 18]]`

| Complexity | Value |
|------------|-------|
| Time       | O(n log n) |
| Space      | O(n)       |

---

## 5️⃣ Search a 2D Matrix

**Problem:** A matrix has rows sorted left-to-right and each row's first element is greater than the previous row's last. Search for a target in O(log(m×n)).

**Example:**
```
Input:  matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
Output: True
```

!!! tip "Hint"
    Treat the matrix as a flattened sorted array. Map `mid` index back to `(mid // cols, mid % cols)`.

```python
def search_matrix(matrix, target):
    rows, cols = len(matrix), len(matrix[0])
    left, right = 0, rows * cols - 1
    while left <= right:
        mid = (left + right) // 2
        val = matrix[mid // cols][mid % cols]
        if val == target:
            return True
        elif val < target:
            left = mid + 1
        else:
            right = mid - 1
    return False

print(search_matrix([[1,3,5,7],[10,11,16,20],[23,30,34,60]], 3))
print(search_matrix([[1,3,5,7],[10,11,16,20],[23,30,34,60]], 13))
```
**Output:**
```
True
False
```

| Complexity | Value |
|------------|-------|
| Time       | O(log(m × n)) |
| Space      | O(1)          |

---

## 6️⃣ Top K Frequent Elements

**Problem:** Given an integer array, return the k most frequent elements.

**Example:**
```
Input:  nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```

!!! tip "Hint"
    Count frequencies with a hash map, then use bucket sort (frequency as index) for O(n) solution.

```python
def top_k_frequent(nums, k):
    from collections import Counter
    count = Counter(nums)
    buckets = [[] for _ in range(len(nums) + 1)]
    for num, freq in count.items():
        buckets[freq].append(num)

    result = []
    for i in range(len(buckets) - 1, 0, -1):
        for num in buckets[i]:
            result.append(num)
            if len(result) == k:
                return result
    return result

print(top_k_frequent([1,1,1,2,2,3], 2))
```
**Output:** `[1, 2]`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## 7️⃣ Capacity To Ship Packages Within D Days

**Problem:** An array `weights` represents package weights. A ship carries packages in order, up to capacity `cap` per day. Find the minimum capacity to ship all packages within `D` days.

**Example:**
```
Input:  weights = [1,2,3,4,5,6,7,8,9,10], days = 5
Output: 15
```

!!! tip "Hint"
    Binary search on the *answer* (capacity). Search space: `[max(weights), sum(weights)]`. Check if a given capacity can ship within D days.

```python
def ship_within_days(weights, days):
    def can_ship(capacity):
        needed_days = 1
        current_load = 0
        for w in weights:
            if current_load + w > capacity:
                needed_days += 1
                current_load = 0
            current_load += w
        return needed_days <= days

    left, right = max(weights), sum(weights)
    while left < right:
        mid = (left + right) // 2
        if can_ship(mid):
            right = mid
        else:
            left = mid + 1
    return left

print(ship_within_days([1,2,3,4,5,6,7,8,9,10], 5))
```
**Output:** `15`

| Complexity | Value |
|------------|-------|
| Time       | O(n log(sum − max)) |
| Space      | O(1)                |

---

## 8️⃣ Find Peak Element

**Problem:** A peak element is greater than its neighbours. Find any peak in O(log n). Assume `nums[-1] = nums[n] = -∞`.

**Example:**
```
Input:  nums = [1,2,3,1]
Output: 2  (index 2, value 3)
```

!!! tip "Hint"
    Binary search. If `nums[mid] < nums[mid+1]`, a peak is to the right (slope going up). Otherwise, it's to the left or at mid.

```python
def find_peak_element(nums):
    left, right = 0, len(nums) - 1
    while left < right:
        mid = (left + right) // 2
        if nums[mid] < nums[mid + 1]:
            left = mid + 1
        else:
            right = mid
    return left

print(find_peak_element([1,2,3,1]))
print(find_peak_element([1,2,1,3,5,6,4]))
```
**Output:**
```
2
5
```

| Complexity | Value |
|------------|-------|
| Time       | O(log n) |
| Space      | O(1)     |

---

## ✅ Quick Reference Summary

| # | Problem | Key Technique | Time | Space |
|---|---------|---------------|------|-------|
| 1 | Search Rotated Sorted Array | Binary Search (sorted half check) | O(log n) | O(1) |
| 2 | Find Min in Rotated Array | Binary Search | O(log n) | O(1) |
| 3 | Kth Largest Element | QuickSelect / Min-Heap | O(n) avg | O(1) |
| 4 | Merge Intervals | Sort + Greedy | O(n log n) | O(n) |
| 5 | Search 2D Matrix | Binary Search (flattened) | O(log mn) | O(1) |
| 6 | Top K Frequent Elements | Bucket Sort | O(n) | O(n) |
| 7 | Ship Packages in D Days | Binary Search on Answer | O(n log S) | O(1) |
| 8 | Find Peak Element | Binary Search (slope) | O(log n) | O(1) |