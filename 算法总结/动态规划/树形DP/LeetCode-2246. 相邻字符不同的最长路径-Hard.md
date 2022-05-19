#### [2246. 相邻字符不同的最长路径](https://leetcode.cn/problems/longest-path-with-different-adjacent-characters/)

难度困难29收藏分享切换为英文接收动态反馈

给你一棵 **树**（即一个连通、无向、无环图），根节点是节点 `0` ，这棵树由编号从 `0` 到 `n - 1` 的 `n` 个节点组成。用下标从 **0** 开始、长度为 `n` 的数组 `parent` 来表示这棵树，其中 `parent[i]` 是节点 `i` 的父节点，由于节点 `0` 是根节点，所以 `parent[0] == -1` 。

另给你一个字符串 `s` ，长度也是 `n` ，其中 `s[i]` 表示分配给节点 `i` 的字符。

请你找出路径上任意一对相邻节点都没有分配到相同字符的 **最长路径** ，并返回该路径的长度。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2022/03/25/testingdrawio.png)

```
输入：parent = [-1,0,0,1,1,2], s = "abacbe"
输出：3
解释：任意一对相邻节点字符都不同的最长路径是：0 -> 1 -> 3 。该路径的长度是 3 ，所以返回 3 。
可以证明不存在满足上述条件且比 3 更长的路径。 
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2022/03/25/graph2drawio.png)

```
输入：parent = [-1,0,0,0], s = "aabc"
输出：3
解释：任意一对相邻节点字符都不同的最长路径是：2 -> 0 -> 3 。该路径的长度为 3 ，所以返回 3 。
```

 

**提示：**

- `n == parent.length == s.length`
- `1 <= n <= 105`
- 对所有 `i >= 1` ，`0 <= parent[i] <= n - 1` 均成立
- `parent[0] == -1`
- `parent` 表示一棵有效的树
- `s` 仅由小写英文字母组成

思路：

采用拓扑排序的思路，用数组 degree 记录当前节点的入度（即子节点个数）。
从叶子节点开始，自底向上更新两个一维数组u和t。
length[i]表示以 i 为根的子树上，能够形成满足条件的最长路径长度；
tree[i]表示在当前已拓展过的节点中以i为根的子树的最大深度；
因此有公式：（parV 为parent[i]，即 i 为新拓展完毕的子树，parV 为当前正在拓展的子树）
length[parV] = max(length[parV], tree[i]+tree[parV]);（表示新拓展一棵子树之后，与已拓展过的子树形成的最长路径）
tree[parV] = max(tree[parV], tree[i]+1);（以新拓展的子树更新最大深度）
(两条公式的前后关系不能搞反，否则在更新了 tree 之后，length 就没法判断了)
注意：判断一下相邻节点是否相等，如果相等就舍弃掉这条路径，以1为深度继续拓展即可。
最后返回u中的最大值即为全局最长路径。

代码部分：

```c++
class Solution {
public:
    int longestPath(vector<int>& parent, string s) {
        int n = parent.size();
        vector<int> degree(n, 0);
        vector<int> tree(n, 1); // 以 i 为根节点的相邻字符不同的树的最大深度
        vector<int> length(n, 1); // 表示以i为根的子树上，能够形成满足条件的最长路径长度
        for (int i = 1; i < n; i++) {
            degree[parent[i]]++;
        }
        queue<int> que;
        for (int i = 0; i < n; i++) {
            if (!degree[i]) {
                que.push(i);
                tree[i] = 1;
            }
        }
        while (!que.empty()) {
            int v = que.front();
            que.pop();
            if (v == 0) {
                break;
            }
            int parV = parent[v];
            if (s[parV] != s[v]) {
                length[parV] = max(length[parV], tree[v] + tree[parV]);
                tree[parV] = max(tree[parV], tree[v] + 1);
            }
            degree[parV]--;
            if (!degree[parV]) {
                que.push(parV);
            }
        }
        int result = 0;
        for (int i = 0; i < n; i++) {
            result = max(result, length[i]);
        }
        return result;
    }
};
/*
用拓扑排序的方法从叶子节点开始，自底向上更新两个一维数组 tree 和 length。
*///2022.4.19
```

方法：【树状DP】

[1245. 树的直径](https://leetcode-cn.com/problems/tree-diameter/)

经典题 树的直径改版

C++版代码：

```c++
class Solution {
private:
    int result = 0;
    vector<int> dp;
    vector<int> tree; // tree[i] 表示以节点 i 为根节点且相邻节点字符不相等的树的最大深度
    vector<vector<int>> son;
    void dfs(int rootIndex, string& s) {
        for (auto v : son[rootIndex]) {
            dfs(v, s);
            if (s[rootIndex] != s[v]) {
                dp[rootIndex] = max(dp[rootIndex], tree[rootIndex] + tree[v]);
                tree[rootIndex] = max(tree[rootIndex], tree[v] + 1);
            }
        }
        result = max(result, dp[rootIndex]);
    }
public:
    int longestPath(vector<int>& parent, string s) {
        int n = parent.size();
        son = vector<vector<int>>(n);
        dp = vector<int>(n, 1);
        tree = vector<int>(n, 1);
        for (int i = 1; i < n; i++) son[parent[i]].push_back(i);
        dfs(0, s);
        return result;
    }
};
// 2022.4.20
```

