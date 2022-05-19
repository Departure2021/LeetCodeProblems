# 数位DP

数位dp,主要用来解决统计满足某类特殊关系或有某些特点的区间内的数的个数，它是按位来进行计数统计的，可以保存子状态，速度较快。数位dp做多了后，套路基本上都差不多，关键把要保存的状态给抽象出来，保存下来。

# 区间DP

区间dp其实就是一种建立在线性结构上的对区间的动态规划，dp本来就是很奇妙的东西，也没有什么套路，就是一种思考的数学思维方式，只有做足够多的题并且想的足够多才可能在比赛中做出来。https://blog.csdn.net/qq_43472263/article/details/98337401

区间dp，顾名思义，在区间上dp，大多数题目的状态都是由区间（类似于dp[l][r]这种形式）构成的，就是我们可以把大区间转化成小区间来处理，然后对小区间处理后再回溯的求出大区间的值，主要的方法有两种，记忆化搜索和递推。

在用递推来求解时，关键在于递推是for循环里面的顺序，以及dp的关键：状态转移方程。

当然大部分的区间dp都是有特点的，我们可以考虑符合什么条件下，大区间可以转化成小区间，然后找出边界条件，进行dp求解。

区间dp也有很经典的板子部分，下面抛出代码和解析：

```c++
memset(dp,0,sizeof(dp))//初始dp数组
for(int len=2;len<=n;len++){//枚举区间长度
    for(int i=1;i<n;++i){//枚举区间的起点
        int j=i+len-1;//根据起点和长度得出终点
        if(j>n) break;//符合条件的终点
        for(int k=i;k<=j;++k)//枚举最优分割点
            dp[i][j]=min(dp[i][j],dp[i][k]+dp[k+1][j]+w[i][j]);//状态转移方程
    }
}
```

#### [516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

给你一个字符串 s ，找出其中最长的回文子序列，并返回该序列的长度。

子序列定义为：不改变剩余字符顺序的情况下，删除某些字符或者不删除任何字符形成的一个序列。

示例 1：

```C++
输入：s = "bbbab"
输出：4
解释：一个可能的最长回文子序列为 "bbbb" 。
```

示例 2：

```C++
输入：s = "cbbd"
输出：2
解释：一个可能的最长回文子序列为 "bb" 。
```


提示：

- 1 <= s.length <= 1000
- s 仅由小写英文字母组成

C++版代码部分：

```c++
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n, 0));
        for (int i = 0; i < n; i++) dp[i][i] = 1;
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                if (s[i] == s[j]) {
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                } else {
                    dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[0][n - 1];
    }
};
/*
1. 确定dp数组（dp table）以及下标的含义
    dp[i][j]：字符串s在[i, j]范围内最⻓的回⽂⼦序列的⻓度为dp[i][j]。
2. 确定递推公式
    在判断回⽂⼦串的题⽬中，关键逻辑就是看s[i]与s[j]是否相同。
    如果s[i]与s[j]相同，那么dp[i][j] = dp[i + 1][j - 1] + 2;
    如果s[i]与s[j]不相同，说明s[i]和s[j]的同时加⼊ 并不能增加[i,j]区间回⽂⼦串的⻓度，那么分别加⼊s[i]、
    s[j]看看哪⼀个可以组成最⻓的回⽂⼦序列。
    加⼊s[j]的回⽂⼦序列⻓度为dp[i + 1][j]。
    加⼊s[i]的回⽂⼦序列⻓度为dp[i][j - 1]。
那么dp[i][j]⼀定是取最⼤的，即：dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
3. dp数组如何初始化
    ⾸先要考虑当i 和j 相同的情况，从递推公式：dp[i][j] = dp[i + 1][j - 1] + 2; 可以看出 递推公式是计算不
到 i 和j相同时候的情况。
    所以需要⼿动初始化⼀下，当i与j相同，那么dp[i][j]⼀定是等于1的，即：⼀个字符的回⽂⼦序列⻓度就
是1。
    其他情况dp[i][j]初始为0就⾏，这样递推公式：dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]); 中dp[i][j]才不会被
初始值覆盖。
4. 确定遍历顺序
从递推公式dp[i][j] = dp[i + 1][j - 1] + 2 和 dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]) 可以看出，dp[i][j]是依
赖于dp[i + 1][j - 1] 和 dp[i + 1][j]，
也就是从矩阵的⻆度来说，dp[i][j] 下⼀⾏的数据。 所以遍历i的时候⼀定要从下到上遍历，这样才能保
证，下⼀⾏的数据是经过计算的。
*/// 2022.4.20
```



# 二维矩阵

#### [174. 地下城游戏](https://leetcode-cn.com/problems/dungeon-game/)

思路及算法

几个要素：「M×N 的网格」「每次只能向右或者向下移动一步」，让人很容易想到该题使用动态规划的方法。

但是我们发现，如果按照从左上往右下的顺序进行动态规划，对于每一条路径，我们需要同时记录两个值。第一个是「从出发点到当前点的路径和」，第二个是「从出发点到当前点所需的最小初始值」。而这两个值的重要程度相同。

我们希望「从出发点到当前点的路径和」尽可能大，而「从出发点到当前点所需的最小初始值」尽可能小。这两条路径各有优劣。

因此，如果按照从左上往右下的顺序进行动态规划，我们无法直接确定到达 (1,2)的方案，因为有两个重要程度相同的参数同时影响后续的决策。也就是说，这样的动态规划是**不满足「无后效性」**的。

于是我们考虑从右下往左上进行动态规划。令`dp[i][j]` 表示从坐标 `(i,j)` 到终点所需的最小初始值。换句话说，当我们到达坐标 `(i,j)` 时，如果此时我们的路径和不小于`dp[i][j]` ，我们就能到达终点。

这样一来，我们就无需担心路径和的问题，只需要关注最小初始值。对于 `dp[i][j]` ，我们只要关心`dp[i][j + 1]`  和 `dp[i + 1][j]` 的最小值`minn` 。记当前格子的值为`dungeon(i,j)`，那么在坐标 `(i,j)` 的初始值只要达到 `minn−dungeon(i,j)` 即可。同时，初始值还必须大于等于 1。这样我们就可以得到状态转移方程：

