---
title: Java Review 5
tags: [Java]
---

## if 判断
```
public class Main {
    public static void main(String[] args) {
        int n = 70;
        if (n >= 90) {
            System.out.println("优秀");
        } else if (n >= 60) {
            System.out.println("及格了");
        } else {
            System.out.println("挂科了");
        }
        System.out.println("END");
    }
}
```

## 判断引用类型相等
`==` 表示“引用是否相等”，或者说，是否指向同一个对象。要判断引用类型的变量内容是否相等，必须使用 `equals()` 方法。

## switch 多重选择
`switch` 语句根据 `switch`（表达式）计算的结果，跳转到匹配的 `case` 结果，然后继续执行后续语句，直到遇到 `break` 结束执行：
```
public class Main {
    public static void main(String[] args) {
        int option = 99;
        switch (option) {
        case 1:
            System.out.println("Selected 1");
            break;
        case 2:
            System.out.println("Selected 2");
            break;
        case 3:
            System.out.println("Selected 3");
            break;
        default:
            System.out.println("Not selected");
            break;
        }
    }
}
```

如果有几个 `case` 语句执行的是同一组语句块，可以这么写：
```
public class Main {
    public static void main(String[] args) {
        int option = 2;
        switch (option) {
        case 1:
            System.out.println("Selected 1");
            break;
        case 2:
        case 3:
            System.out.println("Selected 2, 3");
            break;
        default:
            System.out.println("Not selected");
            break;
        }
    }
}
```

## switch 表达式
从Java 12开始，`switch` 语句升级为更简洁的表达式语法，使用类似模式匹配（Pattern Matching）的方法，保证只有一种路径会被执行，并且不需要 `break` 语句：
```
public class Main {
    public static void main(String[] args) {
        String fruit = "apple";
        switch (fruit) {
        case "apple" -> System.out.println("Selected apple");
        case "pear" -> System.out.println("Selected pear");
        case "mango" -> {
            System.out.println("Selected mango");
            System.out.println("Good choice!");
        }
        default -> System.out.println("No fruit selected");
        }
    }
}
```

使用新的 `switch` 语法，不但不需要 `break`，还可以直接返回值：
```
public class Main {
    public static void main(String[] args) {
        String fruit = "apple";
        int opt = switch (fruit) {
            case "apple" -> 1;
            case "pear", "mango" -> 2;
            default -> 0;
        }; // 注意赋值语句要以;结束
        System.out.println("opt = " + opt);
    }
}
```

## while 循环
Java 提供的 `while` 条件循环。它的基本用法是：
```
while (条件表达式) {
    循环语句
}
// 继续执行后续代码
```

而另一种 `do while` 循环则是先执行循环，再判断条件，条件满足时继续循环，条件不满足时退出。它的用法是：
```
do {
    执行循环语句
} while (条件表达式);
```

## for 循环
```
for (初始条件; 循环检测条件; 循环后更新计数器) {
    // 执行语句
}

public class Main {
    public static void main(String[] args) {
        int sum = 0;
        for (int i=1; i<=100; i++) {
            sum = sum + i;
        }
        System.out.println(sum);
    }
}
```

在 `for` 循环执行前，会先执行初始化语句 `int i=1`，它定义了计数器变量 `i` 并赋初始值为1，然后，循环前先检查循环条件 `i<=100`，循环后自动执行 `i++`，因此，和 `while` 循环相比，`for` 循环把更新计数器的代码统一放到了一起。在 `for` 循环的循环体内部，不需要去更新变量 `i`。使用 `for` 循环时，计数器变量 `i` 要尽量定义在 `for` 循环中。

## for each 循环
`for each` 循环无法指定遍历顺序，也无法获取数组的索引。

```
public class Main {
    public static void main(String[] args) {
        int[] ns = { 1, 4, 9, 16, 25 };
        for (int n : ns) {
            System.out.println(n);
        }
    }
}
```

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1259540152578912)
