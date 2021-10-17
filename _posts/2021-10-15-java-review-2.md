---
title: Java Review 2
tags: [Java]
---

## Java 程序基本结构
剖析一个完整的 Java 程序，它的基本结构是什么：
```
/**
 * 可以用来自动创建文档的注释
 */
public class Hello {
    public static void main(String[] args) {
        // 向屏幕输出文本:
        System.out.println("Hello, world!");
        /* 多行注释开始
        注释内容
        注释结束 */
    }
} // class定义结束
```

因为 Java 是面向对象的语言，一个程序的基本单位就是 `class`，`class` 是关键字。注意到 `public` 是访问修饰符，表示该 `class` 是公开的。不写 `public`，也能正确编译，但是这个类将无法从命令行执行。Java 入口程序规定的方法必须是静态方法，方法名必须为 `main`，括号内的参数必须是 `String` 数组。

## 变量
在 Java 中，变量必须先定义后使用，在定义变量的时候，可以给它一个初始值。例如：
```
int x = 1;
```

不写初始值，就相当于给它指定了默认值。默认值总是0。变量的一个重要特点是可以重新赋值。第一次定义变量 `x` 的时候，需要指定变量类型 `int`，因此使用语句 `int x = 100;`。而第二次重新赋值的时候，变量 `x` 已经存在了，不能再重复定义，因此不能指定变量类型 `int`，必须使用语句 `x = 200;`。变量不但可以重新赋值，还可以赋值给其他变量。

## 基本数据类型
基本数据类型是 CPU 可以直接进行运算的类型。Java 定义了以下几种基本数据类型：
- 整数类型：`byte`，`short`，`int`，`long`
- 浮点数类型：`float`，`double`
- 字符类型：`char`
- 布尔类型：`boolean`

计算机内存的最小存储单元是字节（byte），一个字节就是一个8位二进制数，即8个 bit。它的二进制表示范围从`00000000~11111111`，换算成十进制是`0~255`，换算成十六进制是`00~ff`。

一个字节是 `1byte`，1024字节是 `1K`，1024K是 `1M`，1024M是 `1G`，1024G是 `1T`。一个拥有4T内存的计算机的字节数量是：
```
4T = 4 x 1024G
   = 4 x 1024 x 1024M
   = 4 x 1024 x 1024 x 1024K
   = 4 x 1024 x 1024 x 1024 x 1024
   = 4398046511104
```

不同的数据类型占用的字节数不一样。

## 整型
对于整型类型，Java 只定义了带符号的整型，因此，最高位的 bit 表示符号位（0表示正数，1表示负数）。各种整型能表示的最大范围如下：
- `byte`：-128 ~ 127
- `short`: -32768 ~ 32767
- `int`: -2147483648 ~ 2147483647
- `long`: -9223372036854775808 ~ 9223372036854775807

## 浮点型
浮点类型的数就是小数，因为小数用科学计数法表示的时候，小数点是可以“浮动”的。

```
float f1 = 3.14f;
float f2 = 3.14e38f; // 科学计数法表示的3.14x10^38
double d = 1.79e308;
double d2 = -1.79e308;
double d3 = 4.9e-324; // 科学计数法表示的4.9x10^-324
```

对于 `float` 类型，需要加上 `f` 后缀。

## 布尔类型
```
boolean b1 = true;
boolean b2 = false;
boolean isGreater = 5 > 3; // 计算结果为true
int age = 12;
boolean isAdult = age >= 18; // 计算结果为false
```

Java 语言对布尔类型的存储并没有做规定，因为理论上存储布尔类型只需要1 bit，但是通常 JVM 内部会把 `boolean` 表示为4字节整数。

## 字符类型
字符类型 `char` 表示一个字符。Java 的 `char` 类型除了可表示标准的 `ASCII` 外，还可以表示一个 `Unicode` 字符：
```
public class Main {
    public static void main(String[] args) {
        char a = 'A';
        char zh = '中';
        System.out.println(a);
        System.out.println(zh);
    }
}
```

注意 `char` 类型使用单引号 `'`，且仅有一个字符，要和双引号 `"` 的字符串类型区分开。

## 引用类型
除了上述基本类型的变量，剩下的都是引用类型。例如，引用类型最常用的就是 `String` 字符串：
```
String s = "hello";
```

引用类型的变量类似于 C 语言的指针，它内部存储一个“地址”，指向某个对象在内存的位置。

## 常量
定义变量的时候，如果加上 `final` 修饰符，这个变量就变成了常量：
```
final double PI = 3.14; // PI是一个常量
double r = 5.0;
double area = PI * r * r;
PI = 300; // compile error!
```

常量在定义时进行初始化后就不可再次赋值，再次赋值会导致编译错误。根据习惯，常量名通常全部大写。

## var 关键字
```
StringBuilder sb = new StringBuilder();

is equal to

var sb = new StringBuilder();
```

## 变量的作用范围
```
{
    ...
    int i = 0; // 变量i从这里开始定义
    ...
    {
        ...
        int x = 1; // 变量x从这里开始定义
        ...
        {
            ...
            String s = "hello"; // 变量s从这里开始定义
            ...
        } // 变量s作用域到此结束
        ...
        // 注意，这是一个新的变量s，它和上面的变量同名，
        // 但是因为作用域不同，它们是两个不同的变量:
        String s = "hi";
        ...
    } // 变量x和s作用域到此结束
    ...
} // 变量i作用域到此结束
```

定义变量时，要遵循作用域最小化原则，尽量将变量定义在尽可能小的作用域，并且，不要重复使用变量名。

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1255883729079552)