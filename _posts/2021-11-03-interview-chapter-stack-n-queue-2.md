---
title: 面试之栈与队列（二）
tags: [Algorithm]
---

## Q1 删除字符串中的所有相邻重复项
[leetcode:easy](https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string/)

题目：给出由小写字母组成的字符串 `S`，重复项删除操作会选择两个相邻且相同的字母，并删除它们。在 `S` 上反复执行重复项删除操作，直到无法继续删除。

示例：
```
输入："abbaca"
输出："ca"
```

```
# 使用栈
class Solution:
    def removeDuplicates(self, s: str) -> str:
        res = list()
        for item in s:
            if res and res[-1] == item:
                res.pop()
            else:
                res.append(item)
        return "".join(res)  # 字符串拼接
```

## Q2 逆波兰表达式求值
[leetcode:medium](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

题目：根据**逆波兰表示法**(后缀表示法)，求表达式的值。有效的算符包括 `+`、`-`、`*`、`/`。每个运算对象可以是整数，也可以是另一个逆波兰表达式。

说明：
- 整数除法只保留整数部分。
- 给定逆波兰表达式总是有效的。换句话说，表达式总会得出有效数值且不存在除数为 `0` 的情况。

示例：
```
输入：tokens = ["2","1","+","3","*"]
输出：9
解释：该算式转化为常见的中缀算术表达式为：((2 + 1) * 3) = 9

输入：tokens = ["4","13","5","/","+"]
输出：6
解释：该算式转化为常见的中缀算术表达式为：(4 + (13 / 5)) = 6
```

笔记：我们习惯阅读中缀表达式，但对计算机来说后缀表达式是比较友好的。

<img src="{{ site.baseurl }}/assets/images/leetcode9.png" style="border:1px solid black;">

<img src="{{ site.baseurl }}/assets/images/leetcode10.png" style="border:1px solid black;">

```
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        stack = []
        for item in tokens:
            if item not in {"+", "-", "*", "/"}:
                stack.append(item)
            else:
                first_num, second_num = stack.pop(), stack.pop()
                stack.append(
                    int(eval(f'{second_num} {item} {first_num}'))  # 第一个出来的在运算符后面
                )
        return int(stack.pop()) # 如果一开始只有一个数，那么会是字符串形式的
```

## References
- [逆波兰表示法](https://zh.wikipedia.org/zh-cn/逆波兰表示法)
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
