title: 对象复制
description: copy方法用于复制对象的副本。通常来说，copy方法总是返回对象的不可修改的副本，即使该对象本身是可修改的。mutableCopy 方法用于复制对象的可变副本。mutableCopy 方法总是返回该对象的可修改的副本，即使被复制的对象本身是不可以修改的，调用mutableCopy方法复制出来的副本也是可修改的。
date: 2016/09/11 08:46:25
categories:
- iOS
tags:
- 面向对象

---

### copy与mutableCopy 方法

<br>
NSObject类提供了copy和mutableCopy方法，通过这两个方法即可复制已有对象的副本。

- copy方法用于复制对象的副本。通常来说，copy方法总是返回对象的不可修改的副本，即使该对象本身是可修改的。

- mutableCopy 方法用于复制对象的可变副本。简单说，mutableCopy 方法总是返回该对象的可修改的副本，即使被复制的对象本身是不可以修改的，调用mutableCopy方法复制出来的副本也是可修改的。

无论如何，copy和mutableCopy 返回的总是原对象的副本，当程序对复制的副本进行修改时，原对象通常不会受到影响。不过在这里需要注意，如果我们自定义的类调用copy和mutableCopy 方法来复制副本的时候。这里需要注意的是：

- 使用copy方法，需要实现NSCopying协议，以及copyWithZone：方法。

- 使用mutableCopy方法，则需要实现NSMutableCopying协议，以及mutableCopyWithZone： 方法。

当程序调用对象的copy方法来复制自身时，程序底层需要调用copyWithZone：方法来完成实际的复制工作，copy返回的实际上就是copyWithZone：方法的返回值；同理有关于mutableCopy。

#### 深拷贝与浅拷贝

- 深拷贝 : 拷贝出来的对象与源对象地址不一致! 这意味着修改拷贝对象的值对源对象的值没有任何影响.

- 浅拷贝 : 拷贝出来的对象与源对象地址一致! 反之修改拷贝对象的值会直接影响到源对象.

此处暂时没写代码，以后遇见实际问题的时候，赖在给予例子。