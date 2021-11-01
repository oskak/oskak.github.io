---
title: 面试之 Hash Table 篇（二）
tags: [Algorithm]
---

## Q1 四数相加 II
[leetcode:medium](https://leetcode-cn.com/problems/4sum-ii/)

题目：给你四个整数数组 `nums1`、`nums2`、`nums3` 和 `nums4`，数组长度都是 `n`，请你计算有多少个元组 `(i, j, k, l)` 能满足：
- `0 <= i, j, k, l < n`
- `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`

笔记：

时间复杂度：O(n^2)

空间复杂度：O(n^2)

```
class Solution:
    def fourSumCount(self, A: List[int], B: List[int], C: List[int], D: List[int]) -> int:
        countAB = collections.Counter(u + v for u in A for v in B)
        ans = 0
        for u in C:
            for v in D:
                if -u - v in countAB:
                    ans += countAB[-u - v]
        return ans
```

```
class Solution(object):
    def fourSumCount(self, nums1, nums2, nums3, nums4):
        # use a dict to store the elements in nums1 and nums2 and their sum
        hashmap = dict()
        for n1 in nums1:
            for n2 in nums2:
                if n1 + n2 in hashmap:
                    hashmap[n1+n2] += 1
                else:
                    hashmap[n1+n2] = 1
        
        # if the -(a+b) exists in nums3 and nums4, we shall add the count
        count = 0
        for n3 in nums3:
            for n4 in nums4:
                key = - n3 - n4
                if key in hashmap:
                    count += hashmap[key]
        return count
```

## Q2 赎金信
[leetcode:easy](https://leetcode-cn.com/problems/ransom-note/)

题目：给定一个赎金信 `ransom` 字符串和一个杂志 `magazine` 字符串，判断第一个字符串 `ransom` 能不能由第二个字符串 `magazine` 里面的字符构成。如果可以构成，返回 `true`；否则返回 `false`。

说明：为了不暴露赎金信字迹，要从杂志上搜索各个需要的字母，组成单词来表达意思。杂志字符串中的每个字符只能在赎金信字符串中使用一次。

笔记：使用数组作为哈希表。

```
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:

        arr = [0] * 26

        for x in magazine:
            arr[ord(x) - ord('a')] += 1

        for x in ransomNote:
            if arr[ord(x) - ord('a')] == 0:
                return False
            else:
                arr[ord(x) - ord('a')] -= 1
        
        return True
```

## Q3 三数之和
[leetcode:medium](https://leetcode-cn.com/problems/3sum/)

题目：给你一个包含 `n` 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 `a`，`b`，`c`，使得 `a + b + c = 0`？请你找出所有和为 `0` 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

笔记：其实这道题目使用哈希法并不十分合适，因为在去重的操作中有很多细节需要注意，在面试中很难直接写出没有 bug 的代码。而且使用哈希法，在使用两层 for 循环的时候，能做的剪枝操作很有限，虽然时间复杂度是 `O(n^2)`，也是可以在 leetcode 上通过，但是程序的执行时间依然比较长。

用双指针法（排序 + 双指针）。时间复杂度是 `O(n)` 因为**一共会移动的位置数**为 `O(n)`。

```
class Solution:
    def threeSum(self, nums: [int]) -> [[int]]:
        nums.sort()
        res, k = [], 0
        for k in range(len(nums) - 2):
            if nums[k] > 0: 
                break # because j > i > k
            if k > 0 and nums[k] == nums[k - 1]: 
                continue # skip the same `nums[k]`
            i, j = k + 1, len(nums) - 1

            # double pointer
            while i < j: 
                s = nums[k] + nums[i] + nums[j]
                if s < 0:
                    i += 1
                    while i < j and nums[i] == nums[i - 1]: 
                        i += 1
                elif s > 0:
                    j -= 1
                    while i < j and nums[j] == nums[j + 1]:
                         j -= 1
                else:
                    res.append([nums[k], nums[i], nums[j]])
                    i += 1
                    j -= 1
                    while i < j and nums[i] == nums[i - 1]: 
                        i += 1
                    while i < j and nums[j] == nums[j + 1]: 
                        j -= 1
        return res
```

## Q4 四数之和
[leetcode:medium](https://leetcode-cn.com/problems/4sum/)

题目：给你一个由 `n` 个整数组成的数组 `nums`，和一个目标值 `target`。请你找出并返回满足下述全部条件且不重复的四元组 `[nums[a], nums[b], nums[c], nums[d]]`：
- `0 <= a, b, c, d < n`
- `a`、`b`、`c` 和 `d` 互不相同
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

你可以按任意顺序返回答案。

笔记：“四数之和”和“三数之和”是一个思路，都是使用双指针法，基本解法就是在“三数之和”的基础上再套一层 `for` 循环。

时间复杂度：O(n^3)

空间复杂度：O(log(n))

```
class Solution:
    def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
        quadruplets = list()
        if not nums or len(nums) < 4:
            return quadruplets
        
        nums.sort()
        length = len(nums)
        for i in range(length - 3):
            if i > 0 and nums[i] == nums[i - 1]:
                continue
            if nums[i] + nums[i + 1] + nums[i + 2] + nums[i + 3] > target:
                break
            if nums[i] + nums[length - 3] + nums[length - 2] + nums[length - 1] < target:
                continue
            for j in range(i + 1, length - 2):
                if j > i + 1 and nums[j] == nums[j - 1]:
                    continue
                if nums[i] + nums[j] + nums[j + 1] + nums[j + 2] > target:
                    break
                if nums[i] + nums[j] + nums[length - 2] + nums[length - 1] < target:
                    continue
                left, right = j + 1, length - 1
                while left < right:
                    total = nums[i] + nums[j] + nums[left] + nums[right]
                    if total == target:
                        quadruplets.append([nums[i], nums[j], nums[left], nums[right]])
                        while left < right and nums[left] == nums[left + 1]:
                            left += 1
                        left += 1
                        while left < right and nums[right] == nums[right - 1]:
                            right -= 1
                        right -= 1
                    elif total < target:
                        left += 1
                    else:
                        right -= 1
        
        return quadruplets
```

## References
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
