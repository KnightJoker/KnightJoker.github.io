title: 核心动画(Core Animation)
description: Core Animation is a graphics rendering and animation infrastructure available on both iOS and OS X that you use to animate the views and other visual elements of your app
categories:
- iOS
tags:
- 动画

---

之前写过一篇关于动画(UIView)的一篇[博客](http://dbcxl.com/2016/06/24/Animations/)

不过在 iOS 开发中却有着另外一套功能更为强大、效果更华丽的动画API -- [Core Animation](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/ca_architecture_2x.png?raw=true)

可以看到，核心动画位于UIKit的下一层，相比UIView动画，它可以实现更复杂的动画效果。

给view加上动画，本质上是对其layer进行操作，layer包含了各种支持动画的属性，动画则包含了属性变化的值、变化的速度、变化的时间等等，两者结合产生动画的过程。

核心动画和UIView动画的对比：UIView动画可以看成是对核心动画的封装，和UIView动画不同的是，通过核心动画改变layer的状态（比如position），动画执行完毕后实际上是没有改变的（表面上看起来已改变）。

总体来说核心动画的优点有：

- 性能强大，使用硬件加速，可以同时向多个图层添加不同的动画效果

- 接口易用，只需要少量的代码就可以实现复杂的动画效果。

- 运行在后台线程中，在动画过程中可以响应交互事件（UIView动画默认动画过程中不响应交互事件）。

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/core_animation.png?raw=true)

CAAnimation是所有动画对象的父类，实现CAMediaTiming协议，负责控制动画的时间、速度和时间曲线等等，是一个抽象类，不能直接使用。

CAPropertyAnimation ：是CAAnimation的子类，它支持动画地显示图层的keyPath，一般不直接使用。

iOS9.0之后新增CASpringAnimation类，它实现弹簧效果的动画，是CABasicAnimation的子类。

　综上，核心动画类中可以直接使用的类有以下几种：

- CABasicAnimation
- CAKeyframeAnimation
- CATransition
- CAAnimationGroup
- CASpringAnimation

###### CABasicAnimation ———— 基本动画

- 属性说明：
    + `keyPath`:要改变的属性名称（字符串）
    + `fromValue`:keyPath相应属性的初始值
    + `toValue`:keyPath相应属性的结束值

- 动画过程：
    + 随着动画的进行，在长度为duration的持续时间内，keyPath相应属性的值从fromValue渐渐地变为toValue

    + keyPath内容是CALayer的可动画Animatable属性

    + 如果`fillMode=kCAFillModeForwards`同时`removedOnComletion=NO`，那么在动画执行完毕后，图层会保持显示动画执行后的状态。但在实质上，图层的属性值还是动画执行前的初始值，并没有真正被改变。

###### CAKeyframeAnimation —— 关键帧动画

- 关键帧动画，也是CAPropertyAnimation的子类，与CABasicAnimation的区别是：
CABasicAnimation只能从一个数值（fromValue）变到另一个数值（toValue），而CAKeyframeAnimation会使用一个NSArray保存这些数值

- 属性说明：

    + `values`：上述的NSArray对象。里面的元素称为“关键帧”(keyframe)动画对象会在指定的时间（duration）内，依次显示values数组中的每一个关键帧
    
    + `path`：可以设置一个CGPathRef、CGMutablePathRef，让图层按照路径轨迹移动。path只对CALayer的anchorPoint和position起作用。如果设置了path，那么values将被忽略
    
    + `keyTimes`：可以为对应的关键帧指定对应的时间点，其取值范围为0到1.0，keyTimes中的每一个时间值都对应values中的每一帧。如果没有设置keyTimes，各个关键帧的时间是平分的
    
CABasicAnimation可看做是只有2个关键帧的CAKeyframeAnimation

###### CAAnimationGroup —— 动画组

- 动画组，是CAAnimation的子类，可以保存一组动画对象，将CAAnimationGroup对象加入层后，组中所有动画对象可以同时并发运行

- 属性说明：

    + animations：用来保存一组动画对象的NSArray
    
默认情况下，一组动画对象是同时运行的，也可以通过设置动画对象的beginTime属性来更改动画的开始时间。

###### CATransition ———— 转场动画

- CATransition是CAAnimation的子类，用于做转场动画，能够为层提供移出屏幕和移入屏幕的动画效果。iOS比Mac OS X的转场动画效果少一点

UINavigationController就是通过CATransition实现了将控制器的视图推入屏幕的动画效果

- 动画属性:

    + type：动画过渡类型
    + subtype：动画过渡方向
    + startProgress：动画起点(在整体动画的百分比)
    + endProgress：动画终点(在整体动画的百分比)
    
### 核心动画类的常用属性
</br>
**keyPath：**可以指定keyPath为CALayer的属性值，并对它的值进行修改，以达到对应的动画效果，需要注意的是部分属性值是不支持动画效果的。

以下是具有动画效果的keyPath：

```
     //CATransform3D Key Paths : (example)transform.rotation.z
     //rotation.x
     //rotation.y
     //rotation.z
     //rotation 旋轉
     //scale.x
     //scale.y
     //scale.z
     //scale 缩放
     //translation.x
     //translation.y
     //translation.z
     //translation 平移

     //CGPoint Key Paths : (example)position.x
     //x
     //y

     //CGRect Key Paths : (example)bounds.size.width
     //origin.x
     //origin.y
     //origin
     //size.width
     //size.height
     //size

     //opacity
     //backgroundColor
     //cornerRadius 
     //borderWidth
     //contents 

     //Shadow Key Path:
     //shadowColor 
     //shadowOffset 
     //shadowOpacity 
     //shadowRadius 
```

例子：

```
- (void)position {
    CABasicAnimation * ani = [CABasicAnimation animationWithKeyPath:@"position"];
    ani.toValue = [NSValue valueWithCGPoint:self.centerShow.center];
    ani.removedOnCompletion = NO;
    ani.fillMode = kCAFillModeForwards;
    ani.timingFunction = [CAMediaTimingFunction 	functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
    [self.cartCenter.layer addAnimation:ani forKey:@"PostionAni"];
}
```