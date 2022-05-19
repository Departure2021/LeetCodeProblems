#### [1349. 参加考试的最大学生数](https://leetcode.cn/problems/maximum-students-taking-exam/)

难度困难134收藏分享切换为英文接收动态反馈

给你一个 `m * n` 的矩阵 `seats` 表示教室中的座位分布。如果座位是坏的（不可用），就用 `'#'` 表示；否则，用 `'.'` 表示。

学生可以看到左侧、右侧、左上、右上这四个方向上紧邻他的学生的答卷，但是看不到直接坐在他前面或者后面的学生的答卷。请你计算并返回该考场可以容纳的一起参加考试且无法作弊的最大学生人数。

学生必须坐在状况良好的座位上。

 

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/02/09/image.png)

```
输入：seats = [["#",".","#","#",".","#"],
              [".","#","#","#","#","."],
              ["#",".","#","#",".","#"]]
输出：4
解释：教师可以让 4 个学生坐在可用的座位上，这样他们就无法在考试中作弊。 
```

**示例 2：**

```
输入：seats = [[".","#"],
              ["#","#"],
              ["#","."],
              ["#","#"],
              [".","#"]]
输出：3
解释：让所有学生坐在可用的座位上。
```

**示例 3：**

```
输入：seats = [["#",".",".",".","#"],
              [".","#",".","#","."],
              [".",".","#",".","."],
              [".","#",".","#","."],
              ["#",".",".",".","#"]]
输出：10
解释：让学生坐在第 1、3 和 5 列的可用座位上。
```

 

**提示：**

- `seats` 只包含字符 `'.' 和``'#'`
- `m == seats.length`
- `n == seats[i].length`
- `1 <= m <= 8`
- `1 <= n <= 8`

方法：【状压DP】

C++版代码：

```c++
class Solution {
private:
    vector<vector<int>> state; // 记录每行座位的符合情况
    vector<vector<int>> student; // 记录每行座位的符合情况的学生人数
    void pre(vector<vector<char>>& seats) { // 记录没行座位的符合情况，即符合时候的学生人数
        int m = seats.size();
        int n = seats[0].size();
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < (1 << n); j++) {
                if (j & (j << 1)) continue; // 同一行出现作弊情况：左侧、右侧
                int cnt = 0;
                int num = j;
                bool flag = true;
                int k = n - 1;
                while (num != 0 and flag) {
                    if (num & 1 and seats[i][k] == '.') {
                        cnt++;
                    } else if (num & 1 and seats[i][k] == '#') {
                        flag = false;
                    }
                    k--;
                    num = num >> 1;
                }
                if (flag) {
                    state[i].push_back(j);
                    student[i].push_back(cnt);
                }
            }
        }
    }
public:
    int maxStudents(vector<vector<char>>& seats) {
        int m = seats.size();
        int n = seats[0].size();
        state = vector<vector<int>>(m);
        student = vector<vector<int>>(m);
        pre(seats);

        vector<vector<int>> dp(m);
        for (int i = 0; i < m; i++) { // 因为每行的状态个数不一样，所以 dp 数组每行大小要与状态数大小一样
            dp[i].resize(state[i].size());
        }
        for (int j = 0; j < state[0].size(); j++) { // 初始化第一行
            dp[0][j] = student[0][j];
        }
        for (int i = 1; i < m; i++) {
            for (int j = 0; j < state[i].size(); j++) {
                // 穷举第 i - 1 行的状态，取可以坐的学生最多的那个状态
                for (int t = 0; t < state[i - 1].size(); t++) {
                    if (!(state[i][j] & (state[i - 1][t] >> 1)) and !(state[i][j] & (state[i - 1][t] << 1))) {
                        dp[i][j] = max(dp[i][j], dp[i - 1][t] + student[i][j]);
                    }
                }
            }
        }
        int res = 0;
        for (int j = 0; j < state[m - 1].size(); j++) {
            res = max(res, dp[m - 1][j]);
        }
        return res;
    }
};

/*
1. 确定dp数组（dp table）以及下标的含义
    dp[i][j] 表示第 i 行采取状态 j时最多能坐 dp[i][j] 个学生。
2. 确定递推公式
// 穷举第 i - 1 行的状态，取可以坐的学生最多的那个状态
for (int t = 0; t < state[i - 1].size(); t++) {
    if (!(state[i][j] & (state[i - 1][t] >> 1)) and !(state[i][j] & (state[i - 1][t] << 1))) {
        dp[i][j] = max(dp[i][j], dp[i - 1][t] + student[i][j]);
    }
}
最终答案，从所有符合状态的 j 从 选择最大的一个 res = max(res, dp[m - 1][j]);
3. dp数组如何初始化
    因为每行的状态个数不一样，所以 dp 数组大小要与状态数大小一样
    for (int i = 0; i < m; i++) {
        dp[i].resize(state[i].size());
    }
    // 初始化第一行
    for (int j = 0; j < state[0].size(); j++) {
        dp[0][j] = student[0][j];
    }
4. 确定遍历顺序
    从上到下，从左到右
*/// 2022.4.21
```

