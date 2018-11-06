title: iOS App 打包上线流程以及各种证书
date: 2017/04/03 08:46:25
description: 
categories:
- iOS
tags:
- Distribution

---

之前涉及到一系列的 App 打包上传或者各样的紧急发布等等操作。然后再这个里面就因为各种各样的证书、密钥、签名等等一系列的东西。弄得我头都大了，花了一点时间，好好整理了一下各个之间的关系，希望能对大家有所帮助，哈哈哈，也便于以后个人忘了的时候，回过头来看看。

在 App 上线之前，需要理解或者了解的一些名词：`公钥`、`私钥`、`数字签名`、`数字证书`、`对称与非对称算法`、`HTTPS`

### 公钥加密、私钥解密 ———— 常用于保密信息

如果你想把某个消息秘密的发给某人，那你就可以用他的公钥加密。因为只有他知道他的私钥，所以这消息也就只有他本人能解开，于是你就达到了你的目的。

### 私钥加密、公钥解密 ———— 常用于数字签名

严格来说，这里说的私钥加密是用私钥对摘要进行加密，接收方可以用公钥解密，解密成功则可验证信息的发送者是私钥的拥有人。因为公钥是公开的，所以起不了保密信息的作用。

如果你想发布一个公告，需要一个手段来证明这确实是你本人发的，而不是其他人冒名顶替的。那你可以在你的公告开头或者结尾附上一段用你的私钥加密的内容，那所有其他人都可以用你的公钥来解密，看看解出来的内容是不是相符的。如果是的话，那就说明这公告确实是你发的---因为只有你的公钥才能解开你的私钥加密的内容，而其他人是拿不到你的私钥的。

但这仅仅做到了数字签名的第一部分：证明这消息是你发的。数字签名还有第二部分：证明这消息内容确实是完整的---也就是没有经过任何形式的篡改（包括替换、缺少、新增）。

要做到数字签名的第二部分，需要做的是：把你公告的原文做一次哈希（md5或者sha1都行），然后用你的私钥加密这段哈希作为签名，并一起公布出去。当别人收到你的公告时，他可以用你的公钥解密你的签名，如果解密成功，并且解密出来的哈希值确实和你的公告原文一致，那么他就证明了两点：这消息确实是你发的，而且内容是完整的。

### 对公钥进行的认证 ———— 数字证书

黑客可以替换你的公钥，然后用他的私钥做数字签名给你发信息，而你用黑客伪造的公钥能成功验证，会让你误认为消息来源没变。

这种情况下需要 *CA(证书中心certificate authority)*对公钥进行认证。证书中心用自己的私钥，对信息发送者的公钥和一些相关信息一起加密，生成 *"数字证书"(Digital Certificate）*。

通过在实际的使用中，公钥也不会单独出现，总是以数字证书的方式出现，以确保公钥的安全性和有效性。

### 对称与非对称算法

对称算法是说，加密过程和解密过程是对称的，用一个 密钥加密，可以用同一个密钥解密。使用公私钥的算法是非对称加密算法。

HTTPS一般使用了以下算法，其中就包括非对称和对称加密算法：
非对称加密算法：`RSA，DSA/DSS`
对称加密算法：`AES，RC4，3DES`
HASH算法：`MD5，SHA1，SHA256`
其中非对称加密算法用于在握手过程中加密生成的密码，对称加密算法用于对真正传输的数据进行加密，而HASH算法用于验证数据的完整性。

### HTTPS 的工作原理

HTTPS在传输数据之前需要客户端（浏览器）与服务端（网站）之间进行一次握手，在握手过程中将确立双方加密传输数据的密码信息。TLS/SSL协议不仅仅是一套加密传输的协议，更是一件经过艺术家精心设计的艺术品，TLS/SSL中使用了非对称加密，对称加密以及HASH算法。握手过程的简单描述如下：

    - 浏览器将自己支持的一套加密规则发送给网站。
    - 网站从中选出一组加密算法与HASH算法，并将自己的身份信息以证书的形式发回给浏览器。证书里面包含了网站地址，加密公钥，以及证书的颁发机构等信息。
    - 获得网站证书之后浏览器要做以下工作：

        + 验证证书的合法性（颁发证书的机构是否合法，证书中包含的网站地址是否与正在访问的地址一致等），如果证书受信任，则浏览器栏里面会显示一个小锁头，否则会给出证书不受信的提示。
        + 如果证书受信任，或者是用户接受了不受信的证书，浏览器会生成一串随机数的密码，并用证书中提供的公钥加密。
        + 使用约定好的HASH计算握手消息，并使用生成的随机数对消息进行加密，最后将之前生成的所有信息发送给网站。
 
    - 网站接收浏览器发来的数据之后要做以下的操作：

        + 使用自己的私钥将信息解密取出密码，使用密码解密浏览器发来的握手消息，并验证HASH是否与浏览器发来的一致。
        + 使用密码加密一段握手消息，发送给浏览器。

    - 浏览器解密并计算握手消息的HASH，如果与服务端发来的HASH一致，此时握手过程结束，之后所有的通信数据将由之前浏览器生成的随机密码并利用对称加密算法进行加密。

好了，介绍完这边之后，大概就可以介绍 App 发布之前的一些证书知识啥的了~

### Certification(证书)

证明你具有一个电脑开发资格的认证，每一个开发者有两种:

- Developer Certification (开发证书)

安装在电脑上提供权限：开发人员通过设备进行真机测试。可以生成副本供多台电脑安装

- Distribution Certification (发布证书)

安装在电脑上提供发布iOS程序的权限：开发人员可以制做测试版和发布版的程序。不可生成副本，仅有配置该证书的电脑才可使用。

### Provisioning Profile (授权文件)

授权文件是对设备如iPod Touch、iPad、iPhone的授权，文件内记录的是设备的UDID和程序的App Id，即：使被授权的设备可以安装或调试Bundle identifier与授权文件中记录的App Id对应的程序。

开发者帐号在创建授权文件时候会选择App Id，（开发者帐号下App Id中添加，单选）和UDID（开发者帐号下Devices中添加最多100个，多选）。
授权文件分为两种，对应相应的证书使用：

- Developer Provisioning Profile (开发授权文件)

- Distribution Provisioning Profile (发布授权文件)

### Keychain (开发密钥)

安装证书成功的情况下证书下都会生成Keychain，上面提到的证书副本（导出证书重新命名）就是通过配置证书的电脑导出Keychain（就是.p12文件）安装到其他机子上，让其他机子得到证书对应的权限。Developer Certification就可以制做副本Keychain分发到其他电脑上安装，使其可以进行真机测试。

*注意：Distribution Certification只有配置证书的电脑才可使用，因此即使导出导出Keychain安装到其他电脑上，其他电脑也不可能具有证书的权限。*