title: Objective-C Style Guide
description: 个人Objective-C 代码规范
categories: 
- iOS
tags:
- Style Guide

---

这里的规范主要出自[Google](https://google.github.io/styleguide/objcguide.xml) 和 [Apple](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)

# 编码风格- 方法声明与定义
方法名采用 Camel 命名法,第一个单词首字母小写,后续单词首字母大写。在 - 和 返回值 之间留1个空格,指针类型 和 * 之间留1个空格。if、for 等关键字后面留 1 个空格,操作符与操作数之间留 1 个空格,左括号前面留 1 个空格。

```
- (void)doSomethingWithParam:(NSString *)arg {
	if (i < j) {
	}	for (int i = 0; i < j; i++) { 
	}}```
多参数太多,一行不足以显示,则分行显示,以冒号对齐。
```- (void)doSomethingWithParam:(NSString *)arg1 
				   andParam2:(NSInteger)arg2 
				   andParam3:(NSDictionary *)arg3{//....}```
- 方法调用 所有参数应在同一行中,或者每个参数占用一行且使用冒号对齐。
``` [myObject doFooWith:arg1 name:arg2 error:arg3];```
或```
 [myObject doFooWith:arg1                name:arg2               error:arg3];
```
- 类声明与定义
类名第一个单词首字母大写,后续单词首字母大写。
类名前面留 1 个空格,冒号左右各留 1 个空格,多协议间逗号后面留 1 个空格。
```@interface MyProtocoledClass : NSObject<NSWindowDelegate, NSApplicationDelegate> {@private	//.... 	}@end```- 属性声明与定义
同方法命名规则,第一个单词首字母小写。Getter 的方法名和变量名应相同。 Setter 方法小写 set 为前缀
@property 和修饰符左括号间留 1 个空格,各修饰符间逗号后面留 1 个空格,修饰符右括号和属性类 型间留 1 个空格。
```
@property (nonatomic, assign) NSString *titl
```
- 变量声明与定义
同方法命名规则,第一个单词首字母小写。
为了区别局部变量和属性调用 (self.title) ,成员变量采用 _前缀。

```@interface MyProtocoledClass : NSObject<NSWindowDelegate, NSApplicationDelegate> {	NSString *_memberVariable; 
}@end```- 常量和宏声明与定义
常量名以小写字母 k 开头,使用混合大小写的格式来分隔单词,如 : 
```static const NSInteger kInvalidHandle = 0。
```
宏定义全程大写字母,每单词用下划线分割,如 :
```#define MAX_NUM_OF_FEED_CACHE 50 
```
枚举可参考上述常量或宏的方式来定义。

```typedef enum {	ENUM_APPID_MOOD = 311, //说说 	ENUM_APPID_BLOG = 2, //日志 	ENUM_APPID_PHOTO = 4, //相册 		ENUM_APPID_SHARE = 202, //分享} ENUM_APPID
```
- 注释
文件注释:以版权信息作为文件头部,开始每一个文件,后接文件内容的描述。(xcode 在生成文件时, 会自动加上)
声明注释:每个接口、类别以及协议应该注释,一描述它的目的及作用。


<br>
# 最佳实践
- 使用 #import 引入 Ojbective-C 和 Ojbective-C++头文件,使用 #include 引入 C 和 C++头。 
- 在初始化方法中,不要将变量初始化为“0”或“nil”,那是多余的。
- Objective-C 中,BOOL 被定义为 unsigned char,这意味着除了 YES (1) 和 NO (0)外它还可以是 其他值。禁止直接将 BOOL 和 YES/NO 比较,禁止将 int 直接转换(cast or convert)为 BOOL。
- Delegate Pattern(委托模式),delegate 对象使用 assign,禁止使用 retain。因为 retain 会导 致循环索引导致内存泄露 。
- Model/View/Controller(MVC模式),分离业务逻辑和展现逻辑。扩展模式 Model/Manger/View/Controller ,利于复用业务逻辑。
- 关于 pushViewController 的使用。

```        B -- CA -- <        B -- D
```
当 B 模块是通用模块,例如 UIImagePickerController ,是以一个独立的功能组件存在时,如果 需要从 B 模块进入其他模块,正确的做法是:利用 delegate 机制,将数据从该通用模块回调出来 ,再 在调用者 Controller 中进行 pushViewController 操作。
如果从 A 模块到 B 模块再到其他模块 ,是一整个固定的流程 ,例如从注册页到初始化页再到主页 , 这是一个固定的业务流程,B 模块一般不会成为独立组件,则应该在 B 模块中直接进行 pushViewController 操作。
- 关于函数分类整理。不同分类的函数组之间用 #pragma mark - **** 标注。 `init、dealloc、viewDidLoad、viewDidUnload、didReceiveMemoryWarning 函数`,应归类到一起。`viewWillAppear、viewDidAppear、viewWillDisappear、viewDidDisappear 函数`,应归类到一起。 
公共函数和私有函数应分别归类,公共函数应加上完整注释。
- 善用分类 (category)和扩展(Extension) 主类中提供对外接口(公共函数 ),对不同功能的函数多使用分类(category)使代码结构清晰。
```QZURLCache.hQZURLCache.mQZURLCache+Private.hQZURLCache+Private.mQZURLCache+Helper.hQZURLCache+Helper.m
QZURLCache+Delegate.h 
QZURLCache+Delegate.m 
QZURLCache+Reachability.h 
QZURLCache+Reachability.m
```在头文件中提供对外接口(公共函数 ),在实现文件中使用扩展(Extension)声明私有函数,降低头文 件复杂度,增强可读性。