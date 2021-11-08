---
title: Java Review 8
tags: [Java]
---

## 继承
子类包含了父类已有的字段和方法。严禁定义与父类重名的字段。

Java 使用 `extends` 关键字来实现继承：
```
class Person {
    private String name;
    private int age;

    public String getName() {...}
    public void setName(String name) {...}
    public int getAge() {...}
    public void setAge(int age) {...}
}

class Student extends Person {
    // 不要重复name和age字段/方法
    // 只需要定义新增score字段/方法
    private int score;

    public int getScore() { … }
    public void setScore(int score) { … }
}
```

我们把 `Person` 称为超类（super class），父类（parent class），基类（base class），把 `Student` 称为子类（subclass），扩展类（extended class）。

在 Java 中，没有明确写 `extends` 的类，编译器会自动加上 `extends Object`。所以，任何类，除了 `Object`，都会继承自某个类。Java 只允许一个 `class` 继承自一个类，因此，一个类有且仅有一个父类。只有 `Object` 特殊，它没有父类。

继承有个特点，就是子类无法访问父类的 `private` 字段或者 `private` 方法。例如，`Student` 类就无法访问 `Person` 类的 `name` 和 `age` 字段。为了让子类可以访问父类的字段，我们需要把 `private` 改为 `protected`。用 `protected` 修饰的字段可以被子类访问。`protected` 关键字可以把字段和方法的访问权限控制在继承树内部，一个 `protected` 字段和方法可以被其子类，以及子类的子类所访问。

<img src="{{ site.baseurl }}/assets/images/inherit_tree.png" style="border:1px solid black;">

`super` 关键字表示父类。子类引用父类的字段时，可以用 `super.fieldName`：
```
class Student extends Person {
    public String hello() {
        return "Hello, " + super.name;
    }
}
```

实际上，这里使用 `super.name`，`this.name` 或者 `name`，效果都是一样的。编译器会自动定位到父类的 `name` 字段。这是因为在 Java 中，任何 `class` 的构造方法，第一行语句必须是调用父类的构造方法。如果没有明确地调用父类的构造方法，编译器会帮我们自动加一句 `super();`，所以，`Student` 类的构造方法实际上是这样：
```
class Student extends Person {
    protected int score;

    public Student(String name, int age, int score) {
        super(); // 自动调用父类的构造方法
        this.score = score;
    }
}
```

此时如果 `Person` 类并没有**无参数**的构造方法，编译会失败。解决方法是调用 `Person` 类存在的某个构造方法。例如：
```
class Student extends Person {
    protected int score;

    public Student(String name, int age, int score) {
        super(name, age); // 调用父类的构造方法Person(String, int)
        this.score = score;
    }
}
```

结论：如果父类没有默认的构造方法，子类就必须显式调用 `super()` 并给出参数。子类不会继承任何父类的构造方法。子类默认的构造方法是编译器自动生成的。

## 向上转型
```
#如果一个引用变量的类型是Student，那么它可以指向一个Student类型的实例
Student s = new Student();

#如果一个引用类型的变量是Person，那么它可以指向一个Person类型的实例
Person p = new Person();

#这种指向是允许的
Person p = new Student(); // ???
```

这种把一个子类类型安全地变为父类类型的赋值，被称为向上转型（upcasting）。

## 向下转型
利用 `instanceof`，在向下转型前可以先判断：
```
Person p = new Student();
if (p instanceof Student) {
    // 只有判断成功才会向下转型
    Student s = (Student) p; // 一定会成功
}
```

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1260455778791232)
