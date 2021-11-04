---
title: 谈递归
tags: [Algorithm]
---

## 什么是递归
```
从前有座山，山里有个庙，庙里有个老和尚在给小和尚讲故事，讲的什么呢？
「从前有座山，山里有个庙，庙里有个老和尚在给小和尚讲故事，讲的什么呢？
『从前有座山，山里有个庙，庙里有个老和尚在给小和尚讲故事，讲的什么呢？……』」
```

在计算机科学里，递归指的是一种通过重复将问题分解为同类的子问题而解决问题的方法。套用程序的概念，就是函数执行过程中直接或间接地调用自身。

```
To Iterate is Human, to Recurse, Divine
```

递归的另一个重点在于：一个可以退出递归的出口。

## 递归的一些优化思路
1. 如果你使用递归的时候不进行优化，有非常多的子问题是被重复计算的。使用递归的时候，要考虑有没有重复计算，如果重复计算了，一定要把计算过的状态保存起来。
2. 对于递归的问题，我们一般都是从上往下递归，直到递归到最底，再一层一层把值返回，这时可能栈空间会不够用。对于这种情况，可以考虑自底向上的做法。

## 什么是尾调用？
尾调用指某个函数的最后一步是调用另一个函数：
```
function f(x){
  return g(x); # 函数f的最后一步是调用函数g
}
```

以下两种情况，都不属于尾调用：
```
// 情况一
function f(x){
  let y = g(x);
  return y; # 调用函数g之后，还有别的操作
}

// 情况二
function f(x){
  return g(x) + 1; # 调用后还有操作，即使写在一行内
}
```

尾调用不一定出现在函数尾部，只要是最后一步操作即可：
```
function f(x) {
  if (x > 0) {
    return m(x)
  }
  return n(x);
}
```

## 尾调用优化
函数调用会在内存形成一个"调用记录"，又称"调用帧"（call frame），保存调用位置和内部变量等信息。如果在函数A的内部调用函数B，那么在A的调用记录上方，会形成一个B的调用记录。等到B运行结束，将结果返回到A，B的调用记录才会消失。如果函数B内部还调用函数C，那就还有一个C的调用记录，以此类推。所有的调用记录，就形成一个"调用栈"（call stack）。

<img src="{{ site.baseurl }}/assets/images/call_stack.png" style="border:1px solid black;">

尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用记录，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用记录，取代外层函数的调用记录就可以了。

这就叫做"尾调用优化"（Tail call optimization），即只保留内层函数的调用记录。如果所有函数都是尾调用，那么完全可以做到每次执行时，调用记录只有一项，这将大大节省内存。

函数调用自身，称为递归。如果尾调用自身，就称为尾递归。一个阶乘函数，计算 `n` 的阶乘，最多需要保存 `n` 个调用记录，复杂度 `O(n)`。如果改写成尾递归，只保留一个调用记录，复杂度 `O(1)`。

## 柯里化 "Currying"
One can see more about "currying" [here](https://en.wikipedia.org/wiki/Currying).

Currying is when you break down a function that takes multiple arguments into a series of functions that each take only one argument:
```
function add (a, b) {
  return a + b;
}
```

We will now curry this function:
```
function add (a) {
  return function (b) {
    return a + b;
  }
}
```

## References
- [谈谈递归](http://io.upyun.com/2016/04/05/recursion/)
- [尾调用优化](http://www.ruanyifeng.com/blog/2015/04/tail-call.html)
- [What is 'Currying'?](https://stackoverflow.com/questions/36314/what-is-currying)