$$
dp[i][j]=max(min(dp[i+1][j], dp[i][j+1])−dungeon(i,j),1)
$$
最终答案即为 `dp[0][0]`。

边界条件为，当 `i = n - 1` 或者 `j = m - 1` 时，`dp[i][j]`转移需要用到的 `dp[i][j+1]` 和 `dp[i+1][j]` 中有无效值，因此代码实现中给无效值赋值为极大值。特别地，`dp[n−1][m−1]` 转移需要用到的 `dp[n−1][m]` 和 `dp[n][m−1]` 均为无效值，因此我们给这两个值赋值为 1。

代码部分：

```c++
class Solution {
public:
    int calculateMinimumHP(vector<vector<int>>& dungeon) {
        int n = dungeon.size(), m = dungeon[0].size();
        vector<vector<int>> dp(n + 1, vector<int>(m + 1, INT_MAX));
        dp[n][m - 1] = dp[n - 1][m] = 1;
        for (int i = n - 1; i >= 0; --i) {
            for (int j = m - 1; j >= 0; --j) {
                int minn = min(dp[i + 1][j], dp[i][j + 1]);
                dp[i][j] = max(minn - dungeon[i][j], 1);
            }
        }
        return dp[0][0];
    }
};
//2022.4.14
```

#### [1444. 切披萨的方案数](https://leetcode-cn.com/problems/number-of-ways-of-cutting-a-pizza/)（好难）

给你一个 rows x cols 大小的矩形披萨和一个整数 k ，矩形包含两种字符： 'A' （表示苹果）和 '.' （表示空白格子）。你需要切披萨 k-1 次，得到 k 块披萨并送给别人。

切披萨的每一刀，先要选择是向垂直还是水平方向切，再在矩形的边界上选一个切的位置，将披萨一分为二。如果垂直地切披萨，那么需要把左边的部分送给一个人，如果水平地切，那么需要把上面的部分送给一个人。在切完最后一刀后，需要把剩下来的一块送给最后一个人。

请你返回确保每一块披萨包含 至少 一个苹果的切披萨方案数。由于答案可能是个很大的数字，请你返回它对 10^9 + 7 取余的结果。

示例 1：

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/05/10/ways_to_cut_apple_1.png)

```
输入：pizza = ["A..","AAA","..."], k = 3
输出：3 
解释：上图展示了三种切披萨的方案。注意每一块披萨都至少包含一个苹果。
```

示例 2：

```
输入：pizza = ["A..","AA.","..."], k = 3
输出：1
```

示例 3：

```
输入：pizza = ["A..","A..","..."], k = 1
输出：1
```


提示：

```
1 <= rows, cols <= 50
rows == pizza.length
cols == pizza[i].length
1 <= k <= 10
pizza 只包含字符 'A' 和 '.' 。
```

思路：

本题是统计切割方案数，一看就是使用dp，怎么来思考呢？
首先我们考虑存在的状态数：
毫无疑问，披萨被切成k块肯定是状态之一。
而如何表示当前剩余部分的披萨呢？题中说把左边和上边给一个人，可以知道，右下部分总是会剩余下来。
所以**可以记录左上角的位置来表示剩余的披萨**。

因此一个三维数组可以做为 dp 数组：`dp[i][j][k]`  **i, j 表示披萨剩余部分的左上角, k 表示当前披萨被切成k块**
初始状态显而易见，由于只有一块，没有切，左上角为(0,0)，所以`dp[0][0][1]=1`

1. dp 数组含义

   `dp[i][j][k]` 表示以坐标（i，j）为披萨剩余部分的左上角，将披萨被切成 k 块的方案数为 `dp[i][j][k]`。

2. 递推公式

   记原来的左上角为(i,j),新的左上角为(x,y)，穷举所有的切刀点。

   ```c++
   if(两部分都存在披萨'A') { 
       dp[x][y][k+1] += dp[i][j][k];
   }
   // 穷举水平切
   for (int x = i + 1; x < rows; x++) {
       if (isValid(prefix, i, j, x-1, cols-1) and isValid(prefix, x, j, rows-1, cols-1)) {
           dp[x][j][k] += dp[i][j][k - 1];
           dp[x][j][k] %= MOD;
       }
   }
   
   // 穷举垂直切
   for (int y = j + 1; y < cols; y++) {
       if (isValid(prefix, i, j, rows-1, y-1) and isValid(prefix, i, y, rows-1, cols-1)) {
           dp[i][y][k] += dp[i][j][k - 1];
           dp[i][y][k] %= MOD;
       }
   }
   ```

3. 初始化

   初始状态，由于只有一块，没有切，左上角为(0,0)，所以`dp[0][0][1]=1`

4. 遍历顺序

   有递推公式，先遍历 k，再遍历i，最后j，且都为从小到大。

**大体步骤：**

1. 如何判断是否有苹果，可以使用前缀和数组，计算prefix，前缀和数组，从左上角开始（**从右下角开始计算也可以，看到有人的代码执行速度快了 6 倍**）

   使用左上角方式的前缀和数组，就要分别计算 2 块披萨上面是否有苹果

   ```c++
   // 左上角 (x1, y1)	右下角 (x2, y2)
   bool isValid(vector<vector<int>>& prefix, int x1, int y1, int x2, int y2) {
           int num1 = 0;
           int num2 = 0;
           int num3 = 0;
           if (x1 > 0 and y1 > 0) num1 = prefix[x1 - 1][y1 - 1];
           if (x1 > 0) num2 = prefix[x1 - 1][y2];
           if (y1 > 0) num3 = prefix[x2][y1 - 1];
           if (prefix[x2][y2] - num2 - num3 + num1 > 0) return true; 
           return false;
       }
   ```

   如何使用从右下角开始的前缀和数组，判断是否有苹果的条件可以改为

   ```c++
   prefix[i][j] - prefix[x][j] > 0
   ```

2. 初始化`dp[0][0][1]=1`

3. 状态转移填充

4. 计算答案

代码部分：

