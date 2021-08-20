---
title: "继承"
date: 2021-08-07T17:10:54+08:00
draft: false
categories: ["编程语言学习"]
enableMathJax: false
---

# Java中的继承
继承是java面向对象编程技术的一块基石，因为它允许创建分等级层次的类。  
继承就是子类继承父类的特征和行为，使得子类对象（实例）具有父类的实例域和方法，或子类从父类继承方法，使得子类具有父类相同的行为。

# 示例
定义一个Person类：
```Java
class Person {
    private String name;
    private int age;

    public String getName() {...}
    public void setName(String name) {...}
    public int getAge() {...}
    public void setAge(int age) {...}
}
```
现在，再定义一个Student类：
```Java
class Student {
    private String name; //重复
    private int age; //重复
    private int score;

    public String getName() {...} //重复
    public void setName(String name) {...} //重复
    public int getAge() {...} //重复
    public void setAge(int age) {...} //重复
    public int getScore() { … }
    public void setScore(int score) { … }
}
```
可以发现，`Student`类中包含了很多`Person`类已经有的属性和方法，能不能去除这些重复的代码呢？  
这里就要用到继承了，Java使用`extends`关键字来实现继承：  
```Java
class Person {
    private String name;
    private int age;

    public String getName() {...}
    public void setName(String name) {...}
    public int getAge() {...}
    public void setAge(int age) {...}
}

class Student extends Person {
    // 不要重复name和age字段/方法,
    // 只需要定义新增score字段/方法:
    private int score;

    public int getScore() { … }
    public void setScore(int score) { … }
}
```

# 注意
## 继承限制
继承有个特点，子类只能继承父类的`public`或`protected`属性和方法，无法访问`private`。
## super
super关键字表示父类(超类)，子类引用父类的字段时，可以用`super.fileName`。如：
```Java
class Student extends Person {
    public String hello() {
        return "Hello, " + super.name;
    }
}
```
在上述例子中，使用`super.name`，或者`this.name`，或者`name`，效果都是一样的。编译器会自动定位到父类的`name`字段。  
但在如下情况中就必须使用`super`：
```Java
public class Main {
    public static void main(String[] args) {
        Student s = new Student("Xiao Ming", 12, 89);
    }
}

class Person {
    protected String name;
    protected int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

class Student extends Person {
    protected int score;

    public Student(String name, int age, int score) {
        this.score = score;
    }
}
```
运行上面的代码，会得到一个编译错误，大意是在`Student`的构造方法中，无法调用`Person`的构造方法。  
这是因为在Java中，任何class的构造方法，第一行语句必须是调用父类的构造方法。如果没有明确地调用父类的构造方法，编译器会帮我们自动加一句`super()`;，所以，`Student`类的构造方法实际上是这样：
```Java
class Student extends Person {
    protected int score;

    public Student(String name, int age, int score) {
        super(name, age); // 自动调用父类的构造方法，需要加上父类的构造参数
        this.score = score;
    }
}
```