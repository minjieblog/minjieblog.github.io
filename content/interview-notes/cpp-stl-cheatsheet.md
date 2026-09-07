---
title: C++ STL 刷题手册
date: 2026-05-10T22:12:18+08:00
draft: false
tags:
  - STL
  - 算法
categories:
  - 面试笔记
summary: 系统整理 C++ STL 常用容器与刷题算法模板，包含 Part 1 STL 使用手册和Part 2 算法模板，是 C++ 算法刷题的完整参考。
description: C++ STL 容器与刷题算法模板速查手册，涵盖 string、vector、map、priority_queue 等常用容器的核心用法，以及树、图、回溯、动态规划、双指针、单调栈、前缀和等经典算法的代码模板，供 LeetCode 刷题时快速查阅。
comments: true
---
---

## 📚 Part 1: C++ STL 使用手册

---

### 0. String - 字符串

```cpp
#include <string>
using namespace std;

// 初始化
string s = "hello";
string s2(5, 'a');          // "aaaaa"
string s3 = to_string(123); // 数字转字符串

// 基本属性
int len = s.size();         // 长度（同 s.length()）
bool empty = s.empty();     // 是否为空
char ch = s[0];             // 获取字符（不检查越界）
char ch2 = s.at(0);         // 获取字符（检查越界）
char front = s.front();     // 第一个字符
char back = s.back();       // 最后一个字符

// 查找
int idx = s.find("ll");         // 查找子串，找不到返回 string::npos
int last = s.rfind("l");        // 从后往前查找
bool has = s.find("el") != string::npos; // 是否包含

// 截取
string sub = s.substr(1, 3);    // 从位置1开始，长度3
string sub2 = s.substr(2);      // 从位置2到结尾

// 替换（C++ 无内置全局替换，手动实现）
s.replace(1, 3, "XYZ");         // 将 [1, 1+3) 替换为 "XYZ"

// 拼接
s += " world";                  // 直接拼接
s.append("!!!");                // append 拼接
string concat = s + "abc";      // + 运算符

// 插入 / 删除
s.insert(0, "start ");          // 在位置0插入
s.erase(0, 6);                  // 删除 [0, 0+6)
s.pop_back();                   // 删除最后一个字符
s.push_back('!');               // 追加一个字符

// 比较
bool eq = (s == "hello");
bool lt = (s < "world");        // 字典序比较
int cmp = s.compare("hello");   // 0:相等 <0:小于 >0:大于

// 判断前后缀（C++20）
// bool sw = s.starts_with("he");
// bool ew = s.ends_with("lo");

// 转换
// 字符串转数字
int num = stoi("123");
long l = stol("123");
double d = stod("3.14");

// 大小写转换（需要 <algorithm> 和 <cctype>）
transform(s.begin(), s.end(), s.begin(), ::toupper);
transform(s.begin(), s.end(), s.begin(), ::tolower);

// 字符数组互转
const char* cstr = s.c_str();   // string -> C风格字符串
```

#### stringstream - 字符串流

**使用场景：字符串分割、类型转换、格式化拼接**

```cpp
#include <sstream>

// 字符串分割（按空格）
string line = "a b c d";
stringstream ss(line);
string token;
while (ss >> token) {
    cout << token << endl; // 依次输出 a b c d
}

// 按指定分隔符分割
string csv = "a,b,c";
stringstream ss2(csv);
while (getline(ss2, token, ',')) {
    cout << token << endl; // 依次输出 a b c
}

// 数字转字符串（C++11 前常用）
stringstream ss3;
ss3 << 42;
string result = ss3.str(); // "42"

// 拼接多种类型
stringstream builder;
builder << "id=" << 1 << ", name=" << "Alice";
cout << builder.str(); // "id=1, name=Alice"
```

---

### 1. vector - 动态数组

