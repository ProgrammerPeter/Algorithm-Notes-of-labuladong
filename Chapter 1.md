# 核心套路篇

## 树的遍历

只要涉及递归的问题，基本上都是树的问题。

```c++
void traverse(TreeNode root) {
    // 前序遍历
    traverse(root.left);
    // 中序遍历
    traverse(root.right);
    // 后序遍历
}
```

## 动态规划

1. 动态规划问题的一般形式就是求最值。
2. 求解动态规划的核心问题是穷举。
3. 三要素：重叠子问题、最优子结构、状态转移方程。
4. 状态转移方程：
    - 问题的base case是什么？
    - 问题有什么“状态”？
    - 每个状态下有什么“选择”？
    - 如何定义`dp`数组/函数来表示“状态”和“选择”？

```python
# 初始化base case
dp[0][0][...] = base case
# 进行状态转移
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 求最值(选择1, 选择2, ...)
```

## 回溯算法

```python
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件：
        result.add(路径)
        return
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

1. 解决一个回溯问题，实际上就是一个决策树的遍历过程：
    - 路径：也就是已经做出的选择。
    - 选择列表：也就是你当前可以做的选择。
    - 结束条件：也就是到达决策树底层，无法再做选择的条件。
2. 回溯算法是纯暴力穷举，复杂度一般都很高。
 
## BFS算法

```c++
// 计算从起点start到终点target的最短距离
int BFS(Node start, Node target) {
    Queue<Node> q; // 核心数据结构
    Set<Node> visited; // 避免走回头路
    
    q.offer(start); // 将起点加入队列
    visited.add(start);
    int step = 0; // 记录扩散的步数
    
    while (q not empty) {
        int sz = q.size();
        /* 将当前队列中的所有节点向四周扩散 */
        for (int i = 0; i < sz; i++) {
            Node cur = q.poll();
            /* 划重点：这里判断是否到达终点 */
            if (cur is target)
                return step;
            /* 将cur的相邻节点加入队列 */
            for (Node x : cur.adj())
                if (x not in visited) {
                    q.offer(x);
                    visited.add(x);
                }
        }
        /* 划重点：在这里更新步数 */
        step++;
    }
}
```

1. DFS靠遍历所有分支比较后获得最短路径，DFS齐头并进，可能无需遍历所有节点。
2. DFS时间复杂度高，BFS空间复杂度高。
3. 知道终点位置时可以使用双向BFS，双向BFS是从起点和终点同时开始扩散，当两边有交集的时候停止。
4. 双向BFS不再使用队列，而是使用HashSet，方便、快速地判断两个集合是否有交集。
5. 双向BFS优化：
    - While循环后交换q1和q2的内容，所以只要默认扩散q1即可。
    - 每次都选择一个较小的集合进行扩散，空间增长速度就会慢一些。

## 双指针

### 快、慢指针

1. 判断链表中是否含有环。
2. 已知链表中含有环，返回这个环的起始位置。
3. 寻找无环单链表的中点。
4. 寻找单链表的倒数第k个元素。

### 左、右指针

1. 二分搜索。
2. 两数之和。
3. 反转数组。
4. 滑动窗口。

## 二分搜索算法

```c++
int binary_search(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 直接返回
            return mid;
        }
    }
    // 直接返回
    return -1;
}
```

```c++
int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，收缩右侧边界，锁定左侧边界
            right = mid - 1;
        }
    }
    // 最后要检查left越界的情况
    if (left >= nums.length || nums[left] != target)
        return -1;
    return left;
}
```

```c++
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，收缩左侧边界，锁定右侧边界
            left = mid + 1;
        }
    }
    // 最后要检查right越界的情况
    if (right < 0 || nums[right] != target)
        return -1;
    return right;
}
```

## 滑动窗口算法

```c++
/* 滑动窗口算法框架 */
void slidingWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for (char c : t) need[c]++;
    
    int left = 0, right = 0;
    int valid = 0;
    while (right < s.size()) {
        // c是将移入窗口的字符
        char c = s[right];
        // 右移窗口
        right++;
        // 进行窗口内数据的一系列更新
        ...
        
        /*** debug 输出的位置 ***/
        printf("window: [%d, %d)\n", left, right);
        /********************/
        
        // 判断左侧窗口是否要收缩
        while (window needs shrink) {
            // d是将移出窗口的字符
            char d = s[left];
            // 左移窗口
            left++;
            // 进行窗口内数据的一系列更新
            ...
        }
    }
}
```