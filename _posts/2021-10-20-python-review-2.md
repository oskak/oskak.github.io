---
title: Python Review 2
tags: [Python3]
---

## 字符串和编码
因为计算机只能处理数字，如果要处理文本，就必须先把文本转换为数字。最早的计算机在设计时采用8个比特（bit）作为一个字节（byte），所以，一个字节能表示的最大的整数就是255（二进制 `11111111` = 十进制 `255`）。最早只有127个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为 `ASCII` 编码。但是要处理中文显然一个字节是不够的，至少需要两个字节，而且还不能和 `ASCII` 编码冲突，所以，中国制定了 `GB2312` 编码，用来把中文编进去。`Unicode` 把所有语言都统一到一套编码里，这样就不会再有乱码问题了。`ASCII` 编码是1个字节，而 `Unicode` 编码通常是2个字节。

新的问题又出现了：如果统一成 `Unicode` 编码，乱码问题从此消失了。但是，如果你写的文本基本上全部是英文的话，用 `Unicode` 编码比 `ASCII` 编码需要多一倍的存储空间，在存储和传输上就十分不划算。

所以，本着节约的精神，又出现了把 `Unicode` 编码转化为“可变长编码”的 `UTF-8` 编码。`UTF-8` 编码把一个 `Unicode` 字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节。`ASCII` 编码实际上可以被看成是 `UTF-8` 编码的一部分。搞清楚了 `ASCII`、`Unicode` 和 `UTF-8` 的关系，我们就可以总结一下现在计算机系统通用的字符编码工作方式：在计算机内存中，统一使用 `Unicode` 编码，当需要保存到硬盘或者需要传输的时候，就转换为 `UTF-8` 编码。

## Python 字符串
Python 提供了 `ord()` 函数获取字符的整数表示，`chr()` 函数把编码转换为对应的字符：
```
>>> ord('A')
65
>>> ord('中')
20013
>>> chr(66)
'B'
>>> chr(25991)
'文'
```

可以用十六进制写 `str`：
```
>>> '\u4e2d\u6587'
'中文'
```

由于 Python 的字符串类型是 `str`，在内存中以 `Unicode` 表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把 `str` 变为以字节为单位的 `bytes`。

用带 `b` 前缀的单引号或双引号表示：
```
x = b'ABC'
```

以 `Unicode` 表示的 `str` 通过 `encode()` 方法可以编码为指定的 `bytes`：
```
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
>>> '中文'.encode('ascii')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
```

如果我们从网络或磁盘上读取了字节流，那么读到的数据就是 `bytes`。要把 `bytes` 变为 `str`，就需要用 `decode()` 方法：
```
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```

如果 `bytes` 中只有一小部分无效的字节，可以传入 `errors='ignore'` 忽略错误的字节：
```
>>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
'中'
```

`len()` 函数计算的是 `str` 的字符数，如果换成 `bytes`，`len()` 函数就计算字节数：
```
>>> len(b'ABC')
3
>>> len(b'\xe4\xb8\xad\xe6\x96\x87')
6
>>> len('中文'.encode('utf-8'))
6
```

在操作字符串时，我们经常遇到 `str` 和 `bytes` 的互相转换。为了避免乱码问题，应当始终坚持使用 `UTF-8` 编码对 `str` 和 `bytes` 进行转换。

由于 Python 源代码也是一个文本文件，所以当你的源代码中包含中文的时候，在保存源代码时，就需要指定保存为 `UTF-8` 编码。当 Python 解释器读取源代码时，为了让它按 `UTF-8` 编码读取，我们通常在文件开头写上这两行：
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

申明了 `UTF-8` 编码并不意味着你的 `.py` 文件就是 `UTF-8` 编码的，必须确保文本编辑器正在使用 `UTF-8 without BOM` 编码。

## 格式化字符串
用 `%` 实现：
```
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```

|占位符	|替换内容|
|------|-------|
|%d	|整数|
|%f|	浮点数|
|%s	|字符串|
|%x	|十六进制整数|

格式化整数和浮点数还可以指定是否补0和整数与小数的位数：
```
>>> print('%2d-%02d' % (3, 1))
 3-01
>>> print('%.2f' % 3.1415926)
3.14
```

使用字符串的 `format()` 方法：
```
>>> 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
'Hello, 小明, 成绩提升了 17.1%'
```

使用 `f-string`：
```
>>> r = 2.5
>>> s = 3.14 * r ** 2
>>> print(f'The area of a circle with radius {r} is {s:.2f}')
The area of a circle with radius 2.5 is 19.62
```

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1016959663602400/1017092876846880)