```cpp
#include <vector>

// 初始化
vector<int> v;
vector<int> v2(5, 0);                    // [0, 0, 0, 0, 0]
vector<int> v3 = {1, 2, 3, 4, 5};
vector<int> v4(v3.begin(), v3.end());    // 拷贝构造
vector<vector<int>> mat(3, vector<int>(4, 0)); // 3x4 二维数组

// 增
v.push_back(1);                          // 尾部追加
v.emplace_back(2);                       // 尾部构造（效率更高）
v.insert(v.begin(), 0);                  // 头部插入（O(n)，慎用）
v.insert(v.begin() + 2, 99);            // 指定位置插入

// 删
v.pop_back();                            // 删除尾部
v.erase(v.begin());                      // 删除头部
v.erase(v.begin() + 1, v.begin() + 3);  // 删除 [1, 3)
v.clear();                               // 清空

// 改
v[0] = 100;                              // 直接赋值（不检查越界）
v.at(0) = 100;                           // 安全赋值（检查越界）

// 查
int val = v[0];
int front = v.front();
int back = v.back();
int sz = v.size();
bool empty = v.empty();

// 遍历
for (int x : v) cout << x;
for (int i = 0; i < v.size(); i++) cout << v[i];

// 排序（需要 <algorithm>）
sort(v.begin(), v.end());                // 升序
sort(v.begin(), v.end(), greater<int>());// 降序
sort(v.begin(), v.end(), [](int a, int b){ return a > b; }); // 自定义

// 反转
reverse(v.begin(), v.end());

// 去重（先排序再去重）
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());

// 二分查找（需排序，需要 <algorithm>）
bool found = binary_search(v.begin(), v.end(), 3);
auto it = lower_bound(v.begin(), v.end(), 3); // 第一个 >= 3 的位置
auto it2 = upper_bound(v.begin(), v.end(), 3);// 第一个 >  3 的位置
int pos = it - v.begin();                // 转为下标
```

---

### 2. set / multiset - 集合

#### set - 有序不重复（红黑树）

```cpp
#include <set>

// 初始化
set<int> s;
set<int> s2 = {3, 1, 2};               // 自动排序：{1, 2, 3}

// 增
s.insert(1);
s.insert({2, 3, 4});                    // 批量插入

// 删
s.erase(1);                             // 按值删除
s.erase(s.begin());                     // 按迭代器删除
s.clear();

// 查
bool has = s.count(1);                  // 0 或 1
auto it = s.find(1);                    // 返回迭代器，找不到返回 s.end()
int sz = s.size();
bool empty = s.empty();

// 有序特有操作
auto lo = s.lower_bound(3);             // 第一个 >= 3 的迭代器
auto hi = s.upper_bound(3);             // 第一个 >  3 的迭代器
int first = *s.begin();                 // 最小元素
int last = *s.rbegin();                 // 最大元素

// 遍历（有序）
for (int x : s) cout << x;
```

#### unordered_set - 无序不重复（哈希表）

```cpp
#include <unordered_set>

unordered_set<int> us;
us.insert(1);
us.erase(1);
bool has = us.count(1);                 // O(1) 平均
// 无 lower_bound / upper_bound
```

#### multiset - 有序可重复

```cpp
#include <set>

multiset<int> ms = {3, 1, 2, 1};       // {1, 1, 2, 3}
ms.insert(1);
ms.erase(ms.find(1));                   // 只删一个 1
ms.erase(1);                            // 删除所有 1
int cnt = ms.count(1);                  // 统计某值出现次数
```

---

### 3. map / unordered_map - 映射

#### map - 有序键值对（红黑树）

```cpp
#include <map>

// 初始化
map<string, int> mp;
map<string, int> mp2 = {{"apple", 1}, {"banana", 2}};

// 增 / 改
mp["apple"] = 1;                        // 不存在则插入，存在则修改
mp.insert({"cherry", 3});               // 键已存在则不覆盖
mp.emplace("date", 4);                  // 原地构造

// 删
mp.erase("apple");                      // 按键删除
mp.erase(mp.begin());                   // 按迭代器删除
mp.clear();

// 查
int val = mp["apple"];                  // 不存在会自动插入0！
int val2 = mp.at("apple");             // 不存在抛异常（更安全）
bool has = mp.count("apple");           // 0 或 1
auto it = mp.find("apple");            // 找不到返回 mp.end()

// 安全取值
int v = mp.count("apple") ? mp["apple"] : 0;

// 遍历
for (auto& [key, val] : mp) {           // C++17 结构化绑定
    cout << key << ": " << val << endl;
}
for (auto it = mp.begin(); it != mp.end(); it++) {
    cout << it->first << ": " << it->second << endl;
}

// 有序特有
int firstKey = mp.begin()->first;
int lastKey = mp.rbegin()->first;
auto lo = mp.lower_bound("b");          // 第一个键 >= "b"
auto hi = mp.upper_bound("b");          // 第一个键 >  "b"

// 统计词频（常用技巧）
map<char, int> freq;
for (char c : "hello") freq[c]++;
```

