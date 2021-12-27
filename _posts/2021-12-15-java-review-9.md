---
title: Java Review 9
tags: [Java]
---

## 多态
在继承关系中，子类如果定义了一个与父类方法签名完全相同的方法，称为覆写（Override）。

```
//父类
class Person {
    public void run() {
        System.out.println("Person.run");
    }
}

//子类
class Student extends Person {
    @Override
    public void run() {
        System.out.println("Student.run");
    }
}
```

Override 和 Overload 不同的是，如果方法签名不同，就是 Overload，Overload 方法是一个新方法；如果方法签名相同，并且返回值也相同，就是 Override。

思考一个例子：
```
public class Main {
    public static void main(String[] args) {
        Person p = new Student(); // 一个实际类型为Student，引用类型为Person的变量
        p.run(); // 应该打印Person.run还是Student.run?
    }
}

class Person {
    public void run() {
        System.out.println("Person.run");
    }
}

class Student extends Person {
    @Override
    public void run() {
        System.out.println("Student.run");
    }
}
```

运行上面的代码可知，实际上调用的方法是 `Student` 的 `run()` 方法。因此可得出结论：Java 的实例方法调用是基于运行时的实际类型的动态调用，而非变量的声明类型。这个非常重要的特性在面向对象编程中称为多态(Polymorphic)。多态允许我们添加更多类型的子类来实现功能扩展，却不需要修改基于父类的代码。

## 调用 `super`
在子类的覆写方法中，如果要调用父类的被覆写的方法，可以通过 `super` 来调用。

```
class Person {
    protected String name;
    public String hello() {
        return "Hello, " + name;
    }
}

Student extends Person {
    @Override
    public String hello() {
        // 调用父类的hello()方法:
        return super.hello() + "!";
    }
}
```

## `final`
继承可以允许子类覆写父类的方法。如果一个父类不允许子类对它的某个方法进行覆写，可以把该方法标记为 `final`。用 `final` 修饰的方法不能被 Override。如果一个类不希望任何其他类继承它，那么可以把这个类本身标记为 `final`。对于一个类的实例字段，同样可以用 `final` 修饰。用 `final` 修饰的字段在初始化后不能被修改。

可以在构造方法中初始化 `final` 字段：
```
class Person {
    public final String name;
    public Person(String name) {
        this.name = name;
    }
}
```

## 抽象类
如果父类 `Person` 的 `run()` 方法没有实际意义，能否去掉方法的执行语句？如果父类的方法本身不需要实现任何功能，仅仅是为了定义方法签名，目的是让子类去覆写它，那么可以把父类的方法声明为抽象方法：
```
class Person {
    public abstract void run();
}
```

编译器会告诉我们，无法编译 `Person` 类，因为它包含抽象方法。必须把 `Person` 类本身也声明为 `abstract`，才能正确编译它：
```
abstract class Person {
    public abstract void run();
}
```

## 面向抽象编程
通过抽象类 `Person` 类型去引用具体的子类的实例：
```
Person s = new Student();
Person t = new Teacher();

// 不关心Person变量的具体子类型
s.run();
t.run();
```

这种尽量引用高层类型，避免引用实际子类型的方式，称之为面向抽象编程。面向抽象编程的本质就是：上层代码只定义规范（例如：`abstract class Person`）；不需要子类就可以实现业务逻辑（正常编译）；具体的业务逻辑由不同的子类实现，调用者并不关心。

## 接口
在抽象类中，抽象方法本质上是定义接口规范：即规定高层类的接口，从而保证所有子类都有相同的接口实现，这样，多态就能发挥出威力。

如果一个抽象类没有字段，所有方法全部都是抽象方法，就可以把该抽象类改写为接口（interface）：
```
abstract class Person {
    public abstract void run();
    public abstract String getName();
}
```

```
interface Person {
    void run();
    String getName();
}
```

所谓 interface，就是比抽象类还要抽象的纯抽象接口，因为它连字段都不能有。因为接口定义的所有方法默认都是 `public abstract` 的，所以这两个修饰符不需要写出来。

当一个具体的 class 去实现一个 interface 时，需要使用 `implements` 关键字：
```
class Student implements Person {
    private String name;

    public Student(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        System.out.println(this.name + " run");
    }

    @Override
    public String getName() {
        return this.name;
    }
}
```

在 Java 中，一个类只能继承自另一个类，不能从多个类继承。但是，一个类可以实现多个 interface：
```
class Student implements Person, Hello { // 实现了两个interface
    ...
}
```

## 接口继承
一个 interface 可以继承自另一个 interface。interface 继承自 interface 使用 `extends`，它相当于扩展了接口的方法。

```
interface Hello {
    void hello();
}

interface Person extends Hello {
    void run();
    String getName();
}
```

此时，`Person` 接口继承自 `Hello` 接口，因此，`Person` 接口现在实际上有3个抽象方法签名，其中一个来自继承的 `Hello` 接口。

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1252599548343744/1260464690677856)
