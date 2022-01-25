---
title: Top 75 LeetCode Questions - Array
tags: [Algorithm]
---

## Q1 买卖股票的最佳时机
[leetcode:easy](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

题目：给定一个数组 `prices`，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。你只能选择某一天买入这只股票，并选择在未来的某一个不同的日子卖出该股票。设计一个算法来计算你所能获取的最大利润。返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0`。

笔记：显然，如果我们真的在买卖股票，我们肯定会想：如果我是在历史最低点买的股票就好了。我们只要用一个变量记录一个历史最低价格 `minprice`，我们就可以假设自己的股票是在那天买的。那么我们在第 `i` 天卖出股票能得到的利润就是 `prices[i] - minprice`。因此，我们只需要遍历价格数组一遍，记录历史最低点，然后在每一天考虑这么一个问题：如果我是在历史最低点买进的，那么我今天卖出能赚多少钱？当考虑完所有天数之时，我们就得到了最好的答案。

时间复杂度：O(n)，只需要遍历一次。

空间复杂度：O(1)，只使用了常数个变量。

```
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        inf = int(1e9)
        minprice = inf
        maxprofit = 0
        for price in prices:
            maxprofit = max(price - minprice, maxprofit)
            minprice = min(price, minprice)
        return maxprofit
```

## Q2 存在重复元素
[leetcode:easy](https://leetcode-cn.com/problems/contains-duplicate/)

题目：给你一个整数数组 `nums`。如果任一值在数组中出现至少两次，返回 `true`；如果数组中每个元素互不相同，返回 `false`。

笔记：对于数组中每个元素，我们将它插入到哈希表中。如果插入一个元素时发现该元素已经存在于哈希表中，则说明存在重复的元素。

时间复杂度：O(N)，其中N为数组的长度。

空间复杂度：O(N)，其中N为数组的长度。

```
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        numSet = set()
        for i in nums:
            if i in numSet:
                return True
            else:
                numSet.add(i)
        return False
```

```
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        return len(set(nums)) != len(nums)
```

## Q3 除自身以外数组的乘积
[leetcode:medium](https://leetcode-cn.com/problems/product-of-array-except-self/)

题目：给你一个长度为 `n` 的整数数组 `nums`，其中 `n > 1`，返回输出数组 `output`，其中 `output[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积。

示例：
```
输入: [1,2,3,4]
输出: [24,12,8,6]
```

笔记：当前位置的结果就是它左部分的乘积再乘以它右部分的乘积。因此需要进行两次遍历，第一次遍历用于求左部分的乘积，第二次遍历在求右部分的乘积的同时，再将最后的计算结果一起求出来。
```
原数组：       [1       2       3       4]
左部分的乘积：   1       1      1*2    1*2*3
右部分的乘积： 2*3*4    3*4      4      1
结果：        1*2*3*4  1*3*4   1*2*4  1*2*3*1
```

```
class Solution:
    def productExceptSelf(self, nums: [int]) -> [int]:
        res, p, q = [1], 1, 1
        for i in range(len(nums) - 1): # bottom triangle
            p *= nums[i]
            res.append(p)
        for i in range(len(nums) - 1, 0, -1): # top triangle
            q *= nums[i]
            res[i - 1] *= q
        return res
```

## Q4 乘积最大子数组
[leetcode:medium](https://leetcode-cn.com/problems/maximum-product-subarray/)

题目：给你一个整数数组 `nums`，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

示例：
```
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。

输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是连续子数组。
```

笔记：贪心算法。

```
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        currMaxProduct = nums[0]
        currMinProduct = nums[0]
        maxProduct = nums[0]
        
        for i in range(1, len(nums)):
            prevMinProduct = currMinProduct
            prevMaxProduct = currMaxProduct
            
            currMinProduct = min(nums[i] * prevMaxProduct, nums[i], nums[i] * prevMinProduct)
            currMaxProduct = max(nums[i] * prevMaxProduct, nums[i], nums[i] * prevMinProduct)
            maxProduct = max(maxProduct, currMaxProduct)
            
        return maxProduct
```

## Q5 寻找旋转排序数组中的最小值
[leetcode:medium](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

题目：已知一个长度为 `n` 的数组，预先按照升序排列，经由 `1` 到 `n` 次旋转后，得到输入数组。例如，原数组 `nums = [0,1,2,4,5,6,7]` 在变化后可能得到：
- 若旋转4次，则可以得到 `[4,5,6,7,0,1,2]`
- 若旋转7次，则可以得到 `[0,1,2,4,5,6,7]`

注意，数组 `[a[0], a[1], a[2], ..., a[n-1]]` 旋转一次的结果为数组 `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]`。

给你一个元素值互不相同的数组 `nums`，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的最小元素。

示例：
```
输入：nums = [3,4,5,1,2]
输出：1
解释：原数组为 [1,2,3,4,5] ，旋转 3 次得到输入数组。

输入：nums = [4,5,6,7,0,1,2]
输出：0
解释：原数组为 [0,1,2,4,5,6,7] ，旋转 4 次得到输入数组。

输入：nums = [11,13,15,17]
输出：11
解释：原数组为 [11,13,15,17] ，旋转 4 次得到输入数组。
```

笔记：二分查找。

时间复杂度：`O(logn)`，其中 `n` 是数组 `nums` 的长度。在二分查找的过程中，每一步会忽略一半的区间。

空间复杂度：`O(1)`。

```
class Solution:
    def findMin(self, nums: List[int]) -> int:    
        low, high = 0, len(nums) - 1
        while low < high:
            pivot = low + (high - low) // 2
            if nums[pivot] < nums[high]:
                high = pivot 
            else:
                low = pivot + 1
        return nums[low]
```

## Q6 搜索旋转排序数组
[leetcode:medium](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

题目：整数数组 `nums` 按升序排列，数组中的值互不相同。在传递给函数之前，`nums` 在预先未知的某个下标 `k`（`0 <= k < nums.length`）上进行了旋转，使数组变为 `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]`（下标从 `0` 开始计数）。例如，`[0,1,2,4,5,6,7]` 在下标 `3` 处经旋转后可能变为 `[4,5,6,7,0,1,2]`。给你旋转后的数组 `nums` 和一个整数 `target`，如果 `nums` 中存在这个目标值 `target`，则返回它的下标，否则返回 `-1`。

示例：
```
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4

输入：nums = [4,5,6,7,0,1,2], target = 3
输出：-1

输入：nums = [1], target = 0
输出：-1
```

笔记：二分查找。

```
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        if not nums:
            return -1
        l, r = 0, len(nums) - 1
        while l <= r:
            mid = (l + r) // 2
            if nums[mid] == target:
                return mid
            if nums[0] <= nums[mid]:
                if nums[0] <= target < nums[mid]:
                    r = mid - 1
                else:
                    l = mid + 1
            else:
                if nums[mid] < target <= nums[len(nums) - 1]:
                    l = mid + 1
                else:
                    r = mid - 1
        return -1
```

## Q7 盛最多水的容器
[leetcode:medium](https://leetcode-cn.com/problems/container-with-most-water/)

题目：给你 `n` 个非负整数 `a1，a2，...，an`，每个数代表坐标中的一个点 `(i, ai)`。在坐标内画 `n` 条垂直线，垂直线 `i` 的两个端点分别为 `(i, ai)` 和 `(i, 0)`。找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。说明：你不能倾斜容器。

示例：
```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水的最大值为 49。
```

笔记：`容纳的水量 = 两个指针指向的数字中较小值 * 指针之间的距离`。

```
class Solution:
    def maxArea(self, height: List[int]) -> int:
        l, r = 0, len(height) - 1
        ans = 0
        while l < r:
            area = min(height[l], height[r]) * (r - l)
            ans = max(ans, area)
            if height[l] <= height[r]:
                l += 1
            else:
                r -= 1
        return ans
```

## References
- [New Year Gift - Curated List of Top 75 LeetCode Questions to Save Your Time](https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU)