#### unordered_map - 无序键值对（哈希表）
```c++
unordered_map<string, int> ump;

// 插入 / 更新
ump["a"] = 1;

// 删除
ump.erase("a");

// 查询是否存在
bool has = ump.count("a");       // 首选，简洁

auto it = ump.find("a");         // 需要取值时
if (it != ump.end()) {
    auto val = it->second;
}

if (ump.contains("a")) { ... }   // C++20，语义最清晰

// ⚠️ 勿用 ump["key"] 判断存在性——不存在时会自动插入默认值

// 限制：无 lower_bound / upper_bound
//       需有序遍历 → 改用 map
```

---

### 4. queue / deque / priority_queue - 队列

#### queue - 普通队列（FIFO）

```cpp
#include <queue>

queue<int> q;
q.push(1);                              // 入队
q.push(2);
int front = q.front();                  // 查看队首
int back = q.back();                    // 查看队尾
q.pop();                                // 出队（无返回值！）
int sz = q.size();
bool empty = q.empty();
```

#### deque - 双端队列

```cpp
#include <deque>

deque<int> dq;

// 头部操作
dq.push_front(1);
dq.pop_front();
int front = dq.front();

// 尾部操作
dq.push_back(2);
dq.pop_back();
int back = dq.back();

// 支持随机访问
int val = dq[0];
int sz = dq.size();
```

#### priority_queue - 优先队列（堆）

```cpp
#include <queue>

// 大根堆（默认）
priority_queue<int> maxHeap;
maxHeap.push(3);
maxHeap.push(1);
maxHeap.push(2);
int top = maxHeap.top();               // 3
maxHeap.pop();

// 小根堆
priority_queue<int, vector<int>, greater<int>> minHeap;
minHeap.push(3);
minHeap.push(1);
int topMin = minHeap.top();            // 1

// 自定义比较（对 pair 按第一个元素升序）
priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
pq.push({2, 100});
pq.push({1, 200});
auto [dist, node] = pq.top();          // {1, 200}

// 自定义 lambda 比较
auto cmp = [](vector<int>& a, vector<int>& b) {
    return a[0] > b[0];                // 按第一个元素升序（小根堆）
};
priority_queue<vector<int>, vector<vector<int>>, decltype(cmp)> pq2(cmp);
```

---

### 5. stack - 栈

```cpp
#include <stack>

stack<int> st;
st.push(1);                             // 入栈
st.push(2);
int top = st.top();                     // 查看栈顶（不弹出）
st.pop();                               // 弹出栈顶（无返回值！）
int sz = st.size();
bool empty = st.empty();
```

---

### 6. 常用算法    `<algorithm>`

```cpp
#include <algorithm>

vector<int> v = {3, 1, 4, 1, 5, 9};

// 最大最小
int mx = *max_element(v.begin(), v.end());
int mn = *min_element(v.begin(), v.end());
auto [lo, hi] = minmax_element(v.begin(), v.end()); // C++17

// 求和（需要 <numeric>）
#include <numeric>
int sum = accumulate(v.begin(), v.end(), 0);

// 排列
sort(v.begin(), v.end());
next_permutation(v.begin(), v.end());   // 下一个排列
prev_permutation(v.begin(), v.end());   // 上一个排列

// 填充
fill(v.begin(), v.end(), -1);
fill_n(v.begin(), 3, 0);               // 前3个填0

// 复制
vector<int> dst(v.size());
copy(v.begin(), v.end(), dst.begin());

// 计数
int cnt = count(v.begin(), v.end(), 1);

// 查找
auto it = find(v.begin(), v.end(), 4);

// 翻转
reverse(v.begin(), v.end());

// 去重（先排序）
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
```

---

### 7. 常用数学 `<cmath>`

