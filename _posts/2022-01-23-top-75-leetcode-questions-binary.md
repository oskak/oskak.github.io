---
title: Top 75 LeetCode Questions - Binary
tags: [Algorithm]
---

## Q1 两整数之和
[leetcode:medium](https://leetcode-cn.com/problems/sum-of-two-integers/)

题目：给你两个整数 `a` 和 `b`，不使用运算符 `+` 和 `-`，计算并返回两整数之和。

示例：
```
输入：a = 1, b = 2
输出：3
```

```
class Solution:
    def getSum(self, a: int, b: int) -> int:
        return sum([a, b])
```

## Q2 位1的个数
[leetcode:easy](https://leetcode-cn.com/problems/number-of-1-bits/)

题目：编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 `'1'` 的个数（也被称为汉明重量）。

示例：
```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```

```
class Solution:
    def hammingWeight(self, n: int) -> int:
        return bin(n).count("1")
```

## Q3 比特位计数
[leetcode:easy](https://leetcode-cn.com/problems/counting-bits/)

题目：给你一个整数 `n`，对于 `0 <= i <= n` 中的每个 `i`，计算其二进制表示中 `1` 的个数，返回一个长度为 `n + 1` 的数组 `ans` 作为答案。

示例：
```
输入：n = 2
输出：[0,1,1]
解释：
0 --> 0
1 --> 1
2 --> 10
```

```
class Solution:
    def countBits(self, n: int) -> List[int]:
        ans = []
        for i in range(0,n+1):
            ans.append(bin(i).count("1"))
        return ans
```

## Q4 丢失的数字
[leetcode:easy](https://leetcode-cn.com/problems/missing-number/)

题目：给定一个包含 `[0, n]` 中 `n` 个数的数组 `nums`，找出 `[0, n]` 这个范围内没有出现在数组中的那个数。

示例：
```
输入：nums = [3,0,1]
输出：2
解释：n = 3，因为有 3 个数字，所以所有的数字都在范围 [0,3] 内。2 是丢失的数字，因为它没有出现在 nums 中。
```

方法一：排序。将数组排序之后，即可根据数组中每个下标处的元素是否和下标相等，得到丢失的数字。
```
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        nums.sort()
        for i, num in enumerate(nums):
            if num != i:
                return i
        return len(nums)
```

方法二：使用哈希集合，可以将时间复杂度降低到 `O(n)`。
```
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        s = set(nums)
        for i in range(len(nums) + 1):
            if i not in s:
                return i
```

## Q5 颠倒二进制位
[leetcode:easy](https://leetcode-cn.com/problems/reverse-bits/)

题目：颠倒给定的32位无符号整数的二进制位。

示例：
```
输入：n = 00000010100101000001111010011100
输出：964176192 (00111001011110000010100101000000)
解释：输入的二进制串 00000010100101000001111010011100 表示无符号整数 43261596，
     因此返回 964176192，其二进制表示形式为 00111001011110000010100101000000。
```

```
class Solution:
    def reverseBits(self, n: int) -> int:
        return int("{0:032b}".format(n)[::-1], 2)
```

## References
- [New Year Gift - Curated List of Top 75 LeetCode Questions to Save Your Time](https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU)
