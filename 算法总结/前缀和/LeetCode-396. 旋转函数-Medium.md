#### [396. 旋转函数](https://leetcode.cn/problems/rotate-function/)

难度中等221收藏分享切换为英文接收动态反馈

给定一个长度为 `n` 的整数数组 `nums` 。

假设 `arrk` 是数组 `nums` 顺时针旋转 `k` 个位置后的数组，我们定义 `nums` 的 **旋转函数** `F` 为：

- `F(k) = 0 * arrk[0] + 1 * arrk[1] + ... + (n - 1) * arrk[n - 1]`

返回 *`F(0), F(1), ..., F(n-1)`中的最大值* 。

生成的测试用例让答案符合 **32 位** 整数。

 

**示例 1:**

```
输入: nums = [4,3,2,6]
输出: 26
解释:
F(0) = (0 * 4) + (1 * 3) + (2 * 2) + (3 * 6) = 0 + 3 + 4 + 18 = 25
F(1) = (0 * 6) + (1 * 4) + (2 * 3) + (3 * 2) = 0 + 4 + 6 + 6 = 16
F(2) = (0 * 2) + (1 * 6) + (2 * 4) + (3 * 3) = 0 + 6 + 8 + 9 = 23
F(3) = (0 * 3) + (1 * 2) + (2 * 6) + (3 * 4) = 0 + 2 + 12 + 12 = 26
所以 F(0), F(1), F(2), F(3) 中的最大值是 F(3) = 26 。
```

**示例 2:**

```
输入: nums = [100]
输出: 0
```

 

**提示:**

- `n == nums.length`
- `1 <= n <= 105`
- `-100 <= nums[i] <= 100`

**方法一：前缀和+滑动窗口**

思路：

题目要对「旋转数组」做逻辑，容易想到将 nums 进行复制拼接，得到长度为 2 * n 的新数组，在新数组上任意一个长度为 n 的滑动窗口都对应了一个旋转数组。

然后考虑在窗口的滑动过程中，计算结果会如何变化，假设当前我们处理到下标为 `[i, i + n - 1]` 的滑动窗口，根据题意，当前结果为：

`cur = nums[i] * 0 + nums[i + 1] * 1 + ... + nums[i + n - 1] * (n - 1)`

当窗口往后移动一位，也就是窗口的右端点来到 i + n 的位置，左端点来到 i + 1 的位置。

我们需要增加「新右端点」的值，即增加 `nums[i + n] * (n - 1)`，同时减去「旧左端点」的值，即减少 `nums[i] * 0`（固定为 0），然后更新新旧窗口的公共部分 `[i + 1, i + n - 1]`。

不难发现，随着窗口的逐步右移，每一位公共部分的权值系数都会进行减一。

`nums[i + 1] * 1 + nums[i + 2] * 2 + ... + nums[i + n - 1] * (n - 1)`

变为

`nums[i + 1] * 0 + nums[i + 2] * 1 + ... + nums[i + n - 1] * (n - 2)`

因此，公共部分的差值为 
$$
\sum_{idx = i + 1}^{i + n - 1}nums[idx]
$$
，这引导我们可以使用前缀和进行优化。

至此，我们从旧窗口到新窗口的过渡，都是 O(1)，整体复杂度为 O(n)。

实现上，我们并不需要真正对 nums 进行复制拼接，而只需要在计算前缀和数组 sum 进行简单的下标处理即可。

- 时间复杂度：O(n)
- 空间复杂度：O(n)

C++版代码：

```c++
class Solution {
public:
    int maxRotateFunction(vector<int>& nums) {
        int n = nums.size();
        vector<int> prefix(2 * n + 1, 0); // 增加一个前缀和为 0 的元素（或者理解为不包含本元素的前缀和）
        for (int i = 1; i < prefix.size(); i++) {
            prefix[i] = prefix[i - 1] + nums[(i - 1) % n];
        }

        int res = 0;
        for (int i = 0; i < n; i++) {
            res += i * nums[i];
        }

        for (int i = n + 1, tmp = res; i < 2 * n; i++) {
            tmp += (n - 1) * nums[(i - 1) % n]; // 新增加的一个元素 都是要 * (n - 1)
            tmp -= prefix[i - 1] - prefix[i - n]; // 新增加元素的前缀和 - 滑动窗口第一个元素的前缀和
            res = max(res, tmp);
        }
        return res;
    }
};
// 2022.4.22
```

**方法二：迭代**

思路

记数组 `nums` 的元素之和为 `numSum`。根据公式，可以得到：

$$
F(0) = 0 \times \textit{nums}[0] + 1 \times \textit{nums}[1] + \ldots + (n-1) \times \textit{nums}[n-1]
\\F(1) = 1 \times \textit{nums}[0] + 2 \times \textit{nums}[1] + \ldots + 0 \times \textit{nums}[n-1] 
\\= F(0) + \textit{numSum} - n \times \textit{nums}[n-1]
$$
更一般地，当 `1≤ k <n` 时，`F(k) = F(k − 1) + numSum − n × nums[n − k]`。我们可以不停迭代计算出不同的 F(k)，并求出最大值。

C++版代码：

```c++
class Solution {
public:
    int maxRotateFunction(vector<int>& nums) {
        int n = nums.size();
        int numsSum = 0;
        for (auto num : nums) {
            numsSum += num;
        }
        int f = 0;
        for (int i = 0; i < n; i++) {
            f += i * nums[i];
        }
        int res = f;
        for (int i = 1; i < n; i++) {
            f += numsSum - n * nums[n - i];
            res = max(res, f);
        }
        return res;
    }
};
/*
当 1≤ k <n 时，F(k) = F(k − 1) + numSum − n × nums[n − k]
时间复杂度: O(n)
空间复杂度: O(1)
*/ // 2022.4.22
```

