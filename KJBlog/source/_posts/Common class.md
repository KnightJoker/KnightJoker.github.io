title: Foundation框架中的常用类

categories:
- iOS
tags:
- Foundation框架

---

在我们编译Objective - C程序时看见，通常都是指定-framework Foundation 选线，该选项指定编译程序需要使用Foundation框架。其主要为开发iOS应用的基础框架，其中又包括了字符串、集合、日期、时间等基础类。

#### 字符串（NSString）

字符串就是一连串的字符序列，Objective - C中有两个字符串：NSString和NSMutableString,其中，NSString代表字符序列不可变的字符串，而NSMutableString则代表字符序列可变的字符串。

参考NSString的[文档](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/)，NSString的常用功能如下(其主要方法位于括号内)：

- 创建和初始化字符串（` - init：`）

- 读取文件或者网络URL来初始化字符串 （`- initWithContentsOfFile:encoding:error:`、`- initWithContentsOfURL:encoding:error:
`）

- 将字符串内容写入文件或者URL（`- writeToFile:atomically:encoding:error:`、`-writeToURL:atomically:encoding:error:`）

- 获得字符串长度或者其中的字符、字节（`- lengthOfBytesUsingEncoding:`、`- characterAtIndex:`、`- getCharacters:range:`）

- 获取字符串对应的C风格字符串（`- cStringUsingEncoding:`）

- 连接、分隔、替换、比较字符串、对字符串中大小进行转换。

#### 可变字符串（NSMutableString）

NSString类是一个不可变的类，一旦NSString对象被创建，包含在这个对象中的字符序列是不可以改变的，直至这个对象被销毁。
NSMutableString 对象则代表一个字符序列可变的字符串，而且NSMutableString是NSString的子类，因此前面介绍所有包含NSString的方法，NSMutableString都是可以直接使用的，除此之外NSMutableString中还含有一些特有方法`appendFormat:`、`appendString:`等，可改变该字符串所包含的字符序列。

#### 日期与时间（NSDate）

NSDate 对象代表日期与时间，Objective - C 既提供了类方法来创建NSDate对象，也提供了大量以init开头的方法来初始化NSDate对象，这里大家可以参考[文档](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSDate_Class/)。

#### 日期格式器（NSDateFormatter）

所谓的NSDateFormatter 代表一个日期格式器，他的功能就是完成NSDate与NSString之间的转换，其主要步骤如下：

1. 创建一个NSDateFormatter对象
2. 调用NSDateFormatter的`setDateStyle：`、`setTimeStyle:`方法设置格式化日期、时间的风格。其中支持如下的几个枚举值。
	- NSDateFormatterNoStyle: 不显示日期、时间的风格
	- NSDateFormatterShortStyle: 显示“短”的日期风格
	- NSDateFormatterFullStyle: 显示“完整”日期风格
	
3. 如果需要将NSDate转化为NSString，调用NSDateFormatter的stringFromDate:方法执行格式化即可；如果需要将NSString转化为NSDate，调用NSDateFormatter的dateFromString:方法执行格式化即可。

#### 定时器（NSTimer）

如果程序需要让某个方法重复执行，可以借助Objective - C的定时器来完成，使用起来十分的简单，具体步骤如下：

1. 调用NSTimer的`scheduledTimerWithTimeInterval:invocation:repeats`或者`scheduledTimerWithTimeInterval:target:selector:userInfo:repeats`类方法来创建NSTimer对象。在此请注意如下参数：
	- timeInterval:指定每隔多少秒执行一次任务。
	- invocation或者target与selector:指定重复执行的任务。
	- userInfo： 该参数用于传入额外的附加信息。
	- repeats: 该参数需要指定一个BOOL值，该参数控制是否需要重复执行任务
2. 为第一步的任务编写方法。
3. 销毁定时器，调用定时器的invalidate方法即可。

代码样式如下：

```
[NSTimer scheduledTimerWithTimeInterval:0.5
								  target:self
								selector:@selector(info:) 
								userInfo:nil
								 repeats:YES
];
```



