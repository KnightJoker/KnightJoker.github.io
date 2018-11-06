
title: 内存管理
description: 对于面向对象的编程语言而言，程序需要不断地创建对象。开始创建的时候，程序创建的所有对象通常都有指针指向它，程序可能需要访问这些对象的实例变量或者调用这些对象的方法，总之，这些对象是有用的。随着时间的流逝，当程序再次创建了一些新的对象，二那些老的对象不再使用，并且不会有指针指向它们，此时程序没有回收它们占用的内存，就会出现所谓的内存泄漏
date: 2017/12/11 08:46:25
categories:
- iOS
tags:
- 面向对象

---

## 手动内存管理

<br>

对于面向对象的编程语言而言，程序需要不断地创建对象。开始创建的时候，程序创建的所有对象通常都有指针指向它，程序可能需要访问这些对象的实例变量或者调用这些对象的方法，总之，这些对象是有用的。随着时间的流逝，当程序再次创建了一些新的对象，二那些老的对象不再使用，并且不会有指针指向它们，此时程序没有回收它们占用的内存，就会出现所谓的内存泄漏。

程序内存泄漏引起直接的反应就是，可用内存越来越少，导致应用程序的崩溃。在这里个人认为有效的内存管理，通常是指一下两个方面：

- 内存分配：当程序创建对象时需要为对象分配内存。采用合理的设计，尽量减少对象的创建，并减少对创建过程中的内存开销。

- 内存回收：当程序不在需要对象时，系统必须及时回收这些对象所占的内存，以便程序的再次利用

在这里Xcode 4.2引入了一个比较重要的新特性：**自动引用计数（Automatic Reference Counting,简称ARC）**

#### 对象的引用计数

在这里Objective - C采用了以一种被称为**引用计数(Reference Counting)**的机制来跟踪对象的状态：每个对象都有一个与之关联的整数，这个整数被称作引用计数器。在正常情况下，当一段代码需要访问某一个对象时候，该对象的引用计数加1；反之。当对象的引用计数为0时，改程序不再需要该对象，系统就会回收之前所占用的内存。

系统在销毁对象之前，会自动调用该对象的`dealloc`方法（该方法继承NSObject）来执行一些回收操作。

在手动引用计数中，改变对象的引用计数的方式如下：

- 当程序调用方法名以alloc、new、copy、mutableCopy开头的方法来创建对象时，该对象的引用计数加1。

- 调用对象的retain方法时，该对象的引用计数加1。

- 调用对象的release方法时，该对象的应用计数减1。

（这里需要注意上面所说的方法名都是指以**驼峰写法**开头的，比如，一个名为newBook的方法会导致对象的引用计数加1，反之newbook的方法则不会。）

```
还值得注意的是NSObject中提供了有关引用计数的方法，还有：

- autorelease:不改变该对象的引用计数器的值，只是将对象添加到自动释放池中。

- retainCount:返回该对象的引用计数的值。

```

在这个地方就会遇见一系列的问题了，比如：

- 在main()函数中创建一个KJItem对象

- 程序中KJUser对象持有一个KJItem对象，程序可调用KJUser的setItem：方法来设置该KJUser持有KJItem对象。

于是此时问题便出现了：该KJItem对象是属于KJUser还是属于main()函数？释放的时候又应该由谁来释放呢？

如果让main()函数来销毁KJItem，那么KJUser对象中指向KJItem的指针都变成了悬空指针。

如果让KJUser对象的dealloc方法来销毁KJItem对象，那么在这之前，KJUser的引用计数变为0，这样它所持有的KJItem也被销毁，那么接下来main()函数中指向该对象的指针也变成了悬空指针，这也变得更加危险。

这里我们便可以提供这样的一个思路，在setItem：方法中传入KJItem对象的引用计数加1，这样就可以保证，只有当main()函数调用KJItem的release方法将其引用计数减1，并且KJUser的dealloc方法再次将其引用计数减1，此时KJItem对象的引用计数为0，系统才会真正的释放KJItem对象。

下面直接看代码！(⊙o⊙)哦打~

类接口部分：

```
@interface KJUser : NSObject
{
	//定义KJUser持有KJItem对象
	KJItem* _item;
}
- (void) setItem:(KJItem *) item;
- (KJItem *) item;
```
类实现部分：

