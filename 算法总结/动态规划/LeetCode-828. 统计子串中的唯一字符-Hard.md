#### [828. 统计子串中的唯一字符](https://leetcode.cn/problems/count-unique-characters-of-all-substrings-of-a-given-string/)

难度困难114收藏分享切换为英文接收动态反馈

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