```cpp
#include <cmath>
#include <climits>

// 数学运算
int mx = max(a, b);
int mn = min(a, b);
int ab = abs(-10);
double pw = pow(2, 3);                  // 2^3 = 8.0
double sq = sqrt(16.0);                 // 4.0
double ce = ceil(3.2);                  // 4.0
double fl = floor(3.8);                 // 3.0
long long ro = llround(3.6);            // 4

// 整数极值
int INF = INT_MAX;                      // 2147483647
int NEG_INF = INT_MIN;                  // -2147483648
long long LINF = LLONG_MAX;

// 常用技巧
int log2n = __lg(n);                    // floor(log2(n))，GCC 扩展
int len = __gcd(len1,len2)              //c++ 自带的求最大公约数的函数
bool isPow2 = n > 0 && (n & (n - 1)) == 0;
```

---

### 8. `pair / tuple`

```cpp
#include <utility>
#include <tuple>

// pair
pair<int, string> p = {1, "hello"};
p.first;                                // 1
p.second;                               // "hello"
auto p2 = make_pair(2, "world");

// pair 排序（默认先按 first，再按 second）
vector<pair<int,int>> vp = {{2,1},{1,3},{1,2}};
sort(vp.begin(), vp.end());             // {1,2},{1,3},{2,1}

// tuple
tuple<int, string, double> t = {1, "a", 3.14};
get<0>(t);                              // 1
get<1>(t);                              // "a"
auto [x, y, z] = t;                    // C++17 结构化绑定

auto t2 = make_tuple(42, "hi", 1.5);
```

---

## 🧮 Part 2: 算法模板

### 1. 树的遍历

#### 二叉树定义

```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
```

#### DFS - 深度优先遍历

**前序遍历（根-左-右）**

```cpp
// 递归
void preorder(TreeNode* root, vector<int>& res) {
    if (!root) return;
    res.push_back(root->val);           // 1. 访问根
    preorder(root->left, res);          // 2. 遍历左
    preorder(root->right, res);         // 3. 遍历右
}

// 迭代
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> res;
    if (!root) return res;
    stack<TreeNode*> st;
    st.push(root);
    while (!st.empty()) {
        TreeNode* node = st.top(); st.pop();
        res.push_back(node->val);
        if (node->right) st.push(node->right); // 先压右
        if (node->left)  st.push(node->left);  // 再压左
    }
    return res;
}
```

**中序遍历（左-根-右）**

```cpp
// 递归
void inorder(TreeNode* root, vector<int>& res) {
    if (!root) return;
    inorder(root->left, res);
    res.push_back(root->val);
    inorder(root->right, res);
}

// 迭代
vector<int> inorderTraversal(TreeNode* root) {
    vector<int> res;
    stack<TreeNode*> st;
    TreeNode* curr = root;
    while (curr || !st.empty()) {
        while (curr) {
            st.push(curr);
            curr = curr->left;          // 一直往左走
        }
        curr = st.top(); st.pop();
        res.push_back(curr->val);       // 访问
        curr = curr->right;             // 转向右子树
    }
    return res;
}
```

**后序遍历（左-右-根）**

```cpp
// 递归
void postorder(TreeNode* root, vector<int>& res) {
    if (!root) return;
    postorder(root->left, res);
    postorder(root->right, res);
    res.push_back(root->val);
}

// 迭代（前序变形 + 反转）
vector<int> postorderTraversal(TreeNode* root) {
    vector<int> res;
    if (!root) return res;
    stack<TreeNode*> st;
    st.push(root);
    while (!st.empty()) {
        TreeNode* node = st.top(); st.pop();
        res.push_back(node->val);
        if (node->left)  st.push(node->left);
        if (node->right) st.push(node->right);
    }
    reverse(res.begin(), res.end());    // 反转得到左右根
    return res;
}
```

#### BFS - 层序遍历

```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> res;
    if (!root) return res;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int sz = q.size();              // 当前层节点数
        vector<int> level;
        for (int i = 0; i < sz; i++) {
            TreeNode* node = q.front(); q.pop();
            level.push_back(node->val);
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        res.push_back(level);
    }
    return res;
}
```

#### 线段树（Segment Tree）

