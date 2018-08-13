
title: 无需越狱，自动集成的动态调试工具 —— MonkeyDev
description: MonkeyDev,一套开源的逆向调试工具可以在非越狱或者越狱手机上任（♂）意地为应用添加插件。
date: 2018/08/12 08:46:25
categories: 
- iOS
tags:
- 逆向开发

---

## 背景

最近在进行组件开发的时候，想在一些已有项目上集成组件测试，但出于种种原因，未能拿到项目代码。基于此，向大家推荐一套开源的逆向调试工具 —— [MonkeyDev](https://github.com/AloneMonkey/MonkeyDev)，可以在非越狱或者越狱手机上任（♂）意地为应用添加插件。
简单概括一下该工具的几个主要功能：

- 可以使用 Xcode 开发 CaptainHook Tweak、Logos Tweak 和 Command-line Tool，在越狱机器开发插件。
- 只需要一个砸壳的 ipa 或者 app ，MonkeyDev 就可以帮你自动集成 Reveal、Cycript 和注入的动态库并重签名安装到你的非越狱机器上。
- 支持调试你自己编写的动态库和一些其他的 App
- 可以通过 CocoaPods 搭建一个非越狱插件商店

为了，使大家更好的了解其具体功能，文中将会以美拍 App 以及一台 iPhone 6s Plus, iOS 11.4(非越狱)的机器来进行示例。

## 安装和配置

文中会主要讲解一下关键性成功步骤，如果大家自行操作的过程中遇见了一些奇怪的问题，可以结合 [WiKi](https://github.com/AloneMonkey/MonkeyDev/wiki/%E5%AE%89%E8%A3%85) 或者 [Issues](https://github.com/AloneMonkey/MonkeyDev/issues) 进行操作。

#### 环境

- 安装最新的 [theos](https://github.com/theos/theos/wiki/Installation)

```
    sudo git clone --recursive https://github.com/theos/theos.git /opt/theos
```

- 安装 [ldid](http://iphonedevwiki.net/index.php/Ldid)

```
    brew install ldid
```

#### 安装

- 执行安装命令

```
    xcode-select -p
    sudo /bin/sh -c "$(curl -fsSL https://raw.githubusercontent.com/AloneMonkey/MonkeyDev/master/bin/md-install)"
```

- 卸载

```
    sudo /bin/sh -c "$(curl -fsSL https://raw.githubusercontent.com/AloneMonkey/MonkeyDev/master/bin/md-uninstall)"
```

安装之后，再重启一下 Xcode 便可以使用 MonkeyDev 了。


#### 使用

**在使用之前，需要准备好已经砸壳的 ipa 或者 app**

然后打开你的 Xcode ，点击 `File - New - Project` 创建属于你的 MonkeyApp 。

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/hexo/KJBlog/source/_posts/Img/Create_project.png?raw=true)

创建完成之后，你的工程文件就长这个样子：

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/hexo/KJBlog/source/_posts/Img/Project_files.png?raw=true)

- `MTMonkeyDemoDylib` 这一整个文件下便是即将注入美拍的动态库

    + `MTMonkeyDemoDylib.m` 文件中可以写自己的 Hook 代码，本身支持 ObjC runtime 的 Hook 和 C函数的 fishhook。
    + `MTMonkeyDemoDylib.xm` 也支持 theos logtweak 的写法。
    + `Config` 中放置的是 cycript 的一些脚本下载以及 methodtrace 配置代码。
    + `Tools` 中放置的是 LLDB 的调试代码。
    + `AntiAntiDebug` 反反调试的代码。
    + `fishhook` 集成的 fishhook 模块
    
**拖入编译**

将你准备好的 ipa 文件（或者 app 文件夹）放入 `TargetApp` 目录下(*注意`put ipa or app here` 这个文件不要删除*)

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/hexo/KJBlog/source/_posts/Img/IPA_path.png?raw=true)

好了，到此前期的工作就结束了，直接编译运行到你的手机上就可以啦~

编译过程中也可以使用个人开发者账号进行签名，记得 `MTMonkeyDemo` 和 `MTMonkeyDemoDylib` 选择一直证书即可。

**编译成功**

之后你就可以打开你电脑上面的 `Reveal`, 查看美拍的界面，进行实时修改并查看结果了。

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/hexo/KJBlog/source/_posts/Img/meipai_ui.png?raw=true)

这里需要注意的是 Monkey 中集成的 `Reveal.framework` 是最新版本的，所以你可能需要最新版本的 Reveal，否则使用自己的`Reveal.framework` 替换掉 `/opt/MonkeyDev/frameworks`下面的 `Reveal.framework`。

Reveal 是一个允许开发者在不修改代码、不重新构建项目、不重新部署应用程序的情况下就能够调试 iOS 应用界面的软件。 文末将会提供一个 Reveal 4 破译版本，仅供大家学习使用，如有需要请大家自行在[官网](http://revealapp.com/download/)下载最新版本使用。


**或者从[Cycript](http://www.cycript.org/)下载SDK，然后进去SDK目录运行如下命令，Cycript查看界面**

**动态调试**

等以上都完成以后，就可以开始我们的动态调试了，比如 我现在想 Hook 一下美拍登陆页面的 `viewDidLoad` ,让他什么都不做, 输出一个 666666

我们可以在 `MTMonkeyDemoDylib.m` 文件中添加以下代码即可：

```
CHDeclareClass(LoginViewController)

CHOptimizedMethod0(self, void, LoginViewController, viewDidLoad){
    
    NSLog(@"666666");
}

CHConstructor{
    CHLoadLateClass(LoginViewController);

    CHHook0(LoginViewController, viewDidLoad);
}
```

重新运行便可以达到效果了，当然这个过程中你也可以断点调试，已达到你想要的一些目的。

**使用 CocoaPods 集成三方SDK**

也可以通过这样的方式在美拍线上包中集成 Hawkeye,观察项目中我们一些可能需要的数据。

在 MTMonkeyDemo 路径下新建一个 `Podfile`

```
source 'http://techgit.meitu.com/iosmodules/specs.git'

platform :ios, "8.0"

use_frameworks! 

target 'MTMonkeyDemoDylib' do
    pod 'MTHawkeye'
end

```

然后在 `MTMonkeyDemoDylib.m` 文件中加入 Hawkeye 初始化代码:

```
#import <MTHawkeye/MTHawkeye.h>

void initCycriptServer(){
    [[NSNotificationCenter defaultCenter] addObserverForName:UIApplicationDidFinishLaunchingNotification object:nil queue:[NSOperationQueue mainQueue] usingBlock:^(NSNotification * _Nonnull note) {
        [MTHawkeyeSettings shared].showHideMonitorGestureEnabled = YES;
        [MTHawkeye start];
        CYListenServer(6666);
    }];
}
```

这样美拍的线上包集成 Hawkeye 也就完成了，当然 MonkeyDev 可以支持 CocoaPods了，那么可以将我们自己写的非越狱插件传到 CocoaPods，然后通过 Pod 一键安装，到达一个 Pod 插件商城的效果。




