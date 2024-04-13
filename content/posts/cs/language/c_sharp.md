---
title: "C#学习笔记"
date: 2023-04-08T08:33:35+08:00
draft: true
categories: ["编程语言"]
tags: ["笔记", "语言"]
---

## 1. 可空类型(Nullable)

可空类型为基础**值类型**增加了一个`null`值, 有点像rust里的Result。主要用于以下场景：

1. **数据库交互**：数据库中的字段通常允许`null`值, 而 Nullable 可以允许数据库中的这些值得到准确处理
2. **数据解析**：在处理可能不完整或未知的数据时, 提供了一种区分“无值”和“零值”的方式。
3. **逻辑表示**：在某些逻辑场景中, `null`可以表示“未知”或“不适用”的情况, 而不是一个明确的值

```csharp
// Nullable 的定义
int? a = 3;
Nullable<int> b = new Nullable<int>(3);     // 两种写法等价
int? c = null;

// Nullable 的方法
Console.WriteLine(a.HasValue);      // true
Console.WriteLine(c.HasValue);      // false
Console.WriteLine(b.Value);         // 3
Console.WriteLine(c.Value);         // InvalidOperationException

// null 合并运算符"??"
Console.WriteLine(c ?? 1);     // 如果 c 为 null, 则把 1 赋给 表达式, 此处输出 1
Console.WriteLine(a ?? 1);     // 3
```

## 2. 重载(overload)和覆写(override)

### 重载(overload)

overload 更多地关注如何使用**相同的方法名**来执行基于**不同参数的不同操作**

这可以被看作是一种便于调用的技术, 像是函数的**多版本定义**

```csharp
public class Example
{
    public int Add(int x, int y)
    {
        return x + y;
    }

    public double Add(double x, double y)
    {
        return x + y;
    }

    public int Add(int x, int y, int z)
    {
        return x + y + z;
    }
}

public class Test
{
    public static void Main(string[] args)
    {
        Example example = new Example();
        Console.WriteLine(example.Add(1, 2));           // 3
        Console.WriteLine(example.Add(1.0, 0.5));       // 1.5
        Console.WriteLine(example.Add(1, 2, 3));        // 6
    }
}
```

### 覆写(override)

方法覆写是面向对象编程的一个核心概念, 它允许子类改变继承自父类的一个方法的行为

这是**多态性**的直接体现, 即同一个方法调用可以根据调用它的对象的实际类型而表现出不同的行为

```csharp
public class BaseClass
{
    public virtual void Display()
    {
        Console.WriteLine("Display from BaseClass");
    }
}

public class DerivedClass : BaseClass
{
    public override void Display()
    {
        Console.WriteLine("Display from DerivedClass");
    }
}

public class Test
{
    public static void Main(string[] args)
    {
        BaseClass baseClass = new BaseClass();
        DerivedClass derivedClass = new DerivedClass();

        baseClass.Display();            // Display from BaseClass
        derivedClass.Display();         // Display from DerivedClass
    }
}
```

## 3. 运算符重载

```csharp
public static Box operator+ (Box b, Box c)      // 参数数量与运算符可携带操作数一致
{
   Box box = new Box();
   box.length = b.length + c.length;
   box.breadth = b.breadth + c.breadth;
   box.height = b.height + c.height;
   return box;
}
```

## 4. 预处理指令

|预处理器指令|描述|
|:----------|:--|
|#define|用于定义一系列成为符号的字符|
|#undef|用于取消定义符号|
|#if|用于测试符号是否为真|
|#else|用于创建复合条件指令, 与 #if 一起使用|
|#elif|用于创建复合条件指令|
|#endif|指定一个条件指令的结束|
|#line|可以修改编译器的行数以及（可选地）输出错误和警告的文件名|
|#error|从代码的指定位置生成一个错误|
|#warning|从代码的指定位置生成一级警告|
|#region|在vscode的大纲中, 指定一个可展开或折叠的代码块|
|#endregion|标识着 #region 块的结束|

### \#define 和 \#if

在程序调试和运行上有重要的作用。

比如预处理器指令可以禁止编译器编译代码的某一部分, 这在计划发布不同版本的软件时有重要作用

例如企业版和基础版、DEBUG版和RELEASE版

