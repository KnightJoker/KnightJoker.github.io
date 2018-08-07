title: UILabel 详细介绍
description: 
categories:
- iOS
tags:
- UI控件

---

**UILabel(标签)**应该是iOS中最基本的一个控件了，也是使用频率最高的，经常用来展示一段不可编辑的文本。

这里主要使用参照[文档](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/)

这里首先声明一个UILabel

`@property (strong ,nonatomic) UILabel *labelView;`

### text 设置显示的文本

`_labelView.text = @"这是一个标签"`

### backgroundColor 设置背景色，默认为[UIColor clearColor]

`_labelView.backgroundColor = [UIcolor yellowColor];`

### font 设置字体以及大小

`_labelView.font = [UIFont systemFontOfSize:30] //默认字体，大小30`

### textColor 设置字体颜色，默认为nil为黑色

`_labelView.textColor = [UIColor greenColor];`

### attributedText 在同一字符串中，拥有不同的字体、颜色效果

```
NSMutableAttributedString *sting [[NSMutableAttributedSting alloc] initWithString:@"This is a test"];

//字体大小
[string addAttribute:NSFontAttributeName value:[UIFont systemFontOfSize:20.0] range:NSMakeRang(0,2)];

//下划线
[string addAttribute:NSUnderlineStyleAttributeName value:@(NSUnderlineStyleSingle) range:NSMakeRang(2,2)];

_labelView.attributedText = string;
```
### textAlignment 文字的对齐方式,默认是NSTextAlignmentLeft(左对齐)

```
_labelView.textAlignment = NSTextAlignmentRight //右对齐
_labelView.textAlignment = NSTextAlignmentCenter //居中
```

### lineBreakMode 设置文字长度超出label范围时文字的显示方式
```
_labelView.lineBreakMode = NSLineBreakByTruncatingHead; //缩略头部：“...abc”
_labelView.lineBreakMode = NSLineBreakByTruncatingTail;//缩略尾部: "abc..."
_labelView.lineBreakMode = NSLineBreakByTruncatingMiddle;//缩略中部："ab...yz"
```

### numberOfLines 设置文本显示的行数

`_labelView.numberOfLines = 2;//该行数为2`

### shadowOffset 阴影的偏移量,shadowColor 阴影的颜色
```
_labelView.shadowColor = [UIColor redColor];//阴影颜色为红色
_labelView.shadowOffset = CGSizeMake(100,-10);//向右偏移100，向上偏移10
```

### highlighted 设置高亮 highlightedTextColor 高亮颜色
```
_labelView.highlighted = YES;
_labelView.highlightedTextColor = [UIColol whiteColor];
```