title: 文本输入 (UITextField)
description: UITextField 是常用的文本输入控件。
date: 2016/09/16 08:46:25
categories:
- iOS
tags:
- UI控件

---

在之前我们已经学习过了[UILable](http://dbcxl.com/2016/06/04/UILabel/)，它可以用来在界面中显示文本，但是用户无法选择或者编辑UILable 中的文本。所以这里便需要使用UITextfield 接受用户的文本。

下面便使用具体的例子来说明吧：

在你个人的`ViewController.m` 中修改 `loadView`

```
- (void)loadView {

	CGRect frame = [UIScreen mainScreen].bounds;
	UIView *backgroundView = [[UIView alloc] initWithFrame:frame];
	
	CGRect textFieldRect = CGRectMake(20,20,100,20);
	UITextField *textField = [[UITextField alloc] initWithFrame:textFieldRect];
	
	//设置UIText对象的边框样式，便于查看
	textField.borderStyle = UITextBorderStyleRoundedRect;
	[backgroundView addSubview:textField];
	
	self.view = backgroundView;
}

```

构建并且运行应用，View 页面便会显示一个文本框，这个文本框便是刚刚添加的UITextField 对象。点击文本框，这时候屏幕底部便会弹出键盘，用于向文本框中输入文字。

这里根据官方[文档](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextField_Class/)，其属性大致可以分为以下几种：


**1、创建文本输入框**

 　　`UITextField*textField=[[UITextField alloc]initWithFrame:CGRectMake(10, 10, 200, 20)];`

 
**2、设置边框样式**

　　`textField.borderStyle = UITextBorderStyleRoundedRect;//圆角`

可选属性：

```
   	  UITextBorderStyleNone,无边框

      UITextBorderStyleLine,有边框

      UITextBorderStyleBezel,有边框和阴影

      UITextBorderStyleRoundedRect圆角
```
 
**3、设置背景颜色**

　　`textField.backgroundColor = [UIColor blueColor];`

 
**4、设置背景图片**

　　`textField.background=[UIImage imageNamed:@"test.png"];`

 
**5、提示文字**

　　`textField.placeholder = @"请输入";`

**6、设置和读取文字内容**

```
　　textField.text = @”hello”;

　　NSString * str = textField.text;
```

 

**7、设置字体**

```
　　[textField setFont:[UIFont fontWithName:@"Arial" size:30]];

　　NSLog(@"%@", [UIFont familyNames]); 查看字体集
```
　

**8、密文输入**

　　`textField.secureTextEntry = YES;` 


**9、键盘类型**

```
　　textField.keyboardType = UIKeyboardTypeNumberPad;  数字键

　　UIKeyboardTypeDefault,                   当前键盘（默认）

　　UIKeyboardTypeASCIICapable,          字母输入键

　　UIKeyboardTypeNumbersAndPunctuation,  数字和符号

　　UIKeyboardTypeURL,                       URL键盘

　　UIKeyboardTypeNumberPad,             数字键盘

　　UIKeyboardTypePhonePad,               电话号码输入键盘   

　　UIKeyboardTypeEmailAddress,          邮件地址输入键盘           
 ```

**10、键盘风格**

　　`textView.keyboardAppearance=UIKeyboardAppearanceDefault；`

可选属性

```
　　UIKeyboardAppearanceDefault， 默认外观，浅灰色

　　UIKeyboardAppearanceAlert，     深灰 石墨色
```

**11、再次编辑时是否清空之前内容；默认NO**

　　`textField.clearsOnBeginEditing = YES`

 
**12、对齐方式**

垂直对齐：

　　`textField.contentVerticalAlignment = UIControlContentVerticalAlignmentCenter` 

可选属性：

```
	UIControlContentVerticalAlignmentCenter  居中对齐

	UIControlContentVerticalAlignmentTop    顶部对齐，默认是顶部对齐

	UIControlContentVerticalAlignmentBottom 底部对齐

	UIControlContentVerticalAlignmentFill    完全填充
```

水平对齐：

　　`textField.textAlignment = UITextAlignmentCenter;` 


可选属性：

```
	UITextAlignmentLeft，左对齐，默认是左对齐

	UITextAlignmentCenter，

	UITextAlignmentRight，右对齐 
```

**13、 设置滚动**

```
textField.font = [UIFont systemFontOfSize:30]; //设置文字大小

textField.adjustsFontSizeToFitWidth = YES; //默认是NO  YES当充满边框时，文字会缩小，当小到一定程度时仍然会滚动；自适应宽度；

//设置滚动时最小字号，与滚动相关，要比设置的字体小，否则没有意义，没有设置这一项文字也会缩小和滚动

textField.minimumFontSize = 20; 
```
 

**14、设置return键**

`textField.returnKeyType = UIReturnKeyGoogle;search`

可选属性

```
    UIReturnKeyDefault, 默认 灰色按钮，标有Return

    UIReturnKeyGo,      标有Go的蓝色按钮

    UIReturnKeyGoogle,标有Google的蓝色按钮，用语搜索

    UIReturnKeyJoin,标有Join的蓝色按钮

    UIReturnKeyNext,标有Next的蓝色按钮

    UIReturnKeyRoute,标有Route的蓝色按钮

    UIReturnKeySearch,标有Search的蓝色按钮

    UIReturnKeySend,标有Send的蓝色按钮

    UIReturnKeyYahoo,标有Yahoo的蓝色按钮

    UIReturnKeyYahoo,标有Yahoo的蓝色按钮

    UIReturnKeyEmergencyCall, 紧急呼叫按钮
```

 
**15、设置输入自动纠正模式**

`textField.autocapitalizationType = UITextAutocapitalizationTypeAllCharacters;`

可选属性：

```
    UITextAutocapitalizationTypeNone, 不自动纠正

    UITextAutocapitalizationTypeWords,  单词首字母大写

    UITextAutocapitalizationTypeSentences,  句子的首字母大写

    UITextAutocapitalizationTypeAllCharacters, 所有字母都大写
```

# UITextField 的委托设计模式

在目标-动作中，针对不同的事件，仅仅创建不同的动作信息。比如UIButton 对象的事件比较简单，通常只需要处理点击时间，相反，像这里的UITextField 这种事件复杂的对象，在iOS开发中则需要采取委托设计的模式（也就是我们之前所说的合理使用**Delegate**）

下面咱们就用具体的代码为例子：以MVC为例子，首先在你的`View.h`文件中，写出你的Delegate 接口

```
@protocol TextFieldDelegate <NSObject>

@optional

//该方法触发的时候，输入框已经结束编辑状态
- (void)textFieldDidEndEditing:(UITextField *)textField;

//该方法触发的时候，可以进入编辑状态
- (void)textFieldDidBeginEditing:(UITextField *)textField;

//输入框是否可以结束编辑
- (BOOL)textFieldShouldEndEditing:(UITextField *)textField;
//输入框是否可以开始编辑
- (BOOL)textFieldShouldBeginEditing:(UITextField *)textField;
//键盘右下角返回键点击
- (BOOL)textFieldShouldReturn:(UITextField *)textField;
//是否可以清除内容
- (BOOL)textFieldShouldClear:(UITextField *)textField;



@end

@interface WPInputTextField : UIView

@property (assign, nonatomic) id <TextFieldDelegate> delegate;

@end
```

接着我们便可以在`View.m` 中抛出你的Delegate ，类似于如下几种写法

```

#pragma mark - UITextFieldDelegate

- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    
    if ([_delegate respondsToSelector:@selector(wpTextFieldShouldReturn:)]) {
        return [_delegate wpTextFieldShouldReturn:textField];
    }
    
    return YES;
}

- (void)textFieldDidBeginEditing:(UITextField *)textField {
    
    if ([_delegate respondsToSelector:@selector(wpTextFieldDidBeginEditing:)]) {
        [_delegate wpTextFieldDidBeginEditing:textField];
    }
    
}

```

完成以上操作你就可以去你的`ViewController.m`中去完成你的delegate啦，是不是很简单呢=。=

恩，其实就是这样喵~