```csharp
#define DEBUG   // define指令要在最上面

using System;

class Test
{
    public static void Main(string[] args)
    {
#if DEBUG
        Console.WriteLine("这是DEBUG版代码");
#endif
        Console.WriteLine("hello, world");
    }
}

/*
 * 以上代码将得到两行输出
 * 若没有 #define DEBUG, 或是在之后 #undef DEBUG
 * 将只得到 hello, world输出
 */
```

### \#warning 和 \#error

编译器遇到这两个预处理指令时会分别产生警告和错误, 显示在编译器 log 中(在 vscode 或者 vs 中, 表现为黄色波浪线和红色波浪线)

其中 warning 指令只会跳出警告, 编译继续执行; 而error 指令会使编译停止

```csharp
#if DEBUG && RELEASE  
#error "请勿同时设置DEBUG和RELEASE"  
#endif  
#warning "这段代码还没有改完"  
Console.WriteLine("我最讨厌两种人, 一种是话说到一半的, ");
```

### \#line

用的非常少, 作用是改变编译器用于调试和报告错误的文件名和行号

例如某段代码是从一个 txt 格式的文档模板中生成的：

```txt
// Template.txt
1: // 模板开始
2: public class GeneratedClass
3: {
4:     public void GeneratedMethod()
5:     {
6:         // 一些生成的代码
7:         Console.WriteLine("Inside generated method");
8:         // 更多生成的代码
9:     }
10: }
```

而生成的代码的行号和模板文件行号并不相同：

```csharp
22: public class GeneratedClass
23: {
24:     public void GeneratedMethod()
25:     {
26:         // 一些生成的代码
27:         Console.WriteLine("Inside generated method");
28:         // 更多生成的代码
29:     }
30: }
```

而如果这段代码出现错误, 我们应当去修改模板文件, 而不是这里的代码, 此时就需要用的 #line 指令来帮助定位：

```csharp
22: #line 2 "Template.txt"      // 这一行告诉编译器, 从下一行开始, 应该将文件视为“Template.txt”, 并且从文件的第 2 行开始计数
23: public class GeneratedClass
24: {
25:     public void GeneratedMethod()
26:     {
27:         // 一些生成的代码
28:         Console.WriteLine("Inside generated method");
29:         // 更多生成的代码
30:     }
31: }
32: #line default               // 将文件名和行号重置为默认行为, 即恢复之前的调整
```

## 5. 异常处理

### 关键词

- try：标识了一个可能发生异常的代码块。后跟一个或多个 catch 块
- catch：捕获 try 标识的代码块的异常
- finally：标识的代码块不管异常是否被抛出都会执行
- throw：当问题出现时, 程序抛出一个异常

### 语法

```csharp
try
{
   // 可能引起异常的语句
}
catch( ExceptionName e1 )
{
   // 错误处理代码 1
}
catch( ExceptionName e2 )
{
   // 错误处理代码 2
}
finally
{
   // 必须要执行的语句
}
```

在正常编写代码时, 也可以使用`throw`关键字来抛出异常

```csharp
if (someConditionIsTrue)
{
    throw new InvalidOperationException("错误信息");
}
```

在 catch 代码块中, 也可以再次使用`throw`关键字来继续抛出原先的异常, 或者新的异常

```csharp
try
{
    // 一些可能会引发异常的代码
}
catch (Exception ex)
{
    // 对异常进行一些处理, 例如记录日志
    LogException(ex);
    
    // 重新引发同一个异常
    throw;

    // 或是引发一个新的异常
    throw new InvalidOperationException("错误信息");
}
```

### 自定义异常

用户也可以自定义异常, 这些异常派生自`ApplicationException`类

```csharp
class Test
{
    public class IsZeroException : ApplicationException
    {
        public IsZeroException(string message) : base(message)      // base(message) 调用了基类中的构造函数, 并使用message作为参数
        {
        }
    }

    public static void ErrorTest(int a)
    {
        if (a == 0)
        {
            throw new IsZeroException("can't be zero");
        }
        else
        {
            Console.WriteLine(a);
        }
    }

    public static void Main(string[] args)
    {
        try
        {
            ErrorTest(0);
        }
        catch(IsZeroException e)
        {
            Console.WriteLine(e.Message);       // can't be zero
        }
        finally { Console.WriteLine("over"); }
    }
}
```
