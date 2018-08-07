

title: 属性(Properties)
description: 声明属性，它可以使我们快速方便 的为实例变量创建存取器，并允许我们通过点语法去使用存取器。
categories: 
- iOS
tags:
- 面向对象

---


# 属性(Properties)

`@properties` 声明属性的语法，它可以使我们快速方便 的为实例变量创建存取器，并允许我们通过点语法去使用存取器。

- 存取器（accessor）:用于获取和设置实例变量的方法。用于获取实例变量值得存取器是`getter`，用于设置实例变量值得存取器是`setter`

声明一个属性，等于隐含地为相应名称的实例变量声明一对存取方法。

这里我们用实际的代码来进行说明：

`KJClass.h`

```
@interface KJClass : NSObject {
	NSString *_name;
}

- (void) setName:(NSString *)name;
- (NSString *)name;

@end

```

`KJClass.m`

```

@implementation KJClass

- (void) setName:(NSString *)name {
	_name = name;
}

- (NSString *)name {
	return name;
}

@end
```

而这里我们直接使用`@property`属性，一句代码就可以搞定上面的代码~

```
@property NSString *name;

```

这里注意如果声明了一个名为name的属性，编译器会自动生成一个实例变量_name、取方法name（也就是我们理解的getter方法）、存方法setName:（这里的实例变量以及存取方法的声明都不会出现在文件中，编译器会在编译的时候自动加入这些代码~）

**这里可以注意的是：在以后代码的书写中，大家可以尽量使用`_`调用实例变量，而少去使用`self.`方法，因为点方法实际就是调用了这个self的getter方法，而每一次方法的调用，程序都需要方法遍历一次，这样做也能大大的提高程序的效率，以及代码的可读性**

而在我们的程序中使用了.语法调用属性，程序也会自动根据你使用的环境，来自动判断你是使用了他的getter方法还是setter方法

</br>
# 属性的特性

任何的属性都可以有一组特性，用于描述相应存取方法的行为，比如：

`@property (strong,nonatomic,readonly) NSString *name;`

而这里也可以将其分为三个大类：**多线性特性、读写性、内存管理特性**

### 多线性特性

- `atomic` (默认)：这里的操作是原子性的意味着只有一个线程访问实例变量。atomic是线程安全的，至少在当前的存取器上是安全的。它是一个默认的特性，但是很少使用，因为比较影响效率，这跟ARM平台和内部锁机制有关。

- `nonatomic`表示非原子的，可以被多个线程访问。它的效率比atomic快。但不能保证在多线程环境下的安全性，在单线程和明确只有一个线程访问的情况下广泛使用。


### 读写性

- `readwrite` (默认)表示该属性同时拥有`setter`和`getter`。

- `readonly` 表示只有getter没有setter。


### 内存管理

- `assign` （默认）assign用于值类型，如int、float、double和NSInteger，CGFloat等表示单纯的复制。还包括不存在所有权关系的对象，比如常见的delegate。

- `retain`  在setter方法中，需要对传入的对象进行引用计数加1的操作。
简单来说，就是对传入的对象拥有所有权，只要对该对象拥有所有权，该对象就不会被释放。

- `strong` 表示实例变量对传入的对象要有所有权关系，即强引用。strong跟retain的意思相同并产生相同的代码，但是语意上更好更能体现对象的关系

- `weak`  在setter方法中，需要对传入的对象不进行引用计数加1的操作。
简单来说，就是对传入的对象没有所有权，当该对象引用计数为0时，即该对象被释放后，用weak声明的实例变量指向nil，即实例变量的值为0。

- `copy` 与strong类似，但区别在于实例变量是对传入对象的副本拥有所有权，而非对象本身。


