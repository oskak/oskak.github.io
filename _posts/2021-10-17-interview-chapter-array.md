---
title: 面试之数组篇
tags: [Algorithm]
---

## 数组基础
数组是存放在连续内存空间上的相同类型数据的集合。正是因为数组在内存空间的地址是连续的，所以我们在删除或者增添元素的时候，就难免要移动其他元素的地址。数组的元素是不能删的，只能覆盖。

## Q1 二分查找
[leetcode:easy](https://leetcode-cn.com/problems/binary-search/)

题目：给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target`，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 `-1`。

笔记：区间的定义就是不变量，那么在循环中坚持根据查找区间的定义来做边界处理，就是循环不变量规则。The time complexity of the binary search algorithm is O(log n).

```
# 左闭右开区间
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left, right  = 0, len(nums)
        while left < right:
            mid = (left + right) // 2
            if nums[mid] < target:
                left = mid + 1
            elif nums[mid] > target:
                right = mid
            else:
                return mid
        return -1
```

## Q2 移除元素
[leetcode:easy](https://leetcode-cn.com/problems/remove-element/)

题目：给你一个数组 `nums` 和一个值 `val`，你需要**原地**移除所有数值等于 `val` 的元素，并返回移除后数组的新长度。不要使用额外的数组空间，你必须仅使用 `O(1)` 额外空间并**原地**修改输入数组。元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

笔记：当 `fast` 指针遇到要删除的元素时停止赋值，此时 `slow` 指针暂停移动。

```
class Solution:
    """双指针法
    时间复杂度：O(n)
    空间复杂度：O(1)
    """

    @classmethod
    def removeElement(cls, nums: List[int], val: int) -> int:
        fast = slow = 0

        while fast < len(nums):
            if nums[fast] != val:
                nums[slow] = nums[fast]
                slow += 1
            fast += 1

        return slow
```

## Q3 有序数组的平方
[leetcode:easy](https://leetcode-cn.com/problems/squares-of-a-sorted-array/)

题目：给你一个按**非递减顺序**排序的整数数组 `nums`，返回**每个数字的平方**组成的新数组，要求也按**非递减顺序**排序。

笔记：数组其实是有序的，只不过负数平方之后可能成为最大数了。那么数组平方的最大值就在数组的两端，不是最左边就是最右边，不可能是中间。此时可以考虑双指针法了，`i` 指向起始位置，`j` 指向终止位置。定义一个新数组 `ans`，和 `nums` 数组一样的大小，让 `k` 指向 `ans` 数组终止位置。此时的时间复杂度为 `O(n)`。

```
class Solution:
    def sortedSquares(self, nums: List[int]) -> List[int]:
        n = len(nums)
        i, j, k = 0, n - 1, n - 1
        ans = [-1] * n
        while i <= j:
            lm = nums[i] ** 2
            rm = nums[j] ** 2
            if lm > rm:
                ans[k] = lm
                i += 1
            else:
                ans[k] = rm
                j -= 1
            k -= 1
        return ans
```

## Q4 长度最小的子数组
[leetcode:medium](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)

题目：给定一个含有 `n` 个正整数的数组和一个正整数 `target`。找出该数组中满足其和 `≥ target` 的长度最小的**连续子数组**，并返回其长度。如果不存在符合条件的子数组，返回 `0`。

笔记：滑动窗口的精妙之处在于根据当前子序列和大小的情况，不断调节子序列的起始位置。不要以为 `for` 里放一个 `while` 就是 `O(n^2)`，主要是看每一个元素被操作的次数，每个元素在滑动窗进来操作一次，出去操作一次，每个元素都是被操作两次，所以时间复杂度是 `2 * n` 也就是 `O(n)`。空间复杂度是 `O(1)`。

```
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        res = float("inf")
        sum = 0
        index = 0
        for i in range(len(nums)):
            sum += nums[i]
            while sum >= target:
                res = min(res, i - index + 1)
                sum -= nums[index]
                index += 1
        return 0 if res == float("inf") else res
```

## Q5 螺旋矩阵II
[leetcode:medium](https://leetcode-cn.com/problems/spiral-matrix-ii/)

题目：给你一个正整数 `n`，生成一个包含 `1` 到 `n^2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix`。

笔记：求解本题依然是要坚持循环不变量原则。模拟顺时针画矩阵的过程：填充上行从左到右，填充右列从上到下，填充下行从右到左，填充左列从下到上。由外向内一圈一圈这么画下去。这里一圈下来，我们要画四条边，每画一条边都要坚持一致的左闭右开（拐角处让给新的一条边来继续画）的原则，这样这一圈才能按照统一的规则画下来。

```
class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        # 初始化要填充的正方形
        matrix = [[0] * n for _ in range(n)]

        left, right, up, down = 0, n - 1, 0, n - 1
        number = 1  # 要填充的数字

        while left < right and up < down:

            # 从左到右填充上边
            for x in range(left, right):
                matrix[up][x] = number
                number += 1

            # 从上到下填充右边
            for y in range(up, down):
                matrix[y][right] = number
                number += 1

            # 从右到左填充下边
            for x in range(right, left, -1):
                matrix[down][x] = number
                number += 1

            # 从下到上填充左边
            for y in range(down, up, -1):
                matrix[y][left] = number
                number += 1

            # 缩小要填充的范围
            left += 1
            right -= 1
            up += 1
            down -= 1

        # 如果阶数为奇数，额外填充一次中心
        if n % 2:
            matrix[n // 2][n // 2] = number

        return matrix
```

## References
- [LeetCode 刷题攻略](https://github.com/youngyangyang04/leetcode-master)
