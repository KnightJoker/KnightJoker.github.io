
title: Linux下Nginx服务器的安装和配置

date: 2016/03/12 20:46:25

categories:
- Linux
tags:
- Nginx

---

Nginx ("engine x") 是一个高性能的HTTP和反向代理服务器，也是一个IMAP/POP3/SMTP服务器.其优点主要是在高连接并发的情况下，Nginx是Apache服务器不错的替代品。下面开始我们Nginx服务器的安装和配置：

## 1、Nginx的安装

这里的安装方法主要体现两种：

1.直接使用yum命令进行安装，如：
```
#yum install nginx
```
这样做的好处是方便快捷。

2.从Nginx的官方网站上面下载稳定版本，再使用tar等命令进行解压，安装。采取第二种方法可以自由控制Nginx版本，安装路径等。

在默认情况下，经过编译安装的Nginx包含了大部分可用模块，你也可以通过“./configure  --help”选项设置各个模块的使用情况。

安装完成后执行以下操作验证安装是否成功：
```
	# cd  /usr/local/nginx/sbin 
	# ./nginx -t 
```
结果显示：
```
nginx:	the configuration file	/usr/local/nginx/conf/nginx.conf syntax is ok
nginx:	configuration file /usr/local/nginx/conf/nginx.conf test is successful
```
至此，nginx的安装已成功完成。

## 2、Nginx的配置 

**2.1配置文件**

Nginx的主要配置文件是nginx.conf，位于安装目录下的nginx/conf文件夹里，这里有关Nginx配置问题，我们就主要对文件nginx.conf进行修改就可以了。

**2.2 详细配置**

```
#定义Nginx运行的用户和用户组
user nobody nobody;
#默认请求的设置
  location / { 
          root	 /root; #定义服务器的默认网站根目录位置
          index index.php index.html  index.htm;   #定义首页索引文件的名称
}
#web服务器设置
server {
        listen   80;     #监听80端口
       server_name  www.xx.com;   #定义使用www.xx.com访问
       access_log logs/www.xx.com.access.log  main;    #设定本虚拟主机的访问日志   
} 
```

存盘返回，到此有关于Nginx简单的服务器配置算是成功完成了。

启动nginx之后，浏览器中输入http://localhost可以验证是否安装启动成功。

（注意事项：启动Nginx服务器的时候，应该关闭其他，如Apache等服务器，以及两层防火墙，关闭防火墙命令如下：
```
	#/etc/init.d/iptables stop
	#/usr/sbin/setenforce 0
```
）
## 3、Nginx常用命令 
**3.1启动Nginx**
```
# ./sbin/nginx
# service nginx start
```
**3.2停止 Nginx**
```
# ./sbin/nginx -s stop/quit
# service nginx stop
```
**3.3其他nginx命令参数详解**

查看Nginx进程：
`#ps -ef|grep nginx`

查看Nginx的版本信息：
	`#nginx –v/-V`

Nginx 重载配置：
```
	#./sbin/nginx -s reload
	# service nginx reload
```

 