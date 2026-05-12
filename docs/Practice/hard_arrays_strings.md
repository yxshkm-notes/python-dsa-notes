# 🧩 Hard Practice Problems — Arrays & Strings

!!! abstract "What You'll Learn"
    - ✅ Solve hard-difficulty array and string problems
    - ✅ Apply advanced sliding window, prefix sum, and two-pointer strategies
    - ✅ Combine multiple techniques in a single solution
    - ✅ Optimise brute-force solutions to O(n) or O(n log n)

Hard array and string problems demand that you chain techniques together — a sliding window plus a hash map, or a prefix sum with binary search. If a solution feels like it needs two nested loops, there's almost always a smarter way.

!!! tip "New to Hard Problems?"
    Make sure you're comfortable with all Medium Arrays & Strings patterns before attempting these.

!!! info "Strategy"
    Spend at least 15–20 minutes on each problem before checking the hint. The struggle is where learning happens.

!!! warning "Keep in mind"
    Many hard problems have deceptively simple-looking statements. Re-read carefully — the constraints are the hint.

---

## 1️⃣ Trapping Rain Water

**Problem:** Given `n` non-negative integers representing an elevation map, compute how much water it can trap after raining.

**Example:**
```
Input:  height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

!!! tip "Hint"
    Two pointers. Maintain `left_max` and `right_max`. The pointer at the shorter side determines water trapped at that position.

```python
def trap(height):
    left, right = 0, len(height) - 1
    left_max = right_max = 0
    water = 0

    while left < right:
        if height[left] < height[right]:
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1

    return water

print(trap([0,1,0,2,1,0,1,3,2,1,2,1]))
```
**Output:** `6`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(1)  |

---

## 2️⃣ Minimum Window Substring

**Problem:** Given strings `s` and `t`, return the minimum window substring of `s` that contains all characters of `t`. Return `""` if none exists.

**Example:**
```
Input:  s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
```

!!! tip "Hint"
    Sliding window with two frequency maps. Expand right until window is valid; shrink left to minimise.

```python
from collections import Counter

def min_window(s, t):
    if not t or not s:
        return ""

    need = Counter(t)
    have = {}
    formed = 0
    required = len(need)
    left = 0
    best = (float('inf'), 0, 0)  # (length, l, r)

    for right, char in enumerate(s):
        have[char] = have.get(char, 0) + 1
        if char in need and have[char] == need[char]:
            formed += 1
        while formed == required:
            if right - left + 1 < best[0]:
                best = (right - left + 1, left, right)
            have[s[left]] -= 1
            if s[left] in need and have[s[left]] < need[s[left]]:
                formed -= 1
            left += 1

    return "" if best[0] == float('inf') else s[best[1]:best[2]+1]

print(min_window("ADOBECODEBANC", "ABC"))
```
**Output:** `BANC`

| Complexity | Value |
|------------|-------|
| Time       | O(|s| + |t|) |
| Space      | O(|t|)       |

---

## 3️⃣ Median of Two Sorted Arrays

**Problem:** Given two sorted arrays `nums1` and `nums2`, find the median of the merged array. Must run in O(log(m+n)).

**Example:**
```
Input:  nums1 = [1,3], nums2 = [2]
Output: 2.0

Input:  nums1 = [1,2], nums2 = [3,4]
Output: 2.5
```

!!! tip "Hint"
    Binary search on the smaller array. Partition both arrays so that the left halves contain the smaller half of all elements.

```python
def find_median_sorted_arrays(nums1, nums2):
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1

    m, n = len(nums1), len(nums2)
    left, right = 0, m

    while left <= right:
        i = (left + right) // 2       # partition in nums1
        j = (m + n + 1) // 2 - i     # partition in nums2

        max_left1  = float('-inf') if i == 0 else nums1[i-1]
        min_right1 = float('inf')  if i == m else nums1[i]
        max_left2  = float('-inf') if j == 0 else nums2[j-1]
        min_right2 = float('inf')  if j == n else nums2[j]

        if max_left1 <= min_right2 and max_left2 <= min_right1:
            if (m + n) % 2 == 1:
                return float(max(max_left1, max_left2))
            return (max(max_left1, max_left2) + min(min_right1, min_right2)) / 2.0
        elif max_left1 > min_right2:
            right = i - 1
        else:
            left = i + 1

print(find_median_sorted_arrays([1,3], [2]))
print(find_median_sorted_arrays([1,2], [3,4]))
```
**Output:**
```
2.0
2.5
```

| Complexity | Value |
|------------|-------|
| Time       | O(log(min(m,n))) |
| Space      | O(1)             |

---

## 4️⃣ Sliding Window Maximum (Hard Variant — Return All Windows)

**Problem:** Given `nums` and `k`, return the maximum of every contiguous subarray of size `k`. (Classic hard; every window must be answered.)

**Example:**
```
Input:  nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
```

!!! tip "Hint"
    Monotonic decreasing deque of indices. Drop indices outside the window from the front; drop smaller values from the back before appending.

```python
from collections import deque

