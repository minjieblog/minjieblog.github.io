---
title: "Python 面试算法手册：数据结构、刷题模板与 AI 算法"
date: 2026-09-06T23:34:32+08:00
draft: false
tags: ["Python", "算法", "数据结构", "机器学习", "面试"]
categories: ["面试笔记"]
summary: "Python 面试速查：常用数据结构与方法、树图等经典刷题模板，以及 NumPy / PyTorch 实现的 AI 算法板子。"
description: "系统整理 Python 容器、二分、双指针、树、图、动态规划，以及线性回归、K-Means、PCA、Attention 等面试手写模板，附复杂度、输入约定和易错点。"
comments: true
---

这篇笔记按面试时的使用顺序整理：先熟悉 Python 的容器和方法，再记住常见算法的循环不变量，最后练习 AI 算法的公式、维度和实现。

**使用约定：** Part 1、Part 2 只依赖 Python 标准库；Part 3 使用 NumPy，最后的训练循环使用 PyTorch。代码以 Python 3.8+ 为基线。每个算法代码块可以独立复制；AI 部分除另有说明外，需要先执行 `import numpy as np`，Attention 还会复用前文的 `softmax`。

---

## 📚 Part 1: Python 数据结构与常用方法

### 0. 容器怎么选？

| 结构 | 特点 | 常用场景 | 常见操作复杂度 |
| --- | --- | --- | --- |
| `str` | 不可变字符序列 | 字符串处理 | 索引 O(1)，长度 k 的切片 O(k) |
| `list` | 可变动态数组 | 数组、栈 | 索引 O(1)，尾部追加均摊 O(1)，中间插删 O(n) |
| `tuple` | 不可变序列 | 坐标、复合状态 | 索引 O(1)，查找 O(n) |
| `dict` | 键值映射，保留插入顺序 | 哈希查找、计数 | 查找、插入、删除平均 O(1) |
| `set` | 无重复元素，不保证遍历顺序 | 去重、访问标记 | 查找、插入、删除平均 O(1) |
| `deque` | 双端队列 | BFS、滑动窗口 | 两端插删 O(1)，中间索引 O(n) |
| `heapq` | 基于列表的最小堆 | Top K、最短路 | 堆顶 O(1)，入堆出堆 O(log n) |

复杂度按常见 CPython 实现、固定大小元素估计；哈希容器极端冲突时可退化为 O(n)。字符串或长元组作为键时，还要考虑计算哈希和比较的代价。

### 1. str - 不可变字符串

```python
s = "hello"
len(s)                         # 5
s[0], s[-1]                    # ('h', 'o')
s[1:4]                         # 'ell'，左闭右开
s[::-1]                        # 'olleh'，反转得到新字符串

s.find("ll")                   # 2，找不到返回 -1
s.rfind("l")                   # 3，从右找
"el" in s                      # True
s.startswith("he")             # True
s.endswith("lo")               # True
# s.index("x")                 # 找不到会抛 ValueError

s.replace("l", "L")            # 'heLLo'，不修改 s
"a,b,,c".split(",")             # ['a', 'b', '', 'c']
" a  b\tc ".split()             # ['a', 'b', 'c']，合并空白
"-".join(["a", "b", "c"])       # 'a-b-c'
"  hello\n".strip()             # 'hello'
"hello".upper()                 # 'HELLO'
"HELLO".lower()                 # 'hello'
"abc".isalpha()                 # True
"123".isdigit()                 # True

int("123"), str(123)            # 字符串与整数互转
ord("a"), chr(97)               # (97, 'a')
f"score={3.14159:.2f}"          # 'score=3.14'

chars = list(s)
chars[0] = "H"
s = "".join(chars)              # 'Hello'
```

**易错点：** `strip("ab")` 删除两端连续出现的字符 `a` 或 `b`，不是删除固定子串。`isdigit()` 支持部分 Unicode 数字字符，不等于“必然能被 `int()` 解析”；题目限定 ASCII 数字时可用 `'0' <= ch <= '9'`。循环构造长字符串优先收集到列表后 `join`，避免反复复制。

### 2. list - 动态数组与栈

```python
a = [3, 1, 2]
a.append(4)                    # [3, 1, 2, 4]
a.extend([5, 6])               # 逐个追加元素
a.insert(0, 9)                 # 头部插入 O(n)
last = a.pop()                 # 删除并返回尾元素
first = a.pop(0)               # 删除头元素 O(n)，队列不要这么写
a.remove(3)                    # 删除第一个 3，不存在抛 ValueError
a[0] = 10
a[1:3]                         # 新列表，浅拷贝
a.count(2)                     # 计数 O(n)
2 in a                         # 查找 O(n)

b = sorted(a)                  # 返回新的升序列表
a.sort(reverse=True)           # 原地降序，返回 None
a.reverse()                    # 原地反转，返回 None
rows = [("a", 2), ("b", 1), ("c", 2)]
rows.sort(key=lambda x: (x[1], x[0]))  # 先按第二列，再按第一列

stack = []
stack.append(1)
top = stack[-1]                # 读取前要保证非空
stack.pop()

matrix = [[0] * 3 for _ in range(2)]  # 每一行是独立列表
copy_a = a.copy()               # 浅拷贝；嵌套对象仍共享
squares = [x * x for x in range(5) if x % 2 == 0]
```

