---
title: 初识动态规划
tags: [Algorithm]
---

## 动态规划
如果某一问题有很多重叠子问题，使用动态规划是最有效的。动态规划中每一个状态一定是由上一个状态推导出来的，这一点就区分于贪心，贪心没有状态推导，而是从局部直接选最优的。DP是枚举有希望成为答案的解，也就是说：DP自带剪枝。

## Q1 斐波那契数
[leetcode:easy](https://leetcode-cn.com/problems/fibonacci-number/)

题目：斐波那契数，通常用 `F(n)` 表示，形成的序列称为**斐波那契数列**。该数列由 `0` 和 `1` 开始，后面的每一项数字都是前面两项数字的和。也就是：
```
F(0) = 0，F(1) = 1
F(n) = F(n - 1) + F(n - 2)，其中 n > 1
```

给你 `n`，请计算 `F(n)`。

示例：
```
输入：2
输出：1
解释：F(2) = F(1) + F(0) = 1 + 0 = 1

输入：3
输出：2
解释：F(3) = F(2) + F(1) = 1 + 1 = 2

输入：4
输出：3
解释：F(4) = F(3) + F(2) = 2 + 1 = 3
```

```
# 递归实现
class Solution:
    def fib(self, n: int) -> int:
        if n < 2:
            return n
        return self.fib(n - 1) + self.fib(n - 2)
```

## Q2 爬楼梯
[leetcode:easy](https://leetcode-cn.com/problems/climbing-stairs/)

题目：假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。每次你可以爬 `1` 或 `2` 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 `n` 是一个正整数。

笔记：爬到第一层楼梯有一种方法，爬到二层楼梯有两种方法。那么第一层楼梯再跨两步就到第三层，第二层楼梯再跨一步就到第三层。所以到第三层楼梯的状态可以由第二层楼梯和第一层楼梯状态推导出来，那么就可以想到动态规划了。对于第 `n` 级台阶来说，有两种方法，一是到第 `n-1` 级，然后爬1个台阶；二是到第 `n-2` 个台阶，然后爬2个台阶，可以得出动态规划递推式：
```
F(n) = F(n-1) + F(n-2)
```

```
class Solution:
    def climbStairs(self, n: int) -> int:
        if n==1:
            return 1
        elif n==2:
            return 2
        else:
            tmp = [1,2]
            for i in range(2,n):
                tmp.append(tmp[i-1] + tmp[i-2])
            return tmp[-1]
```

## Q3 不同路径
[leetcode:medium](https://leetcode-cn.com/problems/unique-paths/)

题目：一个机器人位于一个 `m x n` 网格的左上角。机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角。问总共有多少条不同的路径？

```
class Solution: # 动态规划
    def uniquePaths(self, m: int, n: int) -> int:
        dp = [[1 for i in range(n)] for j in range(m)]
        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = dp[i][j - 1] + dp[i - 1][j]
        return dp[m - 1][n - 1]
```

## References
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
