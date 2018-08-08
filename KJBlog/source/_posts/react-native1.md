title: React - Native 学习(一)
description:  React Native enables you to build world-class application experiences on native platforms using a consistent developer experience based on JavaScript and React.
date: 2017/10/05 08:46:25
categories:
- 前端
tags:
- react-native

---

# 关于 React - Native

这里直接引用 FaceBook 上面的一句话：

```
React Native enables you to build world-class application experiences on native platforms using a consistent developer experience based on JavaScript and React.
```

简单的翻译一下：RN（文章后面都这么简称React Native） 能够让你在 JS 和 React 上面创建世界级的 App，而且体验和用Objective - c (swift)开发的体验一样。 

因为自己之前对 JS 的基础比较差（基本上就只有一些常识性的认知），所以在 rn 系列的学习笔记中都会写得比较详细。也希望能够帮助更多的 rn 初学者，希望大家能够共同一起进步。

听起来是不是很 NB，嗯，确实，毕竟是脸书爸爸团队开发的，这点其实很重要，因为这样就会有固定的团队去维护这个社区（也就不会像某些咳咳咳，中途就死了的项目一样）。 至少从 gayhub 上面 4W+ 的stars 还能很能说明这个框架的前景的。

好了，下面就不废话了，开始具体的学习吧，其主要的学习内容来自 [官方文档](https://github.com/facebook/react-native)

### React 介绍

因为 RN 是基于JavaScript 和 React来设计的，关于JS这个语言，在这里就不介绍了，有兴趣的同学可以在这里[学习](http://www.w3school.com.cn/js/)

而 React 则是近几年火起来的一个前端的框架。

有的程序员也认为 React 不属于一个框架，因为它提供了一些新颖的概念、库、 和编程原则来让你能够同时在服务端和 Web 端编写快速、紧凑、漂亮的代码， 它能够让你方便快速的构建web应用的表现层（View）。

React 这个框架，也有两个最大的特点：

- **组件驱动开发**  不同于常见的 MVC 的视图-模型-控制器的设计模型，对React应用而言， 你需要将你的页面分割为一个个的组件。也就是说，开发页面的过程编程了组件开发的过程， 而页面就是组件的拼装结果。
通过分割组件的方式，你能够控制页面的复杂性，通过组件的方式去封装复杂的页面或某个功能区块。 并且，组件是可以被复用的。你可以将这个过程想象为：用乐高积木去拼装不同的物体。

- **Virtual DOM**  虚拟DOM，页面渲染变得更加高效，并且， DOM操纵也变得更加可控。基于这两点，React所构建的视图层具有强大的自上而下的页面渲染能力。（这个地方就可以补充一下从服务器发送过来的数据解析一般会有两种形式，第一种就是我们常见的JSON字段的解析，第二种便是在XML的解析，而XML解析又会细分为DOM解析和SAX解析）  基于React进行开发时所有的DOM构造都是通过虚拟DOM进行，每当数据变化时，React都会重新构建整个DOM树，然后React将当前整个DOM树和上一次的DOM树进行对比，得到DOM结构的区别，然后仅仅将需要变化的部分进行实际的浏览器DOM更新。 尽管每一次都需要构造完整的虚拟DOM树，但是因为虚拟DOM是内存数据，性能是极高的，而对实际DOM进行操作的仅仅是Diff部分，因而能达到提高性能的目的。

好了，React 介绍就讲这么多了，如果想学习更多的同学可以点击这里[ReactJS](https://facebook.github.io/react/docs/introducing-jsx.html)

### 安装 RN

*一些必须的软件,建议大家通过科学上网的方式或者更换npm源进行安装*

### Homebrew 

Mac系统的包管理器，用于安装NodeJS和一些其他必需的工具软件。

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

在Max OS X 10.11（El Capitan)版本中，homebrew在安装软件时可能会碰到/usr/local目录不可写的权限问题。可以使用下面的命令修复：

```
sudo chown -R `whoami` /usr/local
```


### Node、WatchMan

React Native目前需要NodeJS 5.0或更高版本。Homebrew默认安装的是最新版本，一般都满足要求。

而 Watchman 是由 Facebook 提供的监视文件系统变更的工具。安装此工具可以提高开发时的性能（packager可以快速捕捉文件的变化从而实现实时刷新）。

```
brew install node
brew install watchman
```

### The React Native CLI 、 Yarn

React Native的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务。
Yarn是Facebook提供的替代npm的工具，可以加速node模块的下载。（如果没有科学上网，推荐下载安装 Yarn）

```
npm install -g react-native-cli
npm install -g yarn
```

ps. 这里安装npm的时候，在国内可能会遇见更新速度很慢等各种原因，如果你们采取科学上网的手段的话，建议更换一下npm的源,命令如下`npm config set registry https://registry.npm.taobao.org `，最后再`npm install`就 OK 啦。

### Xcode

### 测试安装

```
react-native init AwesomeProject
cd AwesomeProject
react-native run-ios
```
或是双击ios/AwesomeProject.xcodeproj文件然后在Xcode中点击Run(或者command + R)按钮。

### Congratulations!

自从项目便配置完成，安心的去修改你的React Native 项目吧~

### 用 WebStorm 来写RN

首先第一步：File -> Defaults Settings，如图：

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/webstorm%E9%85%8D%E7%BD%AERN1.png?raw=true)

第二步：WebStorm -> preferences -> Settings，如图：

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/webstorm%E9%85%8D%E7%BD%AERN2.png?raw=true)

注意在选择 Libraries 的时候，只需要插入 React 和 React - Native 就可以了

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/webstorm%E9%85%8D%E7%BD%AERN3.png?raw=true)

### 使用 WebStorm 运行 React Native

这里主要解决的问题就是，每次我们运行 RN 的时候，都要打开你的命令行，然后再敲入
 `react-native run-ios(run-android)` 
 这样就显得特别的麻烦，所以这里也可以让你直接在WebStorm中直接跑RN

首先，右上角点击：Edit configurations

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/webstorm%E9%85%8D%E7%BD%AE%E5%9B%BE1.png?raw=true)

然后再填加一个 npm 运行，最后再添加一个 external tool

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/webstorm%E9%85%8D%E7%BD%AE%E5%9B%BE2.png?raw=true)

最后在配置一下 external tool,你就可以使用`command + R` 在 webstorm 里面运行 RN 了

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/master/Img/webstorm%E9%85%8D%E7%BD%AE%E5%9B%BE3.png?raw=true)