**易错点：** 不要写 `matrix = [[0] * m] * n`，多行会引用同一个列表；不要写 `a = a.sort()`。需要复制嵌套可变对象时使用 `copy.deepcopy`。

### 3. tuple - 元组与可哈希状态

```python
point = (2, 3)
x, y = point                   # 解包
single = (1,)                  # 一个元素必须带逗号
empty = ()
tuple([1, 2, 3])
point.count(2)                 # 1
point.index(3)                 # 1

visited = {(0, 0), (1, 2)}
distance = {(0, 0): 0}         # 坐标作为字典键
a, b = 1, 2
a, b = b, a                    # 交换
```

**易错点：** 元组不能修改元素引用，但其中的列表仍可修改。只有所有元素都可哈希，整个元组才可哈希；`([1], 2)` 不能作为字典键或集合元素。

### 4. dict - 哈希表

```python
d = {"a": 1}
d["b"] = 2
d["a"] += 1
d.get("missing", 0)            # 0，不会插入该键
"a" in d                       # 检查键，不是值
d.setdefault("c", [])          # 不存在则插入默认值，并返回该值
d["c"].append(3)
d.update({"a": 5})             # 同名键覆盖
removed = d.pop("b", None)     # 删除并返回，缺失时返回 None
for key, value in d.items():
    pass
keys = list(d.keys())
values = list(d.values())
```

`dict` 的插入顺序不是键的排序顺序。遍历时不要增删键；可以遍历 `list(d)` 再删除。`dict.fromkeys(keys, [])` 会让所有键共享同一个列表，应改成 `{key: [] for key in keys}`。

### 5. set / frozenset - 集合

```python
s = set()                      # {} 是空字典
s.add(1)
s.update([2, 3, 3])            # {1, 2, 3}
s.discard(9)                   # 不存在也不报错
s.remove(1)                    # 不存在会抛 KeyError

a, b = {1, 2}, {2, 3}
a | b                          # 并集 {1, 2, 3}
a & b                          # 交集 {2}
a - b                          # 差集 {1}
a ^ b                          # 对称差 {1, 3}
{1} <= a                       # 是否子集
len(a), 2 in a
frozen = frozenset([1, 2])      # 不可变集合，可作为字典键
unique = list(dict.fromkeys([3, 1, 3, 2]))  # 保序去重 [3, 1, 2]
```

**易错点：** 集合不能按下标访问，`set.pop()` 弹出的也不是最小值。

### 6. collections - 计数器、默认字典、双端队列

```python
from collections import Counter, defaultdict, deque

cnt = Counter("banana")
cnt["a"]                       # 3
cnt["x"]                       # 0
cnt.most_common(2)             # [('a', 3), ('n', 2)]
cnt.update("a")                # 追加计数，不是覆盖
cnt.subtract("aa")             # 允许出现 0 或负数
positive = +cnt                # 仅保留正计数

groups = defaultdict(list)
groups["a"].append(1)           # 首次访问自动创建列表
freq = defaultdict(int)
freq["x"] += 1

q = deque([1, 2])
q.append(3)
q.appendleft(0)
q.popleft()                    # 0
q.pop()                        # 3
```

