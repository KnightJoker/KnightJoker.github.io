
title: KVC与KVO

date: 2017/10/02 08:46:25
categories:
- iOS
tags:
- 面向对象

---
在关于设置和修改对象的属性，之前介绍了setter、getter的方法，也通过简化的点语法来进行设置和修改。然而Objective - C还支持以一种更为灵活的操作方式，这种方式以字符串形式间接操作对象的属性，即为Key Value Coding（简称KVC），译为键值编码。

<br>
## 简单的KVC
<br>

最基本的KVC由NSKeyvaluecoding协议提供支持，最基本的操作属性如下：

- setValue：属性值 forKey：属性名 （为指定属性设置值）

- valueForKey：属性名 （获取指定属性的值）

比如KJUser类：

```
	@property (nonatomic, copy) NSString* name;
	@property (nonatomic, copy) NSString* time;
```

下面使用KVC来设置该对象的属性，以及访问该对象的属性：

```

	KJUser* user = [[KJUser alloc] init];
	
	//使用KVC方式设置属性
	[user setValue:@"KNIGHTJOKER" forKey:"name"];
	[user setValue:[[NSDate alloc] init] forKey:"time"];
	
	//使用KVC获取对象的属性
	NSLog(@"user的name为：%@",[user valueForKey:@"name"]);
	NSLog(@"user的time为：%@",[user valueForKey:@"time"]);
```

在KVC编程方式中，无论调用setValue：forKey方法，还是调用valueForKey：方法，都是通过NSString对象来指定被操作属性的，其中forKey：标签用于传入属性名。

对于setValue：属性值 forKey：@“name”（ valueForKey：@“name”）代码，底层的执行机制如下：

1. 程序优先考虑调用“setName：属性值”方法（同理优先调用getName方法）

2. 如果该类没有setName（getName）方法，KVC机制会搜索该类名为 _name 的成员变量，无论该成员变量是在什么地方定义，也无论它使用哪一个访问控制符修饰，这条KVC代码底层实际就是对 _name成员变量赋值（返回 _name成员变量的值）。

3. 如果该类既没有setName（getName）方法，也没有 _name 成员变量，那么KVC机制任然会继续搜索该类名为name的成员变量，同理无论该成员变量是在什么地方定义，也无论它使用哪一个访问控制符修饰，KVC代码底层也会对name成员变量赋值（返回name成员变量的值）。

4. 如果以上三条都没有找到，系统将会执行该对象的setValue：forUndefinedKey（valueforUndefinedKey）方法 （该方法就是引发一个异常，这个异常将会导致程序结束）

关于处理这个地方不存在的key，我们可以采取重写setValue：forUndefinedKey（valueforUndefinedKey）方法来进行处理。

**KVC的优缺点**

实际上，通过KVC操作对象的性能比通过setter、getter方法操作的性能更差，使用KVC编程的优势更加简洁，更适合提炼一些通用性质的代码。由于KVC方式允许通过字符串形式来操作对象的属性，这个字符串可以使常量，也可以是变量，因此具有极高的灵活性。

<br>

## 键值监听（KVO）

<br>

在IOS应用开发的过程中，IOS应用通常会把应用程序组件分开成数据模型组件和视图组件，其中数据模型组件负责维护应用程序的状态数据，而视图组件则负责显示数据模型组件内部的状态数据。

对于以上的设计结构，其程序存在的需求是：在数据模型组件的状态数据发生改变时，视图组件能动态地更新自己，及时显示数据模型组件更新后的数据。

因此IOS提供了一个优秀的解决方案：利用KVO（Key Value Oberving，即键值监听）机制。

KVO机制由NSKeyValueObserving协议提供支持，当然，NSObject遵守了该协议，因此，NSObject的子类都可以使用该协议中的方法。该协议包含如下常用方法可以用于注册监听器。

- addobserver：forKeyPath：options：context：（注册一个监听器用于监听指定的Key路径）

- removeObserver：forKeyPath：（为key路径删除指定的监听器）

- removeObserver：forKeyPath：context：（为key路径删除指定的监听器，只是多了一个context参数）

对于上述的需求，很容易想到可以让视图组件来监听数据模型组件的改变，当数据模型组件的key路径对应属性值发生改变时，作为监听器的视图组件将会被激发，激发时就会回调监听器自身的监听方法，该监听方法如下：

`observeValueForKeyPath：ofObject:change:context`

由此可见，KVO编程的步骤非常简单。

1. 为被监听对象（通常是数据模型组件）注册监听器。

2. 重写监听器的observeValueForKeyPath：ofObject:change:context方法。