```cpp
/**
 * 线段树：区间查询 + 单点修改
 * 时间复杂度：构建 O(n)，查询/更新 O(log n)
 */
class SegmentTree {
    vector<int> tree;
    int n;
public:
    SegmentTree(vector<int>& nums) {
        n = nums.size();
        tree.resize(4 * n);
        build(nums, 0, 0, n - 1);
    }

    void build(vector<int>& nums, int node, int start, int end) {
        if (start == end) {
            tree[node] = nums[start];
            return;
        }
        int mid = start + (end - start) / 2;
        build(nums, 2*node+1, start, mid);
        build(nums, 2*node+2, mid+1, end);
        tree[node] = tree[2*node+1] + tree[2*node+2];
    }

    int query(int l, int r) { return query(0, 0, n-1, l, r); }
    int query(int node, int start, int end, int l, int r) {
        if (l > end || r < start) return 0;
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        return query(2*node+1, start, mid, l, r)
             + query(2*node+2, mid+1, end, l, r);
    }

    void update(int index, int val) { update(0, 0, n-1, index, val); }
    void update(int node, int start, int end, int index, int val) {
        if (start == end) { tree[node] = val; return; }
        int mid = start + (end - start) / 2;
        if (index <= mid) update(2*node+1, start, mid, index, val);
        else              update(2*node+2, mid+1, end, index, val);
        tree[node] = tree[2*node+1] + tree[2*node+2];
    }
};
```

---

### 2. 图算法

#### 图的表示

```cpp
// 邻接表（稀疏图）
vector<vector<int>> graph(n);
graph[u].push_back(v);                  // 有向边 u -> v

// 带权邻接表
vector<vector<pair<int,int>>> graph(n);
graph[u].push_back({v, w});             // 有向边 u -> v，权重 w

// 邻接矩阵（密集图）
vector<vector<int>> mat(n, vector<int>(n, INT_MAX));
mat[u][v] = w;
```

#### DFS / BFS

```cpp
// DFS（递归）
void dfs(int node, vector<bool>& visited, vector<vector<int>>& graph) {
    visited[node] = true;
    for (int nb : graph[node]) {
        if (!visited[nb]) dfs(nb, visited, graph);
    }
}

// BFS
void bfs(int start, vector<vector<int>>& graph) {
    int n = graph.size();
    vector<bool> visited(n, false);
    queue<int> q;
    q.push(start);
    visited[start] = true;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        for (int nb : graph[node]) {
            if (!visited[nb]) {
                visited[nb] = true;
                q.push(nb);
            }
        }
    }
}
```

#### 拓扑排序（Kahn 算法）

```cpp
vector<int> topoSort(int n, vector<vector<int>>& graph) {
    vector<int> inDeg(n, 0);
    for (int u = 0; u < n; u++)
        for (int v : graph[u]) inDeg[v]++;

    queue<int> q;
    for (int i = 0; i < n; i++)
        if (inDeg[i] == 0) q.push(i);

    vector<int> res;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        res.push_back(node);
        for (int nb : graph[node])
            if (--inDeg[nb] == 0) q.push(nb);
    }
    return res.size() == n ? res : vector<int>{}; // 有环则返回空
}
```

#### Dijkstra 最短路径

```cpp
// 时间复杂度：O(E log V)
vector<int> dijkstra(int n, vector<vector<pair<int,int>>>& graph, int src) {
    vector<int> dist(n, INT_MAX);
    dist[src] = 0;
    // {距离, 节点}，小根堆
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    pq.push({0, src});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;      // 过期节点跳过
        for (auto [v, w] : graph[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

#### Floyd-Warshall 全源最短路

```cpp
// 时间复杂度：O(V³)
void floydWarshall(vector<vector<int>>& dist, int n) {
    // 初始化：dist[i][i]=0，无边处为 INF
    for (int k = 0; k < n; k++)
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                if (dist[i][k] != INT_MAX && dist[k][j] != INT_MAX)
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
}
```

#### 并查集（Union-Find）

```cpp
class UnionFind {
    vector<int> parent, rank;
public:
    UnionFind(int n) : parent(n), rank(n, 0) {
        iota(parent.begin(), parent.end(), 0); // parent[i] = i
    }

    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);       // 路径压缩
        return parent[x];
    }

    bool unite(int x, int y) {
        int rx = find(x), ry = find(y);
        if (rx == ry) return false;
        if (rank[rx] < rank[ry]) swap(rx, ry); // 按秩合并
        parent[ry] = rx;
        if (rank[rx] == rank[ry]) rank[rx]++;
        return true;
    }

    bool connected(int x, int y) { return find(x) == find(y); }
};
```

#### Kruskal 最小生成树

```cpp
// 时间复杂度：O(E log E)
int kruskal(int n, vector<array<int,3>>& edges) {
    // edges[i] = {w, u, v}
    sort(edges.begin(), edges.end());
    UnionFind uf(n);
    int total = 0, cnt = 0;
    for (auto [w, u, v] : edges) {
        if (uf.unite(u, v)) {
            total += w;
            if (++cnt == n - 1) break;
        }
    }
    return cnt == n - 1 ? total : -1;
}
```

---

### 3. 回溯算法

#### 组合问题

```cpp
void backtrack(vector<vector<int>>& res, vector<int>& path, int start, int n, int k) {
    if ((int)path.size() == k) {
        res.push_back(path);
        return;
    }
    for (int i = start; i <= n; i++) {
        path.push_back(i);
        backtrack(res, path, i + 1, n, k);
        path.pop_back();                // 回溯
    }
}

