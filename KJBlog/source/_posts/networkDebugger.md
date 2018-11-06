title: NetworkDebugger 网络监控组件实现
description: 在实际开发过程中，用户会因为实际的网络状况，在使用 App 的过程中可能会遇见各种导致 App 无法进行操作的场景，在很多关键的用户节点，就可能会导致大部分的用户流失，以往解决这些问题，只有靠客户端和服务端两边工程师的经验和猜想，然而如果能在 App 的视角对网络进行监控，进而在遇见网络问题的时候也能更好的在客户端进行处理，也能更有针对性的解决问题的根源。
categories: 
- iOS
tags:
- Networking

---

### 背景

 在实际开发过程中，用户会因为实际的网络状况，在使用 App 的过程中可能会遇见各种导致 App 无法进行操作的场景，在很多关键的用户节点，就可能会导致大部分的用户流失，以往解决这些问题，只有靠客户端和服务端两边工程师的经验和猜想，然而如果能在 App 的视角对网络进行监控，进而在遇见网络问题的时候也能更好的在客户端进行处理，也能更有针对性的解决问题的根源。


### 介绍

基于以上背景 MTNetworkDebugger 孕育而生，MTNetworkDebugger 是 MTHawkeye 中网络监控组件，它主要有以下几个功能：

- 网络监控: 负责记录并显示 App 与服务器之间发送和接受的所有数据。
- 数据分析: 针对已记录的网络数据，可提供诸如耗时、流量计算等具体数据。
- 数据导出: 对于所有的网络数据，可以通过粘贴板，AirDrop 等方式进行数据传输。

对于咱们美图项目来说，MTNetworkDebugger 可以更方便的集成于各种环境的 App 中。
对于开发人员也可以减少对 Charles 等三方工具的依赖，在接口联调过程中，也可以减少配置 Host 等步骤，最大程度上优化了开发效率。
对测试团队的小哥哥小姐姐来说，更是无需任何门槛，直接点击既可以使用了。

###  使用

--> 

### 组件实现

- **NSURLProtocol**

在 iOS 中 Apple 提供了 NSURLConnection、NSURLSession 等优秀的网路接口供我们来调用，而在开源社区也有类似 AFNetworking 和 Alamofire 等优秀的三方库，要实现网络监控务必是一定要和这些网络请求打交道的，
所以在开始初期阅读相关资料，发现 Apple 官方提供了一个抽象类 -- [NSURLProtocol](https://developer.apple.com/documentation/foundation/nsurlprotocol),让我们可以拦截 App 中的所有网络请求，以至于达到网络监控的目的。

在初步完成组件后，却发现 NSURLProtocol 存在一下几个缺点：

- 若一个项目中存在多个 NSURLProtocol，那么 NSURLProtocol 的拦截顺序跟注册的方式和顺序有关,可能导致数据显示的混乱。
- 在使用 NSURLSession 拦截到的 Request 中的 HTTPBody 为 nil 等。

除此之外，NSURLProtocol 在使用过程中还有较多 Apple 实现的深坑，故在后期实现 MTNetworkDebugger 的过程中放弃使用此方法，如果大家在网络监控的精确度不是很高的情况下，也可以采取此方法，在不同程度下会使得你开发效率有所提高。

 - **Hook 和 Proxy**

在经过一系列调研后，MTNetworkDebugger 决定通过 hook NSURLConnection、NSURLConnection 的创建方法，将 delegate 替换为 delegateProxy，来监听所有请求的各个过程。具体操作对应入下图：

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/hexo/KJBlog/source/_posts/Img/networkProcess.png?raw=true)

(在 MTNetworkDebugger 中没有直接使用 Method swizzling 替换相关 Api 来完成此操作,因为替换方法需要指定类名，但是 NSURLConnectionDelegate 和 NSURLSessionDelegate 是由业务方指定，通常来说是不确定，所以在这种场景下使用一个 Proxy 来达到消息转发的目的。)


关于 `NSURLConnection` 和 `NSURLSession` 我们都可以通过这样的方式去处理，具体以 Session 为例：

![](https://github.com/KnightJoker/KnightJoker.github.io/blob/hexo/KJBlog/source/_posts/Img/urlsession.jpeg?raw=true)

在进行 Hook 操作时，可以考虑在对应类创建时进行处理，这样可以避免后期进行遍历 Class List 和 Method List 等工作。

**NSURLSessionTaskMetrics / NSURLSessionTaskTransactionMetrics**


Apple 在 iOS 10 的 `NSURLSessionTaskDelegate` 代理中新增了 `-URLSession: task:didFinishCollectingMetrics:` 方法，如果实现这个代理方法，就可以通过该回调的 NSURLSessionTaskMetrics 类型参数获取到采集的网络指标，实现对网络请求中 DNS 查询/TCP 建立连接/TLS 握手/请求响应等各环节时间的统计。


- NSURLSessionTaskMetrics
    + `transactionMetrics:transactionMetrics` 数组包含了在执行任务时产生的每个请求/响应事务中收集的指标。
        ` @property (copy, readonly) NSArray<NSURLSessionTaskTransactionMetrics *> *transactionMetrics;`
    + `taskInterval`:任务从创建到完成花费的总时间，任务的创建时间是任务被实例化时的时间；任务完成时间是任务的内部状态将要变为完成的时间。
    + `redirectCount`:记录了被重定向的次数。

- NSURLSessionTaskTransactionMetrics
    + `request`:表示了网络请求对象
    + `response`:表示了网络响应对象，如果网络出错或没有响应时，response 为 nil。
    + `networkProtocolName`:获取资源时使用的网络协议，由 ALPN 协商后标识的协议，比如 h2, http/1.1, spdy/3.1。
    + `isProxyConnection`:是否使用代理进行网络连接。
    + `isReusedConnection`:是否复用已有连接。
    + `resourceFetchType`:NSURLSessionTaskMetricsResourceFetchType 枚举类型，标识资源是通过网络加载，服务器推送还是本地缓存获取的。

###  相关坑


- 不同线程读取 HTTPBody / allHTTPeaderFields 会引起 crash，注意处理线程问题。
- 通过 NSURLSession 和 NSURLConnection 发起请求的 Header 信息会损失固定几个字段，若需要完整信息，可以考虑通过 CFNetwork 进行处理。
- 通过 NSURLConnection 发出的网络请求 resquest.HTTPBody 拿到的是 nil，需要转而通过 HTTPBodyStream 读取完成。
- 在计算网络流量大小时候，从 `- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data` 获取到的回调数据 data 是已经经过了 CFNetwork 层的解压，所以按照 data 的长度计算获取到的值都会比真实的值要大一点。所以需要在应用层自己模拟一次 zip 的压缩，可以获取到更加接近真实传输的数据长度。

###  后话

自此 MTNetworkDebugger 的开发就告一段落了，不过我们后续仍会不断对 Hawkeye 组件进行更多的改进，如果大家在使用过程中遇见了什么 Bug，或者在开发过程中遇见了什么瓶颈，或者有什么不错的点子也欢迎大家多多进行交流。



