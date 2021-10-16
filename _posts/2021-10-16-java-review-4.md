---
title: Java Review 4
tags: [Java]
---

## 字符类型
字符类型 `char` 是基本数据类型。一个 `char` 保存一个 `Unicode` 字符：
```
char c1 = 'A';
char c2 = '中';
```

因为 Java 在内存中总是使用 Unicode 表示字符，所以，一个英文字符和一个中文字符都用一个 `char` 类型表示，它们都占用两个字节。要显示一个字符的 `Unicode` 编码，只需将 `char` 类型直接赋值给 `int` 类型即可：
```
int n1 = 'A'; // 字母“A”的Unicodde编码是65
int n2 = '中'; // 汉字“中”的Unicode编码是20013
```

还可以直接用转义字符 `\u+Unicode` 编码来表示一个字符：
```
// 注意是十六进制:
char c3 = '\u0041'; // 'A'，因为十六进制0041 = 十进制65
char c4 = '\u4e2d'; // '中'，因为十六进制4e2d = 十进制20013
```

## 字符串
Java 的编译器对字符串做了特殊照顾，可以使用 `+` 连接任意字符串和其他数据类型。

从 Java 13开始，字符串可以用 `"""..."""` 表示多行字符串：
```
public class Main {
    public static void main(String[] args) {
        String s = """
                   SELECT * FROM
                     users
                   WHERE id > 100
                   ORDER BY name DESC
                   """;
        System.out.println(s);
    }
}
```

Java 的字符串除了是一个引用类型外，还有个重要特点，就是字符串不可变：
```
public class Main {
    public static void main(String[] args) {
        String s = "hello";
        System.out.println(s); // 显示 hello
        s = "world";
        System.out.println(s); // 显示 world
    }
}
```

原来的字符串 `"hello"` 还在，只是我们无法通过变量 `s` 访问它而已。因此，字符串的不可变是指字符串内容不可变。

## 空值null
引用类型的变量可以指向一个空值 `null`，它表示不存在，即该变量不指向任何对象。
```
String s1 = null; // s1是null
String s2; // 没有赋初值值，s2也是null
String s3 = s1; // s3也是null
String s4 = ""; // s4指向空字符串，不是null
```

## 数组类型
```
public class Main {
    public static void main(String[] args) {
        // 5位同学的成绩:
        int[] ns = new int[5];
        ns[0] = 68;
        ns[1] = 79;
        ns[2] = 91;
        ns[3] = 85;
        ns[4] = 62;
    }
}
```

Java 的数组有几个特点：
- 数组所有元素初始化为默认值，整型都是0，浮点型是 `0.0`，布尔型是 `false`；
- 数组一旦创建后，大小就不可改变。

可以用 `数组变量.length` 获取数组大小。

也可以在定义数组时直接指定初始化的元素，这样就不必写出数组大小，而是由编译器自动推算数组大小：
```
public class Main {
    public static void main(String[] args) {
        // 5位同学的成绩:
        int[] ns = new int[] { 68, 79, 91, 85, 62 };
        System.out.println(ns.length); // 编译器自动推算数组大小为5
    }
}
```

还可以进一步简写为：
```
int[] ns = { 68, 79, 91, 85, 62 };
```

## 输入和输出
`println` 表示输出并换行。如果输出后不换行，可以用 `print()`。

如果要把数据显示成我们期望的格式，就需要使用格式化输出的功能。格式化输出使用 `System.out.printf()`，通过使用占位符 `%?`，`printf()` 可以把后面的参数格式化成指定格式：
```
public class Main {
    public static void main(String[] args) {
        double d = 3.1415926;
        System.out.printf("%.2f\n", d); // 显示两位小数3.14
        System.out.printf("%.4f\n", d); // 显示4位小数3.1416
    }
}
```

和输出相比，Java 的输入就要复杂得多：
```
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in); // 创建Scanner对象
        System.out.print("Input your name: "); // 打印提示
        String name = scanner.nextLine(); // 读取一行输入并获取字符串
        System.out.print("Input your age: "); // 打印提示
        int age = scanner.nextInt(); // 读取一行输入并获取整数
        System.out.printf("Hi, %s, you are %d\n", name, age); // 格式化输出
    }
}
```

首先，我们通过 `import` 语句导入 `java.util.Scanner`，`import` 是导入某个类的语句，必须放到 Java 源代码的开头。然后，创建 `Scanner` 对象并传入 `System.in`。`System.out` 代表标准输出流，`System.in` 代表标准输入流。

有了 `Scanner` 对象后，要读取用户输入的字符串，使用 `scanner.nextLine()`，要读取用户输入的整数，使用 `scanner.nextInt()`。`Scanner` 会自动转换数据类型，因此不必手动转换。

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1255887264020640)
