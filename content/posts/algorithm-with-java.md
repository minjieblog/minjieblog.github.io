---
title: "Java 集合使用与算法模板大全"
date: 2025-12-07T15:42:12+08:00
draft: false
tags: ["Java", "算法", "数据结构", "LeetCode"]
categories: ["技术笔记-J"]
summary = '详细介绍java集合的使用,以及在刷算法题中需要使用到的一些板子'
description: "Java 集合框架完整使用手册 + 常用算法模板"
comments: true
---

## 📚 Part 1: Java 集合使用手册

### 1. List - 列表

#### ArrayList - 动态数组
```java
// 初始化
List<Integer> list = new ArrayList<>();
List<String> list2 = new ArrayList<>(Arrays.asList("a", "b", "c"));
List<Integer> list3 = new ArrayList<>(100); // 指定初始容量

// 增
list.add(1);                    // 尾部添加
list.add(0, 10);                // 指定位置添加
list.addAll(Arrays.asList(2,3,4)); // 批量添加

// 删
list.remove(0);                 // 删除指定索引
list.remove(Integer.valueOf(1)); // 删除指定元素
list.clear();                   // 清空

// 改
list.set(0, 100);               // 修改指定位置

// 查
int val = list.get(0);          // 获取元素
int size = list.size();         // 获取大小
boolean empty = list.isEmpty(); // 是否为空
boolean contains = list.contains(1); // 是否包含
int index = list.indexOf(1);    // 查找索引

// 遍历
for (int num : list) {
    System.out.println(num);
}
// 或使用迭代器
Iterator<Integer> it = list.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
}
```

#### LinkedList - 双向链表
```java
LinkedList<Integer> list = new LinkedList<>();

// 头部操作
list.addFirst(1);
list.removeFirst();
int first = list.getFirst();

// 尾部操作
list.addLast(2);
list.removeLast();
int last = list.getLast();

// 作为队列使用
list.offer(3);      // 入队
int head = list.poll(); // 出队

// 作为栈使用
list.push(4);       // 入栈
int top = list.pop(); // 出栈
```

---

### 2. Set - 集合

#### HashSet - 无序不重复
```java
// 初始化
Set<Integer> set = new HashSet<>();
Set<String> set2 = new HashSet<>(Arrays.asList("a", "b", "c"));

// 增
set.add(1);
set.addAll(Arrays.asList(2, 3, 4));

// 删
set.remove(1);
set.clear();

// 查
boolean contains = set.contains(1);
int size = set.size();
boolean empty = set.isEmpty();

// 遍历
for (int num : set) {
    System.out.println(num);
}
```

#### TreeSet - 有序集合（红黑树）
```java
TreeSet<Integer> set = new TreeSet<>();

// 基本操作同 HashSet
set.add(3);
set.add(1);
set.add(2);
System.out.println(set); // [1, 2, 3] 自动排序

// 特有方法
int first = set.first();        // 最小元素
int last = set.last();          // 最大元素
int lower = set.lower(2);       // 小于 2 的最大元素
int higher = set.higher(2);     // 大于 2 的最小元素
int floor = set.floor(2);       // ≤ 2 的最大元素
int ceiling = set.ceiling(2);   // ≥ 2 的最小元素

// 子集操作
SortedSet<Integer> subset = set.subSet(1, 3); // [1, 3)
SortedSet<Integer> headSet = set.headSet(2);  // < 2
SortedSet<Integer> tailSet = set.tailSet(2);  // >= 2
```

#### LinkedHashSet - 保持插入顺序
```java
Set<Integer> set = new LinkedHashSet<>();
set.add(3);
set.add(1);
set.add(2);
System.out.println(set); // [3, 1, 2] 保持插入顺序
```

---

### 3. Map - 映射

#### HashMap - 键值对
```java
// 初始化
Map<String, Integer> map = new HashMap<>();

// 增/改
map.put("apple", 1);
map.put("banana", 2);
map.putIfAbsent("apple", 10); // 键不存在时才添加

// 删
map.remove("apple");
map.clear();

// 查
int val = map.get("apple");
int valOrDefault = map.getOrDefault("orange", 0);
boolean hasKey = map.containsKey("apple");
boolean hasValue = map.containsValue(1);
int size = map.size();

// 遍历键值对
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    String key = entry.getKey();
    int value = entry.getValue();
}

// 遍历键
for (String key : map.keySet()) {
    System.out.println(key);
}

// 遍历值
for (int value : map.values()) {
    System.out.println(value);
}

// Java 8+ 新方法
map.computeIfAbsent("cherry", k -> 3);
map.merge("apple", 1, Integer::sum); // 累加
```

