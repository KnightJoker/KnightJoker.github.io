title: UITableView 详细介绍
description: 
date: 2016/12/16 08:46:25
categories:
- iOS
tags:
- UI控件

---

关于UITableView属于iOS开发中很重要的一个UI控件了，这里我就为大家整理一下这几天有关于UITableview的一些学习。

同样这里主要参照官方API的学习，[传送门](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)在这里。

</br>
# UITableView

在很多的iOS应用界面中都会使用的一种列表控件：用户可以选中、删除或者重排列表中的条目。

首先UITableView 是一个**视图**，根据MVC的设计模式，这里的UITableView 不应该负责处理应用的逻辑或者数据。当在应用中使用UITableView 对象时，就必须考虑如何搭配其他的对象，并与其一起进行工作。

- 在通常情况下，要通过某个视图控制对象（ViewController）来创建和释放UITableView 对象，并负责显示或者隐藏视图。

- UITableView对象要有**数据源**才能正常的工作。UITableView 对象会向数据源查询要显示的行数、显示表格所需的数据和其他所需的数据。凡是遵守UITableViewDataSource 协议的Objective - C对象，都可以成为UITableView的数据源。

- 通常情况下，则要为UITableView 设置委托对象，以便能在该对象发生特定事件时做出相应的处理。而UITableView的委托对象，则需要遵守UITableViewDelegate 协议。

在这里**UITableViewController**则可以扮演以上全部的角色，包括视图的控制、数据源和委托对象。

UITableViewController 是UITableView的子类，所以也具有View 的属性。UITableViewController 对象的view 属性指向一个UITableView对象，并且这个UITableView 属性由其UITableViewController 对象负责设置和显示。UITableViewController 对象会在创建了UITableView之后，自动为UITableView 的dataSource和delegate 赋值，并且自动指向自己，这里他们的具体关系可以参照下图：

![UITableView 和UITableViewController关系图](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/UITableView%20%E4%BB%A5%E5%8F%8AUITableViewController%E5%85%B3%E7%B3%BB%E5%9B%BE.png?raw=true)

这里我们就开始创建一个UITableViewController 对象吧：

```
- (instancetype) init {
    self = [super initWithStyle:UITableViewStylePlain];
    return self;
}

- (instancetype) initWithStyle:(UITableViewStyle)style {
    return [self init];
}
```

UITableViewController 的指定初始化方法是`initWithStyle：`在调用此方法之前需要传入一个类型为UITableViewStyle 的常数，该常数决定了UITbaleView 对象的风格。然而这里可以使用的常数有UITableViewStylePlain 和 UITableViewStyleGrouped ，不过吐槽一下，在我看来两个并没有变化~顺便用！~就要这么任性。

</br>
# UITableView 数据源

在面向对象的编程中，UITableView 对象会自己查询另一个对象已获得需要显示的内容，这个对象就是UITableView 的数据源，也就是dataSource 属性所指向的对象。这里我们可以做的就是为UITableViewController 添加相应的属性和方法，使其能够保存多个Item 对象。

### 创建Model层

Model 对象是一个单例。也就是说，每个应用只会有一个这种类型的对象。如果在应用中尝试创建另一个对象，Model 类就会返回已经存在的那个对象。当一个程序要在很多不同的代码段中使用同一个对象时，将这个对象设置为单例就会很方便，只要向该对象的类发送特定的方法，就可以得到相同的对象。

首先在Model.h 中声明shareModel 类方法：

```
#import <Foundation/Foundation.h>

@interface Model : NSObject

+ (instancetype)shareModel;

@end

```
在Model.m 中实现方法

```
@implementation Model

+ (instancetype) shareModel {
    
    static Model *shareModel = nil;
    
    if (!shareModel) {
        shareModel = [[self alloc] initPrivate];
    }
    return shareModel;
}

- (instancetype) initPrivate {
    self = [super init];
    return self;
}
```

在Model 类收到了sharedModel 消息后，首先会检查是否创建了Model 的单例对象。如果已经创建，就返回已有的对象，否则先创建再返回。

在以上这段代码中，将sharedModel 指针声明为了**静态变量**。当某个定义了静态变量的方法返回时，程序不会释放相应的对象。静态变量和全局变量一样，并不是保存在栈中的。

