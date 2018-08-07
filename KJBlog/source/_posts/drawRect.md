title: 在视图上面自定义绘图
description: 
categories:
- iOS
tags:
- UI控件

---

当iOS 自带的UI控件，没有给我们所需要的控件样式的时候，这时候就需要我们自己自定义绘图，使得控件到达我们所需要的样子。

### 使用drawRect：方法的自定义绘图

</br>

视图根据drawRect：方法将自己绘制到图层上。 UIView 的子类可以覆盖drawRect：，从而完成自定义的绘图任务。

覆盖drawRect：后首先应该获取视图从UIView 继承而来的bounds 属性，该属性定义了一个矩形范围，表示视图的绘制区域。

视图在绘制自己的时候，会参考一个坐标系，bounds 表示的矩形位于自己的坐标系，而frame 表示的矩形位于父视图的坐标系，但是两个矩形的大小是形同的

frame 和 bounds 表示的矩形用法不同。frame 用于确定与视图层次结构中其他视图的相对位置，从而从而将自己的图层与其他视图的图层正确组合成屏幕上的图像。而bounds 属性用于确定绘制区域，避免将自己绘制到图层边界之外。

</br>
# 绘制圆形

这里就具体的举一个例子来说明在iOS 中的绘图问题吧~

首先找到你的View.m 文件，并且重写里面的drawRect：方法，画出一个尽可能大的圆形，但是又要求不要超过视图的绘制区域。

```
- (void)drawRect:(CGRect)rect {
    CGRect bounds = self.bounds;
    
    //根据bounds 计算中心点
    CGPoint center;
    center.x = bounds.origin.x + bounds.size.width / 2.0;
    center.y = bounds.origin.y + bounds.size.height / 2.0;
    
    //根据视图管和高中的较小值来计算圆形的半径
    float radius = (MIN(bounds.size.width, bounds.size.height) / 2.0);
    
    //创建一个UIBezierPath 对象，可以用来绘制直线或者曲线从而组成各种形状
    UIBezierPath *path = [[UIBezierPath alloc] init];
    
    //以中心点为圆心、radius 的值为半径，定义一个0 到 M_PI * 2.0 弧度的路径（整圆）
    [path addArcWithCenter:center
                    radius:radius
                startAngle:0.0
                  endAngle:M_PI * 2.0
                 clockwise:YES];
    
    //设置线条宽度为10
    path.lineWidth = 10;
    
    //设置绘制的颜色为黄色
    [[UIColor yellowColor] setStroke];
    
    //绘制路径
    [path stroke];
}
```