#### TreeMap - 有序映射（红黑树）
```java
TreeMap<Integer, String> map = new TreeMap<>();
map.put(3, "c");
map.put(1, "a");
map.put(2, "b");

// 基本操作同 HashMap
// 特有方法
int firstKey = map.firstKey();
int lastKey = map.lastKey();
Map.Entry<Integer, String> firstEntry = map.firstEntry();
Map.Entry<Integer, String> lastEntry = map.lastEntry();

int lowerKey = map.lowerKey(2);   // < 2 的最大键
int higherKey = map.higherKey(2); // > 2 的最小键
int floorKey = map.floorKey(2);   // <= 2 的最大键
int ceilingKey = map.ceilingKey(2); // >= 2 的最小键
```

#### LinkedHashMap - 保持插入顺序
```java
Map<String, Integer> map = new LinkedHashMap<>();
map.put("c", 3);
map.put("a", 1);
map.put("b", 2);
// 遍历时保持插入顺序: c, a, b
```

---

### 4. Queue - 队列

#### 普通队列
```java
Queue<Integer> queue = new LinkedList<>();

// 入队
queue.offer(1);
queue.add(2); // 队列满时抛异常

// 出队
int head = queue.poll();  // 空时返回 null
int head2 = queue.remove(); // 空时抛异常

// 查看队首
int peek = queue.peek();  // 空时返回 null
int peek2 = queue.element(); // 空时抛异常
```

#### PriorityQueue - 优先队列（堆）
```java
// 小根堆（默认）
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

// 大根堆
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);

// 自定义对象
PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);

minHeap.offer(3);
minHeap.offer(1);
minHeap.offer(2);

while (!minHeap.isEmpty()) {
    System.out.println(minHeap.poll()); // 1, 2, 3
}
```

#### Deque - 双端队列
```java
Deque<Integer> deque = new ArrayDeque<>();

// 头部操作
deque.offerFirst(1);
deque.pollFirst();
deque.peekFirst();

// 尾部操作
deque.offerLast(2);
deque.pollLast();
deque.peekLast();

// 作为栈使用
deque.push(3);
int top = deque.pop();
```

---

### 5. Stack - 栈
```java
Stack<Integer> stack = new Stack<>();

stack.push(1);      // 入栈
int top = stack.pop();  // 出栈
int peek = stack.peek(); // 查看栈顶
boolean empty = stack.isEmpty();
int size = stack.size();

// 推荐使用 Deque 代替 Stack
Deque<Integer> stack2 = new ArrayDeque<>();
```

---

## 🧮 Part 2: 算法模板

### 1. 树的遍历

#### 二叉树定义
```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int val) { this.val = val; }
}
```

#### DFS - 深度优先遍历

**前序遍历（根-左-右）**
```java
// 递归
void preorder(TreeNode root) {
    if (root == null) return;
    System.out.println(root.val);
    preorder(root.left);
    preorder(root.right);
}

// 迭代
List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    if (root == null) return res;
    
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);
    
    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        res.add(node.val);
        if (node.right != null) stack.push(node.right);
        if (node.left != null) stack.push(node.left);
    }
    return res;
}
```

**中序遍历（左-根-右）**
```java
// 递归
void inorder(TreeNode root) {
    if (root == null) return;
    inorder(root.left);
    System.out.println(root.val);
    inorder(root.right);
}

// 迭代
List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<>();
    TreeNode curr = root;
    
    while (curr != null || !stack.isEmpty()) {
        while (curr != null) {
            stack.push(curr);
            curr = curr.left;
        }
        curr = stack.pop();
        res.add(curr.val);
        curr = curr.right;
    }
    return res;
}
```

**后序遍历（左-右-根）**
```java
// 递归
void postorder(TreeNode root) {
    if (root == null) return;
    postorder(root.left);
    postorder(root.right);
    System.out.println(root.val);
}

// 迭代
List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    if (root == null) return res;
    
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);
    
    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        res.add(node.val);
        if (node.left != null) stack.push(node.left);
        if (node.right != null) stack.push(node.right);
    }
    Collections.reverse(res);
    return res;
}
```

