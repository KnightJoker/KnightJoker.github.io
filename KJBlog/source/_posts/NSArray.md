title: 数组
description: NSArray 代表元素有序、可重复的一个集合，集合中每个元素都有其对应的顺序索引。NSArray 集合允许使用重复元素，可以通过索引来访问指定位置的集合元素。因为NSArray 集合默认按元素的添加顺序设置元素的索引，默认从0开始
categories:
- iOS
tags:
- Foundation框架

---

## 数组（NSArray 与 NSMutableArray）

NSArray 代表元素有序、可重复的一个集合，集合中每个元素都有其对应的顺序索引。

NSArray 集合允许使用重复元素，可以通过索引来访问指定位置的集合元素。因为NSArray 集合默认按元素的添加顺序设置元素的索引，默认从0开始。

#### NSArray 的功能与用法

NSArray 分别提供了类方法和实例方法来创建NSArray，两种创建方式需要传入的参数基本相似，只是类方法以array开始，而实例方法则是以init开头，这里我仅仅介绍几种常见的方法：

- array： 创建一个不包含任何元素的空NSArray。

- arrayWithContentsOfFile：（initWithContentsOfFile：） 读取文件内容来创建NSArray

- arrayWithObject：（initWithObject：） 创建只包含指定元素的NSArray

一旦得到了NSArray 对象，接下来咱们就可以调用它的方法来操作NSArray集合。这里集合最大的特点就是集合元素有索引。因此参考NSArray 的[文档](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/)，我们可以看到NSArray 集合的方法大致包含如下几类：

- 查询集合元素在NSArray中的索引。

- 根据索引值取出NSArray集合中的元素。

- 对集合元素整体调用方法。

- 对NSArray集合进行排序。

- 取出NSArray 集合中的部分集合组成新集合。

然后具体用法，看我代码！

```
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        NSLog(@"Hello, World!");
        NSArray* array = [NSArray arrayWithObjects:@"1",@"2",@"3",@"4",nil];
        NSLog(@"first: %@",[array objectAtIndex:0]);
        NSLog(@"last: %@",[array lastObject]);
        
        //获取索引为2的元素开始，以及后面的两个元素组成的新集合
        NSArray* array1 = [array objectsAtIndexes:[NSIndexSet indexSetWithIndexesInRange:NSMakeRange(2,2)]];
        NSLog(@"%@",array1);
        
        //获取元素在集合中的位置
        NSLog(@"2 的位置在：%ld",[array indexOfObject:@"2"]);
        
        //向数组的最后追加一个元素
        //愿NSArrary本身没有改变，只是将新返回的NSArray赋给array
        array = [array arrayByAddingObject:@"5"];
        
        //向数组的最后追加另一个数组的所有元素
        array = [array arrayByAddingObjectsFromArray:[NSArray arrayWithObjects:@"6",@"7",nil]];
        
        for (int i =0 ; i < array.count; i++) {
            NSLog(@"%@",array[i]);
        }
        
    }
    return 0;
}

```

上面程序开始创建了一个NSArray对象，创建NSArray对象时，可以直接传入多个元素，其中最后一个nil表示NSArray元素结束，但是这个nil并不会存入NSArray集合中。

然后大家就可以根据以上文档中的各种方法来对array进行各种各样的操作啦~

#### 可变数组（NSMutableArray）

NSArray 代表集合元素不可变的集合，一旦NSArray 创建成功，程序不能向集合中添加新的元素，不能删除集合中已有的元素，也不能替换集合元素。

这里NSArray 有一个子类：NSMutableArray，这里我们可以把它当做NSArray来进行使用，但在这基础上，它又代表了一个集合元素可变的集合，再次并额外添加了如下的几种方法：

- 添加集合元素：这类方法以add开头

- 删除集合元素：常以remove开头

- 替换集合元素：这里方法则是replace开头

- 对集合本身排序的方法：sort开头

