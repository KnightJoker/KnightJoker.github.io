title: 集合
description: 
categories:
- iOS
tags:
- Foundation框架

---

集合，它类似于一个罐子，一旦把对象“丢进”集合，其中多个对象没有明显的顺序。并且在集合中也不允许包含相同的元素，如果试图把两个相同的元素放在同一个集合中，则仅保留一个元素。

## 集合(NSSet)

<br>

NSSet 是一个广泛使用的集合，NSSet 按Hash 算法来存储集合中的元素，因此具有很好的存取和查找性能。

NSSet不能保证元素的添加顺序，顺序有可能发生变化。与NSArray 相比，NSSet 最大的区别是元素没有索引，因此不能按照索引来进行操作元素。不过NSSet和NSArray 在调用机制方面都有着很多的相似点：

- 都可以通过count方法获取集合元素的数量

- 都可以通过快速枚举来遍历集合元素

- 都提供了makeObjectsPerformSelector:（makeObjectsPerformSelector:withObject:)方法对集合元素整体调用某个方法，以及enumerateObjectsUsingBlock:(enumerateObjectsWithOptions:usingBlock)对集合整体或者部分元素迭代执行代码块

- 可以KVC和KVO编程

这里从功能上来看，NSArray 相当于是NSSet 的子类——虽然Objective - C 在语法上面没有这样的支持，但是在这里也需要重点注意一下这两者的区别：NSSet 代表集合元素无索引并且不允许重复的集合；而NSArray 则代表集合元素有索引且允许重复的集合，还可以通过索引来操作集合元素。

#### NSSet 判断集合元素重复的标准

<br>

当向NSSet 集合中存入一个元素的时候，NSSet 会调用该对象的hash 方法来得到该对象的hashCode 值，然后根据该hashCode 值决定该对象在底层Hash 表中储存的位置。如果根据hashCode 计算出来该元素在底层Hash 表中的存储位置已经不相同，那么系统自然将它们存在不同的位置。

如果两个元素的hashCode 相同，接下来就要用过isEqual：方法判断两个元素是否相等，如果有两个元素通过isEqual:方法比较返回NO,NSSet 依然认为它们不相等，NSSet 会把它们都存在底层Hash 表的同一个位置，只是将在这个位置形成链；如果它们通过 isEqual: 比较返回YES，那么NSSet 认为两个元素相等，后面的元素添加失败。

简单来说：HashSet 集合判断两个元素相等的标准如下：

- 两个对象通过isEqual:方法比较返回YES、

- 两个对象的hash方法返回值也相等