#### BFS - 层序遍历
```java
List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();
    if (root == null) return res;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
        res.add(level);
    }
    return res;
}
```

---

### 2. 线段树（Segment Tree）

```java
class SegmentTree {
    private int[] tree;
    private int n;
    
    public SegmentTree(int[] nums) {
        n = nums.length;
        tree = new int[4 * n];
        build(nums, 0, 0, n - 1);
    }
    
    // 构建线段树
    private void build(int[] nums, int node, int start, int end) {
        if (start == end) {
            tree[node] = nums[start];
            return;
        }
        int mid = start + (end - start) / 2;
        int leftNode = 2 * node + 1;
        int rightNode = 2 * node + 2;
        
        build(nums, leftNode, start, mid);
        build(nums, rightNode, mid + 1, end);
        tree[node] = tree[leftNode] + tree[rightNode];
    }
    
    // 区间查询 [l, r]
    public int query(int l, int r) {
        return query(0, 0, n - 1, l, r);
    }
    
    private int query(int node, int start, int end, int l, int r) {
        if (l > end || r < start) return 0;
        if (l <= start && end <= r) return tree[node];
        
        int mid = start + (end - start) / 2;
        int leftSum = query(2 * node + 1, start, mid, l, r);
        int rightSum = query(2 * node + 2, mid + 1, end, l, r);
        return leftSum + rightSum;
    }
    
    // 单点更新
    public void update(int index, int val) {
        update(0, 0, n - 1, index, val);
    }
    
    private void update(int node, int start, int end, int index, int val) {
        if (start == end) {
            tree[node] = val;
            return;
        }
        int mid = start + (end - start) / 2;
        int leftNode = 2 * node + 1;
        int rightNode = 2 * node + 2;
        
        if (index <= mid) {
            update(leftNode, start, mid, index, val);
        } else {
            update(rightNode, mid + 1, end, index, val);
        }
        tree[node] = tree[leftNode] + tree[rightNode];
    }
}

// 使用示例
int[] nums = {1, 3, 5, 7, 9, 11};
SegmentTree segTree = new SegmentTree(nums);
int sum = segTree.query(1, 3); // 查询 [1,3] 区间和
segTree.update(1, 10);          // 更新 index=1 的值为 10
```

---

### 3. 图算法

#### 图的表示
```java
// 邻接表
Map<Integer, List<Integer>> graph = new HashMap<>();
// 或
List<List<Integer>> graph = new ArrayList<>();

// 邻接矩阵
int[][] graph = new int[n][n];
```

#### DFS - 图的深度优先遍历
```java
void dfs(int node, Set<Integer> visited, List<List<Integer>> graph) {
    visited.add(node);
    System.out.println(node);
    
    for (int neighbor : graph.get(node)) {
        if (!visited.contains(neighbor)) {
            dfs(neighbor, visited, graph);
        }
    }
}

// 使用
Set<Integer> visited = new HashSet<>();
dfs(0, visited, graph);
```

#### BFS - 图的广度优先遍历
```java
void bfs(int start, List<List<Integer>> graph) {
    Set<Integer> visited = new HashSet<>();
    Queue<Integer> queue = new LinkedList<>();
    
    queue.offer(start);
    visited.add(start);
    
    while (!queue.isEmpty()) {
        int node = queue.poll();
        System.out.println(node);
        
        for (int neighbor : graph.get(node)) {
            if (!visited.contains(neighbor)) {
                queue.offer(neighbor);
                visited.add(neighbor);
            }
        }
    }
}
```

#### 拓扑排序（Kahn 算法）
```java
List<Integer> topologicalSort(int n, int[][] edges) {
    List<List<Integer>> graph = new ArrayList<>();
    int[] inDegree = new int[n];
    
    for (int i = 0; i < n; i++) {
        graph.add(new ArrayList<>());
    }
    
    for (int[] edge : edges) {
        graph.get(edge[0]).add(edge[1]);
        inDegree[edge[1]]++;
    }
    
    Queue<Integer> queue = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) {
            queue.offer(i);
        }
    }
    
    List<Integer> res = new ArrayList<>();
    while (!queue.isEmpty()) {
        int node = queue.poll();
        res.add(node);
        
        for (int neighbor : graph.get(node)) {
            inDegree[neighbor]--;
            if (inDegree[neighbor] == 0) {
                queue.offer(neighbor);
            }
        }
    }
    
    return res.size() == n ? res : new ArrayList<>();
}
```

