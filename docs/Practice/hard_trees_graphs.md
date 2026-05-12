# 🌳 Hard Practice Problems — Trees & Graphs

!!! abstract "What You'll Learn"
    - ✅ Solve hard-difficulty tree and graph problems
    - ✅ Apply advanced DFS with state (colours, timestamps, depths)
    - ✅ Implement topological sort, Dijkstra, and Union-Find
    - ✅ Work with binary tree serialisation and segment operations

Hard tree and graph problems demand that you combine traversal with additional data structures. Dijkstra needs a heap; Union-Find needs path compression; topological sort needs in-degree tracking. Knowing *when* to reach for each tool separates strong candidates from great ones.

!!! tip "New to Hard Problems?"
    Make sure you are fluent with DFS/BFS, BST properties, and adjacency list graphs from Medium Trees & Graphs.

!!! info "Strategy"
    Before coding, ask: directed or undirected? Weighted or unweighted? Cyclic or acyclic? The answer determines which algorithm applies.

!!! warning "Keep in mind"
    Graph problems are particularly prone to TLE when you choose the wrong algorithm. Know the time complexity of DFS, BFS, Dijkstra, and Union-Find cold.

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

---

## 1️⃣ Binary Tree Maximum Path Sum

**Problem:** Find the maximum path sum in a binary tree. A path can start and end at any node.

**Example:**
```
Input:  root = [-10,9,20,null,null,15,7]
Output: 42  (15 → 20 → 7)
```

!!! tip "Hint"
    DFS returning the max gain from each node. At each node, compute the local path (left + node + right) and update a global max. Return only the single best branch upward.

```python
def max_path_sum(root):
    max_sum = [float('-inf')]

    def dfs(node):
        if not node:
            return 0
        left  = max(dfs(node.left), 0)
        right = max(dfs(node.right), 0)
        max_sum[0] = max(max_sum[0], node.val + left + right)
        return node.val + max(left, right)

    dfs(root)
    return max_sum[0]

def build(vals):
    if not vals:
        return None
    root = TreeNode(vals[0])
    from collections import deque
    q = deque([root]); i = 1
    while q and i < len(vals):
        node = q.popleft()
        if i < len(vals) and vals[i] is not None:
            node.left = TreeNode(vals[i]); q.append(node.left)
        i += 1
        if i < len(vals) and vals[i] is not None:
            node.right = TreeNode(vals[i]); q.append(node.right)
        i += 1
    return root

root = build([-10,9,20,None,None,15,7])
print(max_path_sum(root))
```
**Output:** `42`

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(h)  |

---

## 2️⃣ Serialize and Deserialize Binary Tree

**Problem:** Design an algorithm to serialize a binary tree to a string and deserialize it back.

**Example:**
```
Input:  root = [1,2,3,null,null,4,5]
Serialized: "1,2,N,N,3,4,N,N,5,N,N"
Deserialized: original tree
```

!!! tip "Hint"
    Preorder DFS. Use a sentinel (e.g. `"N"`) for null nodes. Deserialise by consuming tokens from a queue.

```python
from collections import deque

def serialize(root):
    result = []
    def dfs(node):
        if not node:
            result.append('N')
            return
        result.append(str(node.val))
        dfs(node.left)
        dfs(node.right)
    dfs(root)
    return ','.join(result)

def deserialize(data):
    tokens = deque(data.split(','))
    def dfs():
        val = tokens.popleft()
        if val == 'N':
            return None
        node = TreeNode(int(val))
        node.left = dfs()
        node.right = dfs()
        return node
    return dfs()

root = build([1,2,3,None,None,4,5])
s = serialize(root)
print(s)
root2 = deserialize(s)
print(serialize(root2))
```
**Output:**
```
1,2,N,N,3,4,N,N,5,N,N
1,2,N,N,3,4,N,N,5,N,N
```

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(n)  |

---

## 3️⃣ Word Ladder

**Problem:** Given `beginWord`, `endWord`, and a `wordList`, return the length of the shortest transformation sequence where each step changes exactly one letter and every word must be in the list.

**Example:**
```
Input:  beginWord = "hit", endWord = "cog",
        wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5  (hit → hot → dot → dog → cog)
```

