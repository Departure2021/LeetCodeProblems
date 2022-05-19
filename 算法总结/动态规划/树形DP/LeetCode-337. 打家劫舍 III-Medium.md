#### [337. 打家劫舍 III](https://leetcode.cn/problems/house-robber-iii/)

难度中等1308收藏分享切换为英文接收动态反馈

小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为 `root` 。

除了 `root` 之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果 **两个直接相连的房子在同一天晚上被打劫** ，房屋将自动报警。

给定二叉树的 `root` 。返回 ***在不触动警报的情况下** ，小偷能够盗取的最高金额* 。

 

**示例 1:**

![img](https://assets.leetcode.com/uploads/2021/03/10/rob1-tree.jpg)

```
输入: root = [3,2,3,null,3,null,1]
输出: 7 
解释: 小偷一晚能够盗取的最高金额 3 + 3 + 1 = 7
```

**示例 2:**

![img](https://assets.leetcode.com/uploads/2021/03/10/rob2-tree.jpg)

```
输入: root = [3,4,5,1,3,null,1]
输出: 9
解释: 小偷一晚能够盗取的最高金额 4 + 5 = 9
```

 

**提示：**



- 树的节点数在 `[1, 104]` 范围内
- `0 <= Node.val <= 104`

方法一：【树形DP】

C++版代码：

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    // ⻓度为2的数组，0：不偷，1：偷
    vector<int> robTree(TreeNode* cur) {
        if (cur == NULL) {
            return vector<int>{0, 0};
        }
        vector<int> left = robTree(cur->left);
        vector<int> right = robTree(cur->right);

        // 偷当前节点 cur
        int val1 = cur->val + left[0] + right[0];
        // 不偷当前节点 cur
        int val2 = max(left[0], left[1]) + max(right[0], right[1]);
        return {val2, val1};
    }
    int rob(TreeNode* root) {
        vector<int> result = robTree(root);
        return max(result[0], result[1]);
    }
};
/*
1. 确定递归函数的参数和返回值
    要求⼀个节点 偷与不偷的两个状态所得到的⾦钱，那么返回值就是⼀个⻓度为2的数组
    vector<int> robTree(TreeNode* cur)
    dp数组（dp table）以及下标的含义：下标为0记录不偷该节点所得到的的最⼤⾦钱，下标为1记录偷该节点所得到的的最⼤⾦钱
2. 确定终⽌条件
    在遍历的过程中，如果遇到空间点的话，很明显，⽆论偷还是不偷都是0，所以就返回
    if (cur == NULL) return vector<int>{0, 0};
3. 确定遍历顺序
    使⽤后序遍历。 因为通过递归函数的返回值来做下⼀步计算。
        通过递归左节点，得到左节点偷与不偷的⾦钱。
        通过递归右节点，得到右节点偷与不偷的⾦钱
        // 下标0：不偷，下标1：偷
        vector<int> left = robTree(cur->left); // 左
        vector<int> right = robTree(cur->right); // 右
        // 中
4. 确定单层递归的逻辑
    如果是偷当前节点，那么左右孩⼦就不能偷，val1 = cur->val + left[0] + right[0]; 
    如果不偷当前节点，那么左右孩⼦就可以偷，⾄于到底偷不偷⼀定是选⼀个最⼤的，所以：
        val2 = max(left[0], left[1]) + max(right[0], right[1]);
    最后当前节点的状态就是{val2, val1}; 即：{不偷当前节点得到的最⼤⾦钱，偷当前节点得到的最⼤⾦钱}
5. 举例推导dp数组
*/ // 2022.3.02
```

方法二：【记忆化搜索】

C++版代码：

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    //记忆化递推
    //使⽤⼀个map把计算过的结果保存⼀下，这样如果计算过孙⼦了，那么计算孩⼦的时候可以复⽤孙⼦节点的结果
    unordered_map<TreeNode* , int> umap; // 记录计算过的结果
    int rob(TreeNode* root) {
        if (root == NULL) return 0;
        if (root->left == NULL && root->right == NULL) {
            return root->val;
        }
        if (umap[root]) return umap[root]; // 如果umap⾥已经有记录则直接返回
        // 偷⽗节点
        int val1 = root->val;
        if (root->left) {
            val1 += rob(root->left->left) + rob(root->left->right); // 跳过root->left
        }
        
        if (root->right) {
            val1 += rob(root->right->left) + rob(root->right->right); // 跳过root->right
        }
        // 不偷⽗节点
        int val2 = rob(root->left) + rob(root->right); // 考虑root的左右孩⼦
        umap[root] = max(val1, val2); // umap记录⼀下结果
        return max(val1, val2);
    }
}; // 2022.3.02
```

