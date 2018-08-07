title: 保存、读取数据
description: 固化是由iOS SDK 提供的一种保存和读取对象的机制。当我们的应用程序固化某一个对象的时候，会将该对象的所有属性存入指定的文件。然而当程序解固某个对象的时候，会从指定的文件读取相应的数据，然后根据数据还原对象。
categories:
- iOS
tags:
- 文件I/O

---

# 固化

对于大多数iOS 应用，可以将其功能总结为：提供一套界面，帮助用户管理特定的数据。在这一过程中，可以采取较为常用的
MVC设计模型（不同类型的对象要各司其职：模型对象负责保存数据，视图对象负责显示数据，控制器对象负责在模型对象与视图对象之间同步数据）。因此，在我们需要保存和读取数据的时候，首要的目的便是建立相应的模型对象。

**固化**是由iOS SDK 提供的一种保存和读取对象的机制。当我们的应用程序固化某一个对象的时候，会将该对象的所有属性存入指定的文件。然而当程序解固某个对象的时候，会从指定的文件读取相应的数据，然后根据数据还原对象。

下面这里以具体的代码为例子：

使用固化功能需要遵守<NSCoding>协议，并且实现两个方法：

```
 - (void)encodeWithCoder:(NSCoder *)aCoder;  //对数据进行编码
 - (instancetype)initWithCoder:(NSCoder *)aDecoder;  //还原数据的编码
```

并且创建一个Model类，有如下属性：

```
@interface model : NSObject <NSCoding>

@property (nonatomic, copy) NSString * name;
@property (nonatomic, copy) NSString * detail;
@property (nonatomic, assign) NSInteger ID;

@end
```

并且在Model类中实现上述的两种方法：

```
- (void)encodeWithCoder:(NSCoder *)aCoder {
    //将各个属性编码，以便保存
    // 如果属性是对象类型(NSString)的就用  encodeObject:forKey 方法来编码
    //如果属性是基础类型(NSInteger)的就用  encodeInteger:forKey 方法来编码 
    //具体用哪一个方法来编码，可以进入NSCoder对象中查看
    [aCoder encodeObject:self.name forKey:@"modelName"];
    [aCoder encodeObject:self.detail forKey:@"modelDetail"];
    [aCoder encodeInteger:self.ID forKey:@"modelID"];
}

- (instancetype)initWithCoder:(NSCoder *)aDecoder {
    self = [super init];
    if (self) {
        //对数据进行解码
        _name = [aDecoder decodeObjectForKey:@"modelName"];
        _detail = [aDecoder decodeObjectForKey:@"modelDetail"];
        _ID = [aDecoder decodeIntegerForKey:@"modelID"];
    }
    return self;
}
```

# 应用沙盒

上述通过建立了一个Model 对象遵守NSCoding 协议，可以通过固化机制来保存和读取它。然而在构建应用之后，我们的对象保存在哪里呢？这里就需要介绍一下**应用沙盒**了。

每一个iOS 应用都有属于自己的应用沙盒。其实这里所说的沙盒就是文件系统中的目录，但是iOS 系统会将每个应用的沙盒目录于文件系统的其他部分隔离。

这里就有一个iOS 很重要的安全问题了*每一个应用的必须待在自己的沙盒里面，并且只能访问自己的沙盒*

这里可以通过[文档](https://developer.apple.com/library/mac/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) 来知道自己应用的沙盒。

一般程序应用的沙盒都有三个文件夹：Documents、Library、temp以及一个.app包。

- **Documents 目录：**您应该将所有的应用程序数据文件写入到这个目录下。这个目录用于存储用户数据。该路径可通过配置实现iTunes共享文件。可被iTunes备份。

- **AppName.app 目录：**这是应用程序的程序包目录，包含应用程序的本身。由于应用程序必须经过签名，所以您在运行时不能对这个目录中的内容进行修改，否则可能会使应用程序无法启动。

- **Library 目录：**这个目录下有两个子目录：
	1. **Preferences 目录：**包含应用程序的偏好设置文件。您不应该直接创建偏好设置文件，而是应该使用NSUserDefaults类来取得和设置应用程序的偏好.
	2. **Caches 目录：**用于存放应用程序专用的支持文件，保存应用程序再次启动过程中需要的信息。
可创建子文件夹。可以用来放置您希望被备份但不希望被用户看到的数据。该路径下的文件夹，除Caches以外，都会被iTunes备份。

- **tmp 目录：**这个目录用于存放临时文件，保存应用程序再次启动过程中不需要的信息。该路径下的文件不会被iTunes备份。


### 获取文件路径

其主要方法有这些：

```
// 获取沙盒主目录路径
NSString *homeDir = NSHomeDirectory();
// 获取Documents目录路径
NSString *docDir = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
// 获取Library的目录路径
NSString *libDir = [NSSearchPathForDirectoriesInDomains(NSLibraryDirectory, NSUserDomainMask, YES) lastObject];
// 获取Caches目录路径
NSString *cachesDir = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) firstObject];
// 获取tmp目录路径
NSString *tmpDir =  NSTemporaryDirectory();

```
