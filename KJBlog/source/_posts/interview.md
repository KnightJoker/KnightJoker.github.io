title: Some iOS/ObjC Questions
date: 2016-07-05 14:16:00 
description: 个人整理一些有价值的问题。如果有什么不对的地方，欢迎大家在文章下方留言，或者issure
categories:
- iOS
tags:
- interview

---

# 常识

- 面向对象基础：什么是类、接口、实例变量、方法、类方法 vs 实例方法、头文件 vs 实现文件

- 什么是进程和线程？

```
    进程：是具有一定独立功能的程序、它是系统进行资源分配和调度的一个独立单位
    线程：线程是进程的一个实体,是CPU调度和分派的最小基本单位

    1. 一个程序至少有一个进程,一个进程至少有一个线程.
    2. 进程是系统进行资源分配的基本单位，有独立的内存地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响；线程是CPU调度的基本单位，没有单独地址空间，有独立的栈，局部变量，寄存器，程序计数器等，一个线程卡死后影响整个进程奔溃。
    3. 进程切换开销大，线程切换开销小；进程间通信开销大，线程间通信开销小。

```

- 设计模式

- 黑/白盒测试区别、单元测试？集成测试？回归测试？

- Git Flow

- 堆和栈区别

```
堆栈空间分配
栈（操作系统）：由操作系统自动分配释放 ，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。
堆（操作系统）： 一般由程序员分配释放， 若程序员不释放，程序结束时可能由OS回收，分配方式倒是类似于链表

堆栈缓存方式
栈使用的是一级缓存， 他们通常都是被调用时处于存储空间中，调用完毕立即释放。
堆则是存放在二级缓存中，生命周期由虚拟机的垃圾回收算法来决定（并不是一旦成为孤儿对象就能被回收）。所以调用这些对象的速度要相对来得低一些。

堆栈数据结构区别
堆（数据结构）：堆可以被看成是一棵树，如：堆排序。
栈（数据结构）：一种先进后出的数据结构

```

# ObjC 基础

- ObjC 中面向对象的概念和关键词：接口，实现，继承，属性，协议等

`注意在 ObjC 中， 并不支持多继承，但是可以利用 runtime 、消息转发实现类似多继承`

- 应用的生命周期，应用程序使用的状态以及方法。详细内容可以看[这里](http://www.open-open.com/lib/view/open1491967149927.html)

- 什么是属性？如何创建一个属性？使用属性的优点

```
    @property = ivar(实例变量) + getter + setter;

    而在实际开发中，我们则会使用很多的关键词对 property 进行修饰
    这里也可以分为三个大类：
        1. 存取：readonly、readwrite
        2. 内存操作：
             - strong（强引用，对象的引用计数+1，一个对象只有当有强指针指向它，才会被保留在内存中，否则就会被销毁）
             - weak （弱引用，不改变对象的引用计数，对象被释放的时候，指针为 nil）
             - assign (用于基础数据类型，值复制，不改变对象的引用计数)
             - copy (地址复制，不改变原对象的引用计数)
        3. 原子性：
             - atomic (默认，原子操作，线程同步，保证线程安全，但影响性能)
             - nonatomic (非原子操作，不保证线程安全，效率较快，开发中比较常用)
```

- 如何检查一个对象中的 @optional 协议方法

```
if(self.delegate && [self.delegate respondsToSelector:@selector(closed)]){
    [self.delegate closed];
}
// 注意这里的respondsToSelector，在protocol中需要继承NSObject
```

- 什么是category？extension 和 category 的区别? [详情](http://tech.meituan.com/DiveIntoCategory.html)

- 正式协议与非正式协议 [具体](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Protocol.html)

```
    正式协议： 客户端实现预期类的方法列表
    非正式协议：NSObject 的类别 
```

- 如何声明受保护的方法（仅适用于某些专用类）？

```
使用category 分割头文件，并且包含在其他类中
```

- 什么是指定初始化(designated initializer)?为什么经常 if (self = [super …])

```
- (instancetype)init {
         if (self = [super init]) {
                // Custom initialization
        }
         return self;
}

// 我们知道对象继承的概念，一个子类从父类继承，那么也要实现父类的所有功能，这就是is-a的关系。所以在子类的初始化方法中，必须首先调用父类的初始化方法，以实现父类相关资源的初始化。
```
- 对象之间的相互通信

```
	delegate(这里需要知道delegate通常使用weak修饰，而不用assign，两者都是防止循环引用而导致内存泄漏，而weak为nil的时候，直接释放掉对象，assign为nil则会导致野指针。经常看见代码下ARC会用weak修饰，而MRC则只能使用assign)

	block(带有自动变量的匿名函数，而每一个函数的调用都是在栈区，因此他的消亡不需要我们去控制，但如果我们使用block作为一个对象的话，我们会使用copy进行修饰，这样就可以把block的实现拷贝一份到堆区，这样便拥有该block的所有权，而新建一个指针(__weak typeof(Target) weakTarget = Target )指向Block代码块里的对象，然后用weakTarget进行操作。就可以解决循环引用问题。)

	notification

	target-action
```
- 什么是@selector?怎么创建一个selector

```
类似于C语言中的函数指针，而 Objective - C 中的类不能直接应用函数指针，这样只能做一个@selector语法来取，
它的结果是一个SEL类型。这个类型本质是类方法的编号(函数地址)
```

- ObjC 中方括号和点语法的区别

```
历史发展原因参考 C++ ，而在ObjC 中，方括号代表消息传递，而点语法表示属性访问。
```

- KVO

```
其实就是ObjC 中对观察者设计模式的一种实现。
当你观察一个对象时，会动态的创建该对象类的子类，这个子类重写了被观察属性 keyPath 的 setter 方法，同时将该对象的 isa 指针指向了新创建的子类。在 Objective-C 中对象是通过 isa 指针来查找对应类中的方法列表的，所以这里可以把该对象看为新子类的实例对象。重写的 setter 方法会在调用原 setter 方法之后，通知观察者对象属性值的更改。
```


- KVC

```
它是一种可以通过字符串的名字（key）来访问类属性的机制。而不是通过调用Setter、Getter方法访问。关键方法定义在 NSKeyValueCodingProtocol、KVC支持类对象和内建基本数据类型。

```

- 什么是快速枚举？[详情](http://blog.leichunfeng.com/blog/2016/06/20/objective-c-fast-enumeration-implementation-principle/)

- 判断类的所属

```
isKindOfClass           :实例方法判断
isMemberOfClass         :类方法
isSubclassOfClass       :类型完全匹配的时候才会返回YES
```

# 线程

- 串行、并行、同步和异步

```
串行并行针对的是队列，同步异步针对的是任务
```

- 什么是死锁？怎样发生的？

```
简单来说就是A线程等待B线程结束后执行，B线程等待A线程结束后执行，固发生死锁。
```

- 如何保证线程安全(线程同步和超时)？ [详细](http://www.jianshu.com/p/938d68ed832c)

