```c++
class Solution {
private:
    typedef long long ll;
    const int MOD =  1e9 + 7;
    bool isValid(vector<vector<int>>& prefix, int x1, int y1, int x2, int y2) {
        int num1 = 0;
        int num2 = 0;
        int num3 = 0;
        if (x1 > 0 and y1 > 0) num1 = prefix[x1 - 1][y1 - 1];
        if (x1 > 0) num2 = prefix[x1 - 1][y2];
        if (y1 > 0) num3 = prefix[x2][y1 - 1];
        if (prefix[x2][y2] - num2 - num3 + num1 > 0) return true; 
        return false;
    }
public:
    int ways(vector<string>& pizza, int K) {
        int rows = pizza.size();
        int cols = pizza[0].size();

        vector<vector<int>> prefix(rows, vector<int>(cols, 0)); // 二维前缀和，包含本单元格
        prefix[0][0] = (pizza[0][0] == 'A');
        for (int j = 1; j < cols; j++) { // 初始化前缀和第一行
            prefix[0][j] = prefix[0][j - 1] + (pizza[0][j] == 'A');
        }
        for (int i = 1; i < rows; i++) { // 初始化前缀和第一列
            prefix[i][0] = prefix[i - 1][0] + (pizza[i][0] == 'A');
        }
        for (int i = 1; i < rows; i++) { // 剩余前缀和
            for (int j = 1; j < cols; j++) {
                prefix[i][j] = prefix[i - 1][j] + prefix[i][j - 1] - prefix[i - 1][j - 1] + (pizza[i][j] == 'A');
            }
        }

        vector<vector<vector<ll>>> dp(rows, vector<vector<ll>>(cols, vector<ll>(K + 1, 0)));
        dp[0][0][1] = 1;
        for (int k = 2; k <= K; k++) {
            for (int i = 0; i < rows; i++) {
                for (int j = 0; j < cols; j++) {
                    //dp为0代表不存在这种情况
                    if(dp[i][j][k - 1] == 0) continue;
                        
                    // 穷举水平切
                    for (int x = i + 1; x < rows; x++) {
                        if (isValid(prefix, i, j, x-1, cols-1) and isValid(prefix, x, j, rows-1, cols-1)) {
                            dp[x][j][k] += dp[i][j][k - 1];
                            dp[x][j][k] %= MOD;
                        }
                    }

                    // 穷举垂直切
                    for (int y = j + 1; y < cols; y++) {
                        if (isValid(prefix, i, j, rows-1, y-1) and isValid(prefix, i, y, rows-1, cols-1)) {
                            dp[i][y][k] += dp[i][j][k - 1];
                            dp[i][y][k] %= MOD;
                        }
                    }
                }
            }
        }
        ll result = 0;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                result += dp[i][j][K];
            }
            result %= MOD;
        }
        return result;
    }
};
// 2022.4.20
```



# 状态压缩DP

​		在计算机里，整数是以二进制的方式存储的。把状态信息压缩成二进制当成状态进行动态规划，就是状压DP的基本思想。

#### 位运算基础

|          |   &   | \|     | ^     | <<        | >>        |
| :------- | :---: | :----- | :---- | :-------- | :-------- |
| 中文意思 |  并   | 或     | 异或  | 右移      | 左移      |
| 举例说明 | 1&1=1 | 1\|0=1 | 1^0=1 | 1<<1=10   | 10>>1=1   |
|          | 1&0=0 | 0\|0=0 | 1^1=0 | 11<<1=110 | 101>>1=10 |

