title: 动画(Animation)学习
description: 
categories:
- iOS
tags:
- 动画

---

“动画（Animation）”这个单词来源于拉丁语，意思是“the act of bringing to life(给无生命的物体带来灵魂)”,然而在iOS 开发中，动画也能起到这样的效果。在界面交互过程中穿插适当的动画，可以赋予界面视觉线索，帮助用户了解程序，建造良好的用户体验。

<br>
# 基础动画

在开始添加动画之前，首先要看的就是iOS 提供了哪些动画效果。[动画](https://developer.apple.com/reference/uikit/uiview) 在其中找到Animation~

然后我们就继续以代码为例子,在你的ViewController.m 文件中找到`viewDidload:` 并在其中加入以下代码：

```
  	
  	self.view.backgroundColor = [UIColor whiteColor];
    UILabel *myLabel = [[UILabel alloc] initWithFrame:CGRectMake(100, 200, 200, 20)];
    myLabel.backgroundColor = [UIColor yellowColor];
    
    [self.view addSubview:myLabel];
    
    // 设置myLabel透明度的起始值
    myLabel.alpha = 0.0;
    
    // 让myLabel的透明度变化为2.0
    [UIView animateWithDuration:2.0 animations:^{
        myLabel.alpha = 2.0;
    }];
```
然后这里我们便可以看见UILable 对象就有一个淡入的动画效果。

在带有block 对象的动画API中，最简单的就是这个`animateWithDuration:animations:`。该方法有两个参数，一个参数是动画持续时间，第二个参数是用于执行动画的Block 对象。该方法会遵守一个渐快-减慢的速度曲线，也就是说，动画开始时会逐渐加快，结束时会逐渐减慢。

<br>
### 时间函数

动画的速度曲线是由时间函数控制的。

如果需要设置动画的时间函数，则可以调用
`animateWithDuration:delay:options:animations:completion:`
方法。在这些参数中

- options: 可以设置时间函数和其他的一些选项

- delay: 可以使的动画延迟一段时间执行

- completion: 在动画结束之后执行特定的代码

这里便可以将上述动画代码修改为：

```
  [UIView animateWithDuration:2.0
                          delay:0.0
                        options:UIViewAnimationOptionCurveEaseIn
                     animations:^{
                         myLabel.alpha = 2.0;
                     }
                     completion:nil];

```

与之前的方法不同，这里使用的是减快函数(UIViewAnimationOptionCurveEaseIn)，动画结束时速度将不会逐渐减慢。

*options:*中使用的是位掩码，也就是可以使用按位或运算符（“|”）将多个选项值连接起来。

时间函数选项：

- UIViewAnimationOptionCurveLinear ：该属性既不会使动画加速也不会使动画减速，只是做以线性运动。

- UIViewAnimationOptionCurveEaseIn：该属性使动画在开始时加速运行。

- UIViewAnimationOptionCurveEaseOut：该属性使动画在结束时减速运行。

- UIViewAnimationOptionCurveEaseInOut：该属性结合了上述两种情况，使动画在开始时加速，在结束时减速。

这里有一个反复类的选项：

- UIViewAnimationOptionRepeat：该属性可以使你的动画永远重复的运行。（该选项通常和UIViewAnimationOptionAutoreverse 结合使用）

- UIViewAnimationOptionAutoreverse：该属性可以使你的动画当运行结束后按照相反的行为继续运行回去。


**以下是全部枚举变量含义**

```
 UIViewAnimationOptionLayoutSubviews            //进行动画时布局子控件
 UIViewAnimationOptionAllowUserInteraction      //进行动画时允许用户交互
 UIViewAnimationOptionBeginFromCurrentState     //从当前状态开始动画
 UIViewAnimationOptionRepeat                    //无限重复执行动画
 UIViewAnimationOptionAutoreverse               //执行动画回路
 UIViewAnimationOptionOverrideInheritedDuration //忽略嵌套动画的执行时间设置
 UIViewAnimationOptionOverrideInheritedCurve    //忽略嵌套动画的曲线设置
 UIViewAnimationOptionAllowAnimatedContent      //转场：进行动画时重绘视图
 UIViewAnimationOptionShowHideTransitionViews   //转场：移除（添加和移除图层的）动画效果
 UIViewAnimationOptionOverrideInheritedOptions  //不继承父动画设置

 UIViewAnimationOptionCurveEaseInOut            //时间曲线，慢进慢出（默认值）
 UIViewAnimationOptionCurveEaseIn               //时间曲线，慢进
 UIViewAnimationOptionCurveEaseOut              //时间曲线，慢出
 UIViewAnimationOptionCurveLinear               //时间曲线，匀速

 UIViewAnimationOptionTransitionNone            //转场，不使用动画
 UIViewAnimationOptionTransitionFlipFromLeft    //转场，从左向右旋转翻页
 UIViewAnimationOptionTransitionFlipFromRight   //转场，从右向左旋转翻页
 UIViewAnimationOptionTransitionCurlUp          //转场，下往上卷曲翻页
 UIViewAnimationOptionTransitionCurlDown        //转场，从上往下卷曲翻页
 UIViewAnimationOptionTransitionCrossDissolve   //转场，交叉消失和出现
 UIViewAnimationOptionTransitionFlipFromTop     //转场，从上向下旋转翻页
 UIViewAnimationOptionTransitionFlipFromBottom  //转场，从下向上旋转翻页

```


<br>
# 关键帧动画

之前所说的动画都属于基础动画，只能将UIView的某一个属性从一个值变化到另一个值。如果需要在多个值之间进行变化，这里就可以使用关键帧动画（也就是理解成为若干个连续执行的基础动画）。
 
关键帧动画的使用方法与基础动画类似，对应的UIView 类方法是

`animateKeyframesWiehDuration:delay:options:animations:completion:`。

同时，在animations 的Block 对象中，还需要通过

`addKeyframeWithRelativeStartTime:relativeDuration:animations:`方法还需要添加一个关键帧。

在你的ViewController.m中，将UILable 对象首先移动到屏幕中央，然后在移动到一个随机的位置。

```
    [UIView animateKeyframesWithDuration:2.0
                                   delay:0.0
                                 options:0
                              animations:^{
                                  [UIView addKeyframeWithRelativeStartTime:0
                                                          relativeDuration:0.8
                                                                animations:^{
                                                                    myLabel.center = self.view.center;
                                                                }];
                                  
                                  [UIView addKeyframeWithRelativeStartTime:0.8
                                                          relativeDuration:0.2
                                                                animations:^{
                                                                    int x = arc4random();
                                                                    int y = arc4random();
                                                                    myLabel.center = CGPointMake(x, y);
                                                                }];
                              }
                              completion:NULL];
```

<br>

`addKeyframeWithRelativeStartTime:relativeDuration:animations:`
中的第一个参数是相对起始时间（relative start time），表示该关键帧开始执行的时刻在整个动画持续时间中的百分比，取值范围是0到1.第二个参数是相对持续时间（relative duration），表示该关键帧占整个动画持续时间的百分比，取值范围也是0到1。

运行上述代码便可以看见UILabel 对象首先回移动到屏幕中央，然后会随机移动到另一个位置并停止。


### 在动画完成后执行特定的代码

在上述的方法中的**comletion：**传入一个Block 对象。便可以在动画完成后，控制台就会输出动画执行完成的日志信息。