#### Dijkstra 最短路径算法
```java
int[] dijkstra(int n, int[][] edges, int start) {
    // 构建邻接表: {neighbor, weight}
    List<List<int[]>> graph = new ArrayList<>();
    for (int i = 0; i < n; i++) {
        graph.add(new ArrayList<>());
    }
    for (int[] edge : edges) {
        graph.get(edge[0]).add(new int[]{edge[1], edge[2]});
    }
    
    int[] dist = new int[n];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[start] = 0;
    
    // 优先队列: {distance, node}
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
    pq.offer(new int[]{0, start});
    
    while (!pq.isEmpty()) {
        int[] curr = pq.poll();
        int d = curr[0], node = curr[1];
        
        if (d > dist[node]) continue;
        
        for (int[] neighbor : graph.get(node)) {
            int next = neighbor[0], weight = neighbor[1];
            int newDist = d + weight;
            
            if (newDist < dist[next]) {
                dist[next] = newDist;
                pq.offer(new int[]{newDist, next});
            }
        }
    }
    
    return dist;
}
```

#### Bellman-Ford 算法（可处理负权边）
```java
int[] bellmanFord(int n, int[][] edges, int start) {
    int[] dist = new int[n];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[start] = 0;
    
    // 松弛 n-1 次
    for (int i = 0; i < n - 1; i++) {
        for (int[] edge : edges) {
            int u = edge[0], v = edge[1], w = edge[2];
            if (dist[u] != Integer.MAX_VALUE && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
            }
        }
    }
    
    // 检测负环
    for (int[] edge : edges) {
        int u = edge[0], v = edge[1], w = edge[2];
        if (dist[u] != Integer.MAX_VALUE && dist[u] + w < dist[v]) {
            return null; // 存在负环
        }
    }
    
    return dist;
}
```

#### Floyd-Warshall 全源最短路径
```java
int[][] floydWarshall(int n, int[][] edges) {
    int[][] dist = new int[n][n];
    
    // 初始化
    for (int i = 0; i < n; i++) {
        Arrays.fill(dist[i], Integer.MAX_VALUE / 2);
        dist[i][i] = 0;
    }
    
    for (int[] edge : edges) {
        dist[edge[0]][edge[1]] = edge[2];
    }
    
    // 动态规划
    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
            }
        }
    }
    
    return dist;
}
```

#### Prim 最小生成树
```java
int prim(int n, int[][] edges) {
    List<List<int[]>> graph = new ArrayList<>();
    for (int i = 0; i < n; i++) {
        graph.add(new ArrayList<>());
    }
    for (int[] edge : edges) {
        graph.get(edge[0]).add(new int[]{edge[1], edge[2]});
        graph.get(edge[1]).add(new int[]{edge[0], edge[2]});
    }
    
    boolean[] visited = new boolean[n];
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
    pq.offer(new int[]{0, 0}); // {node, weight}
    
    int totalWeight = 0;
    int edgeCount = 0;
    
    while (!pq.isEmpty() && edgeCount < n) {
        int[] curr = pq.poll();
        int node = curr[0], weight = curr[1];
        
        if (visited[node]) continue;
        visited[node] = true;
        totalWeight += weight;
        edgeCount++;
        
        for (int[] neighbor : graph.get(node)) {
            if (!visited[neighbor[0]]) {
                pq.offer(neighbor);
            }
        }
    }
    
    return edgeCount == n ? totalWeight : -1;
}
```

#### Kruskal 最小生成树（并查集）
```java
class UnionFind {
    int[] parent, rank;
    
    public UnionFind(int n) {
        parent = new int[n];
        rank = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }
    
    public int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);
        }
        return parent[x];
    }
    
    public boolean union(int x, int y) {
        int rootX = find(x), rootY = find(y);
        if (rootX == rootY) return false;
        
        if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        } else if (rank[rootX] > rank[rootY]) {
            parent[rootY] = rootX;
        } else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }
        return true;
    }
}

int kruskal(int n, int[][] edges) {
    Arrays.sort(edges, (a, b) -> a[2] - b[2]);
    UnionFind uf = new UnionFind(n);
    
    int totalWeight = 0;
    int edgeCount = 0;
    
    for (int[] edge : edges) {
        if (uf.union(edge[0], edge[1])) {
            totalWeight += edge[2];
            edgeCount++;
            if (edgeCount == n - 1) break;
        }
    }
    
    return edgeCount == n - 1 ? totalWeight : -1;
}
```