vector<vector<int>> combine(int n, int k) {
    vector<vector<int>> res;
    vector<int> path;
    backtrack(res, path, 1, n, k);
    return res;
}
```

#### 全排列

```cpp
void backtrack(vector<vector<int>>& res, vector<int>& path,
               vector<int>& nums, vector<bool>& used) {
    if (path.size() == nums.size()) {
        res.push_back(path);
        return;
    }
    for (int i = 0; i < (int)nums.size(); i++) {
        if (used[i]) continue;
        used[i] = true;
        path.push_back(nums[i]);
        backtrack(res, path, nums, used);
        path.pop_back();
        used[i] = false;
    }
}

vector<vector<int>> permute(vector<int>& nums) {
    vector<vector<int>> res;
    vector<int> path;
    vector<bool> used(nums.size(), false);
    backtrack(res, path, nums, used);
    return res;
}
```

#### 子集问题

```cpp
void backtrack(vector<vector<int>>& res, vector<int>& path,
               vector<int>& nums, int start) {
    res.push_back(path);                // 每个状态都是一个子集
    for (int i = start; i < (int)nums.size(); i++) {
        path.push_back(nums[i]);
        backtrack(res, path, nums, i + 1);
        path.pop_back();
    }
}

vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> res;
    vector<int> path;
    backtrack(res, path, nums, 0);
    return res;
}
```
---

### 4. 动态规划

#### 0-1 背包

```cpp
int knapsack(vector<int>& weights, vector<int>& values, int capacity) {
    int n = weights.size();
    vector<int> dp(capacity + 1, 0);
    for (int i = 0; i < n; i++)
        for (int w = capacity; w >= weights[i]; w--) // 从后往前
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i]);
    return dp[capacity];
}
```

#### 完全背包

```cpp
int completeKnapsack(vector<int>& weights, vector<int>& values, int capacity) {
    vector<int> dp(capacity + 1, 0);
    for (int i = 0; i < (int)weights.size(); i++)
        for (int w = weights[i]; w <= capacity; w++) // 从前往后
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i]);
    return dp[capacity];
}
```

#### 最长公共子序列（LCS）

```cpp
int longestCommonSubsequence(string& s1, string& s2) {
    int m = s1.size(), n = s2.size();
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for (int i = 1; i <= m; i++)
        for (int j = 1; j <= n; j++)
            if (s1[i-1] == s2[j-1])
                dp[i][j] = dp[i-1][j-1] + 1;
            else
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
    return dp[m][n];
}
```

#### 最长递增子序列（LIS）

```cpp
// O(n log n)
int lengthOfLIS(vector<int>& nums) {
    vector<int> tails;
    for (int x : nums) {
        auto it = lower_bound(tails.begin(), tails.end(), x);
        if (it == tails.end()) tails.push_back(x);
        else *it = x;
    }
    return tails.size();
}
```

#### 编辑距离

```cpp
int minDistance(string& w1, string& w2) {
    int m = w1.size(), n = w2.size();
    vector<vector<int>> dp(m+1, vector<int>(n+1));
    for (int i = 0; i <= m; i++) dp[i][0] = i;
    for (int j = 0; j <= n; j++) dp[0][j] = j;
    for (int i = 1; i <= m; i++)
        for (int j = 1; j <= n; j++)
            if (w1[i-1] == w2[j-1])
                dp[i][j] = dp[i-1][j-1];
            else
                dp[i][j] = min({dp[i-1][j], dp[i][j-1], dp[i-1][j-1]}) + 1;
    return dp[m][n];
}
```

#### 零钱兑换

```cpp
// 最少硬币数
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount+1, amount+1);
    dp[0] = 0;
    for (int i = 1; i <= amount; i++)
        for (int c : coins)
            if (i >= c)
                dp[i] = min(dp[i], dp[i-c] + 1);
    return dp[amount] > amount ? -1 : dp[amount];
}