def max_sliding_window(nums, k):
    dq = deque()
    result = []

    for i, num in enumerate(nums):
        while dq and dq[0] <= i - k:
            dq.popleft()
        while dq and nums[dq[-1]] < num:
            dq.pop()
        dq.append(i)
        if i >= k - 1:
            result.append(nums[dq[0]])

    return result

print(max_sliding_window([1,3,-1,-3,5,3,6,7], 3))
```
**Output:** `[3, 3, 5, 5, 6, 7]`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(k)  |

---

## 5️⃣ First Missing Positive

**Problem:** Given an unsorted integer array, return the smallest missing positive integer. Must run in O(n) time and O(1) space.

**Example:**
```
Input:  nums = [3,4,-1,1]
Output: 2
```

!!! tip "Hint"
    Use the array itself as a hash map. Place each number `x` at index `x-1` if `1 ≤ x ≤ n`. Then scan for the first index where `nums[i] != i+1`.

```python
def first_missing_positive(nums):
    n = len(nums)

    # Place each number at its correct index
    for i in range(n):
        while 1 <= nums[i] <= n and nums[nums[i]-1] != nums[i]:
            correct = nums[i] - 1
            nums[i], nums[correct] = nums[correct], nums[i]

    for i in range(n):
        if nums[i] != i + 1:
            return i + 1

    return n + 1

print(first_missing_positive([3,4,-1,1]))
print(first_missing_positive([1,2,0]))
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

## 6️⃣ Longest Consecutive Sequence (O(n) Strict)

**Problem:** Return the length of the longest consecutive elements sequence in an unsorted array. Must be O(n).

**Example:**
```
Input:  nums = [0,3,7,2,5,8,4,6,0,1]
Output: 9  (0–8)
```

!!! tip "Hint"
    Hash set. Only start a sequence from a number with no left neighbour (`num-1` not in set). Grow rightward and count.

```python
def longest_consecutive(nums):
    num_set = set(nums)
    best = 0
    for num in num_set:
        if num - 1 not in num_set:
            length = 1
            while num + length in num_set:
                length += 1
            best = max(best, length)
    return best

print(longest_consecutive([0,3,7,2,5,8,4,6,0,1]))
```
**Output:** `9`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## 7️⃣ Substring With Concatenation of All Words

**Problem:** Given string `s` and array `words` (all same length), return start indices of all substrings that are a concatenation of all words (in any order).

**Example:**
```
Input:  s = "barfoothefoobarman", words = ["foo","bar"]
Output: [0,9]
```

!!! tip "Hint"
    Sliding window of width `len(words) * word_len`. Use a hash map to track how many of each word you've used; slide one word at a time.

```python
from collections import Counter

def find_substring(s, words):
    if not s or not words:
        return []

    word_len = len(words[0])
    total_len = word_len * len(words)
    word_count = Counter(words)
    result = []

    for i in range(len(s) - total_len + 1):
        seen = {}
        j = 0
        while j < len(words):
            word = s[i + j * word_len : i + (j+1) * word_len]
            if word in word_count:
                seen[word] = seen.get(word, 0) + 1
                if seen[word] > word_count[word]:
                    break
            else:
                break
            j += 1
        if j == len(words):
            result.append(i)

    return result

print(find_substring("barfoothefoobarman", ["foo","bar"]))
```
**Output:** `[0, 9]`

| Complexity | Value |
|------------|-------|
| Time       | O(n × m × w) |
| Space      | O(m)         |

---

## 8️⃣ Candy (Greedy)

**Problem:** `n` children stand in a row with ratings. Each child must get at least one candy. Children with higher ratings than their neighbours get more. Return the minimum total candies.

**Example:**
```
Input:  ratings = [1,0,2]
Output: 5  ([2,1,2])
```

!!! tip "Hint"
    Two passes. Left-to-right: give +1 candy if current rating > left. Right-to-left: give max(current, right+1) if current rating > right.

```python
def candy(ratings):
    n = len(ratings)
    candies = [1] * n

    for i in range(1, n):
        if ratings[i] > ratings[i-1]:
            candies[i] = candies[i-1] + 1

    for i in range(n-2, -1, -1):
        if ratings[i] > ratings[i+1]:
            candies[i] = max(candies[i], candies[i+1] + 1)

    return sum(candies)

print(candy([1,0,2]))
print(candy([1,2,2]))
```
**Output:**
```
5
4
```

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## ✅ Quick Reference Summary

| # | Problem | Key Technique | Time | Space |
|---|---------|---------------|------|-------|
| 1 | Trapping Rain Water | Two Pointers | O(n) | O(1) |
| 2 | Minimum Window Substring | Sliding Window + Freq Map | O(n) | O(t) |
| 3 | Median of Two Sorted Arrays | Binary Search on Partition | O(log min(m,n)) | O(1) |
| 4 | Sliding Window Maximum | Monotonic Deque | O(n) | O(k) |
| 5 | First Missing Positive | Array as Hash Map | O(n) | O(1) |
| 6 | Longest Consecutive Sequence | Hash Set | O(n) | O(n) |
| 7 | Substring Concatenation | Sliding Window + Counter | O(n×m×w) | O(m) |
| 8 | Candy | Two-Pass Greedy | O(n) | O(n) |
