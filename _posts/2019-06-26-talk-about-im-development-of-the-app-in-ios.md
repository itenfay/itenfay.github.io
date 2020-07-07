---
layout: post
title: "谈谈 iOS 应用中的 IM 开发"
header-img: "images/im/im_cover.png"
author: "dgynfi"
date: 2019-06-26
tag: IM
---


> 本文来自：iOS端IM开发从入门到填
>
> 链接：[https://www.jianshu.com/p/b1d54fd570ef](https://www.jianshu.com/p/b1d54fd570ef)


## IM 的实现方式

**1. 使用第三方 IM 服务**

在国内有很多的[ IM 第三方服务商](https://juejin.im/post/5a61fc7cf265da3e23668df3)，底层协议基本上都是基于 TCP 的，例如："网易云信、环信、融云、极光 IM、LeanCloud、云通信（腾讯）、云旺（阿里）、容联云、小能、美洽等等"，技术也相对比较成熟，提供后台管理和定制化的 UI，半小时可集成。

缺点也很明显：定制化程度太高，需要二次开发，很多东西我们不可控，**关键是太贵了**。如果 IM 对于 APP 只是一个辅助功能，如客服系统、消息推送等，也基本够用。

**2. 切合业务自己实现**

几乎所有互联网IM产品都采用服务器中转方式进行消息传输。自己实现也会面临许多选择：
1. 传输协议的选择：TCP 还是 UDP？
2. 选择哪种聊天协议进行开发：MQTT、XMPP、基于 Socket 原生或 WebSocket 的私有协议？
3. 传输数据的格式：用 JSON、还是 XML、还是谷歌推出的 ProtocolBuffer？
4. 我们还有一些细节问题需要考虑，例如 TCP 的长连接如何保持，心跳机制，Qos 机制，重连机制等等。另外，还有一些安全问题需要考虑。

## 一、传输协议选型

移动端IM的传输协议选型：TCP 还是 UDP？

- TCP：基于连接的可靠协议的全双工的可靠信道，有流量控制、差错控制等，占用系统资源较多，传输效率相对低。
- UDP：基于无连接的不可靠协议，没有足够的控制手段，传输效率高，有丢包问题。

基于 UDP 协议开发成本较高，容易各种丢包或乱序，一般小公司或技术不成熟或即时性要求不高的公司，多用 TCP 开发。[TCP 和 UDP 的最完整的区别](https://blog.csdn.net/Li_Ning_/article/details/52117463)

[QQ-IM的私有协议](http://www.52im.net/forum.php?mod=viewthread&tid=279&page=1#pid1092ttp://www.52im.net/forum.php?mod=viewthread)：登录等安全性操作使用TCP协议，好友之间发消息主要使用UDP协议，内网传输文件采用了P2P技术，另外腾讯还用了自己的私有协议，来保证传输的可靠性。

## 二、聊天协议的选择

首先我们以实现方式来切入，基本上有以下四种实现方式：

1. 基于Socket原生：代表框架 [CocoaAsyncSocket](https://github.com/robbiehanson/CocoaAsyncSocket)。
2. 基于WebSocket：代表框架 [SocketRocket](https://github.com/facebookarchive/SocketRocket)。
3. 基于MQTT：代表框架 [MQTTKit](https://github.com/mobile-web-messaging/MQTTKit)。
4. 基于XMPP：代表框架 [XMPPFramework](https://github.com/robbiehanson/XMPPFramework)。

以上四种方式都可以不使用第三方框架，直接基于OS底层Socket去实现我们的自定义封装。其中MQTT和XMPP为聊天协议，是最上层的协议，而WebSocket是传输通讯协议，它是基于Socket封装的一个协议。而上面所说的QQ-IM的私有协议，就是基于WebSocket或者Socket原生进行封装的一个聊天协议。

![聊天协议优劣对比](https://dgynfi.github.io/images/im/chat_protocol_comparison.png)

总之，iOS端要做一个真正的IM产品，一般都是基于Socket或WebSocket等，在之上加上一些私有协议来保证的。

## 三、实现一个简单的IM

### 1.Socket概述

Socket其实并不是一个协议，Socket通常也称作”套接字”，是对TCP/IP 或者UDP/IP协议封装的一组编程接口，用于描述IP地址和端口，使用socket实现进程之间的通信（跨网络的）。它工作在 OSI 模型会话层（第5层），Socket是对TCP/IP等更底层协议封装的一个抽象层，是一个调用接口(API)。网络上的两个程序通过一个双向的通讯连接实现数据的交换，这个双向链路的一端称为一个Socket，一个Socket由一个IP地址和一个端口号唯一确定。

![网络架构](https://dgynfi.github.io/images/im/network_framework.png)

先看下基于C的BSD Socket提供的接口：
```
// socket 创建并初始化 socket，返回该 socket 的文件描述符，如果描述符为 -1 表示创建失败。
int socket(int addressFamily, int type,int protocol);

// 关闭socket连接
int close(int socketFileDescriptor);

// 将 socket 与特定主机地址与端口号绑定，成功绑定返回0，失败返回 -1。
int bind(int socketFileDescriptor,sockaddr *addressToBind,int addressStructLength);

// 接受客户端连接请求并将客户端的网络地址信息保存到 clientAddress 中。
int accept(int socketFileDescriptor,sockaddr *clientAddress, int clientAddressStructLength);

// 客户端向特定网络地址的服务器发送连接请求，连接成功返回0，失败返回 -1。
int connect(int socketFileDescriptor,sockaddr *serverAddress, int serverAddressLength);

// 使用 DNS 查找特定主机名字对应的 IP 地址。如果找不到对应的 IP 地址则返回 NULL。
hostent* gethostbyname(char *hostname);

// 通过 socket 发送数据，发送成功返回成功发送的字节数，否则返回 -1。
int send(int socketFileDescriptor, char *buffer, int bufferLength, int flags);

// 从 socket 中读取数据，读取成功返回成功读取的字节数，否则返回 -1。
int receive(int socketFileDescriptor,char *buffer, int bufferLength, int flags);

// 通过UDP socket 发送数据到特定的网络地址，发送成功返回成功发送的字节数，否则返回 -1。
int sendto(int socketFileDescriptor,char *buffer, int bufferLength, int flags, sockaddr *destinationAddress, int destinationAddressLength);

// 从UDP socket 中读取数据，并保存发送者的网络地址信息，读取成功返回成功读取的字节数，否则返回 -1 。
int recvfrom(int socketFileDescriptor,char *buffer, int bufferLength, int flags, sockaddr *fromAddress, int *fromAddressLength);
```

我们用基于OS底层的原生Socket来实现一个简单的IM。[Socket扩展阅读](https://blog.csdn.net/yeyuangen/article/details/6799575)

### 2.搭建IM服务端

**服务端需要做的工作简单的总结下：**

```
1.服务器调用 socket(...) 创建socket；
2.绑定IP地址、端口等信息到socket上，用函数bind(); 
3.服务器调用 listen(...) 设置缓冲区；
4.服务器通过 accept(...)接受客户端请求建立连接；
5.服务器与客户端建立连接之后，通过 send(...)/receive(...)向客 
户端发送或从客户端接收数据；
6.服务器调用 close 关闭 socket；
```

服务端可以电脑或手机等终端，也可以用多种语言c/c++/java/js等去实现后台，当然OC也可以实现。这里我们借用node.js实现了一个服务端，来验证socket效果。需要在Mac上安装node解释器，[node下载](https://nodejs.org/zh-cn/)，直接下载安装即可，也可以[终端命令安装node](https://blog.csdn.net/shirleyqdd/article/details/80342082?utm_source=blogxgwz4)。

**开启服务器**:

```
1.打开终端
2.cd到目录 服务端(node.js)
3.node Server.js   #开启IM服务器
```

### 3.实现IM客户端

**IM客户端需要做如下5件事**：

```
1.客户端调用 socket(...) 创建socket；
2.绑定IP地址、端口等信息到socket上，用函数bind();
3.客户端调用 connect(...) 向服务器发起连接请求以建立连接；
4.客户端与服务器建立连接之后，就可以通过send(...)/receive(...)向客户端发送或从客户端接收数据；
5.客户端调用 close 关闭 socket；
```

**代码实现**

我们采用[CocoaAsyncSocket](https://github.com/robbiehanson/CocoaAsyncSocket)框架，封装一个名为WYKSocketManager的单例，来对socket相关方法进行调用。

**为了demo演示方便，代码中使用的时间都较短，实际开发中根据需要设置**

```
#import "WYKSocketManager.h"
#import "GCDAsyncSocket.h" // for TCP

static NSString *Khost = @"127.0.0.1";
static uint16_t  Kport = 6969;
static NSInteger KPingPongOutTime  = 3;
static NSInteger KPingPongInterval = 5;

@interface WYKSocketManager()<GCDAsyncSocketDelegate>

@property (nonatomic, strong) GCDAsyncSocket *gcdSocket;
@property (nonatomic, assign) NSTimeInterval reConnectTime;
@property (nonatomic, assign) NSTimeInterval heartBeatSecond;
@property (nonatomic, strong) NSTimer *heartBeatTimer;
@property (nonatomic, assign) BOOL socketOfflineByUser;  //!< 主动关闭
@property (nonatomic, retain) NSTimer *connectTimer; // 计时器

@end

@implementation WYKSocketManager

- (void)dealloc
{
    [self destoryHeartBeat];
}

+ (instancetype)share
{
    static dispatch_once_t onceToken;
    static WYKSocketManager *instance = nil;
    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        [instance initSocket];
    });
    return instance;
}

- (void)initSocket
{
    self.gcdSocket = [[GCDAsyncSocket alloc] initWithDelegate:self delegateQueue:dispatch_get_main_queue()];
}

#pragma mark - 对外的一些接口
//建立连接
- (BOOL)connect
{
    self.reConnectTime = 0;
    return [self autoConnect];
}

//断开连接
- (void)disConnect
{
    self.socketOfflineByUser = YES;
    [self autoDisConnect];
}

//发送消息
- (void)sendMsg:(NSString *)msg
{
    NSData *data = [msg dataUsingEncoding:NSUTF8StringEncoding];
    //第二个参数，请求超时时间
    [self.gcdSocket writeData:data withTimeout:-1 tag:110];
}

#pragma mark - GCDAsyncSocketDelegate
//连接成功调用
- (void)socket:(GCDAsyncSocket *)sock didConnectToHost:(NSString *)host port:(uint16_t)port
{
    NSLog(@"连接成功,host:%@,port:%d",host,port);
    //pingPong
    [self checkPingPong];
    //心跳写在这...
    [self initHeartBeat];
}

//断开连接的时候调用
- (void)socketDidDisconnect:(GCDAsyncSocket *)sock withError:(nullable NSError *)err
{
    NSLog(@"断开连接,host:%@,port:%d",sock.localHost,sock.localPort);
    if (self.delegate && [self.delegate respondsToSelector:@selector(showMessage:)]) {
        NSString *msg = [NSString stringWithFormat:@"断开连接,host:%@,port:%d",sock.localHost,sock.localPort];
        [self.delegate showMessage:msg];
    }

    if (!self.socketOfflineByUser) {
        //断线/失败了就去重连
        [self reConnect];
    }
}

//写的回调
- (void)socket:(GCDAsyncSocket*)sock didWriteDataWithTag:(long)tag
{
    NSLog(@"写的回调,tag:%ld",tag);
    //判断是否成功发送，如果没收到响应，则说明连接断了，则想办法重连
    [self checkPingPong];
}

//收到消息
- (void)socket:(GCDAsyncSocket *)sock didReadData:(NSData *)data withTag:(long)tag
{
    NSString *msg = [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
    NSLog(@"收到消息：%@",msg);
    if (self.delegate && [self.delegate respondsToSelector:@selector(showMessage:)]) {
        [self.delegate showMessage:[NSString stringWithFormat:@"收到：%@",msg]];
    }
    //去读取当前消息队列中的未读消息 这里不调用这个方法，消息回调的代理是永远不会被触发的
    [self pullTheMsg];
}

//为上一次设置的读取数据代理续时 (如果设置超时为-1，则永远不会调用到)
- (NSTimeInterval)socket:(GCDAsyncSocket *)sock shouldTimeoutReadWithTag:(long)tag elapsed:(NSTimeInterval)elapsed bytesDone:(NSUInteger)length
{ 
    NSLog(@"来延时，tag:%ld,elapsed:%f,length:%ld",tag,elapsed,length);
    if (self.delegate && [self.delegate respondsToSelector:@selector(showMessage:)]) {
        NSString *msg = [NSString stringWithFormat:@"来延时，tag:%ld,elapsed:%.1f,length:%ld",tag,elapsed,length];
        [self.delegate showMessage:msg];
    }
    return KPingPongInterval;
}

#pragma mark- Private Methods
- (BOOL)autoConnect
{
    return [self.gcdSocket connectToHost:Khost onPort:Kport error:nil];
}

- (void)autoDisConnect
{
    [self.gcdSocket disconnect];
}

//监听最新的消息
- (void)pullTheMsg
{
    //监听读数据的代理，只能监听10秒，10秒过后调用代理方法  -1永远监听，不超时，但是只收一次消息，
    //所以每次接受到消息还得调用一次
    [self.gcdSocket readDataWithTimeout:-1 tag:110];
}

//用Pingpong机制来看是否有反馈
- (void)checkPingPong
{
    //pingpong设置为3秒，如果3秒内没得到反馈就会自动断开连接
    [self.gcdSocket readDataWithTimeout:KPingPongOutTime tag:110];
}

//重连机制
- (void)reConnect
{
    //如果对一个已经连接的socket对象再次进行连接操作，会抛出异常（不可对已经连接的socket进行连接）程序崩溃
    [self autoDisConnect];
    //重连次数 控制3次
    if (self.reConnectTime >= 5) {
        return;
    }
    __weak __typeof(self) weakSelf = self;
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(self.reConnectTime * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        __strong typeof(weakSelf) strongSelf = weakSelf;
        if (strongSelf.delegate && [strongSelf.delegate respondsToSelector:@selector(showMessage:)]) {
            NSString *msg = [NSString stringWithFormat:@"断开重连中，%f",strongSelf.reConnectTime];
            [strongSelf.delegate showMessage:msg];
        }
        strongSelf.gcdSocket = nil;
        [strongSelf initSocket];
        [strongSelf autoConnect];
    });

    //重连时间增长
    if (self.reConnectTime == 0) {
        self.reConnectTime = 1;
    } else {
        self.reConnectTime += 2;
    }
}

//初始化心跳
- (void)initHeartBeat
{
    [self destoryHeartBeat];
    // 每隔5s像服务器发送心跳包
    self.connectTimer = [NSTimer scheduledTimerWithTimeInterval:5
                                                         target:self selector:@selector(longConnectToSocket)
                                                       userInfo:nil
                                                        repeats:YES];
    // 在longConnectToSocket方法中进行长连接需要向服务器发送的讯息
    [self.connectTimer fire];
}

// 心跳连接
-(void)longConnectToSocket
{
    // 根据服务器要求发送固定格式的数据，但是一般不会是这么简单的指令
    [self sendMsg:@"心跳连接"];
}

//取消心跳
- (void)destoryHeartBeat
{
    if (self.heartBeatTimer  && [self.heartBeatTimer isValid]) {
        [self.heartBeatTimer invalidate];
        self.heartBeatTimer = nil;
    }
}

@end
```

我们发了一条消息，服务端成功的接收到了消息后，把该消息再发送回客户端，绕了一圈客户端又收到了这条消息。至此我们用OS底层socket实现了简单的IM。这里仅仅是实现了Socket的连接并传输字符串，我们要做的远不止于此。

### 3.四个重要的功能：心跳机制、PingPong机制、断线重连、消息可达

#### (1)心跳机制

心跳机制是相对时间内主动向服务器发送心跳包消息，用来检测TCP连接的双方是否可用。TCP的KeepAlive机制只能保证连接的存在，但是并不能保证客户端以及服务端的可用性。

扩展阅读：

[为什么说基于TCP的移动端IM仍然需要心跳保活？](http://www.52im.net/thread-281-1-1.html)

真正需要心跳机制的原因其实主要是在于国内运营商的网络地址转换设备超时，对于家用路由器来说, 使用的是网络地址端口转换(NAPT), 它不仅改IP, 还修改TCP和UDP协议的端口号, 这样就能让内网中的设备共用同一个外网IP，造成连接存在，但并不一定可用。

**而国内的运营商一般NAT超时的时间为5分钟，频繁心跳会带来耗电和耗流量的弊端，所以通常IM心跳设置的时间间隔为3-5分钟，甚至10分钟都行**。微信有一种更高端的实现方式，有兴趣的小伙伴可以看看：[微信的智能心跳实现方式](http://www.52im.net/thread-120-1-1.html)

#### (2)PingPong机制

心跳机制是不能完全保证消息的即时性的，业内的解决方案是辅助采用双向的PingPong机制。

![PingPong机制](https://dgynfi.github.io/images/im/pingpong_mechanism.jpg)

当服务端发出一个Ping，客户端没有在约定的时间内返回响应的ack，则认为客户端已经不在线，这时我们Server端会主动断开Socket连接，并且改由APNS推送的方式发送消息。
同样的是，当客户端去发送一个消息，因为我们迟迟无法收到服务端的响应ack包，则表明客户端或者服务端已不在线，我们也会显示消息发送失败，并且断开Socket连接。

#### (3)重连机制

理论上，自己主动断开的Socket连接（如退出账号，APP退出到后台等），不需要重连。其他的连接断开，我们都需要进行断线重连。一般解决方案是尝试重连几次，如果仍旧无法重连成功，那么不再进行重连。

#### (4)消息可达（即QoS机制）

在移动网络下，丢包、网络重连等情况非常之多，为了保证消息的可达，一般需要做消息回执和重发机制。

一般有三种类型：

QOS(0),最多发送一次：如果消息没有发送过去，那么就直接丢失。<br />
QOS(1),至少发送一次：保证消息一定发送过去，但是发几次不确定。<br />
QOS(2),精确只发送一次：它内部会有一个很复杂的发送机制，确保消息送到，而且只发送一次。

参考易信，每条消息会最多会有3次重发，超时时间为15秒，同时在发送之前会检测当前连接状态，如果当前连接并没有正确建立，缓存消息且定时检查(每隔2秒检查一次，检查15次)。所以一条消息在最差的情况下会有2分钟左右的重试时间，以保证消息的可达。因为重发的存在，接受端偶尔会收到重复消息，这种情况下就需要接收端进行去重。通用的做法是每条消息都戴上自己唯一的message id(一般是uuid)。

扩展阅读：

- [IM消息送达保证机制实现](http://www.52im.net/thread-294-1-1.html)

### 4.IM的其他实现方式

#### (1)基于WebSocket最具代表性的一个第三方框架-[SocketRocket](https://github.com/facebook/SocketRocket)

实现的思路和基于CocoaAsyncSocket框架类似，需要编写遵守webSocket协议的服务端，感兴趣的也可以参照实现一下。

#### (2)基于MQTT协议的框架-[MQTTKit](https://github.com/mobile-web-messaging/MQTTKit)

MQTT是一个聊天协议，它比webSocket更上层，属于应用层，它的基本模式是简单的发布订阅，也就是说当一条消息发出去的时候，谁订阅了谁就会收到消息。其实它并不适合IM的场景，例如用来实现有些简单IM场景，却需要很大量的、复杂的处理。这个框架是c来写的，把一些方法公开在MQTTKit类中，对外用OC来调用，这个库有4年没有更新了。

#### (3)基于XMPP协议的框架-[XMPPFramework](https://github.com/robbiehanson/XMPPFramework)

XMPP是较早的聊天协议（2000年发布第一个公开版本），当时主要是用来打通 ICQ、MSN 等 PC 端的聊天软件而设计的，技术比较成熟，它本身有很多优点，如开放、标准、可扩展，并且客户端和服务器端都有很多开源的实现，但是相对于移动端它也有很明显的缺点，譬如数据负载过重、不支持二进制，在交互中有50% 以上的流量是协议本身消耗的，需要做深度的二次开发。

## 三、关于IM通信协议的选择

### 1.序列化与反序列化

移动互联网相对于有线网络最大特点是：带宽低，延迟高，丢包率高和稳定性差，流量费用高。所以在私有协议的序列化上一般使用二进制协议，而不是文本协议。

常见的二进制序列化库有Protocol Buffers和MessagePack，当然你也可以自己实现自己的二进制协议序列化和反序列的过程，比如蘑菇街的TeamTalk。但是前面二者无论是可拓展性还是可读性都完爆TeamTalk(TeamTalk连Variant都不支持，一个int传输时固定占用4个字节)，所以大部分情况下还是不推荐自己去实现二进制协议的序列化和反序列化过程。

一条消息数据用Protobuf序列化后的大小是 JSON 的1/10、XML格式的1/20、是二进制序列化的1/10。同 XML 相比， Protobuf 性能优势明显。它以高效的二进制方式存储，比 XML 小 3 到 10 倍，快 20 到 100 倍。

同时心跳包协议对IM的电量和流量影响很大，对心跳包协议上进行了极简设计：仅 1 Byte 。
ProtocolBuffer可能会造成 APP 的包体积增大，通过 Google 提供的脚本生成的 Model，会非常“庞大”，Model 一多，包体积也就会跟着变大。

如何测试验证 Protobuf 的高性能？

对数据分别操作100次，1000次，10000次和100000次进行了测试，纵坐标是完成时间，单位是毫秒。

![序列化](https://dgynfi.github.io/images/im/serialization.jpg)

![反序列化](https://dgynfi.github.io/images/im/serialization.jpg)

[Xml, Json, Hessian, Protocol Buffers序列化对比](http%3A%2F%2Fwww.cnblogs.com%2Fbeyondbit%2Fp%2F4778264.html)

选择传输格式的时候：ProtocolBuffer > JSON > XML

- [ProtocolBuffer for Objective-C 运行环境配置及使用](https://www.jianshu.com/p/8c6c009bc500)
- [iOS之ProtocolBuffer搭建和示例demo](http%3A%2F%2Fwww.qingpingshan.com%2Frjbc%2Fios%2F181571.html)

### 2.协议格式设计

基于TCP的应用层协议一般都分为包头和包体(如HTTP)，IM协议也不例外。包头一般用于表示每个请求/反馈的公共部分，如包长，请求类型，返回码等。而包头则填充不同请求/反馈对应的信息。

一个最简单的包头可以定义为：

```
struct PackHeader
{
    int32_t     length_;    //包长度
    int32_t     serial_;    //包序列号
    int32_t     command_;   //包请求类型
    int32_t     code_;      //返回码
};
```

以心跳包为例，假设当前的serial为1，心跳包的command为10，那么使用MessagePack做序列化时:length=4，serial=1，command=10，code=0，每个字段各占一个字节，包体为空，仅需要4个字节。

当然这是最简单的一个例子，面对真正的业务逻辑时，包体里面会需要塞入更多地信息，这个需要开发根据自己的业务逻辑总结公共部分，如为了兼容加入的协议版本号，为了负载均衡加入的模块id等。

## 四、IM一些其它问题

### 1.IM的可靠性：

除了心跳机制、PingPong机制、断线重连机制这些被用来保证连接的可用，要提高IM服务时的可靠性，能做的还有很多：比如在大文件传输的时候使用分片上传、断点续传、秒传技术、P2P技术等来保证文件的传输。

### 2.安全性：

我们通常还需要一些安全机制来保证我们IM通信安全。如：加密传输、防止 DNS 污染、帐号安全、第三方服务器鉴权、单点登录等。

### 3.一些其他的优化：

精简心跳包，心跳包只在空闲时发送，动态化心跳间隔。文件上传、下载优化等。类似微信，服务器不做聊天记录的存储，只在本机进行缓存，这样可以减少对服务端数据的请求，一方面减轻了服务器的压力，另一方面减少客户端流量的消耗。

我们进行http连接的时候尽量采用上层API，类似NSUrlSession。而网络框架尽量使用AFNetWorking3.0以上版本。因为这些上层网络请求都用的是HTTP/2 ，我们请求的时候可以复用这些连接。

更多优化相关请参考这篇文章：

- [《iOS端移动网络调优的8条建议》](http://www.52im.net/thread-134-1-1.html)
- [IM 即时通讯技术在多应用场景下的技术实现，以及性能调优（ iOS 视角）](https://www.jianshu.com/p/8cd908148f9e)

## 五、实时音视频通话

IM应用中的实时音视频技术，几乎是IM开发中的最后一道高墙。原因在于：实时音视频技术 = 音视频处理技术 + 网络传输技术 的横向技术应用集合体，而公共互联网不是为了实时通信设计的。

实时音视频技术上的实现内容主要包括：音视频的采集、编码、网络传输、解码、播放等环节。这么多项并不简单的技术应用，如果把握不当，将会在在实际开发过程中遇到一个又一个的坑。


## 参考文章

《[即时通讯音视频开发（一）：视频编解码之理论概述](http://www.52im.net/thread-228-1-1.html)》<br />
《[即时通讯音视频开发（二）：视频编解码之数字视频介绍](http://www.52im.net/thread-229-1-1.html)》<br />
《[即时通讯音视频开发（三）：视频编解码之编码基础](http://www.52im.net/thread-232-1-1.html)》<br />
《[即时通讯音视频开发（四）：视频编解码之预测技术介绍](http://www.52im.net/thread-235-1-1.html)》<br />
《[即时通讯音视频开发（五）：认识主流视频编码技术H.264](http://www.52im.net/thread-237-1-1.html)》<br />
《[即时通讯音视频开发（六）：如何开始音频编解码技术的学习](http://www.52im.net/thread-241-1-1.html)》<br />
《[即时通讯音视频开发（七）：音频基础及编码原理入门](http://www.52im.net/thread-242-1-1.html)》<br />
《[即时通讯音视频开发（八）：常见的实时语音通讯编码标准](http://www.52im.net/thread-243-1-1.html)》<br />
《[即时通讯音视频开发（九）：实时语音通讯的回音及回音消除概述](http://www.52im.net/thread-247-1-1.html)》<br />
《[即时通讯音视频开发（十）：实时语音通讯的回音消除技术详解](http://www.52im.net/thread-250-1-1.html)》<br />
《[即时通讯音视频开发（十一）：实时语音通讯丢包补偿技术详解](http://www.52im.net/thread-251-1-1.html)》<br />
《[即时通讯音视频开发（十二）：多人实时音视频聊天架构探讨](http://www.52im.net/thread-253-1-1.html)》<br />
《[即时通讯音视频开发（十三）：实时视频编码H.264的特点与优势](http://www.52im.net/thread-266-1-1.html)》<br />
《[即时通讯音视频开发（十四）：实时音视频数据传输协议介绍](http://www.52im.net/thread-267-1-1.html)》<br />
《[即时通讯音视频开发（十五）：聊聊P2P与实时音视频的应用情况](http://www.52im.net/thread-269-1-1.html)》<br />
《[即时通讯音视频开发（十六）：移动端实时音视频开发的几个建议](http://www.52im.net/thread-270-1-1.html)》<br />
《[即时通讯音视频开发（十七）：视频编码H.264、V8的前世今生](http://www.52im.net/thread-274-1-1.html)》

## 扩展阅读：

推荐一个专业IM开发的网站：

- [即时通讯网](http://www.52im.net)
- [移动端IM开发需要面对的技术问题](http://www.52im.net/thread-133-1-1.html)


*声明：本文并非由本人完全所创。整理一些开发技能知识，以作存档用于学习。*