// 方案数
int change(int amount, vector<int>& coins) {
    vector<int> dp(amount+1, 0);
    dp[0] = 1;
    for (int c : coins)
        for (int i = c; i <= amount; i++)
            dp[i] += dp[i-c];
    return dp[amount];
}
```

---

### 5. 双指针

#### 对撞指针

```cpp
// 两数之和（有序数组）
vector<int> twoSum(vector<int>& nums, int target) {
    int l = 0, r = nums.size() - 1;
    while (l < r) {
        int sum = nums[l] + nums[r];
        if (sum == target) return {l, r};
        else if (sum < target) l++;
        else r--;
    }
    return {-1, -1};
}

// 三数之和
vector<vector<int>> threeSum(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    vector<vector<int>> res;
    int n = nums.size();
    for (int i = 0; i < n - 2; i++) {
        if (i > 0 && nums[i] == nums[i-1]) continue;
        int l = i+1, r = n-1;
        while (l < r) {
            int sum = nums[i] + nums[l] + nums[r];
            if (sum == 0) {
                res.push_back({nums[i], nums[l], nums[r]});
                while (l < r && nums[l] == nums[l+1]) l++;
                while (l < r && nums[r] == nums[r-1]) r--;
                l++; r--;
            } else if (sum < 0) l++;
            else r--;
        }
    }
    return res;
}
```

#### 快慢指针（链表）

```cpp
struct ListNode { int val; ListNode* next; };

// 链表中点
ListNode* findMiddle(ListNode* head) {
    ListNode* slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;
}

// 检测环
bool hasCycle(ListNode* head) {
    ListNode* slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true;
    }
    return false;
}
```

---

### 6. 单调栈

```cpp
// 下一个更大元素
vector<int> nextGreaterElement(vector<int>& nums) {
    int n = nums.size();
    vector<int> res(n, -1);
    stack<int> st;                       // 存下标
    for (int i = 0; i < n; i++) {
        while (!st.empty() && nums[st.top()] < nums[i]) {
            res[st.top()] = nums[i];
            st.pop();
        }
        st.push(i);
    }
    return res;
}

// 柱状图中最大矩形
int largestRectangleArea(vector<int>& heights) {
    heights.push_back(0);               // 哨兵，清空栈
    stack<int> st;
    st.push(-1);                        // 哨兵
    int maxArea = 0;
    for (int i = 0; i < (int)heights.size(); i++) {
        while (st.top() != -1 && heights[st.top()] >= heights[i]) {
            int h = heights[st.top()]; st.pop();
            int w = i - st.top() - 1;
            maxArea = max(maxArea, h * w);
        }
        st.push(i);
    }
    return maxArea;
}
```

---

### 7. 前缀和

```cpp
// 一维前缀和
class PrefixSum {
    vector<int> pre;
public:
    PrefixSum(vector<int>& nums) {
        pre.resize(nums.size() + 1, 0);
        for (int i = 0; i < (int)nums.size(); i++)
            pre[i+1] = pre[i] + nums[i];
    }
    // 查询区间 [l, r] 的和
    int query(int l, int r) { return pre[r+1] - pre[l]; }
};

// 二维前缀和
class MatrixPrefixSum {
    vector<vector<int>> pre;
public:
    MatrixPrefixSum(vector<vector<int>>& mat) {
        int m = mat.size(), n = mat[0].size();
        pre.assign(m+1, vector<int>(n+1, 0));
        for (int i = 1; i <= m; i++)
            for (int j = 1; j <= n; j++)
                pre[i][j] = pre[i-1][j] + pre[i][j-1]
                           - pre[i-1][j-1] + mat[i-1][j-1];
    }
    // 查询子矩阵 [r1,c1] 到 [r2,c2] 的和
    int query(int r1, int c1, int r2, int c2) {
        return pre[r2+1][c2+1] - pre[r1][c2+1]
             - pre[r2+1][c1] + pre[r1][c1];
    }
};
```