---

### 4. 回溯算法

#### 组合问题
```java
List<List<Integer>> combine(int n, int k) {
    List<List<Integer>> res = new ArrayList<>();
    backtrack(res, new ArrayList<>(), 1, n, k);
    return res;
}

void backtrack(List<List<Integer>> res, List<Integer> path, int start, int n, int k) {
    if (path.size() == k) {
        res.add(new ArrayList<>(path));
        return;
    }
    
    for (int i = start; i <= n; i++) {
        path.add(i);
        backtrack(res, path, i + 1, n, k);
        path.remove(path.size() - 1);
    }
}
```

#### 全排列
```java
List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    backtrack(res, new ArrayList<>(), nums, new boolean[nums.length]);
    return res;
}

void backtrack(List<List<Integer>> res, List<Integer> path, int[] nums, boolean[] used) {
    if (path.size() == nums.length) {
        res.add(new ArrayList<>(path));
        return;
    }
    
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) continue;
        
        path.add(nums[i]);
        used[i] = true;
        backtrack(res, path, nums, used);
        used[i] = false;
        path.remove(path.size() - 1);
    }
}
```

#### 子集问题
```java
List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    backtrack(res, new ArrayList<>(), nums, 0);
    return res;
}

void backtrack(List<List<Integer>> res, List<Integer> path, int[] nums, int start) {
    res.add(new ArrayList<>(path));
    
    for (int i = start; i < nums.length; i++) {
        path.add(nums[i]);
        backtrack(res, path, nums, i + 1);
        path.remove(path.size() - 1);
    }
}
```

#### N 皇后问题
```java
List<List<String>> solveNQueens(int n) {
    List<List<String>> res = new ArrayList<>();
    char[][] board = new char[n][n];
    for (int i = 0; i < n; i++) {
        Arrays.fill(board[i], '.');
    }
    backtrack(res, board, 0);
    return res;
}

void backtrack(List<List<String>> res, char[][] board, int row) {
    if (row == board.length) {
        res.add(construct(board));
        return;
    }
    
    for (int col = 0; col < board.length; col++) {
        if (!isValid(board, row, col)) continue;
        
        board[row][col] = 'Q';
        backtrack(res, board, row + 1);
        board[row][col] = '.';
    }
}

boolean isValid(char[][] board, int row, int col) {
    int n = board.length;
    
    // 检查列
    for (int i = 0; i < row; i++) {
        if (board[i][col] == 'Q') return false;
    }
    
    // 检查左上对角线
    for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
        if (board[i][j] == 'Q') return false;
    }
    
    // 检查右上对角线
    for (int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
        if (board[i][j] == 'Q') return false;
    }
    
    return true;
}

List<String> construct(char[][] board) {
    List<String> res = new ArrayList<>();
    for (char[] row : board) {
        res.add(new String(row));
    }
    return res;
}
```

---

### 5. 动态规划

#### 0-1 背包问题
```java
int knapsack(int[] weights, int[] values, int capacity) {
    int n = weights.length;
    int[][] dp = new int[n + 1][capacity + 1];
    
    for (int i = 1; i <= n; i++) {
        for (int w = 0; w <= capacity; w++) {
            if (weights[i - 1] <= w) {
                dp[i][w] = Math.max(
                    dp[i - 1][w],
                    dp[i - 1][w - weights[i - 1]] + values[i - 1]
                );
            } else {
                dp[i][w] = dp[i - 1][w];
            }
        }
    }
    
    return dp[n][capacity];
}

// 空间优化版本
int knapsackOptimized(int[] weights, int[] values, int capacity) {
    int[] dp = new int[capacity + 1];
    
    for (int i = 0; i < weights.length; i++) {
        for (int w = capacity; w >= weights[i]; w--) {
            dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }
    
    return dp[capacity];
}
```

