---
title: Java Review 6
tags: [Java]
---

## 二维数组
```
public class Main {
    public static void main(String[] args) {
        int[][] ns = {
            { 1, 2, 3, 4 },
            { 5, 6, 7, 8 },
            { 9, 10, 11, 12 }
        };
        int[] arr0 = ns[0];
        System.out.println(arr0.length); // 4
    }
}
```

访问二维数组的某个元素需要使用 `array[row][col]`。要打印一个二维数组，可以使用两层嵌套的 `for` 循环。

## 三维数组
```
int[][][] ns = {
    {
        {1, 2, 3},
        {4, 5, 6},
        {7, 8, 9}
    },
    {
        {10, 11},
        {12, 13}
    },
    {
        {14, 15, 16},
        {17, 18}
    }
};
```

理论上，我们可以定义任意的N维数组。但在实际应用中，除了二维数组在某些时候还能用得上，更高维度的数组很少使用。

## 命令行参数
Java 程序的入口是 `main` 方法，而 `main` 方法可以接受一个命令行参数，它是一个 `String[]` 数组。

```
public class Main {
    public static void main(String[] args) {
        for (String arg : args) {
            if ("-version".equals(arg)) {
                System.out.println("v 1.0");
                break;
            }
        }
    }
}
```

在命令行执行，我们先编译它：
```
$ javac Main.java
```

执行的时候，给它传递一个 `-version` 参数：
```
$ java Main -version
```

## 面向对象编程
和面向对象编程不同的，是面向过程编程。面向过程编程，是把模型分解成一步一步的过程。面向对象编程，是一种通过对象的方式，把现实世界映射到计算机模型的一种编程方法。`class` 本身就是一种数据类型，而 `instance` 是对象实例。

## 定义 class
创建一个类：
```
class Person {
    public String name;
    public int age;
}
```

一个 `class` 可以包含多个字段（field）。`public` 是用来修饰字段的，它表示这个字段可以被外部访问。

## 创建实例
根据对象模版创建出真正的对象实例，用 `new` 操作符。`new` 操作符可以创建一个实例，然后我们需要定义一个引用类型的变量来指向这个实例：
```
Person ming = new Person();
```

访问实例变量可以用 `变量.字段`：
```
ming.name = "Xiao Ming"; // 对字段name赋值
ming.age = 12; // 对字段age赋值
System.out.println(ming.name); // 访问字段name
```

## 方法
直接把 field 用 `public` 暴露给外部可能会破坏封装性。为了避免外部代码直接去访问 field，可以用 `private` 修饰 field，拒绝外部访问：
```
class Person {
    private String name;
    private int age;
}
```

需要使用方法（method）来让外部代码可以间接修改 field：
```
public class Main {
    public static void main(String[] args) {
        Person ming = new Person();
        ming.setName("Xiao Ming"); // 设置name
        ming.setAge(12); // 设置age
        System.out.println(ming.getName() + ", " + ming.getAge());
    }
}

class Person {
    private String name;
    private int age;

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return this.age;
    }

    public void setAge(int age) {
        if (age < 0 || age > 100) {
            throw new IllegalArgumentException("invalid age value");
        }
        this.age = age;
    }
}
```

一个类通过定义方法，就可以给外部代码暴露一些操作的接口，同时，内部自己保证逻辑一致性。

定义方法的语法是：
```
修饰符 方法返回类型 方法名(方法参数列表) {
    若干方法语句;
    return 方法返回值;
}
```

如果没有返回值，返回类型设置为 `void`，可以省略 `return`。`private` 方法不允许外部调用。内部方法可以调用 `private` 方法。

## this 变量
通过 `this.field` 可以访问当前实例的字段。

如果没有命名冲突，可以省略 `this`：
```
class Person {
    private String name;

    public String getName() {
        return name; // 相当于this.name
    }
}
```

如果有局部变量和字段重名，那么局部变量优先级更高，就必须加上 `this`：
```
class Person {
    private String name;

    public void setName(String name) {
        this.name = name; // 前面的this不可少，少了就变成局部变量name了
    }
}
```

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1255945064769408)
