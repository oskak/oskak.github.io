---
title: 面试之 Hash Table 篇（一）
tags: [Algorithm, Interview]
---

## 哈希表
其实数组就是一张哈希表。哈希表中 Key 就是数组的索引下表，然后通过下表直接访问数组中的元素。一般哈希表都是用来快速判断一个元素是否出现在集合里。哈希法是牺牲空间换取时间，因为我们要使用额外的数组，set 或者是 map 来存放数据，才能实现快速的查找。

## 哈希函数 哈希碰撞
一般哈希碰撞有两种解决方法，链表法和线性探测法。

## Q1 有效的字母异位词
[leetcode:easy](https://leetcode-cn.com/problems/valid-anagram/)

题目：给定两个字符串 `s` 和 `t`，编写一个函数来判断 `t` 是否是 `s` 的字母异位词。

注意：若 `s` 和 `t` 中每个字符出现的次数都相同，则称 `s` 和 `t` 互为字母异位词。

```
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        record = [0] * 26
        for i in range(len(s)):
            #并不需要记住字符a的ASCII，只要求出一个相对数值就可以了
            record[ord(s[i]) - ord("a")] += 1

        for i in range(len(t)):
            record[ord(t[i]) - ord("a")] -= 1
        for i in range(26):
            if record[i] != 0:
                #record数组如果有的元素不为0，说明字符串s和t 一定是谁多了字符或者谁少了字符。
                return False
        return True
```

## Q2 查找共用字符
[leetcode:easy](https://leetcode-cn.com/problems/find-common-characters/)

题目：给你一个字符串数组 `words`，请你找出所有在 `words` 的每个字符串中都出现的共用字符（包括重复字符），并以数组形式返回。你可以按任意顺序返回答案。

笔记：遍历所有字符串并计算 `freq` 的时间复杂度为 `O(nm)`，其中 `n` 是数组的长度（即字符串的数目），`m` 是字符串的平均长度。

```
class Solution:
    def commonChars(self, words: List[str]) -> List[str]:
        minfreq = [float("inf")] * 26
        for word in words:
            freq = [0] * 26
            for ch in word:
                freq[ord(ch) - ord("a")] += 1
            for i in range(26):
                minfreq[i] = min(minfreq[i], freq[i])
        
        ans = list()
        for i in range(26):
            ans.extend([chr(i + ord("a"))] * minfreq[i])
        return ans
```

## Q3 两个数组的交集
[leetcode:easy](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

题目：给定两个数组，编写一个函数来计算它们的交集。

笔记：使用到一种哈希数据结构：`unordered_set`，这个数据结构可以解决很多类似问题。使用数组来做哈希的题目时，一般是因为题目都限制了数值的大小。而这道题目没有限制数值的大小，就无法使用数组来做哈希表了。使用 set 占用空间比数组大，而且速度要比数组慢。数组的大小是受限制的，而且如果元素很少，而哈希值太大，会造成内存空间的浪费。

首先使用两个集合分别存储两个数组中的元素，然后遍历较小的集合，判断其中的每个元素是否在另一个集合中，如果元素也在另一个集合中，则将该元素添加到返回值。该方法的时间复杂度可以降低到 `O(m+n)`。

```
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        return list(set(nums1) & set(nums2))    # 两个数组先变成集合，求交集后还原为数组
```

```
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        set1 = set(nums1)
        set2 = set(nums2)
        return self.set_intersection(set1, set2)

    def set_intersection(self, set1, set2):
        if len(set1) > len(set2):
            return self.set_intersection(set2, set1)
        return [x for x in set1 if x in set2]
```

## Q4 快乐数
[leetcode:easy](https://leetcode-cn.com/problems/happy-number/)

题目：编写一个算法来判断一个数 `n` 是不是快乐数。「快乐数」定义为：
- 对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
- 然后重复这个过程直到这个数变为 `1`，也可能是**无限循环**但始终变不到 `1`。
- 如果可以变为 `1`，那么这个数就是快乐数。

如果 `n` 是快乐数就返回 `true`；不是，则返回 `false`。

笔记：判断 `sum_` 是否重复出现就可以使用 `unordered_set`。

```
class Solution:
    def isHappy(self, n: int) -> bool:
        def calculate_happy(num):
            sum_ = 0
            
            # 从个位开始依次取，平方求和
            while num:
                sum_ += (num % 10) ** 2
                num = num // 10
            return sum_

        # 记录中间结果
        record = set()

        while True:
            n = calculate_happy(n)
            if n == 1:
                return True
            
            # 如果中间结果重复出现，说明陷入死循环了，该数不是快乐数
            if n in record:
                return False
            else:
                record.add(n)
```

## Q5 两数之和
[leetcode:easy](https://leetcode-cn.com/problems/two-sum/)

题目：给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出**和为目标值** `target` 的那**两个**整数，并返回它们的数组下标。你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。你可以按任意顺序返回答案。

笔记：Map 是一种 `key-value` 的存储结构。

```
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        records = dict()

        # 用枚举更方便，就不需要通过索引再去取当前位置的值
        for idx, val in enumerate(nums):
            if target - val not in records:
                records[val] = idx
            else:
                return [records[target - val], idx] # 如果存在就返回字典记录索引和当前索引
```

## References
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
