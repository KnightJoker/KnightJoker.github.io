title: 浅析AFNetworking（一）
description: Perhaps the most important feature of all, however, is the amazing community of developers who use and contribute to AFNetworking every day. AFNetworking powers some of the most popular and critically-acclaimed apps on the iPhone, iPad, and Mac.
date: 2017/06/13 08:46:25
categories: 
- iOS
tags:
- AFNetworking

---

![AFNetworking](https://camo.githubusercontent.com/1560be050811ab73457e90aee62cd1cd257c7fb9/68747470733a2f2f7261772e6769746875622e636f6d2f41464e6574776f726b696e672f41464e6574776f726b696e672f6173736574732f61666e6574776f726b696e672d6c6f676f2e706e67)

> Perhaps the most important feature of all, however, is the amazing community of developers who use and contribute to AFNetworking every day. AFNetworking powers some of the most popular and critically-acclaimed apps on the iPhone, iPad, and Mac.

随着4G网络的普及，手机移动数据的加速，AFNetworking 可以说是如今 iOS 开发中不可缺少的组件之一。

在这里主要了解两个问题：

- 如何使用 NSURLSession 发出 HTTP 请求

- 如何使用 AFNetworking 发出 HTTP 请求


# NSURLSession

NSURLSession 以及与它相关的类为我们提供了下载内容的 API，这个 API 提供了一系列的代理方法来支持身份认证，并且支持后台下载。

使用 NSURLSession 来进行 HTTP 请求并且获得数据总共有五个步骤：

1. 实例化一个 `NSURLRequest/NSMutableURLRequest`，设置 URL

2. 通过 - `sharedSession:` 方法获取 NSURLSession

3. 在 session 上调用 - `dataTaskWithRequest:completionHandler: `方法返回一个 NSURLSessionDataTask

4. 向 data task 发送消息 - resume，开始执行这个任务

5. 在` completionHandler` 中将数据编码，返回字符串。

```
NSMutableURLRequest *request = [[NSMutableURLRequest alloc] initWithURL:[[NSURL alloc] initWithString:@"http://dbcxl.com/"]];

NSURLSession *session = [NSURLSession sharedSession];

NSURLSessionDataTask *task = [session dataTaskWithRequest:request
                                       completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
                                           NSString *dataStr = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                                           NSLog(@"%@", dataStr);
                                       }];
[task resume];

```

当你在运行完这段代码之后，你就可以在控制台看见[来自遥远星系的技术空间站](http://dbcxl.com/)的 HTML 了。

# AFNetworking 

AFNetworking 的使用也是比较简单的，使用它来发出 HTTP 请求有两个步骤

1. 以服务器的主机地址或者域名生成一个` AFHTTPSessionManager` 的实例
2. 调用` - GET:parameters:progress:success:failure:` 方法

```
AFHTTPSessionManager *manager = [[AFHTTPSessionManager alloc] initWithBaseURL:[[NSURL alloc] initWithString:@"hostname"]];
[manager GET:@"relative_url" parameters:nil progress:nil
    success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        NSLog(@"%@" ,responseObject);
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        NSLog(@"%@", error);
    }];
```