!!! tip "Hint"
    BFS where each word is a node. Generate neighbours by replacing each character with a–z and checking the word list.

```python
from collections import deque

def ladder_length(beginWord, endWord, wordList):
    word_set = set(wordList)
    if endWord not in word_set:
        return 0

    queue = deque([(beginWord, 1)])
    visited = {beginWord}

    while queue:
        word, steps = queue.popleft()
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                new_word = word[:i] + c + word[i+1:]
                if new_word == endWord:
                    return steps + 1
                if new_word in word_set and new_word not in visited:
                    visited.add(new_word)
                    queue.append((new_word, steps + 1))

    return 0

print(ladder_length("hit", "cog", ["hot","dot","dog","lot","log","cog"]))
```
**Output:** `5`

| Complexity | Value |
|------------|-------|
| Time       | O(M² × N) — M = word length, N = list size |
| Space      | O(M² × N) |

---

## 4️⃣ Alien Dictionary

**Problem:** Given a sorted list of words from an alien language, determine the order of letters in that language. Return any valid ordering or `""` if impossible.

**Example:**
```
Input:  words = ["wrt","wrf","er","ett","rftt"]
Output: "wertf"
```

!!! tip "Hint"
    Build a directed graph from adjacent word comparisons. Run topological sort (DFS or Kahn's BFS). If a cycle exists, return `""`.

```python
from collections import defaultdict, deque

def alien_order(words):
    adj = defaultdict(set)
    in_degree = {c: 0 for word in words for c in word}

    for i in range(len(words) - 1):
        w1, w2 = words[i], words[i+1]
        min_len = min(len(w1), len(w2))
        if len(w1) > len(w2) and w1[:min_len] == w2[:min_len]:
            return ""
        for j in range(min_len):
            if w1[j] != w2[j]:
                if w2[j] not in adj[w1[j]]:
                    adj[w1[j]].add(w2[j])
                    in_degree[w2[j]] += 1
                break

    queue = deque([c for c in in_degree if in_degree[c] == 0])
    result = []

    while queue:
        c = queue.popleft()
        result.append(c)
        for neighbour in adj[c]:
            in_degree[neighbour] -= 1
            if in_degree[neighbour] == 0:
                queue.append(neighbour)

    return ''.join(result) if len(result) == len(in_degree) else ""

print(alien_order(["wrt","wrf","er","ett","rftt"]))
```
**Output:** `wertf`

| Complexity | Value |
|------------|-------|
| Time       | O(C) — C = total characters |
| Space      | O(1) — at most 26 letters |

---

## 5️⃣ Network Delay Time (Dijkstra)

**Problem:** Given a directed weighted graph, a starting node `k`, and `n` nodes, return the time for all nodes to receive the signal. Return `-1` if impossible.

**Example:**
```
Input:  times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2
Output: 2
```

!!! tip "Hint"
    Dijkstra's algorithm from node `k`. Use a min-heap of `(distance, node)`. Track shortest distance to each node.

```python
import heapq
from collections import defaultdict

def network_delay_time(times, n, k):
    graph = defaultdict(list)
    for u, v, w in times:
        graph[u].append((v, w))

    dist = {i: float('inf') for i in range(1, n+1)}
    dist[k] = 0
    heap = [(0, k)]

    while heap:
        d, u = heapq.heappop(heap)
        if d > dist[u]:
            continue
        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                heapq.heappush(heap, (dist[v], v))

    max_dist = max(dist.values())
    return max_dist if max_dist < float('inf') else -1

print(network_delay_time([[2,1,1],[2,3,1],[3,4,1]], 4, 2))
```
**Output:** `2`

| Complexity | Value |
|------------|-------|
| Time       | O((V + E) log V) |
| Space      | O(V + E)         |

---

## 6️⃣ Critical Connections in a Network (Bridges)

**Problem:** Given a network of `n` servers and connections, find all critical connections — removing any one would disconnect the network.

**Example:**
```
Input:  n = 4, connections = [[0,1],[1,2],[2,0],[1,3]]
Output: [[1,3]]
```

!!! tip "Hint"
    Tarjan's bridge-finding algorithm. Track `disc[u]` (discovery time) and `low[u]` (lowest reachable time). An edge `(u, v)` is a bridge if `low[v] > disc[u]`.

```python
from collections import defaultdict

def critical_connections(n, connections):
    graph = defaultdict(list)
    for u, v in connections:
        graph[u].append(v)
        graph[v].append(u)

    disc = [-1] * n
    low  = [-1] * n
    timer = [0]
    bridges = []

    def dfs(u, parent):
        disc[u] = low[u] = timer[0]
        timer[0] += 1
        for v in graph[u]:
            if v == parent:
                continue
            if disc[v] == -1:
                dfs(v, u)
                low[u] = min(low[u], low[v])
                if low[v] > disc[u]:
                    bridges.append([u, v])
            else:
                low[u] = min(low[u], disc[v])

    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)

    return bridges

print(critical_connections(4, [[0,1],[1,2],[2,0],[1,3]]))
```
**Output:** `[[1, 3]]`

| Complexity | Value |
|------------|-------|
| Time       | O(V + E) |
| Space      | O(V + E) |

---

## 7️⃣ Recover Binary Search Tree

**Problem:** Two nodes in a BST were swapped by mistake. Recover the BST without changing its structure.

**Example:**
```
Input:  root = [1,3,null,null,2]  (3 and 1 are swapped)
Output: [3,1,null,null,2]
```

!!! tip "Hint"
    In-order traversal of a BST should be sorted. Find the two nodes that are out of order — the first is where the value is too large, the second is where it's too small. Swap their values.

```python
def recover_tree(root):
    first = second = prev = None

    def in_order(node):
        nonlocal first, second, prev
        if not node:
            return
        in_order(node.left)
        if prev and prev.val > node.val:
            if not first:
                first = prev
            second = node
        prev = node
        in_order(node.right)

    in_order(root)
    first.val, second.val = second.val, first.val
```

| Complexity | Value |
|------------|-------|
| Time       | O(n)  |
| Space      | O(h)  |

---

## 8️⃣ Count of Smaller Numbers After Self

**Problem:** Given an integer array, return a count array where `count[i]` is the number of elements to the right of `nums[i]` that are smaller.

**Example:**
```
Input:  nums = [5,2,6,1]
Output: [2,1,1,0]
```

!!! tip "Hint"
    Modified merge sort. During the merge step, count how many elements from the right half are placed before elements of the left half.

```python
def count_smaller(nums):
    n = len(nums)
    counts = [0] * n
    indices = list(range(n))

    def merge_sort(idx):
        if len(idx) <= 1:
            return idx
        mid = len(idx) // 2
        left = merge_sort(idx[:mid])
        right = merge_sort(idx[mid:])

        merged = []
        l = r = 0
        right_count = 0

        while l < len(left) and r < len(right):
            if nums[right[r]] < nums[left[l]]:
                right_count += 1
                merged.append(right[r])
                r += 1
            else:
                counts[left[l]] += right_count
                merged.append(left[l])
                l += 1

        while l < len(left):
            counts[left[l]] += right_count
            merged.append(left[l])
            l += 1

        merged.extend(right[r:])
        return merged

    merge_sort(indices)
    return counts

print(count_smaller([5,2,6,1]))
```
**Output:** `[2, 1, 1, 0]`

| Complexity | Value |
|------------|-------|
| Time       | O(n log n) |
| Space      | O(n)       |

---

## ✅ Quick Reference Summary

| # | Problem | Key Technique | Time | Space |
|---|---------|---------------|------|-------|
| 1 | Binary Tree Max Path Sum | DFS with Global Max | O(n) | O(h) |
| 2 | Serialize / Deserialize Tree | Preorder DFS + Token Queue | O(n) | O(n) |
| 3 | Word Ladder | BFS on Word Graph | O(M²×N) | O(M²×N) |
| 4 | Alien Dictionary | Topological Sort (Kahn's) | O(C) | O(1) |
| 5 | Network Delay Time | Dijkstra | O((V+E) log V) | O(V+E) |
| 6 | Critical Connections | Tarjan's Bridge Algorithm | O(V+E) | O(V+E) |
| 7 | Recover BST | In-order DFS | O(n) | O(h) |
| 8 | Count Smaller After Self | Merge Sort with Count | O(n log n) | O(n) |
