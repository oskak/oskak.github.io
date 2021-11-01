---
title: 面试之字符串 (二) - 字符串模式匹配[Sunday 算法]
tags: [Algorithm]
---

## Q1 实现 strStr()
[leetcode:easy](https://leetcode-cn.com/problems/implement-strstr/)

题目：给你两个字符串 `haystack` 和 `needle`，请你在 `haystack` 字符串中找出 `needle` 字符串出现的第一个位置（下标从 `0` 开始）。如果不存在，则返回 `-1`。

说明：当 `needle` 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。对于本题而言，当 `needle` 是空字符串时我们应当返回 `0`。这与 C 语言的 `strstr()` 以及 Java 的 `indexOf()` 定义相符。

示例：
```
输入：haystack = "hello", needle = "ll"
输出：2
```

笔记：Sunday 是一个线性字符串模式匹配算法。它在发现不匹配时，算法能跳过尽可能多的字符以进行下一步的匹配，从而提高了匹配效率。运行时间平均情况：`O(n)`。

<img src="{{ site.baseurl }}/assets/images/leetcode5.png" style="border:1px solid black;">
<img src="{{ site.baseurl }}/assets/images/leetcode6.png" style="border:1px solid black;">
<img src="{{ site.baseurl }}/assets/images/leetcode7.png" style="border:1px solid black;">

```
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
    
        # 计算偏移表
        def calShiftMat(st):
            dic = {}
            for i in range(len(st)-1,-1,-1):
                if not dic.get(st[i]):
                    dic[st[i]] = len(st)-i
            dic["ot"] = len(st)+1
            return dic
        
        # 其他情况判断
        if len(needle) > len(haystack):return -1
        if needle=="": return 0
       
        # 偏移表预处理    
        dic = calShiftMat(needle)
        idx = 0
    
        while idx+len(needle) <= len(haystack):
            
            # 待匹配字符串
            str_cut = haystack[idx:idx+len(needle)]
            
            # 判断是否匹配
            if str_cut==needle:
                return idx
            else:
                # 边界处理
                if idx+len(needle) >= len(haystack):
                    return -1
                # 不匹配情况下，根据下一个字符的偏移，移动idx
                cur_c = haystack[idx+len(needle)]
                if dic.get(cur_c):
                    idx += dic[cur_c]
                else:
                    idx += dic["ot"]
            
        return -1
```

## Q2 重复的子字符串
[leetcode:easy](https://leetcode-cn.com/problems/repeated-substring-pattern/)

题目：给定一个非空的字符串，判断它是否可以由它的一个子串重复多次构成。给定的字符串只含有小写英文字母，并且长度不超过10000。

示例：
```
(1)
输入: "abab"
输出: True
解释: 可由子字符串 "ab" 重复两次构成。

(2)
输入: "aba"
输出: False

(3)
输入: "abcabcabcabc"
输出: True
解释: 可由子字符串 "abc" 重复四次构成。 (或者子字符串 "abcabc" 重复两次构成。)
```

笔记：
<img src="{{ site.baseurl }}/assets/images/leetcode8.png" style="border:1px solid black;">

```
class Solution:
    def repeatedSubstringPattern(self, s: str) -> bool:
        return s in (s+s)[1:-1]
```

## References
- [Sunday 解法](https://leetcode-cn.com/problems/implement-strstr/solution/python3-sundayjie-fa-9996-by-tes/)
- [构造双倍字符串](https://leetcode-cn.com/problems/repeated-substring-pattern/solution/gou-zao-shuang-bei-zi-fu-chuan-by-elevenxx/)
