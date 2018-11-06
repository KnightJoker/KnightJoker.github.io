title: 协议与委托
description: 协议不提供任何实现。（协议定义的是多个类共同的公共行为规范，这就意味着协议里通常定义是一组公用的方法，方法的实现则交给类去完成）
date: 2016/12/07 08:46:25
categories:
- iOS
tags:
- 面向对象

---

### 协议（protocol）
<br>

协议是Objective - C的一个重要的知识点，其作用类似于接口，用于多个类应该遵守的规范。

**规范、协议与接口**

`规范 → 类 → 对象`

由以上可以看出，同一个类的内部状态数据、各种方法的实现细节完全相同，类是一种具体实现体。而协议则定义了一种规范，协议定义某一批类所需要遵循的规范，它不关心这些类的内部状态数据，也不关心这些类里方法的实现细节，它只规定这批类中必须提供某些方法，提供这些方法的类就可以满足实际需要。

协议不提供任何实现。（协议定义的是多个类共同的公共行为规范，这就意味着协议里通常定义是一组公用的方法，方法的实现则交给类去完成）

#### 使用类别实现非正式协议

**类别（category）：** Objective - C的动态特征允许使用类别为现有的类添加新方法，并且不需要创建子类，不需要访问原有类的源代码。通过使用类别即可动态地为现有的类添加新方法，而且可以将类定义模块化分布到多个相关文件中。

其同样由接口和实现部分组成，**接口部分**语法格式如下：

```
	@interface 已有类 （类别名）
	//方法定义
	@end
```

类别实现部分的语法格式如下：

```
	@implmentation 已有类 （类别名）
	//方法实现
	@end
```

注意上面的语法格式，虽然这个语法格式看上去很像在定义类，但在类名后面有一个圆括号，而且圆括号中带一个类别名，并且存在以下差异：

- 定义类时使用的类名必须是该项目中没有的类，而且定义类别时使用的类名必须是已有的类。

- 定义类别时必须使用圆括号来包含类别名。

- 类别中通常只定义方法。

这里注意Xcode并不强制遵守非正式的类必须实现该协议中所有的方法，但如果该类没有实现非正式协议中的某个方法，那么程序运行时如果调用该方法，就会引发`unrecognized selectoe`错误。

#### 正式协议

**正式协议的定义：**

和定义类不同，正式协议不再使用@interface、@implementation 关键字，而是使用@protocol，其基本语法如下：

```
	@protocol 协议名 <父协议1，父协议2>
	{
		//零到多个方法定义
	}
```

关于以上语法，在我看来：

- 协议名与类名采用相同的命名规则。

- 一个协议可以有多个直接父协议，但协议只能继承协议，不能继承类。

- 协议中定义的方法只有方法签名，没有方法实现；协议中的方法可以是类方法，也可以是实例方法。

- 协议中所有的方法都是公开的访问权限。

**遵守（实现协议）：**

在类定义的接口部分可指定该类继承的父类，以及遵守的协议，语法如下：

```
	@interface 类名 ： 父类 <协议1，协议2>
```

如果在程序中需要使用协议来定义变量，有如下两种语法：

- NSObject <协议1，协议2>* 变量；

- id<协议1，协议2> 变量;

通过上面的语法格式定义的变量，他们的编译类型仅仅只是所遵守的协议类型，因此只能调用该协议中的定义的方法。

这里则要特别注意，实现正式协议则必须实现协议中定义的所有方法。然而为了弥补这点造成的灵活性不足，在OC 2.0新增了@optional、@required 两个关键字：

- @optional ：位于该关键字之后、@end之前声明的方法是可选的——实现类即可选择实现这些方法，也可不实现这些方法。

- @required ：位于该关键字之后、@end之前声明的方法是必须的——实现类必须实现这些方法。如果没有实现这些方法，编译器就会提示警告。且@required 是默认行为。

在正式的协议中通过使用@optional、 @required 两个关键字，正式协议完全可以替代非正式协议的功能。

<br>
### 委托（delegate）

<br>
协议体现的是一种规范，定义协议的类可以把协议定义的方法委托给实现协议的类，这样可以让类定义具有更好的通用性质，因为具体的动作交给协议的实现类去完成，在各种iOS应用开发中，需要大量的依赖委托这个概念。

这里我们就需要举个栗子来说明一下这个delegate了：
> 假设你的软件有一个表格(UITableView)界面，当你点击了表格中的某行(cell)，这个表格就说：“点我干嘛？我负责显示而已，其他事不关我的事。一边凉快去！”。不过，这个表格看到你猛搓屏幕、欲哭无泪的可怜样子，于是心软了，决定帮帮你。表格就拜托(委托)它的一个朋友帮忙，名字挺洋气的，叫“UITableViewController”(以下简称C)，C就很仗义地帮忙了，当你再点击的时候，C就利用tableView: didSelectRowAtIndexPath:方法，让你点击了表格后，有了相关的反馈(比如跳入下一页)。

以上——“表格”委托“UITableViewController”处理点击事件。这就是“委托/ Delegation”，它就是一种思想，一种思路，一种“设计模式”。

所以，“委托”的使用，只是为了降低程序的耦合性，提高代码重复利用的价值。