#### 完全背包问题
```java
int completeKnapsack(int[] weights, int[] values, int capacity) {
    int[] dp = new int[capacity + 1];
    
    for (int i = 0; i < weights.length; i++) {
        for (int w = weights[i]; w <= capacity; w++) {
            dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }
    
    return dp[capacity];
}
```

#### 最长公共子序列（LCS）
```java
int longestCommonSubsequence(String text1, String text2) {
    int m = text1.length(), n = text2.length();
    int[][] dp = new int[m + 1][n + 1];
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    
    return dp[m][n];
}
```

#### 最长递增子序列（LIS）
```java
// O(n^2) 解法
int lengthOfLIS(int[] nums) {
    int n = nums.length;
    int[] dp = new int[n];
    Arrays.fill(dp, 1);
    
    int maxLen = 1;
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[i] > nums[j]) {
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
        maxLen = Math.max(maxLen, dp[i]);
    }
    
    return maxLen;
}

// O(n log n) 二分解法
int lengthOfLISBinary(int[] nums) {
    List<Integer> tails = new ArrayList<>();
    
    for (int num : nums) {
        int left = 0, right = tails.size();
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (tails.get(mid) < num) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        
        if (left == tails.size()) {
            tails.add(num);
        } else {
            tails.set(left, num);
        }
    }
    
    return tails.size();
}
```

#### 编辑距离
```java
int minDistance(String word1, String word2) {
    int m = word1.length(), n = word2.length();
    int[][] dp = new int[m + 1][n + 1];
    
    for (int i = 0; i <= m; i++) dp[i][0] = i;
    for (int j = 0; j <= n; j++) dp[0][j] = j;
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                dp[i][j] = Math.min(
                    Math.min(dp[i - 1][j], dp[i][j - 1]),
                    dp[i - 1][j - 1]
                ) + 1;
            }
        }
    }
    
    return dp[m][n];
}
```

#### 股票买卖问题

**只能买卖一次**
```java
int maxProfit(int[] prices) {
    int minPrice = Integer.MAX_VALUE;
    int maxProfit = 0;
    
    for (int price : prices) {
        minPrice = Math.min(minPrice, price);
        maxProfit = Math.max(maxProfit, price - minPrice);
    }
    
    return maxProfit;
}
```

**可以买卖多次**
```java
int maxProfitMultiple(int[] prices) {
    int profit = 0;
    
    for (int i = 1; i < prices.length; i++) {
        if (prices[i] > prices[i - 1]) {
            profit += prices[i] - prices[i - 1];
        }
    }
    
    return profit;
}
```

**最多买卖 k 次**
```java
int maxProfitK(int k, int[] prices) {
    if (prices.length == 0) return 0;
    
    int n = prices.length;
    if (k >= n / 2) {
        // 退化为无限次交易
        int profit = 0;
        for (int i = 1; i < n; i++) {
            if (prices[i] > prices[i - 1]) {
                profit += prices[i] - prices[i - 1];
            }
        }
        return profit;
    }
    
    int[][] dp = new int[k + 1][n];
    
    for (int i = 1; i <= k; i++) {
        int maxDiff = -prices[0];
        for (int j = 1; j < n; j++) {
            dp[i][j] = Math.max(dp[i][j - 1], prices[j] + maxDiff);
            maxDiff = Math.max(maxDiff, dp[i - 1][j] - prices[j]);
        }
    }
    
    return dp[k][n - 1];
}
```

#### 打家劫舍
```java
// 基础版：线性排列
int rob(int[] nums) {
    if (nums.length == 0) return 0;
    if (nums.length == 1) return nums[0];
    
    int prev2 = 0, prev1 = 0;
    for (int num : nums) {
        int temp = prev1;
        prev1 = Math.max(prev1, prev2 + num);
        prev2 = temp;
    }
    
    return prev1;
}

// 环形排列
int robCircular(int[] nums) {
    if (nums.length == 1) return nums[0];
    return Math.max(
        robRange(nums, 0, nums.length - 2),
        robRange(nums, 1, nums.length - 1)
    );
}

int robRange(int[] nums, int start, int end) {
    int prev2 = 0, prev1 = 0;
    for (int i = start; i <= end; i++) {
        int temp = prev1;
        prev1 = Math.max(prev1, prev2 + nums[i]);
        prev2 = temp;
    }
    return prev1;
}
```

