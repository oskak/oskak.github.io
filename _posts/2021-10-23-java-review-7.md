---
title: Java Review 7
tags: [Java]
---

## 方法参数
方法可以包含0个或任意个参数。方法参数用于接收传递给方法的变量值。调用方法时，必须严格按照参数的定义一一传递。例如：
```
class Person {
    ...
    public void setNameAndAge(String name, int age) {
        ...
    }
}

Person ming = new Person();
ming.setNameAndAge("Xiao Ming"); // 编译错误：参数个数不对
ming.setNameAndAge(12, "Xiao Ming"); // 编译错误：参数类型不对
```

## 可变参数
可变参数用 `类型...` 定义，可变参数相当于数组类型：
```
class Group {
    private String[] names;

    public void setNames(String... names) {
        this.names = names;
    }
}

Group g = new Group();
g.setNames("Xiao Ming", "Xiao Hong", "Xiao Jun"); // 传入3个String
g.setNames("Xiao Ming", "Xiao Hong"); // 传入2个String
g.setNames("Xiao Ming"); // 传入1个String
g.setNames(); // 传入0个String
```

完全可以把可变参数改写为 `String[]` 类型。但是，调用方需要自己先构造 `String[]`。例如：
```
Group g = new Group();
g.setNames(new String[] {"Xiao Ming", "Xiao Hong", "Xiao Jun"}); // 传入1个String[]
```

另一个问题是，调用方可以传入 `null`：
```
Group g = new Group();
g.setNames(null);
```

而可变参数可以保证无法传入 `null`，因为传入0个参数时，接收到的实际值是一个空数组而不是 `null`。

## 参数绑定
调用方把参数传递给实例方法时，调用时传递的值会按参数位置一一绑定。基本类型参数的传递，是调用方值的复制，双方各自的后续修改互不影响。引用类型参数的传递，调用方的变量，和接收方的参数变量，指向的是同一个对象，双方任意一方对这个对象的修改，都会影响对方。

```
public class Main {
    public static void main(String[] args) {
        Person p = new Person();
        String bob = "Bob";
        p.setName(bob); // 传入bob变量
        System.out.println(p.getName()); // "Bob"
        bob = "Alice"; // bob改名为Alice
        System.out.println(p.getName()); // "Bob"还是"Alice"?
        // 改变了变量的指向，并没有改变起先传入方法中的对象，因此结果不变。
    }
}

class Person {
    private String name;

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

## 构造方法
创建实例的时候，我们经常需要同时初始化这个实例的字段。

```
public class Main {
    public static void main(String[] args) {
        Person p = new Person("Xiao Ming", 15);
        System.out.println(p.getName());
        System.out.println(p.getAge());
    }
}

class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() {
        return this.name;
    }

    public int getAge() {
        return this.age;
    }
}
```

构造方法的名称就是类名。构造方法的参数没有限制，在方法内部，也可以编写任意语句。但是，和普通方法相比，构造方法没有返回值（也没有 `void`），调用构造方法，必须用 `new` 操作符。

如果一个类没有定义构造方法，编译器会自动为我们生成一个默认构造方法，它没有参数，也没有执行语句，类似这样：
```
class Person {
    public Person() {
    }
}
```

如果既要能使用带参数的构造方法，又想保留不带参数的构造方法，那么只能把两个构造方法都定义出来。

没有在构造方法中初始化字段时，引用类型的字段默认是 `null`，数值类型的字段用默认值，`int` 类型默认值是0，布尔类型默认值是 `false`：
```
class Person {
    private String name; // 默认初始化为null
    private int age; // 默认初始化为0

    public Person() {
    }
}
```

也可以对字段直接进行初始化：
```
class Person {
    private String name = "Unamed";
    private int age = 10;
}
```

## 多构造方法
可以定义多个构造方法，在通过 `new` 操作符调用的时候，编译器通过构造方法的参数数量、位置和类型自动区分：
```
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Person(String name) {
        this.name = name;
        this.age = 12;
    }

    public Person() {
    }
}
```

一个构造方法可以调用其他构造方法，这样做的目的是便于代码复用。调用其他构造方法的语法是 `this(...)`：
```
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Person(String name) {
        this(name, 18); // 调用另一个构造方法Person(String, int)
    }

    public Person() {
        this("Unnamed"); // 调用另一个构造方法Person(String)
    }
}
```

## 方法重载
方法名相同，但各自的参数不同，称为方法重载（Overload）。方法重载的返回值类型通常都是相同的。方法重载的目的是，**功能类似的方法**使用同一名字，更容易记住，因此，调用起来更简单。

例如 `String` 类提供了多个重载方法 `indexOf()`，可以查找子串：
- `int indexOf(int ch)`：根据字符的 Unicode 码查找；
- `int indexOf(String str)`：根据字符串查找；
- `int indexOf(int ch, int fromIndex)`：根据字符查找，但指定起始位置；
- `int indexOf(String str, int fromIndex)`：根据字符串查找，但指定起始位置。

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1260454548196032)
