title: TCP/IP学习笔记（二）
description: Transmission Control Protocol/Internet Protocol的简写，中译名为传输控制协议/因特网互联协议，又名网络通讯协议，是Internet最基本的协议、Internet国际互联网络的基础，由网络层的IP协议和传输层的TCP协议组成。
date: 2016/02/02 08:46:25
categories: 
- 计算机基础
tags:
- TCP/IP

---

# UDP协议

UDP是传输层协议，和TCP协议处于一个分层中，但是与TCP协议不同，UDP协议并不提供超时重传，出错重传等功能，也就是说其是不可靠的协议。

# TCP协议

TCP和UDP处在同一层---运输层，但是TCP和UDP最不同的地方是，TCP提供了一种可靠的数据传输服务，TCP是面向连接的，也就是说，利用TCP通信的两台主机首先要经历一个“拨打电话”的过程，等到通信准备结束才开始传输数据，最后结束通话。所以TCP要比UDP可靠的多，UDP是把数据直接发出去，而不管对方是不是在收信，就算是UDP无法送达，也不会产生ICMP差错报文。

这里也就简述一下TCP保证可靠性工作的原理：

1. 应用数据被分割成TCP认为最适合发送的数据块。这和UDP完全不同，应用程序产生的数据报长度将保持不变。

2. 当TCP发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段。

3. 当TCP收到发自TCP连接另一端的数据，它将发送一个确认。

4. TCP将保持它首部和数据的检验和。这是一个端到端的检验和，目的是检测数据在传输过程中的任何变化。如果收到段的检验和有差错， TCP将丢弃这个报文段和不确认收到此报文段（希望发端超时并重发）。

5. TCP报文段作为IP数据报来传输。IP数据报失序后，也会导致TCP报文段失序。

6. TCP还能提供流量控制。TCP连接的每一方都有固定大小的缓冲空间。TCP的接收端只允许另一端发送接收端缓冲区所能接纳的数据。这将防止较快主机致使较慢主机的缓冲区溢出。

有以上便可以得出TCP保持可靠性的方式就是**超时重发**

可以想象一个TCP数据的发送应该是如下的一个过程：

- 双方建立连接

- 发送方给接受方TCP数据报，然后等待对方确认TCP数据报，如果没有，就重新发，如果有，就发送下一个数据报。

- 接受方等待发送方的数据报，如果得到数据报并检验无误，就发送ACK（确认）数据报，并等待下一个TCP数据报的到来。直到收到FIN（发送完成数据报）

- 终止连接

### TCP连接的建立与中止

TCP连接的建立可以简单的称为三次握手，而连接的中止则可以叫做四次握手。

###### 三次握手（连接的建立）

1. 第一次
	- 建立连接时，客户端向服务器发送SYN包，等待服务器确认。
2. 第二次
	- 服务器收到SYN包，确认客户的SYN后，自己在发送一个SYN+ACK的包
3. 第三次
	- 客户端收到服务器的SYN+ACK包，向服务器发送确认包ACK，此包发送完毕后，客户端和服务器便进入TCP连接成功状态，完成三次握手。

###### 四次握手（中止连接）

1. TCP客户端发送一个FIN，用来关闭客户到服务器的数据传输。

2. 服务器收到这个FIN，它发回一个ACK，确认序号为收到序号加1。和SYN一样，一个FIN将占用一个序号。

3. 服务器关闭客户端的连接，发送一个FIN给客户端。

4. 客户端发回ACK报文确认，并将确认序号设置为收到序号加1.