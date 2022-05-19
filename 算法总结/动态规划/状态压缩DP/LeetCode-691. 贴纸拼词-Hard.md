#### [691. 贴纸拼词](https://leetcode.cn/problems/stickers-to-spell-word/)

难度困难221收藏分享切换为英文接收动态反馈

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
- `1 <= target.length <= 15`
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

自己写了一下：

```c++
class Solution {
private:
    const int INF = 51;
public:
    int minStickers(vector<string>& stickers, string target) {
        int m = target.size();
        vector<int> dp(1 << m, -1);
        dp[0] = 0;
        function<int(int)> helper = [&] (int state) { // 状态 state 为 int 型的二进制数字序列（只包含 0 和 1）
            if (dp[state] != -1) return dp[state]; // 状态 state 已经搜索过，直接返回
            dp[state] = INF; // 设置到达状态 state 的步数为最大值 INF

            for (auto & sticker : stickers) { // 遍历每个 stickers[i]
                int leftState = state;
                vector<int> charCnt(26, 0); // 统计每个 stickers[i] 中各个字符的个数
                for (char & c : sticker) {
                    charCnt[c - 'a']++;
                }

                for (int i = 0; i < m; i++) { // 遍历状态 state 的每位数字，总共 m 位，m 为字符串 target 的长度
                    if ((state >> i & 1) && charCnt[target[i] - 'a'] > 0) {
                        charCnt[target[i] - 'a']--;
                        leftState ^= 1 << i;
                    }
                }
                if (leftState < state) { // state 状态是否发生改变
                    dp[state] = min(dp[state], helper(leftState) + 1); // 更新状态 state
                }
            }
            return dp[state];
        };

        int res = helper((1 << m) - 1);
        return res > m ? -1 : res;
    }
}; // 2022.5.14
```

