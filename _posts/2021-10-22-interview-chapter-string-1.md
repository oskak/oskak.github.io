---
title: 面试之字符串 (一)
tags: [Interview, Algorithm]
---

## Q1 反转字符串
[leetcode:easy](https://leetcode-cn.com/problems/reverse-string/)

题目：编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 `s` 的形式给出。不要给另外的数组分配额外的空间，你必须原地修改输入数组，使用 `O(1)` 的额外空间解决这一问题。

```
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        left, right = 0, len(s) - 1
        
        # 该方法不需要判断奇偶数
        while left < right:
            s[left], s[right] = s[right], s[left]
            left += 1
            right -= 1
```

## Q2 反转字符串 II
[leetcode:easy](https://leetcode-cn.com/problems/reverse-string-ii/)

题目：给定一个字符串 `s` 和一个整数 `k`，从字符串开头算起，每计数至 `2k` 个字符，就反转这 `2k` 字符中的前 `k` 个字符。
- 如果剩余字符少于 `k` 个，则将剩余字符全部反转。
- 如果剩余字符小于 `2k` 但大于或等于 `k` 个，则反转前 `k` 个字符，其余字符保持原样。

笔记：模拟法。

时间复杂度：`O(n)`

空间复杂度：`O(1)` 或 `O(n)`，取决于使用的语言中字符串类型的性质

```
class Solution:
    def reverseStr(self, s: str, k: int) -> str:
        t = list(s)
        for i in range(0, len(t), 2 * k):
            t[i: i + k] = reversed(t[i: i + k])
        return "".join(t)
```

## Q3 替换空格
[leetcode:easy](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

题目：请实现一个函数，把字符串 `s` 中的每个空格替换成"%20".

实例：
```
输入：s = "We are happy."
输出："We%20are%20happy."
```

笔记：不用额外的辅助空间。首先扩充数组到每个空格替换成"%20"之后的大小。然后从后向前替换空格，也就是双指针法，`i` 指向新长度的末尾，`j` 指向旧长度的末尾。

如果从前向后填充就是 `O(n^2)` 的算法了，因为每次添加元素都要将添加元素之后的所有元素向后移动。很多数组填充类问题，都可以先预先给数组扩容到填充后的大小，然后再从后向前进行操作。

时间复杂度：`O(n)`

空间复杂度：`O(1)`

```
class Solution:
    def replaceSpace(self, s: str) -> str:
        counter = s.count(' ')
        
        res = list(s)
        # 每碰到一个空格就多拓展两个格子，1 + 2 = 3个位置存’%20‘
        res.extend([' '] * counter * 2)
        
        # 原始字符串的末尾，拓展后的末尾
        left, right = len(s) - 1, len(res) - 1
        
        while left >= 0:
            if res[left] != ' ':
                res[right] = res[left]
                right -= 1
            else:
                # [right - 2, right), 左闭右开
                res[right - 2: right + 1] = '%20'
                right -= 3
            left -= 1
        return ''.join(res)
```

## Q4 翻转字符串里的单词
[leetcode:medium](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

题目：给你一个字符串 `s`，逐个翻转字符串中的所有单词。单词是由非空格字符组成的字符串。`s` 中使用至少一个空格将字符串中的单词分隔开。请你返回一个翻转 `s` 中单词顺序并用单个空格相连的字符串。

说明：
- 输入字符串 `s` 可以在前面、后面或者单词间包含多余的空格。
- 翻转后单词间应当仅用一个空格分隔。
- 翻转后的字符串中不应包含额外的空格。

示例：
```
输入：s = "the sky is blue"
输出："blue is sky the"
```

```
class Solution:
    def reverseWords(self, s: str) -> str:
        k = s.split(" ")
        while "" in k:
            k.remove("")
        k[:] = k[::-1]
        
        return " ".join(k)
```

## Q5 左旋转字符串
[leetcode:easy](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

题目：字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。不能申请额外空间，只能在本串上操作。

笔记：通过 `局部反转 + 整体反转` 达到左旋转的目的。具体步骤为：
- 反转区间为前n的子串
- 反转区间为n到末尾的子串
- 反转整个字符串

<img src="{{ site.baseurl }}/assets/images/leetcode4.png" style="border:1px solid black;">

实例：
```
输入: s = "abcdefg", k = 2
输出: "cdefgab"
```

```
#切片
class Solution:
    def reverseLeftWords(self, s: str, n: int) -> str:
        return s[n:] + s[0:n]
```

```
#有些面试中不允许使用切片，使用笔记的方法
class Solution:
    def reverseLeftWords(self, s: str, n: int) -> str:
        s = list(s)
        s[0:n] = list(reversed(s[0:n]))
        s[n:] = list(reversed(s[n:]))
        s.reverse()
        
        return "".join(s)
```

```
# 方法三：如果连reversed也不让使用，那么自己手写一个
class Solution:
    def reverseLeftWords(self, s: str, n: int) -> str:
        def reverse_sub(lst, left, right):
            while left < right:
                lst[left], lst[right] = lst[right], lst[left]
                left += 1
                right -= 1
        
        res = list(s)
        end = len(res) - 1
        reverse_sub(res, 0, n - 1)
        reverse_sub(res, n, end)
        reverse_sub(res, 0, end)
        return ''.join(res)

# 同方法二
# 时间复杂度：O(n)
# 空间复杂度：O(n)，python的string为不可变，需要开辟同样大小的list空间来修改
```

## References
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
