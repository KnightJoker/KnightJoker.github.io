title: Charles 入门
description: Charles 是在 Mac 下常用的网络封包截取工具，在做移动开发时，我们为了调试与服务器端的网络通讯协议，常常需要截取网络封包来分析。
categories: 
- 计算机基础
tags:
- charles

---

# Charles 入门

因为之前在写代码的时候，多用于应用层的一些开发，相对于网络层这边的可能就显得有些不太熟悉。

很多时候在和后台进行接口对接的时候，就会遇见一些莫名其妙的尴尬。

比如，有些时候你不知道是你客户端的逻辑出错，还是后台返回给你的数据有问题（又或者说是你对后台请求，后台并没有成功帮你处理等等之类的原因）

这种时候你就需要一个抓包的工具，把你每次对后台的请求（或者返回值给记录下来），这样也就能很清楚的知道到底是客户端的问题，还是后台数据问题了。 当然 Charles 的作用远远不止这些，下面也会为大家简单的介绍一下：

### 什么是 Charles

Charles 是在 Mac 下常用的网络封包截取工具，在做移动开发时，我们为了调试与服务器端的网络通讯协议，常常需要截取网络封包来分析。

Charles 通过将自己设置成系统的网络访问代理服务器，使得所有的网络访问请求都通过它来完成，从而实现了网络封包的截取和分析。

除了在做移动开发中调试端口外，Charles 也可以用于分析第三方应用的通讯协议。配合 Charles 的 SSL 功能，Charles 还可以分析 Https 协议。

Charles 主要的功能包括：

- 截取 Http 和 Https 网络封包。
- 支持重发网络请求，方便后端调试。
- 支持修改网络请求参数。
- 支持网络请求的截获并动态修改。
- 支持模拟慢速网络。

Charles 4 新增的主要功能包括：

- 支持 Http 2。
- 支持 IPv6。

### 安装和破解

Charles 是收费软件，可以免费试用 30 天。试用期过后，未付费的用户仍然可以继续使用，但是每次使用时间不能超过 30 分钟，并且启动时将会有 10 秒种的延时。只是当你需要长时间进行封包调试时，会因为 Charles 强制关闭而遇到影响。

下面就具体来说说，安装和破解 Charles 好了。

**安装**

直接去 Charles 的[官方网站](http://www.charlesproxy.com) 进行下载就可以了

**破解** （如果大家经济允许还是希望大家进行购买，良心软件）

网上下载一个对应 Charles 版本的 `jar破解包`

然后打开应用程序，选择Charles,选择显示包内容，`Contents->Resources->Java:`

下载下来的charles.jar替换这里的charles.jar就可以了。

至于破解包，我这里就为大家提供一个4.0.2版本的破解包好了(如果失效，就请大家自行去查找好了)

```
链接: https://pan.baidu.com/s/1pKGcQoz 
密码: 6c37
```

### 使用 Charles

之前提到，Charles 是通过将自己设置成代理服务器来完成封包截取的，所以使用 Charles 的第一步是将其设置成系统的代理服务器。

不过在你第一次启动应用的时候，他会询问你是否将它设置系统代理权限，你只需要点击是就好了。不过如果没有点击的话，你也可以直接这么设置就好了：

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/charles%E4%BB%A3%E7%90%86.png?raw=true)

然后接下来就是具体使用 Charles

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/charles%E4%B8%BB%E8%A6%81%E7%95%8C%E9%9D%A2.png?raw=true)

Charles 主要提供两种查看封包的视图，分别名为 `Structure` 和 `Sequence`。

- Structure 视图将网络请求按访问的域名分类。
- Sequence 视图将网络请求按访问的时间排序。

大家可以根据具体的需要在这两种视图之前来回切换。请求多了有些时候会看不过来，Charles 提供了一个简单的 Filter 功能，可以输入关键字来快速筛选出 URL 中带指定关键字的网络请求。

对于某一个具体的网络请求，你可以查看其详细的请求内容和响应内容。如果请求内容是 POST 的表单，Charles 会自动帮你将表单进行分项显示。如果响应内容是 JSON 格式的，那么 Charles 可以自动帮你将 JSON 内容格式化，方便你查看。如果响应内容是图片，那么 Charles 可以显示出图片的预览。

看见请求之后右键就可以对该请求作出各种各样的操作了，这里就不具体介绍了：

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/charles%E6%93%8D%E4%BD%9C.png?raw=true)

**截取 Https 通讯信息**

在 iOS 9 以后苹果引入了新特性 App Transport Security (ATS)。(ATS)新特性要求App内访问的网络必须使用HTTPS协议。

这就很蛋疼了，因为 charles 默认是不截取 https 信息的。

1、 安装证书

如果你需要截取分析 Https 协议相关的内容。那么需要安装 Charles 的 CA 证书。具体步骤如下。

首先我们需要在 Mac 电脑上安装证书。点击 Charles 的顶部菜单，选择 “Help” -> “SSL Proxying” -> “Install Charles Root Certificate”，然后输入系统的帐号密码，即可在 KeyChain 看到添加好的证书。如下图所示：

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/charles%E8%AF%81%E4%B9%A6.png?raw=true)


需要注意的是，即使是安装完证书之后，Charles 默认也并不截取 Https 网络通讯的信息，如果你想对截取某个网站上的所有 Https 网络请求，可以在该请求上右击，选择 SSL proxy，如下图所示：

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/charles%E8%AF%81%E4%B9%A6%E4%BB%A3%E7%90%86.png?raw=true)

这样我们就可以开心的截取https协议啦~