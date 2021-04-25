[toc]



# socket

服务器通信原理



# 阻塞、非阻塞，同步、异步、



同步、异步是一种通讯模式

阻塞、非阻塞是线程的处理模式



同步非阻塞的一种实践的是IO多路复用

Java语言的NIO组件所使用的就是IO多路复用模型。



## 异步IO

采取注册、回调、或者通知的方式，可以理解为观察者模式。



# Java NIO与OIO



## 比较

OIO是面向流，NIO是面向缓冲区

OIO是阻塞的，NIO是非阻塞的

OIO没有选择器，NIO有选择器

## NIO核心组件

- 通道 channel

- 缓冲区 buffer

- selector 选择器



### 缓冲区

用于读读写的区域

三个属性

limit

capacity

position



同一时刻，一个缓冲区只能是一个状态



buffer的几个方法



1、get

2、put

3、flip

4、clear



### Channel

通道，表示一个底层的文件描述符，在linux中，所有东西都是文件。Java NIO中，可以更加细化，比如，可以是不同的网络传输。



### FileChannel



### SocketChannel

ScoketChannel负责传输，支持阻塞和非阻塞两种模式。

ServerSocketChannel负责监听。

### selector

1、channel注册到选择器

2、IO事件

selector监控channel的状况。



# Reactor模式

>Reactor模式由Reactor线程、Handlers处理器两大角色组成，两大角色的职责分别如下：
>
>（1）Reactor线程的职责：负责响应IO事件，并且分发到Handlers处理器。
>
>（2）Handlers处理器的职责：非阻塞的执行业务处理逻辑。



## 1、解决什么问题？

OIO中CPT问题，每个请求一个线程会导致请求很多的时候线程很多，系统资源被大量消耗。线程间之间切换也是一笔开销。



Reactor通过一个线程处理大量的连接，



## 2、核心实现原理

### 2.1 单线程实现

重要组件

1、Reactor

> 负责查询IO事件，当检测到一个IO事件时将其发送给相应的Handler处理器去处理。这里的IO事件就是NIO中选择器查询出来的通道IO事件

2、Handler

> 与IO事件（或者选择键）绑定，负责IO事件的处理，完成真正的连接建立、通道的读取、处理业务逻辑、负责将结果写到通道等。



### 2.2 多线程实现

## 3、优缺点



# Netty入门

netty.io

1、异步

2、事件驱动

3、基于NIO



适用于：

服务端

客户端

TCP/UDP



特性

高吞吐

低延迟

低开销

零拷贝

可扩容

松耦合

易维护



基本概念

channel

NIO的基础概念，代表一个连接，可以执行读取、写入IO操作。netty对channel的IO操作是非阻塞的。

channelFuture



Event & Handler



Encoder & Decoder



ChannelPipline