#### 分割等和子集
```java
boolean canPartition(int[] nums) {
    int sum = 0;
    for (int num : nums) sum += num;
    
    if (sum % 2 != 0) return false;
    
    int target = sum / 2;
    boolean[] dp = new boolean[target + 1];
    dp[0] = true;
    
    for (int num : nums) {
        for (int j = target; j >= num; j--) {
            dp[j] = dp[j] || dp[j - num];
        }
    }
    
    return dp[target];
}
```

#### 零钱兑换
```java
// 最少硬币数
int coinChange(int[] coins, int amount) {
    int[] dp = new int[amount + 1];
    Arrays.fill(dp, amount + 1);
    dp[0] = 0;
    
    for (int i = 1; i <= amount; i++) {
        for (int coin : coins) {
            if (i >= coin) {
                dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }
    }
    
    return dp[amount] > amount ? -1 : dp[amount];
}

// 组成方案数
int change(int amount, int[] coins) {
    int[] dp = new int[amount + 1];
    dp[0] = 1;
    
    for (int coin : coins) {
        for (int i = coin; i <= amount; i++) {
            dp[i] += dp[i - coin];
        }
    }
    
    return dp[amount];
}
```

---

### 6. 二分查找

#### 标准二分查找
```java
int binarySearch(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return -1;
}
```

#### 查找左边界
```java
int leftBound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return left < nums.length && nums[left] == target ? left : -1;
}
```

#### 查找右边界
```java
int rightBound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] <= target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return right >= 0 && nums[right] == target ? right : -1;
}
```

#### 旋转数组查找
```java
int searchRotated(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) return mid;
        
        // 左半部分有序
        if (nums[left] <= nums[mid]) {
            if (target >= nums[left] && target < nums[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        } 
        // 右半部分有序
        else {
            if (target > nums[mid] && target <= nums[right]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }
    
    return -1;
}
```

---

### 7. 滑动窗口

#### 固定窗口大小
```java
// 最大和的子数组（固定长度 k）
int maxSumSubarray(int[] nums, int k) {
    int sum = 0;
    for (int i = 0; i < k; i++) {
        sum += nums[i];
    }
    
    int maxSum = sum;
    for (int i = k; i < nums.length; i++) {
        sum += nums[i] - nums[i - k];
        maxSum = Math.max(maxSum, sum);
    }
    
    return maxSum;
}
```

#### 可变窗口大小
```java
// 最小覆盖子串
String minWindow(String s, String t) {
    Map<Character, Integer> need = new HashMap<>();
    Map<Character, Integer> window = new HashMap<>();
    
    for (char c : t.toCharArray()) {
        need.put(c, need.getOrDefault(c, 0) + 1);
    }
    
    int left = 0, right = 0;
    int valid = 0;
    int start = 0, len = Integer.MAX_VALUE;
    
    while (right < s.length()) {
        char c = s.charAt(right);
        right++;
        
        if (need.containsKey(c)) {
            window.put(c, window.getOrDefault(c, 0) + 1);
            if (window.get(c).equals(need.get(c))) {
                valid++;
            }
        }
        
        while (valid == need.size()) {
            if (right - left < len) {
                start = left;
                len = right - left;
            }
            
            char d = s.charAt(left);
            left++;
            
            if (need.containsKey(d)) {
                if (window.get(d).equals(need.get(d))) {
                    valid--;
                }
                window.put(d, window.get(d) - 1);
            }
        }
    }
    
    return len == Integer.MAX_VALUE ? "" : s.substring(start, start + len);
}

// 无重复字符的最长子串
int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> window = new HashMap<>();
    int left = 0, right = 0;
    int maxLen = 0;
    
    while (right < s.length()) {
        char c = s.charAt(right);
        right++;
        window.put(c, window.getOrDefault(c, 0) + 1);
        
        while (window.get(c) > 1) {
            char d = s.charAt(left);
            left++;
            window.put(d, window.get(d) - 1);
        }
        
        maxLen = Math.max(maxLen, right - left);
    }
    
    return maxLen;
}
```

---

### 8. 双指针