sharedModel 变量的初始值为nil。当程序第一次执行sharedModel 方法时候，就会创建一个Model对象，并且将新创建对象的地址赋给sharedModel 变量。以后程序再次执行sharedModel 方式的时候，sharedModel变量依然会指向最初的那个Model 对象。因为指向Model 对象的sharedModel 变量是强引用，且程序永远不会释放该变量，所以sharedModel 变量所指的Model 对象也永远不会被释放。

UITableViewController需要创建一个新的Item 对象时会像Model对象发送信息，收到消息的Model 对象会创建一个Item 对象并将其保存到一个Item 数组中，之后UITableViewController 可以通过该数组获取所有的Item 对象。

在Model.h中声明一个方法和属性，分别用于创建和保存Item 对象：

```
#import <Foundation/Foundation.h>

@class Item;

@interface Model : NSObject

@property (nonatomic,readonly)NSArray *allItems;

+ (instancetype)shareModel;
- (Item *)createItem;

@end
```

这段代码使用了@class 指令。该指令的作用是告诉编辑器，某处代码定义了一个名为Item 的类。当某个文件只需要使用Item 类的声明，无须知道具体的实现细节时，就可以使用该指令。使用该指令后，不用在Model.h中导入Item.h,就能将createItem 方法的返回类型声明为指向Item 对象的指针。当某个类的头文件发生变化时，就那些通过@class 指令声明该类的其他文件，编译器就可以不用重新编译了。

*这里注意Model 管理Item数组——包括添加、删除和排序。因此，除了Model 之外的类都不应该对Item 数组做这些操作了。在Model 内部，需要将Item 数组定义为可变数组，然而对于其他类来说，Item则是一个不可变的数组，这是一种常见的设计模式。*

接着就是在Model.m的类扩展中声明一个可变数组。

```
@interface Model ()

@property (nonatomic) NSMutableArray *privateItems;

@end

@implementation Model

+ (instancetype) shareModel {
    
    static Model *shareModel = nil;
    
    if (!shareModel) {
        shareModel = [[self alloc] initPrivate];
    }
    return shareModel;
}

- (instancetype) initPrivate {
    self = [super init];
    
    if (self) {
        _privateItems = [[NSMutableArray alloc] init];
    }
    return self;
}

- (NSArray *)allItems {
    return self.privateItems;
}

- (Item *)createItem {
    Item *item = [[NSArray alloc] init];
    
    [self.privateItems addObject:item];
    
    return item;
}

@end

```

### 实现数据源方法

代码如下：

```
#import "Model"
- (instancetype) init {
    
    self = [super initWithStyle:UITableViewStylePlain];
    
    if (self) {
        for (int i = 0; i < 5; i++) {
            [[Model shareModel] createItem];
        }
    }
    return self;
}
```


将若干Item 对象加入Model 对象之后，下面就需要让ViewController 对象将这些Model 对象转变成UITableView 对象可以显示的表格行，当某个UITableView 对象要获取显示数据时，会向其数据源发送一组特定的消息。这些消息都是在UITableViewDataSource 协议中声明的。

想让UITableViewController 遵守 UITableViewDataSource 协议，就必须让其实现`tableView：numberOfRowsInSetion：`和`tableView：cellForRowAtIndexPath：`这两个必需的方法。UITableView 对象就可以通过数据源对象的这两个方法获得应该显示的行数以及显示各行所需的视图。

当某个UITableView 对象要显示表格内容时，会向自己的数据源（dataSource属性所指的对象）发送一系列消息，其中包括`tableView：numberOfRowsInSection：`（必需方法）会返回一个整型值，代表UITableView 对象显示的行数。**对于UITableView 对象来说，Model 中的每一个Item 对象都应该对应一个表格行**

在UITableViewController.m中实现`tableView:numberOfRowsInSection:`方法


```
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section{
    return [[[Model shareModel] allItems] count];
}

```


传入tableView：numberOfRowsInSection：方法的section 参数起什么作用？UITableView 对象可以分段显示数据，每个表格段（section）包含一组独立的行。

UITableViewDataSource 协议中另外一个必须实现的方法是tableView：cellForRowAtIndexPath： 在实现这个方法之前，需要知道另外一个类UITableViewCell。