![QQ截图20190429202509.png](https://cdn.acwing.com/media/article/image/2019/04/29/1130_d74b057c6a-QQ%E6%88%AA%E5%9B%BE20190429202509.png)

#### 判断算法

首先拿到一道题目,我们第一步就是要看**数据范围,题目描述.** 而且当你发现数据范围和题目描述具有**以下三大特点**的时候,那么我们就可以**初步判断**这道题目需要使用状态压缩动态规划.

1. 数据中的N,M范围很小,**基本上不超过30,20**.(N,M广义理解)
2. 题目似乎要我们**求方案数,或者说极值问题.**
3. 题目似乎是个**棋盘覆盖**这种类型的问题.

#### 巧妙的位运算

一.  &, |, !,<<,>>,^,~

1. ```c++
   //求二进制最低位为1所在的位置
   x & (-x);
   ```

2. ```cpp
   //求二进制最低位为1所在的位置
   x & (x - 1)
   ```

3. 

   ```
    功能           |            示例            |          位运算
   ———————————————+————————————————————————————+——————–————————————————
   去掉最后一位     | (101101->10110)            | x >>1
   在最后加一个0    | (101101->1011010)          | x << 1
   在最后加一个1    | (101101->1011011)          | x << 1+1
   把最后一位变成1   | (101100->101101)          | x | 1
   把最后一位变成0   | (101101->101100)          | x | 0
   最后一位取反     | (101101->101100)           | x ^ 1
   把右数第k位变成1  | (101001->101101,k=3)      | x | (1 << (k-1))
   把右数第k位变成0  | (101101->101001,k=3)      | x & ! (1 << (k-1))
   右数第k位取反    | (101001->101101,k=3)       | x ^ (1 << (k-1))
   取末三位         | (1101101->101)            | x & 7
   取末k位         | (1101101->1101,k=5)        | x & (1 << k-1)
   取右数第k位      | (1101101->1,k=4)           | x >> (k-1) & 1
   把末k位变成1     | (101001->101111,k=4)       | x | (1 << k-1)
   末k位取反       | (101001->100110,k=4)       | x x| (1 << k-1)
   把右边连续的1变成0  | (100101111->100100000)  | x & (x+1)
   把右起第一个0变成1  | (100101111->100111111)  | x | (x+1)
   把右边连续的0变成1  | (11011000->11011111)    | x | (x-1)
   取右边连续的1     | (100101111->1111)        | (x | (x+1)) >> 1
   去掉右起第一个1的左边 | (100101000->1000)      | x & (x x| (x-1))
   ```



## 例题

| 题目                                                         | 类型               | 难度 |
| ------------------------------------------------------------ | ------------------ | ---- |
| 骑士(P1896 [SCOI2005]互不侵犯)                               | 状态压缩DP经典入门 |      |
| [1349. 参加考试的最大学生数](https://leetcode-cn.com/problems/maximum-students-taking-exam/) | 状态压缩DP         | 困难 |
| [691. 贴纸拼词](https://leetcode.cn/problems/stickers-to-spell-word/) | 状态压缩           | 困难 |

#### 骑士(P1896 [SCOI2005]互不侵犯)

题目描述

在N×N的棋盘里面放K个国王，使他们互不攻击，共有多少种摆放方案。国王能攻击到它上下左右，以及左上左下右上右下八个方向上附近的各一个格子，共8个格子。

注：数据有加强（2018/4/25）

输入格式

只有一行，包含两个数N，K （ 1 <=N <=9, 0 <= K <= N * N）

输出格式

所得的方案数

输入样例

3 2

4 4

样例输出

16

79

```c++
#include<iostream>
#include<vector>
#include<stack>
#include<algorithm>
#include<unordered_map>
#include<unordered_set>
#include<queue>
#include<string>
#include<math.h>
#include<stdio.h>
using namespace std;

typedef long long ll;
typedef long double ld;
typedef pair<int, int> pii;
typedef pair<long long, long long> pll;
typedef vector<int> vi;
const ll INF = (ll)1e18;

class Solution {
private:
    vector<int> map;
    vector<int> kings;
    vector<vector<vector<int>>> dp;
    int N;
    int K;
    int stateNum = 0;
    void pre() {
        map.push_back(0); // 添加一个废数据
        kings.push_back(0); // 添加一个废数据
        for (int i = 0; i < (1 << N); i++) { // 枚举每个状态
            if (i & (i << 1)) continue; // 有国王相邻
            map.push_back(i);
            stateNum++; // 记录有几个合格的状态
            int count = 0;
            int num = i;
            while (num != 0) {
                if (num & 1) count++;
                num = num >> 1;
            }
            kings.push_back(count); // 记录第 i 个状态中,国王的个数为 kings[i]
        }
    }
public:
    int totalNumberOfProgrammes(int N, int K) {
        this->N = N;
        this->K = K;
        pre();
        dp = vector<vector<vector<int> > >(N + 1, vector<vector<int>>(stateNum + 1, vector<int>(K + 1)));
        dp[0][1][0] = 1; // 初始化第 0 行放在 0 个国王数的方案数为 1
        for (int i = 1; i <= N; i++) {
            for (int j = 1; j <= stateNum; j++) {
                for (int k = 0; k <= K; k++) {
                    if (k < kings[j]) continue; // 国王数小于当前状态 j 时，跳过
                    for (int t = 1; t <= stateNum; t++) { // 枚举第 i - 1 行的所有状态
                        if (!(map[j] & map[t]) and !((map[j] << 1) & map[t]) and !((map[j] >> 1) & map[t])){
                            dp[i][j][k] += dp[i - 1][t][k - kings[j]];
                            //cout << "dp[" << i << "][" << j << "][" << k << "] = " << dp[i][j][k] << endl;
                        }
                    }
                }
            }
        }
        int res = 0;
        for (int i = 1; i <= stateNum; i++) {
            res += dp[N][i][K];
        }
        return res;
    }
};

int main()
{
    int N, K;
    while (cin >> N >> K) {
        Solution sol;
        int res = sol.totalNumberOfProgrammes(N, K);
        cout << res << endl;
    }
    return 0;
}
/*
输入样例

3 2

4 4

样例输出

16

79
*/
// 2022.4.11
```

#### [1349. 参加考试的最大学生数](https://leetcode-cn.com/problems/maximum-students-taking-exam/)（不难）

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

示例 2：

```
输入：seats = [[".","#"],
              ["#","#"],
              ["#","."],
              ["#","#"],
              [".","#"]]
输出：3
解释：让所有学生坐在可用的座位上。
```

示例 3：

```
输入：seats = [["#",".",".",".","#"],
              [".","#",".","#","."],
              [".",".","#",".","."],
              [".","#",".","#","."],
              ["#",".",".",".","#"]]
输出：10
解释：让学生坐在第 1、3 和 5 列的可用座位上。
```


提示：

```
seats 只包含字符 '.' 和'#'
m == seats.length
n == seats[i].length
1 <= m <= 8
1 <= n <= 8
```

$$
时间复杂度：O(4^nmn)
\\空间复杂度：O(2^nm)
\\需要求解的情况最多有 2^nm种，求解每个情况所需的时间是 O(2^nn)，
\\因此时间复杂度为O(4^nmn)，空间复杂度为 O(2^nm)。
$$

代码部分：

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

#### [691. 贴纸拼词](https://leetcode.cn/problems/stickers-to-spell-word/)

难度困难

我们有 `n` 种不同的贴纸。每个贴纸上都有一个小写的英文单词。

您想要拼写出给定的字符串 `target` ，方法是从收集的贴纸中切割单个字母并重新排列它们。如果你愿意，你可以多次使用每个贴纸，每个贴纸的数量是无限的。

返回你需要拼出 `target` 的最小贴纸数量。如果任务不可能，则返回 `-1` 。

**注意：**在所有的测试用例中，所有的单词都是从 `1000` 个最常见的美国英语单词中随机选择的，并且 `target` 被选择为两个随机单词的连接。

 

**示例 1：**

```
输入： stickers = ["with","example","science"], target = "thehat"
输出：3
解释：
我们可以使用 2 个 "with" 贴纸，和 1 个 "example" 贴纸。
把贴纸上的字母剪下来并重新排列后，就可以形成目标 “thehat“ 了。
此外，这是形成目标字符串所需的最小贴纸数量。
```

**示例 2:**

```
输入：stickers = ["notice","possible"], target = "basicbasic"
输出：-1
解释：我们不能通过剪切给定贴纸的字母来形成目标“basicbasic”。
```

 

**提示:**

- `n == stickers.length`
- `1 <= n <= 50`
- `1 <= stickers[i].length <= 10`
- `1 <= target <= 15`
- `stickers[i]` 和 `target` 由小写英文单词组成

方法一：【DFS + 记忆化搜索】
		为了方便，我们记 target 的长度为 n。

​		使用一个 state（一个 int 类型变量）来代表当前 target  的凑成情况：若 target [i] 已被凑成，则在 state 中低 i 位为 1，否则为 0。

​		起始时有 state = 0，最终若能凑成 target ，则有 state = (1 << n) - 1。

​		由于每个 stickers[i]  可以被使用多次，因此对于一个特定的 state 而言，其转换为最终的 (1 << n) - 1 的最小步数固定，因此我们可以使用「记忆化搜索」来避免对相同的 state 进行重复搜索。

​		而在单步的搜索过程中，我们枚举每个 stickers[i] 来更新 state，假设使用某个 stickers[i] 得到的新状态为 nstate，则所有的 dfs(nstate) + 1 的最小值即是 f[state]。

方法二：【DP + 记忆化搜索】

C++版代码：（官方代码）

```c++
class Solution {
public:
    int minStickers(vector<string>& stickers, string target) {
        int m = target.size();
        vector<int> dp(1 << m, -1);
        dp[0] = 0;
        function<int(int)> helper = [&](int mask) {
            if (dp[mask] != -1) {
                return dp[mask];
            }
            dp[mask] = m + 1;
            for (auto & sticker : stickers) {
                int left = mask;
                vector<int> cnt(26);
                for (char & c : sticker) {
                    cnt[c - 'a']++;
                }
                for (int i = 0; i < m; i++) {
                    if ((mask >> i & 1) && cnt[target[i] - 'a'] > 0) {
                        cnt[target[i] - 'a']--;
                        left ^= 1 << i;
                    }
                }
                if (left < mask) {
                    dp[mask] = min(dp[mask], helper(left) + 1);
                }
            }
            return dp[mask];
        };
        int res = helper((1 << m) - 1);
        return res > m ? -1 : res;
    }
}; // 2022.5.14
```



# 树形DP

#### 一、概念

##### 1、什么是树型动态规划 

树形dp是建立在树这种数据结构上的dp,一般状态比较好想，通过 dfs 维护**从根到叶子**或**从叶子到根**的状态转移。

树型动态规划就是在“树”的数据结构上的动态规划，平时作的动态规划都是线性的或者是建立在图上的，线性的动态规划有二种方向既向前和向后，相应的线性的动态规划有二种方法既顺推与逆推，而树型动态规划是建立在树上的，所以也相应的有二个方向：   

1. 叶－>根：在回溯的时候从叶子节点往上更新信息   
2. 根 - >叶：往往是在从叶往根dfs一遍之后（相当于预处理），再重新往下获取最后的答案。   

不管是 从叶－>根 还是 从 根 - >叶，两者都是根据需要采用，没有好坏高低之分。

##### 2、树真的是一种特别特别优美的结构！

用来做动态规划也简直是锦上添花再美不过的事，因为树本身至少就有“子结构”性质（树和子树）；本身就具有递归性。所以在树上DP其实是其所当然的事，相比线性动态规划来讲，转移方程更直观，更易理解。

##### 3、难点   

1. 和线性动态规划相比，树形DP往往是要利用递归+记忆化搜索。   
2. 细节多，较为复杂的树形DP，从子树，从父亲，从兄弟……一些小的要处理的地方，脑子不清晰的时候做起来颇为恶心   
3. 状态表示和转移方程，也是真正难的地方。做到后面，树形DP的老套路都也就那么多，难的还是怎么能想出转移方程，各种DP做到最后都是这样！

## 例题

| 题目                                                         | 类型       | 难度 |
| ------------------------------------------------------------ | ---------- | ---- |
| [337. 打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/) | 入门树形DP | 中等 |
| [6073. 相邻字符不同的最长路径](https://leetcode-cn.com/problems/longest-path-with-different-adjacent-characters/) | 树形DP     | 困难 |
|                                                              |            |      |

# LCS问题

#### [1143. 最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)

难度中等961

给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 **公共子序列** 的长度。如果不存在 **公共子序列** ，返回 `0` 。

一个字符串的 **子序列** 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

- 例如，`"ace"` 是 `"abcde"` 的子序列，但 `"aec"` 不是 `"abcde"` 的子序列。

两个字符串的 **公共子序列** 是这两个字符串所共同拥有的子序列。

 

**示例 1：**

```
输入：text1 = "abcde", text2 = "ace" 
输出：3  
解释：最长公共子序列是 "ace" ，它的长度为 3 。
```

**示例 2：**

```
输入：text1 = "abc", text2 = "abc"
输出：3
解释：最长公共子序列是 "abc" ，它的长度为 3 。
```

**示例 3：**

```
输入：text1 = "abc", text2 = "def"
输出：0
解释：两个字符串没有公共子序列，返回 0 。
```

 

**提示：**

- `1 <= text1.length, text2.length <= 1000`
- `text1` 和 `text2` 仅由小写英文字符组成。

C++版代码：

```c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int len1 = text1.size();
        int len2 = text2.size();
        vector<vector<int>> dp(len1 + 1, vector<int>(len2 + 1, 0));

        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (text1[i - 1] == text2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }
                else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[len1][len2];
    }
};
/*
1. 确定dp数组（dp table）以及下标的含义
    dp[i][j]：⻓度为[0, i - 1]的字符串 text1 与⻓度为[0, j - 1]的字符串 text2 的最⻓公共⼦序列长度为 dp[i][j]
2. 确定递推公式
    主要就是两⼤情况： text1[i - 1] 与 text2[j - 1]相同，text1[i - 1] 与 text2[j - 1]不相同
    如果text1[i - 1] 与 text2[j - 1]相同，那么找到了⼀个公共元素，所以 dp[i][j] = dp[i - 1][j - 1] + 1;
    如果text1[i - 1] 与 text2[j - 1]不相同，那就看看text1[0, i - 2] 与 text2[0, j - 1] 的最⻓公共⼦序列 和
 text1[0, i - 1] 与 text2[0, j - 2] 的最⻓公共⼦序列，取最⼤的。
    即：dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
3. dp数组如何初始化
    test1[0, i-1]和空串的最⻓公共⼦序列⾃然是0，所以dp[i][0] = 0;
    同理dp[0][j]也是0。
4. 确定遍历顺序
   从前向后，从上到下来遍历这个矩阵
*/ // 2022.3.12
```



#### [1035. 不相交的线](https://leetcode.cn/problems/uncrossed-lines/)

难度中等310

在两条独立的水平线上按给定的顺序写下 `nums1` 和 `nums2` 中的整数。

现在，可以绘制一些连接两个数字 `nums1[i]` 和 `nums2[j]` 的直线，这些直线需要同时满足满足：

-  `nums1[i] == nums2[j]`
- 且绘制的直线不与任何其他连线（非水平线）相交。

请注意，连线即使在端点也不能相交：每个数字只能属于一条连线。

以这种方法绘制线条，并返回可以绘制的最大连线数。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2019/04/26/142.png)

```
输入：nums1 = [1,4,2], nums2 = [1,2,4]
输出：2
解释：可以画出两条不交叉的线，如上图所示。 
但无法画出第三条不相交的直线，因为从 nums1[1]=4 到 nums2[2]=4 的直线将与从 nums1[2]=2 到 nums2[1]=2 的直线相交。
```

**示例 2：**

```
输入：nums1 = [2,5,1,2,5], nums2 = [10,5,2,1,5,2]
输出：3
```

**示例 3：**

```
输入：nums1 = [1,3,7,1,7,5], nums2 = [1,9,2,5,1]
输出：2
```

 

**提示：**

- `1 <= nums1.length, nums2.length <= 500`
- `1 <= nums1[i], nums2[j] <= 2000`

思路：

​	这是一道「[最长公共子序列（LCS）](https://leetcode-cn.com/problems/longest-common-subsequence/solution/gong-shui-san-xie-zui-chang-gong-gong-zi-xq0h/)」的轻度变形题。

C++版代码：

```c++
class Solution {
public:
    int maxUncrossedLines(vector<int>& nums1, vector<int>& nums2) {
        int len1 = nums1.size();
        int len2 = nums2.size();
        vector<vector<int>> dp(len1 + 1, vector<int>(len2 + 1, 0));

        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (nums1[i - 1] == nums2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }
                else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[len1][len2];
    }
};
/*
直线不能相交，这就是说明在字符串A中 找到⼀个与字符串B相同的⼦序列，且这个⼦序列不能改变相对
顺序，只要相对顺序不改变，链接相同数字的直线就不会相交。

其实也就是说A和B的最⻓公共⼦序列是[1,4]，⻓度为2。 这个公共⼦序列指的是相对顺序不变（即数字
4在字符串A中数字1的后⾯，那么数字4也应该在字符串B数字1的后⾯）
这么分析完之后，⼤家可以发现：本题说是求绘制的最⼤连线数，其实就是求两个字符串的最⻓公共⼦
序列的⻓度！

1. 确定dp数组（dp table）以及下标的含义
    dp[i][j]：⻓度为[0, i - 1]的字符串 text1 与⻓度为[0, j - 1]的字符串 text2 的最⻓公共⼦序列长度为 dp[i][j]
2. 确定递推公式
    主要就是两⼤情况： text1[i - 1] 与 text2[j - 1]相同，text1[i - 1] 与 text2[j - 1]不相同
    如果text1[i - 1] 与 text2[j - 1]相同，那么找到了⼀个公共元素，所以 dp[i][j] = dp[i - 1][j - 1] + 1;
    如果text1[i - 1] 与 text2[j - 1]不相同，那就看看text1[0, i - 2] 与 text2[0, j - 1] 的最⻓公共⼦序列 和
 text1[0, i - 1] 与 text2[0, j - 2] 的最⻓公共⼦序列，取最⼤的。
    即：dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
3. dp数组如何初始化
    test1[0, i-1]和空串的最⻓公共⼦序列⾃然是0，所以dp[i][0] = 0;
    同理dp[0][j]也是0。
4. 确定遍历顺序
   从前向后，从上到下来遍历这个矩阵
*/ // 2022.3.13
```

# 子串统计类

## 例题

| 题目                                                         | 类型     | 难度 |
| ------------------------------------------------------------ | -------- | ---- |
| [2262. 字符串的总引力](https://leetcode.cn/problems/total-appeal-of-a-string/) | 子串统计 | 困难 |
| [828. 统计子串中的唯一字符](https://leetcode.cn/problems/count-unique-characters-of-all-substrings-of-a-given-string/) | 子串统计 | 困难 |
|                                                              |          |      |



#### [2262. 字符串的总引力](https://leetcode.cn/problems/total-appeal-of-a-string/)(好难，思路太巧妙了)

字符串的 **引力** 定义为：字符串中 **不同** 字符的数量。

- 例如，`"abbca"` 的引力为 `3` ，因为其中有 `3` 个不同字符 `'a'`、`'b'` 和 `'c'` 。

给你一个字符串 `s` ，返回 **其所有子字符串的总引力** **。**

**子字符串** 定义为：字符串中的一个连续字符序列。

 

**示例 1：**

```
输入：s = "abbca"
输出：28
解释："abbca" 的子字符串有：
- 长度为 1 的子字符串："a"、"b"、"b"、"c"、"a" 的引力分别为 1、1、1、1、1，总和为 5 。
- 长度为 2 的子字符串："ab"、"bb"、"bc"、"ca" 的引力分别为 2、1、2、2 ，总和为 7 。
- 长度为 3 的子字符串："abb"、"bbc"、"bca" 的引力分别为 2、2、3 ，总和为 7 。
- 长度为 4 的子字符串："abbc"、"bbca" 的引力分别为 3、3 ，总和为 6 。
- 长度为 5 的子字符串："abbca" 的引力为 3 ，总和为 3 。
引力总和为 5 + 7 + 7 + 6 + 3 = 28 。
```

**示例 2：**

```
输入：s = "code"
输出：20
解释："code" 的子字符串有：
- 长度为 1 的子字符串："c"、"o"、"d"、"e" 的引力分别为 1、1、1、1 ，总和为 4 。
- 长度为 2 的子字符串："co"、"od"、"de" 的引力分别为 2、2、2 ，总和为 6 。
- 长度为 3 的子字符串："cod"、"ode" 的引力分别为 3、3 ，总和为 6 。
- 长度为 4 的子字符串："code" 的引力为 4 ，总和为 4 。
引力总和为 4 + 6 + 6 + 4 = 20 。
```

 

**提示：**

- `1 <= s.length <= 105`
- `s` 由小写英文字母组成

方法：【DP】

提示 1-1
将所有子串按照其末尾字符的下标分组。

提示 1-2
考虑两组相邻的子串：以 s[i - 1] 结尾的子串、以 s[i] 结尾的子串。

提示 1-3
**以 s[i] 结尾的子串，可以看成是以 s[i - 1] 结尾的子串，在末尾添加上 s[i] 组成。**

**上面这一串提示是思考子串统计类问题的通用技巧之一。**

提示 2-1
从左往右遍历 s，考虑将 s[i] 添加到以 s[i - 1] 结尾的子串的末尾。添加后，这些子串的引力值会增加多少？

提示 2-2
分类讨论：

1. 如果 s[i] 之前没有遇到过，那么这些子串的引力值都会增加 1，这些子串的引力值之和会增加 i，再加上 1，即为 s[i] 单独组成的子串的引力值；`ans += i + 1;`
2. 如果 s[i] 之前遇到过，设其上次出现的下标为 j，
   - 那么向子串 $$s[0..i-1],\ s[1..i-1],\ s[2..i-1],\cdots,s[j..i-1]$$ 的末尾添加 s[i] 后，这些子串的引力值是不会变化的，因为 s[i] 已经在 s[j] 处出现过了；
   - 而子串 $$s[j+1..i-1],\ s[j+2..i-1],\cdots,s[i-1..i-1]$$ 由于不包含字符 s[i]，这些子串的引力值都会增加 1，因此有 $$i - j- 1$$ 个子串的引力值会增加 1，这些子串的引力值之和会增加 $$i-j-1$$，再加上 1，即 s[i] 单独组成的子串的引力值。



提示 2-3
模拟上述过程，遍历 s 的过程中用一个变量 $$\textit{sumG}$$ 维护以 s[i] 结尾的子串的引力值之和，同时用一个数组 $$\textit{last}$$ 记录每个字符上次出现的下标。

累加遍历中的 $$\textit{sumG}$$ 即为答案。（提示 1-1）

复杂度分析
时间复杂度：$$O(n)$$，其中 n 为 s 的长度。
空间复杂度：$$O(|\Sigma|)$$，其中 $$|\Sigma|$$ 为字符集合的大小，本题中字符均为小写字母，所以 $$|\Sigma|=26$$。
**相似题目：**

- [828. 统计子串中的唯一字符](https://leetcode-cn.com/problems/count-unique-characters-of-all-substrings-of-a-given-string/)
- [907. 子数组的最小值之和](https://leetcode-cn.com/problems/sum-of-subarray-minimums/)
- [1498. 满足条件的子序列数目](https://leetcode-cn.com/problems/number-of-subsequences-that-satisfy-the-given-sum-condition/)
- [2104. 子数组范围和](https://leetcode-cn.com/problems/sum-of-subarray-ranges/)

C++版代码：

```
class Solution {
public:
    long long appealSum(string &s) {
        long ans = 0L;
        vector<int> last(26, -1);
        for (int i = 0, sum_g = 0; i < s.length(); ++i) {
            char c = s[i] - 'a';
            sum_g += i - last[c]; // sum_g 表示以 s[i] 结尾的子串的引力值之和
            ans += sum_g;
            last[c] = i;
        }
        return ans;
    }
}; // 2022.5.12
```

#### [828. 统计子串中的唯一字符](https://leetcode.cn/problems/count-unique-characters-of-all-substrings-of-a-given-string/)

难度困难

我们定义了一个函数 `countUniqueChars(s)` 来统计字符串 `s` 中的唯一字符，并返回唯一字符的个数。

例如：`s = "LEETCODE"` ，则其中 `"L"`, `"T"`,`"C"`,`"O"`,`"D"` 都是唯一字符，因为它们只出现一次，所以 `countUniqueChars(s) = 5` 。

本题将会给你一个字符串 `s` ，我们需要返回 `countUniqueChars(t)` 的总和，其中 `t` 是 `s` 的子字符串。注意，某些子字符串可能是重复的，但你统计时也必须算上这些重复的子字符串（也就是说，你必须统计 `s` 的所有子字符串中的唯一字符）。

由于答案可能非常大，请将结果 **mod 10 ^ 9 + 7** 后再返回。

 

**示例 1：**

```
输入: s = "ABC"
输出: 10
解释: 所有可能的子串为："A","B","C","AB","BC" 和 "ABC"。
     其中，每一个子串都由独特字符构成。
     所以其长度总和为：1 + 1 + 1 + 2 + 2 + 3 = 10
```

**示例 2：**

```
输入: s = "ABA"
输出: 8
解释: 除了 countUniqueChars("ABA") = 1 之外，其余与示例 1 相同。
```

**示例 3：**

```
输入：s = "LEETCODE"
输出：92
```

 

**提示：**

- `0 <= s.length <= 10^5`
- `s` 只包含大写英文字符

方法：【DP】

思路：

​		以 s[i] 结尾的子串，可以看成是以 s[i - 1] 结尾的子串，在末尾添加上 s[i] 组成。

分类讨论：

1. 若字符 s[i] 之前没出现过：则以 s[i] 字符结尾的子串的唯一字符个数都会增加 1，总共增加 i + 1，即：为 s[i] 单独组成的子串的唯一字符（子串 [0, i] 的长度）。
2. 若字符 s[i] 之前出现过，又可以分为两种情况：第一种：字符 s[i] 此前出现过一次；第二种：出现过大于等于两次。

第一种情况：

若字符 s[i] 此前出现过一次（当前为第二次出现），设其上次出现的下标为 $$last_1$$，将字符串 $$s[0, i - 1]$$ 以 $$last_1$$为分割点 分割为两部分。

- 向子串 $$s[0..i-1],\ s[1..i-1],\ s[2..i-1],\cdots,s[last_1..i-1]$$ 的末尾添加 s[i] 后， 此时字符 s[i] 为**第二次出现**，那么这些子串的唯一字符个数都应该**减一**，一共减去 $$last_1 + 1$$ 个；
- 而子串 $$s[last_1+1..i-1],\ s[last_1+2..i-1],\cdots,s[i-1..i-1]$$ 由于不包含字符 s[i]，这些子串的唯一字符个数都会增加 1，因此有 $$i - last_1 - 1$$ 个子串的唯一字符个数会增加 1，这些子串的唯一字符个数之和会增加 $$i-last_1 -1$$，再加上 1，即 为s[i] 单独组成的子串的唯一字符个数，值为  $$i - last_1$$。

第二种情况：

若字符 s[i] 此前出现过两次（当前为第 n 次出现，n > 2），设其上次出现的下标为 $$last_1$$，上上次（倒数第二次）出现的下标为 $$last_2$$。将字符串 $$s[0, i - 1]$$ 以 $$last_1$$，$$last_2$$为分割点分割为三部分，即：$$[0, last_2]$$， $$[last_2 + 1, last_1]$$，$$[last_1 + 1, i - 1]$$。

​		前两部分：$$[0, last_2]$$， $$[last_2 + 1, last_1]$$ 在遇到 $$s[last_1]$$ 字符的时候已经处理过了，目前只需要处理 $$[last_2 + 1, last_1]$$，$$[last_1 + 1, i - 1]$$ 部分。

​		对于第二个部分，可以转化为第一种情况，只需要做一下小的修改。区间为$$[last_2, i - 1]$$

- 向子串 $$s[last_2 + 1..last_1-1],\ s[last_2 + 2..last_1-1],\ s[last_2 + 3..last_1-1],\cdots,s[last_1 - 1..last_1-1]$$ 的末尾添加 $$s[last_1]$$ 后， 此时字符 $$s[last_1]$$ 为**第二次出现**，那么这些子串的唯一字符个数都应该**减一**，一共减去 $$last_1 - last_2$$ 个；
- 而子串 $$s[last_1+1..i-1],\ s[last_1+2..i-1],\cdots,s[i-1..i-1]$$ 由于不包含字符 s[i]，这些子串的唯一字符个数都会增加 1，因此有 $$i - last_1 - 1$$ 个子串的唯一字符个数会增加 1，这些子串的唯一字符个数之和会增加 $$i-last_1 -1$$，再加上 1，即 为s[i] 单独组成的子串的唯一字符个数，值为  $$i - last_1$$。

代码实现过程：

1. 用数组 pos 记录每个字符出现的所有下标，为了简化代码，初始时每个字符的下标都为 -1，每次遍历到字符 s[i]，都会在所有操作完成后将 i 存入 pos 数组中。
2. 用 $$sum_cnt $$ 记录以字符 s[i] 结尾的子串的唯一字符个数总和。
3. 用 $$last_1$$ 记录字符 s[i] 上次出现的下标，如果中 s[i] 字符上次出现的下标 不为 -1，则用 $$last_2$$ 记录字符 s[i] 上上次出现的下标（倒数第二次）。然后进行分类修改 sum_cnt，并计算最终结果。

C++版本代码：

```c++
class Solution {
private:
    const int MOD = 1e9 + 7;
public:
    int uniqueLetterString(string s) {
        int n = s.size();
        vector<vector<int>> pos(26, vector<int>(1, -1)); // 记录字符每次出现的位置下标
        int ans = 0;
        // sum_cnt 表示：以字符 s[i] 结尾的子串的唯一字符个数总和为 sum_cnt
        for (int i = 0, sum_cnt = 0; i < s.size(); ++i) {
            int idx = s[i] - 'A';
            int last1 = pos[idx].back(); // i 之前倒数第一个 s[i] 字符的下标
            if (last1 == -1) { // s[i] 之前没有出现过
                sum_cnt = (sum_cnt % MOD) + i - last1;
            } else { // s[i] 之前出现过
                int last2 = pos[idx][pos[idx].size() - 2]; // i 之前倒数第二个 s[i] 字符的下标
                sum_cnt -= (last1 - last2);
                sum_cnt = (sum_cnt % MOD) + i - last1;
            }
            ans = ans % MOD + sum_cnt % MOD;
            pos[idx].push_back(i);
        }
        return ans;
    }
}; // 2022.5.13
```

C++版本代码：精简

```c++
class Solution {
private:
    const int MOD = 1e9 + 7;
public:
    int uniqueLetterString(string s) {
        int n = s.size();
        vector<vector<int>> pos(26, vector<int>(1, -1)); // 记录字符每次出现的位置下标
        int ans = 0;
        // sum_cnt 表示：以字符 s[i] 结尾的子串的唯一字符个数总和为 sum_cnt
        for (int i = 0, sum_cnt = 0; i < s.size(); ++i) {
            int idx = s[i] - 'A';
            int last1 = pos[idx].back(); // i 之前倒数第一个 s[i] 字符的下标
            sum_cnt = (sum_cnt % MOD) + i - last1;

            if (pos[idx].size() >= 2) {
                int last2 = pos[idx][pos[idx].size() - 2]; // i 之前倒数第二个 s[i] 字符的下标
                sum_cnt -= (last1 - last2);
            }
            ans = ans % MOD + sum_cnt % MOD;
            pos[idx].push_back(i);
        }
        return ans;
    }
}; // 2022.5.13
```

C++版代码：（Debug 过程）

```c++
/*
子串 "ABCDEFGHC" 的唯一字符个数总和为 39, 再加上一个 C，子串 "ABCDEFGHCC" 唯一字符个数总和为 34
ABCDEFGHC 长度为9
ABCDEFGHC   7   加上 C 后   7
BCDEFGHC    6   加上 C 后   6
CDEFGHC     5   加上 C 后   5
倒数第二个字符 C 的下标 +1 后，依次减一
DEFGHC      6   加上 C 后   5
EFGHC       5   加上 C 后   4
FGHC        4   加上 C 后   3
GHC         3   加上 C 后   2
HC          2   加上 C 后   1
C           1   加上 C 后   0
                加上 C 后   1
*/
```