#### 对撞指针
```java
// 两数之和（有序数组）
int[] twoSum(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    
    while (left < right) {
        int sum = nums[left] + nums[right];
        if (sum == target) {
            return new int[]{left, right};
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    
    return new int[]{-1, -1};
}

// 三数之和
List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    Arrays.sort(nums);
    
    for (int i = 0; i < nums.length - 2; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) continue;
        
        int left = i + 1, right = nums.length - 1;
        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];
            if (sum == 0) {
                res.add(Arrays.asList(nums[i], nums[left], nums[right]));
                while (left < right && nums[left] == nums[left + 1]) left++;
                while (left < right && nums[right] == nums[right - 1]) right--;
                left++;
                right--;
            } else if (sum < 0) {
                left++;
            } else {
                right--;
            }
        }
    }
    
    return res;
}
```

#### 快慢指针
```java
// 链表中点
ListNode findMiddle(ListNode head) {
    ListNode slow = head, fast = head;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    
    return slow;
}

// 环形链表
boolean hasCycle(ListNode head) {
    if (head == null) return false;
    
    ListNode slow = head, fast = head;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) return true;
    }
    
    return false;
}

// 找环的入口
ListNode detectCycle(ListNode head) {
    ListNode slow = head, fast = head;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        
        if (slow == fast) {
            slow = head;
            while (slow != fast) {
                slow = slow.next;
                fast = fast.next;
            }
            return slow;
        }
    }
    
    return null;
}
```

---

### 9. 单调栈

```java
// 下一个更大元素
int[] nextGreaterElement(int[] nums) {
    int n = nums.length;
    int[] res = new int[n];
    Arrays.fill(res, -1);
    Stack<Integer> stack = new Stack<>();
    
    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
            int idx = stack.pop();
            res[idx] = nums[i];
        }
        stack.push(i);
    }
    
    return res;
}

// 每日温度
int[] dailyTemperatures(int[] temperatures) {
    int n = temperatures.length;
    int[] res = new int[n];
    Stack<Integer> stack = new Stack<>();
    
    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && temperatures[stack.peek()] < temperatures[i]) {
            int idx = stack.pop();
            res[idx] = i - idx;
        }
        stack.push(i);
    }
    
    return res;
}

// 柱状图中最大的矩形
int largestRectangleArea(int[] heights) {
    Stack<Integer> stack = new Stack<>();
    stack.push(-1);
    int maxArea = 0;
    
    for (int i = 0; i < heights.length; i++) {
        while (stack.peek() != -1 && heights[stack.peek()] >= heights[i]) {
            int h = heights[stack.pop()];
            int w = i - stack.peek() - 1;
            maxArea = Math.max(maxArea, h * w);
        }
        stack.push(i);
    }
    
    while (stack.peek() != -1) {
        int h = heights[stack.pop()];
        int w = heights.length - stack.peek() - 1;
        maxArea = Math.max(maxArea, h * w);
    }
    
    return maxArea;
}
```

---

### 10. 前缀和

```java
// 一维前缀和
class PrefixSum {
    private int[] preSum;
    
    public PrefixSum(int[] nums) {
        preSum = new int[nums.length + 1];
        for (int i = 0; i < nums.length; i++) {
            preSum[i + 1] = preSum[i] + nums[i];
        }
    }
    
    // 查询区间 [left, right] 的和
    public int query(int left, int right) {
        return preSum[right + 1] - preSum[left];
    }
}

// 二维前缀和
class MatrixPrefixSum {
    private int[][] preSum;
    
    public MatrixPrefixSum(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        preSum = new int[m + 1][n + 1];
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                preSum[i][j] = preSum[i - 1][j] + preSum[i][j - 1] 
                             - preSum[i - 1][j - 1] + matrix[i - 1][j - 1];
            }
        }
    }
    
    // 查询子矩阵 [row1, col1] 到 [row2, col2] 的和
    public int query(int row1, int col1, int row2, int col2) {
        return preSum[row2 + 1][col2 + 1] 
             - preSum[row1][col2 + 1] 
             - preSum[row2 + 1][col1] 
             + preSum[row1][col1];
    }
}
```

---

## 📌 总结

这份文档涵盖了：
- **Java 集合框架**：List、Set、Map、Queue、Stack 的完整使用方法
- **树算法**：DFS、BFS、线段树
- **图算法**：遍历、最短路径、最小生成树
- **经典算法**：回溯、动态规划、二分查找、滑动窗口、双指针、单调栈、前缀和

建议配合 LeetCode 刷题使用，遇到问题时可以快速查阅对应的模板代码。

**持续更新中...** 🚀