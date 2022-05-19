#### [462. 最少移动次数使数组元素相等 II](https://leetcode.cn/problems/minimum-moves-to-equal-array-elements-ii/)

难度中等204收藏分享切换为英文接收动态反馈

给你一个长度为 `n` 的整数数组 `nums` ，返回使所有数组元素相等需要的最少移动数。

在一步操作中，你可以使数组中的一个元素加 `1` 或者减 `1` 。

 

**示例 1：**

```
输入：nums = [1,2,3]
输出：2
解释：
只需要两步操作（每步操作指南使一个元素加 1 或减 1）：
[1,2,3]  =>  [2,2,3]  =>  [2,2,2]
```

**示例 2：**

```
输入：nums = [1,10,2,9]
输出：16
```

 

**提示：**

- `n == nums.length`
- `1 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`

方法一：【前缀和数组】

思路：

​		用 prefix 记录 nums 的前缀和数组。令 n 为 nums 的长度。

​		遍历所有元素，如果使所有数组元素相等且为当前元素 nums[i] 时，计算需要移动的次数，并更新结果 res。

如何计算需要移动的次数：

​		将 nums 以 i 为分割点，将其分为两部分， $$[0, i - 1]$$ 与 $$[i, n]$$ ，前一个区间的所有元素执行 +1 操作变为 nums[i] 所需的次数为 left；后一个区间的所有元素执行 -1 操作变为 nums[i] 所需的次数为 right。总移动次数为 left + right

C++版代码：

```c++
class Solution {
private:
    const long INF = 1e18;
public:
    int minMoves2(vector<int>& nums) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        vector<long> prefix(n + 1, 0);
        for (int i = 1; i <= n; i++) {
            prefix[i] = prefix[i - 1] + nums[i - 1];
        }
        long res = INF;
        for (int i = 1; i <= n; i++) {
            long left = (long)nums[i - 1] * (i - 1) - prefix[i - 1];
            long right = prefix[n] - prefix[i] - (long)nums[i - 1] * (n - i);
            res = min(res, left + right);
        }
        return (int)res;
    }
}; // 2022.5.19
```