`Counter` 计数变成 0 后键仍存在；判断次数用 `cnt[x] > 0`，不是 `x in cnt`。`defaultdict` 的 `d[key]` 会为缺失键创建默认值，`d.get(key)` 不会。详细接口可查 [Python collections 文档](https://docs.python.org/3/library/collections.html)。

### 7. heapq / bisect - 堆与二分工具

```python
import heapq
from bisect import bisect_left, bisect_right, insort

h = [3, 1, 2]
heapq.heapify(h)                # 原地建最小堆 O(n)
heapq.heappush(h, 0)
smallest = heapq.heappop(h)
top = h[0]
heapq.nlargest(2, [1, 4, 2, 3]) # [4, 3]

max_heap = []                  # 数值取负模拟最大堆
heapq.heappush(max_heap, -5)
maximum = -heapq.heappop(max_heap)

tasks = []
heapq.heappush(tasks, (1, 0, {"name": "A"}))
heapq.heappush(tasks, (1, 1, {"name": "B"}))
# (优先级, 唯一序号, 对象)，避免同优先级时比较不可排序对象

a = [1, 2, 2, 4]              # 必须已升序
left = bisect_left(a, 2)       # 1，第一个 >= 2 的位置
right = bisect_right(a, 2)     # 3，第一个 > 2 的位置
count = right - left          # 2 的个数
insort(a, 3)                   # 插入仍是 O(n)，查位置才是 O(log n)
```

### 8. 内置函数与 Python 刷题坑

```python
from functools import lru_cache
from itertools import accumulate
from math import gcd, inf

a = [2, 4, 6]
list(enumerate(a))              # [(0, 2), (1, 4), (2, 6)]
list(zip([1, 2], [3, 4]))       # [(1, 3), (2, 4)]
sum(a), min(a), max(a)
any(x > 5 for x in a)          # True
all(x % 2 == 0 for x in a)     # True
list(accumulate(a))            # [2, 6, 12]
divmod(7, 3)                   # (2, 1)
pow(2, 10, 1000)               # 模幂 24
gcd(12, 18)                    # 6
```

| 容易写错的地方 | 记法 |
| --- | --- |
| `/` 与 `//` | `/` 得到浮点数；`//` 向下取整，`-3 // 2 == -2` |
| `is` 与 `==` | `is` 比对象身份；值比较用 `==`，空值用 `is None` |
| 默认可变参数 | 不写 `def f(path=[])`，用 `None` 后在函数内新建 |
| 空容器 | `if not a` 判断空；`min([])`、空列表 `pop()` 会报错 |
| 迭代器 | `map`、`zip` 通常只遍历一次，必要时转 `list` |
| 递归深度 | 深链、长图优先用显式栈；提高递归上限不保证栈安全 |
| 拷贝路径 | 回溯存答案用 `path.copy()`，不能直接存同一个 `path` |
| 整数溢出 | Python 整数可扩展，但大整数运算不是固定 O(1) |

---

## 🧩 Part 2: 常见算法题模板

### 0. 看到题目先选什么？

| 题目信号 | 优先考虑 |
| --- | --- |
| 有序数组、答案满足单调性 | 二分查找 / 二分答案 |
| 连续子串、区间维护 | 滑动窗口、前缀和 |
| 下一个更大元素 | 单调栈 |
| 树的层数、无权最短路 | BFS |
| 连通块、路径枚举 | DFS、并查集、回溯 |
| 依赖关系、课程顺序 | 拓扑排序 |
| 非负权最短路 | Dijkstra |
| 最优值、方案数、重叠子问题 | 动态规划 |

### 1. 哈希表 - 两数之和

**思路：** 遍历到 `x` 时，只在之前的元素中找 `target - x`，避免重复使用自身。时间 O(n)，空间 O(n)。

```python
def two_sum(nums, target):
    seen = {}
    for i, x in enumerate(nums):
        if target - x in seen:
            return [seen[target - x], i]
        seen[x] = i
    return []
```

### 2. 二分 - 第一个大于等于 target 的位置

**不变量：** `[0, left)` 都小于目标，`[right, n)` 都大于等于目标；待查区间是 `[left, right)`。时间 O(log n)，空间 O(1)。

```python
def lower_bound(nums, target):
    left, right = 0, len(nums)
    while left < right:
        mid = (left + right) // 2
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid
    return left                # 可能等于 len(nums)，不能直接取下标
```

改为 `nums[mid] <= target` 就是第一个严格大于目标的位置。二分答案时，把条件替换为“当前答案是否可行”，先确认可行性具有单调性。

### 3. 双指针与滑动窗口

#### 有序数组两数之和

时间 O(n)，额外空间 O(1)；返回从 0 开始的下标。

```python
def two_sum_sorted(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        total = nums[left] + nums[right]
        if total == target:
            return [left, right]
        if total < target:
            left += 1
        else:
            right -= 1
    return []
```

#### 最长无重复子串

**不变量：** 每轮收缩后 `[left, right]` 没有重复字符。每个字符最多进出窗口一次，时间 O(n)，空间 O(k)，k 为窗口内不同字符数。

```python
def longest_unique_substring(s):
    window = set()
    left = answer = 0
    for right, ch in enumerate(s):
        while ch in window:
            window.remove(s[left])
            left += 1
        window.add(ch)
        answer = max(answer, right - left + 1)
    return answer
```

**易错点：** 重复可能需要多次收缩，必须用 `while`。用“区间和超过目标就收缩”的窗口通常要求元素非负；含负数时先考虑前缀和。

### 4. 前缀和 + 哈希 - 和为 k 的子数组个数

`prefix[j] - prefix[i] == k`，因此统计之前出现过多少个 `prefix[j] - k`。支持负数，时间 O(n)，空间 O(n)。

```python
from collections import defaultdict

def subarray_sum(nums, k):
    count = defaultdict(int)
    count[0] = 1               # 空前缀，让从下标 0 开始的区间也被计入
    prefix = answer = 0
    for x in nums:
        prefix += x
        answer += count[prefix - k]
        count[prefix] += 1     # 必须先查询再插入，避免统计空区间
    return answer
```

### 5. 单调栈 - 每日温度

栈保存尚未找到更高温度的下标，温度从栈底到栈顶非递增。每个下标最多入栈出栈一次，时间 O(n)，空间 O(n)。

```python
def daily_temperatures(temperatures):
    answer = [0] * len(temperatures)
    stack = []
    for i, value in enumerate(temperatures):
        while stack and temperatures[stack[-1]] < value:
            j = stack.pop()
            answer[j] = i - j
        stack.append(i)
    return answer
```

### 6. 链表 - 反转与快慢指针判环

两者时间 O(n)、额外空间 O(1)。反转会修改原链表；判环比较节点身份。

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def reverse_list(head):
    prev, curr = None, head
    while curr is not None:
        nxt = curr.next        # 先保存后继
        curr.next = prev
        prev, curr = curr, nxt
    return prev

def has_cycle(head):
    slow = fast = head
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False
```

### 7. 二叉树 - DFS、BFS、最近公共祖先

```python
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def inorder(root):
    result, stack = [], []
    curr = root
    while curr is not None or stack:
        while curr is not None:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        result.append(curr.val)  # 中序：左 -> 根 -> 右
        curr = curr.right
    return result

def level_order(root):
    if root is None:
        return []
    q, result = deque([root]), []
    while q:
        level = []
        for _ in range(len(q)): # 固定本层节点数
            node = q.popleft()
            level.append(node.val)
            if node.left is not None:
                q.append(node.left)
            if node.right is not None:
                q.append(node.right)
        result.append(level)
    return result

def lowest_common_ancestor(root, p, q):
    # 前提：p、q 都在树中，按节点身份查找
    if root is None or root is p or root is q:
        return root
    left = lowest_common_ancestor(root.left, p, q)
    right = lowest_common_ancestor(root.right, p, q)
    if left is not None and right is not None:
        return root
    return left if left is not None else right
```

三者时间均为 O(n)。中序遍历与 LCA 的辅助空间为 O(h)，h 是树高；BFS 为 O(w)，w 是最大层宽，均不含输出。LCA 的递归在退化长链上可能超深，应改用父节点表等迭代方案。

**遍历记法：** 根在前面处理是前序，左右之间处理中序，左右处理完后是后序。求高度、子树信息通常用后序。

### 8. 图 - BFS 无权最短路与 DFS 连通块

**约定：** 顶点编号 `0..n-1`，邻接表 `graph[u]` 保存邻居，无向边需双向添加。时间 O(V + E)，辅助空间 O(V)。

```python
from collections import deque

def bfs_distances(graph, start):
    dist = [-1] * len(graph)
    dist[start] = 0
    q = deque([start])
    while q:
        u = q.popleft()
        for v in graph[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1  # 入队时标记，防止重复入队
                q.append(v)
    return dist                # -1 表示不可达

def count_components(graph):
    # 仅用于无向图；有向图强连通分量需要其他算法
    visited = set()
    count = 0
    for start in range(len(graph)):
        if start in visited:
            continue
        count += 1
        visited.add(start)
        stack = [start]
        while stack:
            u = stack.pop()
            for v in graph[u]:
                if v not in visited:
                    visited.add(v)
                    stack.append(v)
    return count
```

#### 网格 DFS - 岛屿数量

输入是由字符 `'0'`、`'1'` 组成的矩形网格；使用访问集合保留原网格。时间、额外空间均为 O(mn)。

```python
def num_islands(grid):
    if not grid or not grid[0]:
        return 0
    m, n = len(grid), len(grid[0])
    visited = set()
    answer = 0
    for r in range(m):
        for c in range(n):
            if grid[r][c] != "1" or (r, c) in visited:
                continue
            answer += 1
            visited.add((r, c))
            stack = [(r, c)]
            while stack:
                x, y = stack.pop()
                for dx, dy in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                    nx, ny = x + dx, y + dy
                    if (0 <= nx < m and 0 <= ny < n
                            and grid[nx][ny] == "1"
                            and (nx, ny) not in visited):
                        visited.add((nx, ny))
                        stack.append((nx, ny))
    return answer
```

### 9. 拓扑排序 - Kahn 算法

**约定：** `(u, v)` 表示先完成 u 再完成 v；适用于有向图。无法弹出所有顶点说明有环。时间、存储空间 O(V + E)。

```python
from collections import deque

def topological_sort(n, edges):
    graph = [[] for _ in range(n)]
    indegree = [0] * n
    for u, v in edges:
        graph[u].append(v)
        indegree[v] += 1
    q = deque(i for i in range(n) if indegree[i] == 0)
    order = []
    while q:
        u = q.popleft()
        order.append(u)
        for v in graph[u]:
            indegree[v] -= 1
            if indegree[v] == 0:
                q.append(v)
    return order if len(order) == n else None  # None 表示有环
```

### 10. Dijkstra - 非负权最短路

`graph[u]` 存 `(v, weight)`，所有权重必须非负。下面使用懒删除堆，时间 O(V + E log(E + 2))、辅助空间 O(V + E)；简单图中通常写成 O((V + E) log V)。

```python
import heapq

def dijkstra(graph, start):
    dist = [float("inf")] * len(graph)
    dist[start] = 0
    heap = [(0, start)]
    while heap:
        distance, u = heapq.heappop(heap)
        if distance != dist[u]:
            continue           # 跳过已被更短路径替代的旧记录
        for v, weight in graph[u]:
            candidate = distance + weight
            if candidate < dist[v]:
                dist[v] = candidate
                heapq.heappush(heap, (candidate, v))
    return dist                # inf 表示不可达
```

**易错点：** 不能像 BFS 一样在首次入堆时就把节点永久标记为完成。负权图应考虑 Bellman-Ford 等算法。

### 11. 并查集 - 动态连通性

路径压缩 + 按集合大小合并，单次操作均摊 O(α(n))，空间 O(n)。可用于无向图判环、连通分量、Kruskal 最小生成树。

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n
        self.components = n

    def find(self, x):
        while x != self.parent[x]:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x

    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return False
        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]
        self.components -= 1
        return True
```

### 12. 回溯 - 含重复元素的全排列

**步骤：** 做选择 → 递归 → 撤销选择。先排序，用“相同元素按固定顺序使用”去重。最坏时间 O(n · n!)，辅助空间 O(n)，输出最多 O(n · n!)。

```python
def permute_unique(nums):
    nums = sorted(nums)
    used = [False] * len(nums)
    path, answer = [], []

    def dfs():
        if len(path) == len(nums):
            answer.append(path.copy())
            return
        for i, x in enumerate(nums):
            if used[i]:
                continue
            if i > 0 and nums[i] == nums[i - 1] and not used[i - 1]:
                continue       # 同层去重，允许同一路径使用两个相同值
            used[i] = True
            path.append(x)
            dfs()
            path.pop()
            used[i] = False

    dfs()
    return answer
```

### 13. 动态规划 - 背包、零钱兑换、LIS

先说清楚 **状态、转移、初始化、遍历顺序、答案位置**，再写代码。

#### 0/1 背包：每件物品最多使用一次

重量是正整数，容量是非负整数，允许不选物品。`dp[c]` 表示总重量不超过 c 的最大价值。时间 O(nC)，空间 O(C)。

```python
def knapsack_01(weights, values, capacity):
    dp = [0] * (capacity + 1)
    for weight, value in zip(weights, values):
        for c in range(capacity, weight - 1, -1):
            dp[c] = max(dp[c], dp[c - weight] + value)
    return dp[capacity]
```

**为什么倒序？** 保证读取的是上一轮状态，当前物品不会被重复使用。若题目要求恰好装满，只有 `dp[0] = 0`，其他位置应初始化为负无穷。

#### 零钱兑换：每种硬币无限使用，求最少枚数

硬币面值必须为正整数，amount 为非负整数。时间 O(kA)，空间 O(A)。

```python
def coin_change(coins, amount):
    dp = [float("inf")] * (amount + 1)
    dp[0] = 0
    for coin in coins:
        for total in range(coin, amount + 1):
            dp[total] = min(dp[total], dp[total - coin] + 1)
    return -1 if dp[amount] == float("inf") else dp[amount]
```

#### 最长严格递增子序列：维护每个长度的最小末尾值

`tails[i]` 表示长度为 `i+1` 的递增子序列的最小末尾。时间 O(n log n)，空间 O(n)。`tails` 本身不保证是原数组的一条子序列。

```python
from bisect import bisect_left

def length_of_lis(nums):
    tails = []
    for x in nums:
        pos = bisect_left(tails, x)
        if pos == len(tails):
            tails.append(x)
        else:
            tails[pos] = x
    return len(tails)
```

求非递减子序列时换成 `bisect_right`。记忆化搜索可用 `@lru_cache(None)`，参数必须可哈希，并注意递归深度。

### 14. 堆 - 第 k 大元素

维护大小为 k 的最小堆，堆顶就是当前第 k 大。重复元素按出现次数计算。时间 O(n log(k + 1))，空间 O(k)。

```python
import heapq

def kth_largest(nums, k):
    if not 1 <= k <= len(nums):
        raise ValueError("k 必须在 [1, len(nums)] 内")
    heap = []
    for x in nums:
        if len(heap) < k:
            heapq.heappush(heap, x)
        elif x > heap[0]:
            heapq.heapreplace(heap, x)
    return heap[0]
```

---

## 🤖 Part 3: 常见 AI 算法模板

这一部分以**能解释公式、手写核心步骤**为目标。约定 N 为样本数、D 为特征数、C 为类别数、B 为批大小、T 为序列长度。数值输入默认有限、非空；训练集与验证集应先划分，归一化与降维参数只在训练集上拟合。

### 0. NumPy - 形状与广播速查

```python
import numpy as np

X = np.array([[1., 2., 3.], [4., 5., 6.]])  # (N=2, D=3)
w = np.ones(3)                              # (D,)
X.shape                                    # (2, 3)
X @ w                                      # (N,)，矩阵乘法
X * w                                      # (N, D)，逐元素广播
X.T                                        # (D, N)
X.mean(axis=0, keepdims=True)                # (1, D)，沿样本维求均值
X.sum(axis=1, keepdims=True)                 # (N, 1)，每行求和
X.reshape(-1)                              # 展平为 (6,)
X[:, None, :].shape                         # (N, 1, D)
np.argmax(X, axis=1)                        # 每行最大值下标
np.argsort(X, axis=1)                       # 每行升序排序下标
```

**广播从尾维对齐：** 对应维度相等，或其中一个为 1 才能广播。`(N,)` 与 `(N, 1)` 相减会得到 `(N, N)`，回归标签与预测值应保持同样形状。

### 1. 标准化与余弦相似度

标准化公式：`(x - mean) / std`。零方差特征将分母设为 1，避免除零。两函数均按实际数组元素数线性耗时。

```python
import numpy as np

def fit_standardizer(X):
    X = np.asarray(X, dtype=float)           # (N, D)
    mean = X.mean(axis=0)
    std = X.std(axis=0)
    scale = np.where(std > 0, std, 1.0)
    return mean, scale

def cosine_similarity(a, b):
    a, b = np.asarray(a, dtype=float), np.asarray(b, dtype=float)
    denominator = np.linalg.norm(a) * np.linalg.norm(b)
    # 此处约定零向量相似度为 0；数学上零向量的余弦未定义
    return 0.0 if denominator == 0 else float(a @ b / denominator)

X_train = np.array([[1., 2.], [3., 2.], [5., 2.]])
X_valid = np.array([[7., 2.]])
mean, scale = fit_standardizer(X_train)
train_scaled = (X_train - mean) / scale
valid_scaled = (X_valid - mean) / scale      # 复用训练集统计量
```

### 2. 稳定 Sigmoid、Softmax 与交叉熵

Sigmoid 将 logit 映射到二分类概率；Softmax 沿类别维归一化。Softmax 先减最大值，避免直接计算大数指数。

```python
import numpy as np

def sigmoid(x):
    x = np.asarray(x, dtype=float)
    # exp 的输入始终 <= 0，避免大幅负 logit 导致 exp(-x) 溢出
    z = np.exp(-np.abs(x))
    return np.where(x >= 0, 1.0 / (1.0 + z), z / (1.0 + z))

def softmax(x, axis=-1):
    x = np.asarray(x, dtype=float)
    shifted = x - np.max(x, axis=axis, keepdims=True)
    exp_x = np.exp(shifted)
    return exp_x / exp_x.sum(axis=axis, keepdims=True)

def cross_entropy_logits(logits, labels):
    # logits: (N, C)；labels: (N,) 的整数类别，范围 [0, C)
    logits = np.asarray(logits, dtype=float)
    labels = np.asarray(labels, dtype=int)
    shifted = logits - logits.max(axis=1, keepdims=True)
    log_sum_exp = np.log(np.exp(shifted).sum(axis=1))
    return float(np.mean(log_sum_exp - shifted[np.arange(len(labels)), labels]))
```

**面试要点：** 多分类交叉熵为 `-log p_true`；对 logits 的平均损失梯度为 `(probabilities - one_hot) / N`。直接从 logits 计算损失能避免先求概率再取对数造成的下溢。上面的 Softmax 要求每行至少有一个有限值；全为负无穷的行需另外处理。

### 3. 线性回归 - 手写梯度下降

预测 `prediction = X @ w + b`；损失 `L = mean((prediction - y)²) / 2`。因此 `dw = X.T @ error / N`，`db = mean(error)`。

```python
import numpy as np

def linear_regression_gd(X, y, lr=0.01, steps=1000):
    X = np.asarray(X, dtype=float)           # (N, D)
    y = np.asarray(y, dtype=float)           # (N,)
    n, d = X.shape
    w, b = np.zeros(d), 0.0
    for _ in range(steps):
        error = X @ w + b - y
        w -= lr * (X.T @ error / n)
        b -= lr * error.mean()
    return w, b
```

每轮时间 O(ND)，不计输入的辅助空间 O(N + D)。学习率过大可能发散，特征尺度差异较大时先标准化。若损失定义为不带 `1/2` 的 MSE，梯度需要乘 2。

### 4. 逻辑回归 - 二分类与 L2 正则

虽然叫回归，输出是类别 1 的概率。平均二元交叉熵加正则 `l2 * ||w||² / 2`，不正则化偏置。本块独立实现稳定 Sigmoid。

```python
import numpy as np

def logistic_regression_gd(X, y, lr=0.1, steps=1000, l2=0.0):
    X = np.asarray(X, dtype=float)           # (N, D)
    y = np.asarray(y, dtype=float)           # (N,)，元素为 0 或 1
    n, d = X.shape
    w, b = np.zeros(d), 0.0
    for _ in range(steps):
        logits = X @ w + b
        z = np.exp(-np.abs(logits))
        probability = np.where(logits >= 0, 1 / (1 + z), z / (1 + z))
        error = probability - y
        w -= lr * (X.T @ error / n + l2 * w)
        b -= lr * error.mean()
    logits = X @ w + b
    loss = np.mean(np.logaddexp(0, logits) - y * logits) + l2 * (w @ w) / 2
    return w, b, float(loss)
```

每轮时间 O(ND)，辅助空间 O(N + D)。预测类别可以用 `(X @ w + b >= 0).astype(int)`，相当于概率阈值 0.5。

### 5. KNN - K 近邻分类

计算一个查询点到训练样本的平方欧氏距离，选最近的 k 个投票。标签约定为整数；平票时返回较小标签。

```python
import numpy as np

def knn_predict_one(X_train, y_train, query, k=3):
    X_train = np.asarray(X_train, dtype=float) # (N, D)
    y_train = np.asarray(y_train, dtype=int)  # (N,)
    query = np.asarray(query, dtype=float)    # (D,)
    if not 1 <= k <= len(X_train):
        raise ValueError("k 超出训练样本范围")
    distances = np.sum((X_train - query) ** 2, axis=1)
    indices = np.argsort(distances, kind="stable")[:k]
    labels, counts = np.unique(y_train[indices], return_counts=True)
    return int(labels[np.argmax(counts)])
```

该排序版单次查询时间 O(ND + N log N)，距离计算的临时数组占 O(ND)。可用 `argpartition` 优化选邻居，但要另行约定距离相同时的处理方式。距离模型通常需要先标准化特征。

### 6. K-Means - 分配簇与更新中心

目标是最小化样本到所属中心的平方距离之和。步骤：初始化中心 → 最近中心分配 → 取簇均值 → 迭代。

```python
import numpy as np

def kmeans(X, k, max_iter=100, tol=1e-4, seed=42):
    X = np.asarray(X, dtype=float)           # (N, D)
    if not 1 <= k <= len(X) or max_iter < 1:
        raise ValueError("要求 1 <= k <= N 且 max_iter >= 1")
    rng = np.random.RandomState(seed)
    centers = X[rng.choice(len(X), k, replace=False)].copy()
    for _ in range(max_iter):
        dist2 = ((X[:, None, :] - centers[None, :, :]) ** 2).sum(axis=2)
        labels = dist2.argmin(axis=1)        # (N,)
        new_centers = centers.copy()
        for cluster in range(k):
            members = X[labels == cluster]
            if len(members):
                new_centers[cluster] = members.mean(axis=0)
            # 空簇保留旧中心，避免空数组求均值产生 NaN
        shift = np.linalg.norm(new_centers - centers)
        centers = new_centers
        if shift <= tol:
            break
    # 返回的 labels 必须对应最终 centers
    dist2 = ((X[:, None, :] - centers[None, :, :]) ** 2).sum(axis=2)
    labels = dist2.argmin(axis=1)
    inertia = dist2[np.arange(len(X)), labels].sum()
    return centers, labels, float(inertia)
```

每轮时间 O(NKD)，广播实现临时空间 O(NKD)。初始化影响结果，可能收敛到局部最优；重复数据或空簇策略可能导致有效簇数少于 k。工程中通常采用更好的初始化、多次运行或分批距离计算。

### 7. PCA - 用 SVD 做主成分分析

先按特征中心化，再对中心化矩阵做 SVD；右奇异向量对应特征空间的主成分。这里**只中心化，不自动标准化**。

```python
import numpy as np

def pca_fit_transform(X, k):
    X = np.asarray(X, dtype=float)           # (N, D)
    n, d = X.shape
    if n < 2 or not 1 <= k <= min(n, d):
        raise ValueError("要求 N >= 2 且 1 <= k <= min(N, D)")
    mean = X.mean(axis=0)
    centered = X - mean
    _, singular_values, vt = np.linalg.svd(centered, full_matrices=False)
    components = vt[:k]                     # (k, D)，每行一个主成分
    transformed = centered @ components.T  # (N, k)
    variance = singular_values ** 2 / (n - 1)
    total = variance.sum()
    ratio = variance[:k] / total if total > 0 else np.zeros(k)
    return transformed, mean, components, ratio
```

验证集使用 `(X_valid - mean) @ components.T`。SVD 分解接口与返回形状见 [NumPy 文档](https://numpy.org/doc/stable/reference/generated/numpy.linalg.svd.html)。稠密 SVD 时间通常为 O(ND · min(N, D))；此实现空间为 O(ND + min(N, D)²)。奇异向量整体翻转符号不影响主成分子空间。

### 8. LayerNorm - 沿最后一维归一化

输入 `(B, T, D)`，对每个 token 的 D 个特征分别求均值、方差；`gamma`、`beta` 均为 `(D,)`。时间和输出空间 O(BTD)。

```python
import numpy as np

def layer_norm(x, gamma, beta, eps=1e-5):
    x = np.asarray(x, dtype=float)
    mean = x.mean(axis=-1, keepdims=True)
    var = x.var(axis=-1, keepdims=True)       # 总体方差，ddof=0
    return (x - mean) / np.sqrt(var + eps) * gamma + beta
```

**面试要点：** LayerNorm 不依赖其他样本的统计量；BatchNorm 的统计维度不同，并且训练与推理阶段的统计量处理通常不同。

### 9. Scaled Dot-Product Attention 与多头拆分

公式：`Attention(Q, K, V) = softmax(Q @ Kᵀ / sqrt(d_k)) @ V`。除以 `sqrt(d_k)` 用于控制点积的尺度。

下面支持 `(..., Tq, Dk)` 的 Q、`(..., Tk, Dk)` 的 K、`(..., Tk, Dv)` 的 V；前导维可以是批与头。**本模板的布尔 mask 中 True 表示允许关注**，每行至少允许一个 key。复用第 2 节的 `softmax`。

```python
import numpy as np

def scaled_dot_product_attention(q, k, v, mask=None):
    q, k, v = [np.asarray(x, dtype=float) for x in (q, k, v)]
    scores = q @ np.swapaxes(k, -1, -2) / np.sqrt(q.shape[-1])
    if mask is not None:
        allowed = np.broadcast_to(np.asarray(mask, dtype=bool), scores.shape)
        if not np.all(allowed.any(axis=-1)):
            raise ValueError("每个 query 至少需要一个可见 key")
        scores = np.where(allowed, scores, -np.inf)
    weights = softmax(scores, axis=-1)       # 沿 key 维归一化
    return weights @ v, weights

def multi_head_self_attention(x, wq, wk, wv, wo, heads, causal=False):
    # x: (B, T, D)，四个投影矩阵均为 (D, D)，省略偏置与 dropout
    batch, length, dim = x.shape
    if heads < 1 or dim % heads:
        raise ValueError("heads 必须为正数且整除 D")
    head_dim = dim // heads

    def split_heads(tensor):
        return tensor.reshape(batch, length, heads, head_dim).transpose(0, 2, 1, 3)

    q, k, v = [split_heads(x @ w) for w in (wq, wk, wv)]
    mask = np.tril(np.ones((length, length), dtype=bool)) if causal else None
    context, weights = scaled_dot_product_attention(q, k, v, mask)
    merged = context.transpose(0, 2, 1, 3).reshape(batch, length, dim)
    return merged @ wo, weights
```

多头形状变化：`(B,T,D) → (B,H,T,D/H) → (B,H,T,T) → (B,H,T,D/H) → (B,T,D)`。注意先转置再合并头；不能直接把原布局 reshape 回去。

注意力矩阵计算时间 O(BT²D)，权重空间 O(BHT²)，线性投影另需 O(BTD²)。Causal mask 屏蔽未来位置；上面仅覆盖等长自注意力，不含 KV cache 的位置偏移。不同框架接口的 mask 语义可能相反，不能直接套用。完整 Transformer 层还需要残差连接、归一化与 FFN。

### 10. PyTorch - 最小训练与验证循环

**输入约定：** model 接收 `(B, D)` 浮点特征并输出 `(B, C)` logits；loader 提供 `(features, labels)`，labels 为 `(B,)` 的 `torch.long` 类别编号。以下示例使用 CPU。

```python
import torch
from torch import nn
from torch.utils.data import DataLoader, TensorDataset

def train_epoch(model, loader, optimizer):
    model.train()
    loss_fn = nn.CrossEntropyLoss()
    total_loss, total = 0.0, 0
    for features, labels in loader:
        optimizer.zero_grad(set_to_none=True)
        logits = model(features)
        loss = loss_fn(logits, labels)
        loss.backward()
        optimizer.step()
        total_loss += loss.item() * labels.size(0)
        total += labels.size(0)
    return total_loss / total if total else 0.0

@torch.no_grad()
def evaluate(model, loader):
    model.eval()
    correct, total = 0, 0
    for features, labels in loader:
        predictions = model(features).argmax(dim=-1)
        correct += (predictions == labels).sum().item()
        total += labels.size(0)
    return correct / total if total else 0.0

torch.manual_seed(42)
features = torch.randn(80, 4)
labels = (features[:, 0] > 0).long()
train_loader = DataLoader(TensorDataset(features[:64], labels[:64]),
                          batch_size=16, shuffle=True)
valid_loader = DataLoader(TensorDataset(features[64:], labels[64:]), batch_size=16)
model = nn.Sequential(nn.Linear(4, 16), nn.ReLU(), nn.Linear(16, 2))
optimizer = torch.optim.Adam(model.parameters(), lr=1e-2)
for _ in range(5):
    train_loss = train_epoch(model, train_loader, optimizer)
valid_accuracy = evaluate(model, valid_loader)
```

**易错点：** `CrossEntropyLoss` 接收 logits，不要提前做 Softmax；梯度默认累积，每步要清零。`eval()` 切换 Dropout / BatchNorm 等层的行为，`no_grad()` 关闭梯度记录，两者作用不同。循环结构可对照 [PyTorch 官方训练教程](https://docs.pytorch.org/tutorials/beginner/basics/optimization_tutorial.html)。

### 11. 面试前自查

- **数据结构：** 能否解释 `list` 与 `deque` 的区别、哈希键的限制、浅拷贝与共享引用？
- **算法模板：** 能否先说清不变量、终止条件、边界和复杂度，再写代码？至少用空输入、单元素、重复值、无解情况检查一次。
- **AI 手写：** 能否标出每个矩阵的形状，推导梯度方向，解释数值稳定性与 mask 的含义？
- **模型训练：** 能否避免数据泄漏，区分 logits 与概率，说明训练和验证阶段的不同？
