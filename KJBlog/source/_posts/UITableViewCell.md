title: UITableViewCell 介绍
description: UITableView 所显示的每一行都是一个独立的视图，这些视图是UITableViewCell 对象。这里我就学习了一下创建UITableViewCell 对象并且使用其来填充UITableView。
date: 2016/10/16 08:46:25
categories:
- iOS
tags:
- UI控件

---

</br>
在之前我们知道了UITableViewDataSource 协议中另外一个必须实现的方法是`tableView：cellForRowAtIndexPath：` 在实现这个方法之前，需要知道另外一个类UITableViewCell。

UITableView 所显示的每一行都是一个独立的视图，这些视图是UITableViewCell 对象。这里我就学习了一下创建UITableViewCell 对象并且使用其来填充UITableView。

</br>

### 创建并获取UITableViewCell 对象

这里我们完成之前的任务，通过UITableViewCell 对象的textLabel 属性显示Item 对象的描述信息。

对于UITableViewController 类，`tableView：cellForRowAtIndexPath：` 需要创建一个UItableView 对象，获取UITableViewCell 对象代表的Item 对象，像Item对象发送description 消息并获得消息，将得到的描述信息赋给UITableViewCell 对象的textLabel 属性，最后返回UITableViewCell。

**如何确定Item 对象和UITableViewCell 对象的对应关系？**

传入`tableView:cellForRowAtIndexPath:`的第二个实参是一个NSIndexPath 对象，该对象包含了两个属性：section（段）和row（行）。

这里在UITableViewController.m 中实现`tableView：cellForRowAtIndexPath：`，让第n行显示allItems数组中的第n个Item 对象，代码如下：

```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    //创建UITableViewCell 对象，风格默认
    UITableViewCell *cell = [[UITableViewCell alloc] initWithStyle:UITableViewStylePlain reuseIdentifier:@"UITableViewCell"];
    
    //这里的n是该UITableViewCell 对象所对应的表格行索引
    NSArray *items = [[Model shareModel] allItems];
    Item *item = items[indexPath.row];
    cell.textLabel = [item description];
    
    return cell;
    
}
```
### 重用UITableViewCell 对象

由于iOS 设备内存有限。如果某个UITableView对象要显示大量的记录，并且针对每一条记录创建相应的UITableViewCell 对象的话，这样内存资源就会被很快的消耗掉。

如图所示：

![UITableViewCell 的重用](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/UITableViewCell%20%E7%9A%84%E9%87%8D%E7%94%A8.png?raw=true)

当用户滚动UITableView 对象时候，部分的UITableViewCell 会移动出窗口。UITableView对象便会将移出的一部分放入UITableViewCell 对象池，等待重用。

当UITableView 对象要求数据源返回某个UITableViewCell 对象时，数据源可以先查看对象池。如果UITableViewCell并未存在，那就可以使用新的数据配置这个UITableViewCell，从而避免直接创建新的对象。

因为有时候需要创建UITableViewCell 的子类，用于实现特定的外观和特性，所以UITableView 对象可能会拥有不同类型的UITableViewCell 对象。如果UITableViewCell 对象池中的对象创建自不同的子类，那么UITableView 对象就有可能得到了错误类型的UITableViewCell。综上所述，就必须确保UITableView 对象能够得到指定的UITableViewCell 对象，这样才能确定返回对象拥有哪些属性和方法。

从UITableViewCell 对象池获取对象时，无须关心取回的是否是某个特定的对象。因为无论取回的是哪一个对象，都需要重新设置数据。真正关心的是取回的对象是否是某个特定类型。

*每一个UITableViewCell 对象都有一个类型为NSString 的reuseIdentifier 属性，当数据源向UITableView 对象获取可以重用的UITableViewCell 时，可传入一个字符串并要求UITableView 对象返回相应的UITableViewCell 对象，这些对象的reuseIdentifier 属性必须和传入的字符串相同。*

所以应该在UITableViewController.m中更新一下tableView：cellForRowAtIndexPath：

```

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

//    UITableViewCell *cell = [[UITableViewCell alloc] initWithStyle:UITableViewStylePlain reuseIdentifier:@"UITableViewCell"];
   
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"UITableViewCell" forIndexPath:indexPath]
    NSArray *items = [[Model shareModel] allItems];
    Item *item = items[indexPath.row];
    cell.textLabel = [item description];
    
    return cell;
    
}

    
}

```

为了能够重用UITabelViewCell对象，必须将创建的过程交由系统管理——需要告诉表视图，如果对象池中没有UITableViewCell 对象，就应该初始化哪种类型的UITableViewCell 对象

所以应该在UITbaleViewController.m中覆盖viewDidLoad方法，向UITableView 注册应该使用的UITableViewCell

```

- (void)viewDidLoad {
    
    [super viewDidLoad];

    [self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:@"UITableViewCell"];
    
}
```

