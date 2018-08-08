title: Swift 笔记
description: 记录一些swift学习过程中遇见的一些问题，过程顺序可能比较乱
categories:
- iOS
tags:
- swift
---

在学习的过程中，尽量会对比 Objective-C 里面的一些概念来说，这样可能有助于一些 Objective-C 程序猿对于 Swift 入门，（当然这样比较方便我个人去理解 Swift里面的一些概念，以及设计理论）,这里记录的一些问题顺序可能会比较乱（因为基于个人学习过程中，想到哪就写到哪，后期可能会不定时整理一下）,这里主要参考[文档](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID309)

Swift 写分号是可选的，类似 JavaScript （注意编码风格）

# 类（对象）和结构体区别

一个老生常谈的问题了，个人的理解在于三点：

1. 结构体只能封装属性，类不仅有属性还有方法 (Swift中类、结构体、枚举也可以定义方法)
2. 结构体内存分配在栈，类分配在堆
3. 结构体是值传递，类是指针传递p
    ```
        关于第三点，举个具体的栗子来说：

        struct a = b; (复制值后，直接储存在栈区，b发生改变后不会影响a的值)
        class *a = b; (a,b 指针存在栈区，对象实例存储在堆区，对象的值发生改变，a,b同时发生改变)
    ```

考虑使用结构体的条件：

- 该数据结构的主要目的是用来封装少量相关简单数据值。
- 有理由预计该数据结构的实例在被赋值或传递时，封装的数据将会被拷贝而不是被引用。
- 该数据结构中储存的值类型属性，也应该被拷贝，而不是被引用。
- 该数据结构不需要去继承另一个既有类型的属性或者行为。

# Arrays、Sets、Dictionaries

在 Swift 中，主要使用 let 和 var 来区分变量和常量，所以这里就没有（NSArray和 NSMutableArray）这样的区别了

这里主要说一下 Sets (参考 Objective - C 中的 NSSet)，不过看见这里的时候，却发现实际开发中，真的很少使用到这个数据结构，能想到的只有去重（利用集合特性），还有在处理 Touch 以及 Gesture 集合的时候，会用到 Sets。

# Control Flow 

有关于控制流这里就主要讲解一下 Switch ，其他的 Control Flow 可以看[这里](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID309)

下面就具体说一下 Switch 与 Objective - C 中使用的不同：

- 在 Swift 中，当匹配的 case 分支中的代码执行完毕后，程序会终止switch语句，而不会继续执行下一个 case 分支。简单来说就是不用写 break 了（我能说在 ObjC 中我经常忘记写这个嘛2333333333）。但是在 Swift 中，每一个 case 中都必须包含一个语句。

- case 匹配的对象，不仅仅限于 int 和 bool,也可以是字符、区间、元组等。

- 值绑定（Value Bindings）：case 分支允许将匹配的值绑定到一个临时的常量或变量，并且在case分支体内使用

    ```
        let anotherPoint = (2, 0)
        switch anotherPoint {
            case (let x, 0):
                 print("on the x-axis with an x value of \(x)")
            case (0, let y):
                 print("on the y-axis with a y value of \(y)")
            case let (x, y):
                 print("somewhere else at (\(x), \(y))")
        }
        // 输出 "on the x-axis with an x value of 2"
    ```
case 分支的模式可以使用where语句来判断额外的条件。（类比 SQL ？）

# Functions 

1. 在关于函数这个方面，与 Objective - C 中最大的不同就是，在 Swift 中允许函数的重载 (overload:可以理解为函数名相同，参数不同)

2. 函数的返回值，Swift 中可以利用***元组***让多个值作为一个复合值从函数中返回。反之在 Objective - c 没有办法这么直观的做到多值的返回。元组的返回值可以是 optional （表示该数值是允许为nil的，具体在代码中的表现为变量后面添加一个?号）

3. 在 Swift 函数的调用中，允许默认参数值，这就意味着当默认值被定义后，调用这个函数时可以忽略这个参数，那么实参就将会以默认值传入函数,一般将不带有默认值的参数放在函数参数列表的最前

4. 通过在变量类型名后面加入（...）的方式来定义可变参数。而一个函数最多只能有一个可变参数

5. **Closures** 闭包，在 Swift 里面，可以看做一种特殊的函数，类似 Objective - C 中的 Block (其他语言中的匿名函数)

# Inheritance （继承）

Swift 中的类并不是从一个通用的基类继承而来。如果你不为你定义的类指定一个父类的话，这个类就自动成为基类。（不继承其他类的类，在 Objective - C 中例如 NSObject 和 NSProxy）

# Automatic Reference Counting （自动引用计数）

参照 Objective - C 中的ARC，这里需要注意解决的是强引用循环，在 swift 使用 weak 和 unowned 关键字解决此项，具体两者之间的不同可以看 [官方文档](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Initialization.html#//apple_ref/doc/uid/TP40014097-CH18-ID203) 和 [喵神说明](http://swifter.tips/retain-cycle/) 

# Generics（泛型）

*泛型代码让你能够根据自定义的需求，编写出适用于任意类型、灵活可重用的函数及类型。它能让你避免代码的重复，用一种清晰和抽象的方式来表达代码的意图。如果是之前有些过 Java 或者 JavaScript 的同学应该能有所理解，不过这点在 Obejective - C 中却没有体现，也可以说是 Swift 中比较重要的特性之一了。*

这个地方也是比较重要的一点，有兴趣的同学可以[参照这里](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Generics.html#//apple_ref/doc/uid/TP40014097-CH26-ID179)


























