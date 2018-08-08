title: NSNotificationCenter (通知中心)
description: 
date: 2017/04/23 08:46:25
categories:
- iOS
tags:
- 消息传递

---

通知中心是 Foundation 框架的一个子系统，它向应用程序中注册为某个事件观察者的所有对象广播消息（即通知）。（从编程角度而言，它是 NSNotificationCenter 类的实例）。该事件可以是发生在应用程序中的任何事情，例如进入后台状态，或者用户开始在文本栏中键入。通知是告诉观察者，事件已经发生或即将发生，因此让观察者有机会以合适的方式响应。通过通知中心来传播通知，是增加应用程序对象间合作和内聚力的一种途径。

<br>

![NSNotificationCenter](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/NSNotificationCenter.gif?raw=true)

任何对象都可以观察通知，但要做到这一点，该对象必须注册，以接收通知。在注册时，它必须指定选择器，以确定由通知传送所调用的方法；方法签名必须只有一个参数：通知对象。注册后，观察者也可以指定发布对象。

（以上便是[官方文档](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSNotificationCenter_Class/)中的解释）

<br>
# 获取通知中心

每个程序都会有一个默认的通知中心。为此，**NSNotificationCenter**提供了一个类方法来获取这个通知中心：

`+ (NSNotificationCenter *)defaultCenter`

获取了这个默认的通知中心对象后，我们就可以使用它来处理通知相关的操作了，包括注册观察者，移除观察者、发送通知等。

通常如果不是出于必要，我们一般都使用这个默认的通知中心，而不自己创建维护一个通知中心。

添加观察者

如果想让对象监听某个通知，则需要在通知中心中将这个对象注册为通知的观察者。早先，**NSNotificationCenter**提供了以下方法来添加观察者：

```
- (void)addObserver:(id)notificationObserver
           selector:(SEL)notificationSelector
               name:(NSString *)notificationName
             object:(id)notificationSender
```

这个方法带有4个参数，分别指定了通知的观察者、处理通知的回调、通知名及通知的发送对象。这里需要注意几个问题：

- notificationObserver不能为nil。

- notificationSelector回调方法有且只有一个参数(NSNotification对象)。

- 如果notificationName为nil，则会接收所有的通知，否则，就由接收所有来自于notificationSender的所有通知。

- 如果notificationSender为nil，则会接收所有notificationName定义的通知；否则，接收由notificationSender发送的通知。

*监听同一条通知的多个观察者，在通知到达时，它们执行回调的顺序是不确定的，所以我们不能去假设操作的执行会按照添加观察者的顺序来执行。*

<br>
然后以具体例子来说明NSNotificationCenter的使用方法

定义了两个类：**Poster（发送消息）**和 **Observer（接受消息）**

### Poster.h 


```
#import <Foundation/Foundation.h>  
  
@interface Poster : NSObject  
  
-(void)postMessage;  
  
@end  
```

### Poster.m

```
#import "Poster.h"  
  
@implementation Poster  
  
-(void)postMessage{  
      
    //1.下面两条语句等价  
    //二者的区别是第一条语句是直接发送消息内容，第二条语句先创建一个消息，然后再发送消息  
    [[NSNotificationCenter defaultCenter] postNotificationName:@"PosterOne" object:@"This is posterone!"];  
      
//    [[NSNotificationCenter defaultCenter] postNotification:[NSNotification notificationWithName:@"PosterOne" object:@"This is posterone"]];  
      
    //2.下面两条语句等价  
    //参数：userInfo  --- Information about the the notification.  
    [[NSNotificationCenter defaultCenter] postNotificationName:@"PosterTwo" object:@"This is postertwo" userInfo:[NSDictionary dictionaryWithObject:@"value" forKey:@"key"]];  
      
//    [[NSNotificationCenter defaultCenter] postNotification:[NSNotification notificationWithName:@"PosterTwo" object:@"This is postertwo" userInfo:[NSDictionary dictionaryWithObject:@"value" forKey:@"key"]]];  
}  
  
@end  
```

### Observer.h

```
#import <Foundation/Foundation.h>  
  
@interface Observer : NSObject  
  
-(void)observer;  
  
@end  
```

### Observer.m

```
#import "Observer.h"  
  
@implementation Observer  
  
-(void)observer {  
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(callBack1:) name:@"PosterOne" object:nil];  
      
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(callBack2:) name:@"PosterTwo" object:nil];  
      
    //删除所有的observer  
//    [[NSNotificationCenter defaultCenter] removeObserver:self];  
    //删除名字为name的observer  
//    [[NSNotificationCenter defaultCenter] removeObserver:self name:@"PosterOne" object:nil];  
      
}  
  
-(void)callBack1:(NSNotification*)notification{  
    NSString *nameString = [notification name];  
    NSString *objectString = [notification object];  
    NSLog(@"name = %@,object = %@",nameString,objectString);  
}  
  
-(void)callBack2:(NSNotification*)notification{  
    NSString *nameString = [notification name];  
    NSString *objectString = [notification object];  
    NSDictionary *dictionary = [notification userInfo];  
    NSLog(@"name = %@,object = %@,userInfo = %@",nameString,objectString,[dictionary objectForKey:@"key"]);  
}  
  
@end  
```

### main.m

```
#import <Foundation/Foundation.h>  
#import "Poster.h"  
#import "Observer.h"  
  
int main(int argc, const char * argv[])  
{  
  
    @autoreleasepool {  
          
        //注意这里的顺序，要先observer，然后再poster  
        Observer *myObserver = [[Observer alloc] init];  
        [myObserver observer];  
          
        Poster *poster = [[Poster alloc] init];  
        [poster postMessage];  
    }  
    return 0;  
} 
```

# 小结

在我们的应用程序中，一个大的话题就是两个对象之间如何通信。我们需要根据对象之间的关系来确定采用哪一种通信方式。对象之间的通信方式主要有以下几种：

1. 直接方法调用
 
2. Target-Action

3. Delegate

4. 回调(block)

5. KVO

6. 通知

一般情况下，我们可以根据以下两点来确定使用哪种方式：

- 通信对象是一对一的还是一对多的

- 对象之间的耦合度，是强耦合还是松耦合

Objective-C中的通知由于其广播性及松耦合性，非常适合于大的范围内对象之间的通信(模块与模块，或一些框架层级)。通知使用起来非常方便，也正因为如此，所以容易导致滥用。所以在使用前还是需要多想想，是否有更好的方法来实现我们所需要的对象间通信。毕竟，通知机制会在一定程度上会影响到程序的性能。

对于使用**NSNotificationCenter**，最后总结一些小建议：

1. 在需要的地方使用通知。

2. 注册的观察者在不使用时一定要记得移除，即添加和移除要配对出现。

3. 尽可能迟地去注册一个观察者，并尽可能早将其移除，这样可以改善程序的性能。因为，每post一个通知，都会是遍历通知中心的分发表，确保通知发给每一个观察者。

4. 记住通知的发送和处理是在同一个线程中。

5. 使用-addObserverForName:object:queue:usingBlock:务必处理好内存问题，避免出现循环引用。

6. NSNotificationCenter是线程安全的，但并不意味着在多线程环境中不需要关注线程安全问题。不恰当的使用仍然会引发线程问题。
