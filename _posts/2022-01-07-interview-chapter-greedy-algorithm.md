---
title: 面试之贪心算法篇
tags: [Algorithm]
---

## 什么是贪心
贪心的本质是选择每一阶段的局部最优，从而达到全局最优。想清楚局部最优，想清楚全局最优，感觉局部最优是可以推出全局最优，而且想不出反例，那么就试一试贪心。

## Q1 分发饼干
[leetcode:easy](https://leetcode-cn.com/problems/assign-cookies/)

题目：假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。对每个孩子 `i`，都有一个胃口值 `g[i]`，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 `j`，都有一个尺寸 `s[j]`。如果 `s[j] >= g[i]`，我们可以将这个饼干 `j` 分配给孩子 `i`，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。

笔记：局部最优就是大饼干喂给胃口大的，充分利用饼干尺寸喂饱一个，全局最优就是喂饱尽可能多的小孩。也可以换一个思路，小饼干先喂饱小胃口。

```
class Solution:
    def findContentChildren(self, g: List[int], s: List[int]) -> int:
        g.sort()
        s.sort()
        res = 0
        for i in range(len(s)):
            if res <len(g) and s[i] >= g[res]:  #小饼干先喂饱小胃口
                res += 1
        return res
```

## Q2 最大子数组和
[leetcode:easy](https://leetcode-cn.com/problems/maximum-subarray/)

题目：给你一个整数数组 `nums`，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。子数组是数组中的一个连续部分。

示例：
```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

笔记：贪心贪的是哪里呢？如果 `-2`，`1` 在一起，计算起点的时候，一定是从1开始计算，因为负数只会拉低总和，这就是贪心贪的地方。局部最优：当前“连续和”为负数的时候立刻放弃，从下一个元素重新计算“连续和”，因为负数加上下一个元素，“连续和”只会越来越小。全局最优：选取最大“连续和”。

```
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        result = -float('inf')
        count = 0
        for i in range(len(nums)):
            count += nums[i]
            if count > result:
                result = count
            if count <= 0:
                count = 0
        return result
```

## Q3 买卖股票的最佳时机 II
[leetcode:medium](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

题目：给定一个数组 `prices`，其中 `prices[i]` 是一支给定股票第 `i` 天的价格。设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

笔记：这道题目可能我们只会想，选一个低的买入，再选个高的卖，再选一个低的买入，循环反复。如果想到其实最终利润是可以分解的，那么本题就很容易了。如何分解呢？假如第0天买入，第3天卖出，那么利润为：`prices[3] - prices[0]`。相当于 `(prices[3] - prices[2]) + (prices[2] - prices[1]) + (prices[1] - prices[0])`。此时就是把利润分解为每天为单位的维度，而不是从0天到第3天整体去考虑。局部最优：收集每天的正利润。全局最优：求得最大利润。

```
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        result = 0
        for i in range(1, len(prices)):
            result += max(prices[i] - prices[i - 1], 0)
        return result
```

## Q4 跳跃游戏
[leetcode:medium](https://leetcode-cn.com/problems/jump-game/)

题目：给定一个非负整数数组 `nums`，你最初位于数组的第一个下标。数组中的每个元素代表你在该位置可以跳跃的最大长度。判断你是否能够到达最后一个下标。

笔记：关键在于可跳的覆盖范围。贪心算法局部最优解：每次取最大跳跃步数（取最大覆盖范围），整体最优解：最后得到整体最大覆盖范围，看是否能到终点。

```
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        cover = 0
        if len(nums) == 1: return True
        i = 0
        # python不支持动态修改for循环中变量,使用while循环代替
        while i <= cover:
            cover = max(i + nums[i], cover)
            if cover >= len(nums) - 1: return True
            i += 1
        return False
```

## References
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