```
@implementation KJUser

- (void) setItem:(KJItem *)item
{
	if(_item != item)
	{
		//让item的引用计数加1
		[item retain];
		_item = item;
	}
}

- (KJItem *) item
{
	returen _item;
}

- (void) dealloc
{
	//让_item的引用计数减1
	[_item release];
	[super dealloc];
}
@end
```
程序重写了KJUser的dealloc方法，并在该方法中调用了_item的release方法将该对象的引用计数减1，这样就保证了setter方法中对_item引用计数的家加1相抵。

其实手动内存释放的基本思路很简单，在任何实体（包括对象、函数）在结束前应该把其他对象的引用计数恢复到开始前的状态。

main()函数部分：

```
int main(int argc , char * argv[])
{
	//调用new方法创建对象，该对象的引用计数为0
	JKItem* item = [KJItem new];
	KJUser* user = [[KJItem alloc] init];
	[user setItem:item];
	
	//main方法将item的引用计数减1，item的引用计数为1
	[item release];
	
	//user的引用计数减1，此时为0
	//系统调用user的dealloc方法，调用dealloc方法会让KJItem的引用计数减1
	//这样item的引用计数为0，系统便会自动执行item的dealloc的方法进行销毁
	[user release];
}

```
**这里要记住的是：谁（对象、函数等）把对象的引用计数加了1，谁就要负责在“临死”前将该对象的引用计数减1**

###### 使用自动释放池

为了能保证函数、方法返回的对象不会在被返回之前就被销毁，我们需要保证被函数、方法返回的对象能被延迟销毁，于是在这里就可以使用**自动释放池**

所谓自动释放池，指一个存放对象的容器（比如集合），而自动释放池就会保证延迟释放该池中所有的对象。

为了把一个对象添加到自动释放池中，可以调用该对象的autorelease方法

```
	- (id) autorelease: 该方法不会修改对象的引用计数，只是将该对象添加到自动释放池中，该方法将会返回调用该方法的对象本身。
```
借助自动释放池的功能，可以控制当函数、方法需要返回一个对象时，先调用该对象的autorelease方法。这样只要在自动释放调用该方法时，这个对象就会被添加到自动释放池中。

### 手动内存管理的规则总结

- 调用对象的release方法并不是销毁该对象，只是将该对象的引用计数减1；当一个对象的引用计数为0时，系统会自动调用该对象的dealloc方法来销毁该对象。

- 当自动释放池被回收时，自动释放池会依次调用池中每个对象的release方法。如果该对象调用release方法后引用计数为0，那么该对象将要被销毁；否则该对象可以从自动释放池中“活下来”

- 当程序使用alloc 、new、 copy、 mutableCopy开头的方法创建对象后，该对象的引用计数为1，当不再使用该对象时，需要调用该对象的release方法或者autorelease方法。

- 如果使用retain方法为对象增加过引用计数，则用完该对象后需要调用release方法来减少该对象的医用计数，并保证retain次数与release次数相等。

- 如果通过其他方法获取了对象，且该对象是一个临时对象，如果在自动释放使用该对象，那么使用完成后无须理会该对象的回收，系统会自动回收该对象。如果程序需要保留这个临时对象，则需要手动调用retain来增加该临时对象的引用计数；或者将该临时对象赋值给retain 、strong或者copy指示符修饰的属性。

- 在iOS的时间循环中，每个时间处理方法执行之前会创建自动释放池，方法执行后会回收自动释放池。如果希望自动释放池被回收后，池中某些对象能够活下来，程序必须增加该对象的引用计数，保证该对象的引用计数大于它调用的autorelease的次数。

## 自动引用计数

自动引用计数（ARC）可以避免手动引用计数可能引入的一些潜在陷阱，自动引用计数同样是基于引用计数的，只不过由系统来负责跟踪对象的引用计数，系统可以判断何时需要保持对象，何时需要自动释放对象。

换句话来说，上面瞎扯了这么一大堆，其实并没有什么鸟用，下次你再建立工程的时候，在ARC选项那里画上一个小钩钩就可以了！！(⊙﹏⊙)b


















