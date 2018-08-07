title: pug和sass的使用教程
description: 自己写了一个坑爹的pug和sass入门教程吧，网上查了半天，各种不靠谱，坑死了T T，自己动手丰衣足食！这里主要使用这两个工具来简化我们前端代码书写的工作量。
categories: 
- 前端
tags:
- pug

---

*一些写在前面的话，如果大家有什么看不懂，我有什么地方写错的，可以直接[邮件](cbearxl@gmail.com)私戳我。不要问我，有什么问题可以问问神奇的海螺，问我，我也不知道o(╯□╰)o*

以下文章有关学习：

- [谷歌](google.com)

- [Pug官方文档](https://pugjs.org/api/getting-started.html)

- [Pug的Github](https://github.com/pugjs/pug)

好了以下开始正文~

# 配置项目环境

##### Pug安装编译

`$ npm install -g pug`

之后运行`pug --version`正确显示版本号则表示安装成功。请注意，为了方便，这里是全局安装，Mac和Linux用户请在该命令前加`sudo`。在具体的项目开发时，也可以选择仅在目前项目下安装（去掉-g）。

这里如果遇见了

`bash: pug: command not found`

那么就是你的插入♂方式的不对，这样你就需要

`npm install -g pug-cli`

OK~事情完美解决，具体原因，Github给出的解释是这样的：

```
You need to install pug-cli. The CLI was separated from the core library as part of this release.
```
然后再打开命令行，就可以将你文件中的.pug文件编译成为大家熟悉的.html文件了~

`$ pug --pretty XX/XX.pug`	这里的XX指的是你的文件名

后面还说说一下sass和gulp的方法，这个地方等我先去预习预习~
