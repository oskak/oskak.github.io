---
title: Java Development Environment Setup
tags: [Java]
---

## 什么是 OpenJDK
Java 最早由 SUN 公司发明，2006年 SUN 公司将 Java 开源，此时的 JDK 即为 OpenJDK。

OpenJDK 是 Java SE 的开源实现，由 SUN 和 Java 社区提供支持。2009年 Oracle 收购了 SUN 公司，自此 Java 的维护方之一的 SUN 也变成了 Oracle。

大多数 JDK 都是在 OpenJDK 的基础上编写实现的，比如 IBM J9 和 Oracle JDK。几乎现有的所有 JDK 都派生自 OpenJDK，他们之间不同的是许可证。

## 什么是 Oracle JDK
实际上，Oracle JDK 是基于 OpenJDK 源代码构建的，因此 Oracle JDK 和 OpenJDK 之间没有重大的技术差异。

## JRE 和 JDK 的区别是什么？
JRE 只是 Java 程序的运行环境，它最核心的内容就是 JVM（Java 虚拟机）及核心类库。

JDK 是程序员使用 java 语言编写 java 程序所需的开发工具包，是提供给程序员使用的。JDK 包含了JRE，同时还包含了编译 java 源码的编译器 javac，还包含了很多 java 程序调试和分析的工具。

如果你需要运行 java 程序，只需安装 JRE 就可以了。如果你需要编写 java 程序，需要安装JDK。

## 我的机器上有多少个不同版本的 Java？
Only one JRE can be installed on the system at any time. Installing a JRE removes the previously installed JRE. See [Java Platform, Standard Edition Installation Guide](https://docs.oracle.com/javase/9/install/installation-jdk-and-jre-macos.htm#JSJIG-GUID-5F4A0659-BFC5-4CB9-9920-D2DEABF29894).

## Download JDK
[Java SE Development Kit 17 downloads](https://www.oracle.com/java/technologies/downloads/)

On Mac OS X 10.5 or later, we can use `/usr/libexec/java_home` to return the location of the default JDK:
```
$ /usr/libexec/java_home   
$ /usr/libexec/java_home -V
```

On macOS 11 Big Sur, the zsh is the default Terminal shell, and we can set the `$JAVA_HOME` environment variable in either `~/.zshenv` or `~/.zshrc`:
```
export JAVA_HOME=$(/usr/libexec/java_home)
```

```
$ echo $JAVA_HOME
/Library/Java/JavaVirtualMachines/jdk-17.jdk/Contents/Home
```

## References
- [OpenJDK和Oracle JDK有什么区别和联系？](https://blog.csdn.net/bisal/article/details/104832084)
- [JRE 和 JDK 的区别详解（附JVM简单说明）](https://blog.csdn.net/kingscoming/article/details/78860702)
- [How to Set $JAVA_HOME environment variable on macOS](https://mkyong.com/java/how-to-set-java_home-environment-variable-on-mac-os-x/#java-home-and-macos-11-big-sur)
