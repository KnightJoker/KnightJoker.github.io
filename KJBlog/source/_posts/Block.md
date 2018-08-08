title: Block 
description: What the fucking block?
date: 2017/02/21 08:46:25
categories:
- iOS
tags:
- 面向对象

---

写这篇博客之前，我要先日一下狗，不要问我为啥，一个下午为了这个玩意我都要爆炸了！！

来人，牵朕的爱狗上来！

![日了狗](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/%E6%97%A5%E7%8B%97.png?raw=true)

好了，废话就扯这么多，下面开始正事，顺便感谢九月姐姐孜孜不倦的教诲~

这里主要会讲解block在iOS(Objective­ - C)中的基本知识,以及普及一下 block的常用语法

# Block

Block 本质上是和其他变量类似。不同的是，Block 存储的数据是一个函数体。使用Block，你可以像调用其他标准函数一样，传入参数数，并得到返回值。

这里也可以把它理解为一个匿名函数，至于匿名函数顾名思义就是不带名字的函数，Objective - C中的Block则可以用指针来直接调用一个函数，但虽说如此我们还是需要知道指针的名称。（关于这点，我就特别想吐槽了啊，虽然相当于匿名函数，但是这个指针名称还是必须的啊~！～！～～）

脱字符（^）是Block的语法标记。按照我们熟悉的参数语法规约所定义的返回值以及Block的主体（也就是可以执行的代码）。下图是如何把块变量赋值给一个变量的语法讲解:

![Block 的声明](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/block.png?raw=true)

在这个例子中，myBlock是一个Block变量，它接受一个int类型的参数，返回一个int类型的值。

由此我们在顺便介绍一下**Block 的语法**

### block 作为本地变量（local variable）

`returnType (^blockName)(parameterTypes) = ^returnType(parameters) {...}`

### block 作为类的成员属性（property）

`@property (nonatomic, copy)returnType (^blockName)(parameters);`

### block 作为函数参数 （method parameter）

`- (void)someMethodThatTakesABlock:(returnType (^)(parameterTypes))blockName`

### 调用包括block参数的函数 （argument to a method call）
`[someObject somethodThatTakesABlock:^returnType(parameters{...})];`

### 使用typedef 定义block类型 （typedef）
```
typedef returnType (^TypeName)(parameterTypes);
TypeName blockName = ^returnType(parameters){...};
```

### 这里做一个简单的Block回调代码：

```
int main(int argc, const char * argv[]) {
      @autoreleasepool {
          int wheetalk = 13;
          void(^ HolaweenBlock)() = ^(){
              printf("%d\n", wheetalk);
          };
          wheetalk = 27;
          HolaweenBlock();
      }
return 0; 
}
```
现在遇见的问题就是在Block 的使用中，尽量不需要使用到_ 和 self 的全局变量，比较容易引起循环引用，程序容易崩溃。
