---
title: Top 75 LeetCode Questions - Interval And More
tags: [Algorithm]
---

## Q1 合并区间
[leetcode:medium](https://leetcode-cn.com/problems/merge-intervals/)

题目：以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]`。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

示例：
```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

```
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals.sort(key=lambda x: x[0])

        merged = []
        for interval in intervals:
            # 如果列表为空，或者当前区间与上一区间不重合，直接添加
            if not merged or merged[-1][1] < interval[0]:
                merged.append(interval)
            else:
                # 否则的话，我们就可以与上一区间进行合并
                merged[-1][1] = max(merged[-1][1], interval[1])

        return merged
```

## Q2 无重叠区间
[leetcode:medium](https://leetcode-cn.com/problems/non-overlapping-intervals/)

题目：给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。

注意：
- 可以认为区间的终点总是大于它的起点。
- 区间 `[1,2]` 和 `[2,3]` 的边界相互“接触”，但没有相互重叠。

示例：
```
输入: [ [1,2], [2,3], [3,4], [1,3] ]
输出: 1
解释: 移除 [1,3] 后，剩下的区间没有重叠。

输入: [ [1,2], [1,2], [1,2] ]
输出: 2
解释: 你需要移除两个 [1,2] 来使剩下的区间没有重叠。

输入: [ [1,2], [2,3] ]
输出: 0
解释: 你不需要移除任何区间，因为它们已经是无重叠的了。
```

笔记：贪心思想：先留下右边界最小的区间，再去除重复区间。
- 根据区间右边界升序排列；
- 维护 `right`，代表已留下区间的最大右边界；
- 遍历排序后的区间：
  - 如果当前区间的左边界 `≥ right`，该区间可以留下，更新 `right`；
  - 如果当前区间的左边界 `< right`，该区间去除，更新结果 `res`。

```
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        if not intervals:
            return 0
        intervals.sort(key = lambda x : x[1])
        res = 0
        right = intervals[0][1]
        for i in range(1, len(intervals)):
            if intervals[i][0] < right:
                res += 1
            else:
                right = intervals[i][1]
        return res
```

## Q3 合并K个升序链表
[leetcode:hard](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

题目：给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。

笔记：归并排序：先两两合并 `mergeTwoLists`，然后对已经经过一次两两合并的链表们再进行一次两两合并，一直合并到生成最终的升序链表为止。

```
class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        n = len(lists)
        if n == 0: return None
        if n == 1: return lists[0]
        
        mid = n // 2
        return self.mergeTwoLists(self.mergeKLists(lists[:mid]), self.mergeKLists(lists[mid:]))
    
    # 迭代写法 
    def mergeTwoLists(self, node1, node2):
        dummy = cur = ListNode()
        while node1 and node2:
            if node1.val <= node2.val:
                cur.next = node1
                node1 = node1.next
            else:
                cur.next = node2
                node2 = node2.next
            cur = cur.next

        cur.next = node1 if node1 else node2
        return dummy.next
```

## Q4 前K个高频元素
[leetcode:medium](https://leetcode-cn.com/problems/top-k-frequent-elements/)

题目：给你一个整数数组 `nums` 和一个整数 `k`，请你返回其中出现频率前 `k` 高的元素。你可以按任意顺序返回答案。

示例：
```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]

输入: nums = [1], k = 1
输出: [1]
```

```
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        freq = {}
        for i in nums: 
            if i in freq: 
                freq[i]+=1
            else: 
                freq[i]=1
        res = sorted(freq.items(),key = lambda x: x[1], reverse=True)
        out = []
        for i in res: 
            if len(out) == k: 
                return out
            out.append(i[0])
        return out
```

## Q5 有效的括号
[leetcode:easy](https://leetcode-cn.com/problems/valid-parentheses/)

题目：给定一个只包括 `'('，')'，'{'，'}'，'['，']'` 的字符串 `s`，判断字符串是否有效。

示例：
```
输入：s = "()"
输出：true

输入：s = "()[]{}"
输出：true

输入：s = "(]"
输出：false

输入：s = "([)]"
输出：false
```

```
class Solution:
    def isValid(self, s: str) -> bool:
        if len(s) % 2 == 1:
            return False
        
        pairs = {
            ")": "(",
            "]": "[",
            "}": "{",
        }
        stack = list()
        for ch in s:
            if ch in pairs:
                if not stack or stack[-1] != pairs[ch]:
                    return False
                stack.pop()
            else:
                stack.append(ch)
        
        return not stack
```

## Q6 验证回文串
[leetcode:easy](https://leetcode-cn.com/problems/valid-palindrome/)

题目：给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

说明：本题中，我们将空字符串定义为有效的回文串。

示例：
```
输入: "A man, a plan, a canal: Panama"
输出: true
解释："amanaplanacanalpanama" 是回文串

输入: "race a car"
输出: false
解释："raceacar" 不是回文串
```

```
#API
class Solution:
    def isPalindrome(self, s: str) -> bool:
        sgood = "".join(ch.lower() for ch in s if ch.isalnum())
        return sgood == sgood[::-1]
```

```
#双指针
class Solution:
    def isPalindrome(self, s: str) -> bool:
        n = len(s)
        left, right = 0, n - 1
        
        while left < right:
            while left < right and not s[left].isalnum():
                left += 1
            while left < right and not s[right].isalnum():
                right -= 1
            if left < right:
                if s[left].lower() != s[right].lower():
                    return False
                left, right = left + 1, right - 1

        return True
```

## Q7 有效的字母异位词
[leetcode:easy](https://leetcode-cn.com/problems/valid-anagram/)

题目：给定两个字符串 `s` 和 `t`，编写一个函数来判断 `t` 是否是 `s` 的字母异位词。

注意：若 `s` 和 `t` 中每个字符出现的次数都相同，则称 `s` 和 `t` 互为字母异位词。

示例：
```
输入: s = "anagram", t = "nagaram"
输出: true

输入: s = "rat", t = "car"
输出: false
```

```
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        return sorted(s)==sorted(t)
```

```
import collections
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        return collections.Counter(s) == collections.Counter(t)
```

## Q8 字母异位词分组
[leetcode:medium](https://leetcode-cn.com/problems/group-anagrams/)

题目：给你一个字符串数组，请你将**字母异位词**组合在一起。可以按任意顺序返回结果列表。**字母异位词**是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。

示例：
```
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]

输入: strs = [""]
输出: [[""]]

输入: strs = ["a"]
输出: [["a"]]
```

```
#排序
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        mp = collections.defaultdict(list)

        for st in strs:
            key = "".join(sorted(st))
            mp[key].append(st)
        
        return list(mp.values())
```

## Q9 无重复字符的最长子串
[leetcode:medium](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

题目：给定一个字符串 `s`，请你找出其中不含有重复字符的**最长子串**的长度。

示例：
```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
    请注意，你的答案必须是子串的长度，"pwke" 是一个子序列，不是子串。

输入: s = ""
输出: 0
```

```
#优化的滑动窗口
def lengthOfLongestSubstring(self, s: str) -> int:
    sub = ""
    ans = 0
    for letter in s:
        if letter not in sub:
            sub += letter
        else:
            if len(sub) > ans:
                ans = len(sub)
            sub = sub[sub.index(letter) + 1:] + letter
        
    return max(ans, len(sub))
```

## Q10 最长回文子串
[leetcode:medium](https://leetcode-cn.com/problems/longest-palindromic-substring/)

题目：给你一个字符串 `s`，找到 `s` 中最长的回文子串。

示例：
```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。

输入：s = "cbbd"
输出："bb"

输入：s = "a"
输出："a"
```

```
#中心扩散法
class Solution:
    def longestPalindrome(self, s: str) -> str:
        res = ''
        for i in range(len(s)):
            s1 = self.find(s, i, i)       # 以当前字符为中心的最长回文子串
            s2 = self.find(s, i, i+1)     # 以当前字符和下一字符为中心的最长回文子串
            #如果最大长度有变化则更新res
            if max(len(s1), len(s2)) > len(res):
                res = s2 if len(s1) < len(s2) else s1
        return res

    def find(self, s, left, right):
        #找到当前中心的最大长度子串
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return s[left+1:right]
```

## Q11 回文子串
[leetcode:medium](https://leetcode-cn.com/problems/palindromic-substrings/)

题目：给你一个字符串 `s`，请你统计并返回这个字符串中**回文子串**的数目。**回文字符串**是正着读和倒过来读一样的字符串。**子字符串**是字符串中的由连续字符组成的一个序列。具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

示例：
```
输入：s = "abc"
输出：3
解释：三个回文子串: "a", "b", "c"

输入：s = "aaa"
输出：6
解释：6个回文子串: "a", "a", "a", "aa", "aa", "aaa"
```

笔记：在长度为 `N` 的字符串中，可能的回文串中心位置有 `2N - 1` 个：字母，或两个字母中间。

```
class Solution:
    def countSubstrings(self, s: str) -> int:
        L = len(s)
        cnt = 0
        # 以某一个元素为中心的奇数长度的回文串的情况
        for center in range(L):
            left = right = center
            while left >= 0 and right < L and s[left] == s[right]:
                cnt += 1
                left -= 1
                right += 1
        # 以某对元素为中心的偶数长度的回文串的情况
        for left in range(L - 1):
            right = left + 1
            while left >= 0 and right < L and s[left] == s[right]:
                cnt += 1
                left -= 1
                right += 1

        return cnt
```

## References
- [New Year Gift - Curated List of Top 75 LeetCode Questions to Save Your Time](https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU)
