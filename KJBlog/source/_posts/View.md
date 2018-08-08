title: 视图与其层次结构
description: 但凡你在iOS 系统中能看见的，触摸的，使用的都是UIView 对象，可以说是iOS 开发中最重要的一个类。
date: 2016/07/16 08:46:25
categories:
- iOS
tags:
- UI控件

---

我们在学习之前，你首先需要知道：

- 视图是UIView 对象，或者是其子类

- 视图知道如何绘制自己

- 视图可以处理事件，比如touch

- 视图会按层次结构排列，位于视图层次结构顶端的是应用窗口

</br>

# 视图层次结构

在你所开发的每一个应用都有且只有一个UIWindow 对象。UIWindow 对象就像一个容器，负责包含应用中所有的视图。应用需要在启动时创建并且设置UIWindow 对象，然后在为其添加视图。

加入窗口的视图会成为该窗口的**子视图(subview)**。窗口的子视图还可以有自己的子视图，从而构成一个以UIWindow 对象为根视图的视图层次结构。

在视图的层次结构形成之后，便可以将其绘制到屏幕上了，这个过程可以分为两步：

- 每一个视图（包括UIWindow）分别绘制自己，然后视图会将自己绘制到图层（layer）上，每一个UIView 对象都有一个layer 属性，指向一个CALayer 类的对象。

- 所有视图的图层组合成一幅图像，绘制到屏幕上。

</br>
# 视图

在创建UIView 子类的模板会生成一个方法，`initWithFrame:`这个方法是UIView的指定初始化方法，带有一个CGRect 结构类型的参数，该参数会被赋值给UIView 的frame 属性。

`@property (nonatomic) CRGect frame;`

视图的frame 属性是保存视图的大小以及相对于父视图的位置。

*CGRect 结构包含另外两个结构：origin和size.origin 的类型是CGPoint结构，该结构包含了两个float 类型成员：x和y。 size 的类型是CGSize 结构，该结构也包含了两个float类型成员：width 和 height*

![CGRect](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/CGRect.png?raw=true)

由frame可见，视图对象的形状一定是矩形。

然后打开Appdelegate.m由具体的代码教你使用CGRect：

```
#import "AppDelegate.h"
#import "KJView.h"

@interface AppDelegate ()

@end

@implementation AppDelegate


- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    
    CGRect firstFrame = CGRectMake(20, 40, 100, 200);
    KJView *firstView = [[KJView alloc] initWithFrame:firstFrame];
    firstView.backgroundColor = [UIColor yellowColor];
    
    [self.window addSubview:firstView];
    self.window.backgroundColor = [UIColor whiteColor];

    [self.window makeKeyAndVisible];
    return YES;
}
```

这里需要注意的是该值得单位为点（point），而不是像素（pixels）。如果单位是像素的话，视图在Retina和非Retina 显示器上面的大小显示便无法保持一致。

- 在Retina显示器上面，一个点事两个像素高度。

- 在非Retina显示器上面，一个点则就是一个像素高度。

每一个UIView 对象都有一个superview 属性。讲一个视图作为子视图加入另一个视图时，会自动创建相应的反向关联。比如这里KJView 对象的superview 属相指向应用的UIWindow对象。（superview属性是弱引用。）
                               
注意这里如果直接运行代码的话，可能会遇见`exception 'NSInternalInconsistencyException', reason: 'Application windows are expected to have a root view controller at the end of application launch'`这样的一个错误提示。

原因是在你的程序中没有设置一个rootViewController

解决方法就是创建一个ViewController，并设置该VC为该程序的rootViewController 就可以了

然后在使用VC去控制View 的各种变化和显示，这样也就符合最基本的MVC设计模式了=。=
























