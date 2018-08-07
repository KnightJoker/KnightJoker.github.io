title: Undefined symbols for architecture x86_64等的完整解决方案
description: linker command failed with exit code 1 (use -v to see invocation) 问题的修复。
categories: 
- iOS
tags:
- BugFix

---

原文出自[Luca大丑比](http://blog.sina.com.cn/s/blog_8070e98f0102x7fp.html)

相信每一个写iOS的同学都遇到过类似的问题，后面的x86_64偶尔也会是其他的架构，如armv7，i386。当然了，这个报错往往会伴随着以下这一句大家都再熟悉不过又最无可奈何说了等于没说的报错……

`clang: error: linker command failed with exit code 1 (use -v to see invocation)​`

今天在开发过程中也遇到了这个问题，并且尝试尽自己熟悉的几个方法（下面1-4点）都无法解决，查了4个小时的方法，终于解决了。在这里总结一下这个问题的一些常见解决方案，以及今天我遇到的“在模拟器报错，但是在真机稳定运行”的解决方案。

</br>

1.最常见问题：m文件没有导入   在Build Phases里的Compile Sources 中添加报错的文件​

![](http://images.cnitblog.com/blog/561767/201308/27103159-f5015f09f21c45b8b0749a31e263576d.png)

2.framework文件没有导入静态库编译时往往需要一些库的支持，查看你是否有没有导入的库文件同样是在Build Phases里的Link Binary With Libraries中添加

![](http://images.cnitblog.com/blog/561767/201308/27103232-71ff22b18cae4fbba9f111af4fb4a34a.png)

3.重复编译，可能你之前复制过两个地方，在这里添加过两次，删除时系统没有默认删除编译引用地址在Build Settings里搜索Search Paths  将里面Library Search Paths 中没有用到的地址删除

![](http://images.cnitblog.com/blog/561767/201308/27103303-583181fe731f4b2685d5766b838209cd.png)

4.另外一个问题，出在静态库生成上面。系统编译生成的静态库有两个，一个真机调用的，一个模拟器调用的。当你在真机测试时导入模拟器静态库，运行就会报错；同样在模拟器测试时调用真机静态库也会报错。

解决这一问题也很简单，就是将两个静态库合并，生成一个兼容的静态库。通过Show in finder 找到两个静态库文件，将两个文件复制到一个文件夹里，当然要进行重命名啦，否则就覆盖了。

下面打开终端，CD到存放两个文件的文件夹。

通过lipo[空格]-create[空格] [真机静态库文件名][空格] [模拟器静态库文件名][空格]-output[空格] [合并后的文件名]


![](http://images.cnitblog.com/blog/561767/201308/27103338-a4d680822a2a4873a451e1443557014a.png)

5.当再次遇见这个问题的时候，现象是在模拟器上无论怎么处理，都提示这个报错，但是真机却可以编译成功，甚至打包（Achieve）都可以打出来。
删除   /Users/用户名/Library/Developer/Xcode/DerivedData  这个文件夹下对应工程的文件，重新编译即可。