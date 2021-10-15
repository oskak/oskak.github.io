---
title: Java Review 1
tags: [Java]
---

## Java 语言介绍
Java 介于编译型语言和解释型语言之间。编译型语言如 C、C++，代码是直接编译成机器码执行，但是不同的平台（`x86`、`ARM`等）CPU的指令集不同，因此，需要编译出每一种平台的对应机器码。解释型语言如 Python、Ruby 没有这个问题，可以由解释器直接加载源码然后运行，代价是运行效率太低。而 Java 是将代码编译成一种“字节码”，它类似于抽象的 CPU 指令，然后，针对不同平台编写虚拟机，不同平台的虚拟机负责加载字节码并执行，这样就实现了“一次编写，到处运行”的效果。当然，这是针对 Java 开发者而言。对于虚拟机，需要为每个平台分别开发。为了保证不同平台、不同公司开发的虚拟机都能正确执行 Java 字节码，SUN 公司制定了一系列的 Java 虚拟机规范。从实践的角度看，JVM 的兼容性做得非常好，低版本的 Java 字节码完全可以正常运行在高版本的 JVM 上。

随着 Java 的发展，SUN 给 Java 又分出了三个不同版本：
- Java SE：Standard Edition
- Java EE：Enterprise Edition
- Java ME：Micro Edition

毫无疑问，Java SE 是整个 Java 平台的核心，而 Java EE 是进一步学习 Web 应用所必须的。我们熟悉的 Spring 等框架都是 Java EE 开源生态系统的一部分。

## JSR 规范
为了保证 Java 语言的规范性，SUN 公司搞了一个 JSR 规范，凡是想给 Java 平台加一个功能，比如说访问数据库的功能，大家要先创建一个 JSR 规范，定义好接口，这样，各个数据库厂商都按照规范写出 Java 驱动程序，开发者就不用担心自己写的数据库代码在 MySQL 上能跑，却不能跑在 PostgreSQL 上。

所以 JSR（Java Specification Request）是一系列的规范，从 JVM 的内存模型到 Web 程序接口，全部都标准化了。而负责审核JSR的组织就是JCP（Java Community Process）。

一个 JSR 规范发布时，为了让大家有个参考，还要同时发布一个“参考实现”，以及一个“兼容性测试套件”：
- RI：Reference Implementation
- TCK：Technology Compatibility Kit

## 第一个 Java 程序
一个 Java 源码只能定义一个 `public` 类型的 `class`，并且 `class` 名称和文件名要完全一致：
```
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

注意到类的定义中，我们定义了一个名为 `main` 的方法。这里的 `main` 方法有一个参数，参数类型是 `String[]`，参数名是 `args`。`public`、`static` 用来修饰方法，这里表示它是一个公开的静态方法，`void` 是方法的返回类型，而花括号 `{}` 中间的就是方法的代码。

Java 规定，某个类定义的 `public static void main(String[] args)` 是 Java 程序的固定入口方法，因此，Java 程序总是从 `main` 方法开始执行。

## 如何运行 Java 程序
Java 源码本质上是一个文本文件，我们需要先用 `javac` 把 `Hello.java` 编译成字节码文件 `Hello.class`，然后，用 `java` 命令执行这个字节码文件。因此，可执行文件 `javac` 是编译器，而可执行文件 `java` 就是虚拟机。

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1255876875896416)
