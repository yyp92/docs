# WebRTC NAT 穿越与 ICE

> WebRTC 进行端对端进行实时音视频通讯时，常常一方或者双方都是隐藏在 NAT 之后的内网地址。ICE 则用于寻找一条传输数据通道连接。本文介绍了 NAT 穿越和 ICE 框架的基础知识和主要步骤。

我们知道使用 WebRTC 进行端对端进行实时音视频通讯时，WebRTC 本身是基于点对点（Peer-to-Peer）连接的，最便捷的方式就是通话的双方通过 IP 直连，摆脱原始的直播服务器中转的方式。

如果连接双方都是公网地址，则可以直接访问到对方，从而建立连接。但是在现实的应用场景中，大部分情况下其中一方或者双方都不是公网地址，而是隐藏在 NAT（Network Address Translation，网络地址转换）之后的内网地址。

比如局域网 A 中的 192.168.2.232 和局域网 B 中的 192.168.2.161 之间是不能直接连接通讯的。由于需要连接的个人设备绝大多数都隐藏在各自的内网当中，导致无法直接获取客户端 IP 地址，也无法直接进行 P2P 的音视频通信。

为了解决这个问题，WebTRTC 采用了 ICE 技术框架来实现 NAT 穿越。

## NAT 网络地址转换

### 什么是 NAT

或许你在之前听闻过 IPv4 地址枯竭的报道，IPv4 地址只有 32 位长，理论最多 42.9 亿条。大概在 94 年时候，提出了 **NAT（ Network Address Translation 网络地址转换）**RFC 规范，作为一个临时方案来解决 IPv4 地址枯竭的问题。

这个方案就是把 IP 地址重用，在边缘网络引入 NAT 设备，由它来负责维护本地服务 IP 和端口的映射到公网 IP 和端口。NAT 内部的本地 IP 地址空间可以被许多不同的子网络重用，从而解决地址耗尽的问题。

![](../../\images\webRTC-5-1.png)

可是临时方案很快成为了**最终方案**，成为了互联网基础设施的组成部分。它不仅用来解决 IP 地址枯竭的问题，你能发现路由器、防火墙、代理设备都具备 NAT 功能。

### NAT 类型

关于 NAT 被人们研究总结过，大体两种：锥型和对称型。而锥型又可分细分为三种。

所以归纳起来，总共四种类型：**完全锥型、IP 限制锥型、端口限制锥型**和**对称型。**

**a. 完全锥型**

![](../../\images\webRTC-5-2.jpg)

当内网 Host 与外网机器通讯，就会在 NAT 上打洞，这个过程就是指在 NAT 建立内外网映射表，这个表上记录内外网 IP 端口映射关系。外网机器与 P 地址 p 端口的通讯，都会在 NAT 上转发到对应的内网地址与端口，从而实现和内网 host 机器通讯。

**b. IP 限制型**

![](../../\images\webRTC-5-3.jpg)

IP 限制锥型更加严格，在完全锥形基础上，只允许 Host 访问过的 IP 通过打洞。（映射表多记录了被访问**外网的 IP 地址**）如图，A 和 C 等其它外网主机想通过 B 机的打洞 IP 端口，也是无法和 Host 通讯。

**c. 端口限制锥型**

![](../../\images\webRTC-5-4.jpg)

端口限制锥型比 IP 限制锥型更加严格，IP 限制锥型不限制端口。IP 限制锥型情况下，同一外网主机的其它端口服务，都能和内网 Host 通讯。但是端口限制性情况下，只允许外网机器指定的端口服务通过打洞。（映射表多记录了被访问的**外网的 IP 地址和端口**）

**d. 对称型**

![](../../\images\webRTC-5-5.jpg)

对称型，是最严格的 NAT 类型，它一样有 IP 和端口限制（这点和端口限制锥型一致）。它最大不同在于，每次连接都会在 NAT 上新开 IP 地址或者新开 IP 地址和端口与外网通讯。也就是说每一次 NAT 的打洞都不相同。基本上对称型 NAT 是无法穿越的。

### NAT 类型检测

根据上面的介绍，我们可以了解到，在实际的网络场景中，各种设备所处的网络环境是不同的。那么在进行设备间的通信之前，首先需要判断出设备当前所处的网络类型是非常重要的一步。

下图是在 STUN（Session Traversal Utilities for NAT），对于 NAT 类型检测的流程图。当走到红色结束时：表明穿越失败，无法 UDP 通讯。当走到绿色（公网地址）或黄色时（锥型 NAT）才可能进行通讯。

![](../../\images\webRTC-5-6.jpg)

整个流程大体上发起了 5 次检测：

**test1**

- 主机向服务器 IP 端口发请求，服务器通过同样 IP 端口返回，收到返回？yes：next；no：udp 不通。

- 是否为同一个地址？yes：没有 NAT=>走 test2；no：在NAT后=>走 test3。

**test2**

- 判断返回的主机外网 IP 地址是否与主机自身 IP 地址是否一样?  yes：是公网地址；no：存在对称型防火墙。

**test3**

- 在  NAT 背后，主机向服务器发请求，服务器通过另一个网卡 IP 和不同端口返回，是否收到？yes：完全锥型；no：限制锥形=>test4。

**test4**

- 主机向另一台服务器发请求，对比服务器 1 和 2 上获取的本机映射的公网 IP 端口是否一致？yes：非对称型（至少 IP 不限制）；no：对称型=>test5。

**test5**

- 主机向 test4 的服务器再发一次请求，该服务器用相同 IP 但不同的端口返回？yes：IP 限制锥型；no：端口限制锥型

以上关于 NAT，及其打洞和穿越原理的介绍。实际穿越比这个包含更多细节且更加复杂，可以参考有相应的RFC规范[RFC5389](https://mp.weixin.qq.com/s?__biz=MzI1ODE4NzE1Nw==&mid=2247490405&idx=1&sn=826d1ec578084da233225a8173d54041&chksm=ea0d519fdd7ad889f421c0ed03db2e8657232eb9f5dbb22cbf217089b0071374e23db5d1bd5d&scene=126&sessionid=1670824050&subscene=227&key=88ac9a6a403070a3b8afab995aab9d49fd1698fa50013cef0b80b4f61358c25fa56d737b6ef5ed4777ad30bcc66b17cde27479446a4f58452a06580adb89605c9e571eb82c110f707278c44923ad32d26326d232441793c2909e09a1c4592b083f147e88e1c518a65f300fdcf9ec7c9a4959f7f37ced932c2dc58c9734f7b805&ascene=14&uin=MTQxNDMzMjI0Mg%3D%3D&devicetype=Windows+10+x64&version=63080029&lang=zh_CN&exportkey=n_ChQIAhIQ0AFRIglVi8jSI5OJLJiVuhL0AQIE97dBBAEAAAAAAJW6BE0EWN4AAAAOpnltbLcz9gKNyK89dVj03vxcVvQpe1OiTgKUN1oYu98ig3xYdtBSrWra6H%2FxqzJcbWpLu5Qs8WY%2FS9Y5WS19HjvHP2wfm0V%2BuV3ts%2BC5TB4cgqL1jZ04m%2B%2FIlgcNOaMLc8t9MIVIeLhM1CFJRdanhDsTm7FokvKPoeDXnBbCoSPoWmBn59XTLhIly826K3%2BhL7bDgYUPQAEEEihmXe%2B219ajUUCRMbJIkUDL4eJwdDQAs9Mv%2Fffx5rdZWBCGmu6s2I0K0NhjyM0FeYRbEVO0QHSqXJblHu82uzIXHtk%3D&acctmode=0&pass_ticket=zGYJE0TQlbim1%2BqTFi2YXzCKvfzu%2BGiyZH4oT1DF6ETJDSmhmSLYRnLPMIOP1x0jpz%2FMWyXf6%2FEd%2BUZVF5EUYA%3D%3D&wx_header=1&fontgear=2)进行了解，但是整个耗时还是相当快的。

## STUN/TURN 协议

前文提到，客户主机不可避免的在防火墙或 NAT 之后。在 UDP 传输时，一般只会带上 NAT 的 Host。如果没有目标机器的 entry 是不会转发到目标机器。在 client-server 情形下没有问题。如果是 peer-to-peer 情形下则无法传输。因此我们需要借助 STUN/TURN 方式进行 NAT 穿透。

WebRTC 采用了 ICE（Interactive Connectivity Establishment）建立端到端的数据通道。说到 ICE，就不得不提到它的两个工具协议：STUN（Session Traversal Utilities for NAT）和 TURN（Travelsal Using Relays around NAT）协议。

### 1. STUN

#### a. 标准规范定义

STUN，首先在 RFC3489 中定义，作为一个完整的 NAT 穿透解决方案，英文全称是 Simple Traversal of UDP Through NATs，即简单的用 UDP 穿透 NAT。

在 RFC5389 修订中把 STUN 协议定位于为穿透 NAT 提供工具，而不是一个完整的解决方案，英文全称是 Session Traversal Utilities for NAT，即 NAT 会话穿透效用。

![](../../\images\webRTC-5-7.png)

#### b. STUN用途

- 会话参与双方获取对方的 IP 地址和端口

- 检测两端点间的连接性

- 维持 NAT 绑定

#### c. STUN 服务的简易过程

内网主机需要借助 STUN 服务器，遵循 STUN 协议机制，便能得到本机 NAT 映射后的外网的 IP 和端口，以下是简易过程。

1. 首先在搭建一个 STUN 服务器，现在比较流行的 STUN 服务器是 CoTURN。

2. 内网主机发送一个 binding request 的 STUN 消息到 STUN 服务器。

3. STUN 服务器收到 binding request 后，会将请求的 IP 地址和端口填充到 binding response 消息中，再原路将该消息返回给内网主机。

4. 收到 binding response 的内网主机就会解析 binding response 消息了，并可以从中得到自己的外网 IP 和端口。

### 2. TURN

#### a. 标准规范定义

TURN，在 RFC5766 中定义，英文全称 Traversal Using Relays around NAT（TURN）：Relay Extensions to Session Traversal Utilities for NAT（STUN），即使用中继穿透 NAT：STUN 的中继扩展。简单的说，TURN 是通过两方通讯的“中间人”方式实现穿透。 `注意：这里Relay 或 TURN 是同一个概念都是值中继型传输。`

#### b. 用途

当 STUN 服务检测，发现直接以 peer-to-peer 的形式连接时，就走 TURN 方式，使用中间网点提供的中继连接服务。TURN 协议就是用来允许主机控制中继的操作并且使用中继与对端交换数据。TURN 与其它中继控制协议不同的是它能够允许一个客户端使用一个中继地址与多个对端连接。

![](../../\images\webRTC-5-8.png)

## ICE连接机制

### 1. 收集 ICE Candidate（候选项）

WebRTC 两端要进行连接时，每一端都会提供多个候选者，比如一端有两块网卡，那么每块网卡的不同端口都对应一个候选者。

ICE Candidate 主要分为以下三种类型：

- **host 类型**：即本机内网的 IP 和端口

- **srflx 类型**：即本机 NAT 映射后的外网的 IP 和端口

- **relay 类型**：即中继服务器的 IP 和端口

一般由以下字段组成:

```textile
IP: xxx.xxx.xxx.xxx       \\IP地址
  port: number              \\端口
  type: host/srflx/relay    \\类型
  priority: number          \\优先级
  protocol: UDP/TCP         \\传输协议
  usernameFragment: string  \\访问服务的用户名
  ...
```

### 2. ICE 连接流程

#### a. 连通性检测

当收集完 ICE Candidate 后，双方通过信令通道交换，并拿到彼此的 ICE candidate 之后，WebRTC 就开始按优先级顺序进行连通性检测了。

一般情况下：host 类型的候选者优先级是最高的，srflx 类型次之，最后是 relay 类型。

这个阶段对于对等项发来的通过身份验证的任何 STUN 连接 request，ICE 代理都会生成 STUN response。候选项依照之前的排序按次序进行检查，当收到对方的响应时，检查视为成功，而如果检查超时后仍没有收到响应时，则该候选对失败。

连通性检测的原则简单归纳:

- 以一定的优先级将候选地址对进行排序

- 以该优先级顺序发送 checks 请求

- 从其它终端接收到 checks 的确认信息

#### b. 选定候选项

在 WebRTC 中，P2PTransportChannel 会维护连接状态表，并排序表中记录（Sort-Connections-And-UpdateState）。排序指的是计算每条记录的连接“成本“，把成本最低的排在第一位。如何计算成本则涉及到很多因素，比如发出 STUN 请求到收到应答耗时，用时越少的“成本”自然会低些。当一端有视频 RTP 数据要发送时，会检查状态表的第一条记录，如果判断出它的状态是发送就绪，就会用此 Connection 进行发送。否则直接放弃这个发送任务。　

#### c. ICE 长连接和重启

为了确保 NAT 映射和过滤规则不在音视频通话过程中超时，ICE 会不断对使用中的候选项对（通道）进行连接检查，每 15s 发送一次，这样是为了保证在音视频流暂停等情况下没有发送数据流时，仍然有数据包持续发送。

当 ICE 代理检测到正在使用的传输地址发生更改或连接时，会触发重新启动 ICE 事件，也就是会重新回到收集 ICE candidate 及其之后的流程。

## 小结

**其实 WebRTC 的 ICE 就是包括 STUN、TURN 协议的一套框架，用于找到一条可用且最优传输数据通道连接**。了解 NAT 穿越和 ICE 框架的基础知识，你会更容易理解 WebRTC 如何建立连接并传输数据。当然实际的 ICE 过程要复杂很多，本文只是简要介绍主要的步骤，感兴趣的读者可以自行阅读参考 [RFC5245](https://mp.weixin.qq.com/s?__biz=MzI1ODE4NzE1Nw==&mid=2247490405&idx=1&sn=826d1ec578084da233225a8173d54041&chksm=ea0d519fdd7ad889f421c0ed03db2e8657232eb9f5dbb22cbf217089b0071374e23db5d1bd5d&scene=126&sessionid=1670824050&subscene=227&key=88ac9a6a403070a3b8afab995aab9d49fd1698fa50013cef0b80b4f61358c25fa56d737b6ef5ed4777ad30bcc66b17cde27479446a4f58452a06580adb89605c9e571eb82c110f707278c44923ad32d26326d232441793c2909e09a1c4592b083f147e88e1c518a65f300fdcf9ec7c9a4959f7f37ced932c2dc58c9734f7b805&ascene=14&uin=MTQxNDMzMjI0Mg%3D%3D&devicetype=Windows+10+x64&version=63080029&lang=zh_CN&exportkey=n_ChQIAhIQ0AFRIglVi8jSI5OJLJiVuhL0AQIE97dBBAEAAAAAAJW6BE0EWN4AAAAOpnltbLcz9gKNyK89dVj03vxcVvQpe1OiTgKUN1oYu98ig3xYdtBSrWra6H%2FxqzJcbWpLu5Qs8WY%2FS9Y5WS19HjvHP2wfm0V%2BuV3ts%2BC5TB4cgqL1jZ04m%2B%2FIlgcNOaMLc8t9MIVIeLhM1CFJRdanhDsTm7FokvKPoeDXnBbCoSPoWmBn59XTLhIly826K3%2BhL7bDgYUPQAEEEihmXe%2B219ajUUCRMbJIkUDL4eJwdDQAs9Mv%2Fffx5rdZWBCGmu6s2I0K0NhjyM0FeYRbEVO0QHSqXJblHu82uzIXHtk%3D&acctmode=0&pass_ticket=zGYJE0TQlbim1%2BqTFi2YXzCKvfzu%2BGiyZH4oT1DF6ETJDSmhmSLYRnLPMIOP1x0jpz%2FMWyXf6%2FEd%2BUZVF5EUYA%3D%3D&wx_header=1&fontgear=2)。

参考文章与规范：  

 [https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Protocols](https://mp.weixin.qq.com/s?__biz=MzI1ODE4NzE1Nw==&mid=2247490405&idx=1&sn=826d1ec578084da233225a8173d54041&chksm=ea0d519fdd7ad889f421c0ed03db2e8657232eb9f5dbb22cbf217089b0071374e23db5d1bd5d&scene=126&sessionid=1670824050&subscene=227&key=88ac9a6a403070a3b8afab995aab9d49fd1698fa50013cef0b80b4f61358c25fa56d737b6ef5ed4777ad30bcc66b17cde27479446a4f58452a06580adb89605c9e571eb82c110f707278c44923ad32d26326d232441793c2909e09a1c4592b083f147e88e1c518a65f300fdcf9ec7c9a4959f7f37ced932c2dc58c9734f7b805&ascene=14&uin=MTQxNDMzMjI0Mg%3D%3D&devicetype=Windows+10+x64&version=63080029&lang=zh_CN&exportkey=n_ChQIAhIQ0AFRIglVi8jSI5OJLJiVuhL0AQIE97dBBAEAAAAAAJW6BE0EWN4AAAAOpnltbLcz9gKNyK89dVj03vxcVvQpe1OiTgKUN1oYu98ig3xYdtBSrWra6H%2FxqzJcbWpLu5Qs8WY%2FS9Y5WS19HjvHP2wfm0V%2BuV3ts%2BC5TB4cgqL1jZ04m%2B%2FIlgcNOaMLc8t9MIVIeLhM1CFJRdanhDsTm7FokvKPoeDXnBbCoSPoWmBn59XTLhIly826K3%2BhL7bDgYUPQAEEEihmXe%2B219ajUUCRMbJIkUDL4eJwdDQAs9Mv%2Fffx5rdZWBCGmu6s2I0K0NhjyM0FeYRbEVO0QHSqXJblHu82uzIXHtk%3D&acctmode=0&pass_ticket=zGYJE0TQlbim1%2BqTFi2YXzCKvfzu%2BGiyZH4oT1DF6ETJDSmhmSLYRnLPMIOP1x0jpz%2FMWyXf6%2FEd%2BUZVF5EUYA%3D%3D&wx_header=1&fontgear=2) 

[https://datatracker.ietf.org/doc/html/rfc5245](https://mp.weixin.qq.com/s?__biz=MzI1ODE4NzE1Nw==&mid=2247490405&idx=1&sn=826d1ec578084da233225a8173d54041&chksm=ea0d519fdd7ad889f421c0ed03db2e8657232eb9f5dbb22cbf217089b0071374e23db5d1bd5d&scene=126&sessionid=1670824050&subscene=227&key=88ac9a6a403070a3b8afab995aab9d49fd1698fa50013cef0b80b4f61358c25fa56d737b6ef5ed4777ad30bcc66b17cde27479446a4f58452a06580adb89605c9e571eb82c110f707278c44923ad32d26326d232441793c2909e09a1c4592b083f147e88e1c518a65f300fdcf9ec7c9a4959f7f37ced932c2dc58c9734f7b805&ascene=14&uin=MTQxNDMzMjI0Mg%3D%3D&devicetype=Windows+10+x64&version=63080029&lang=zh_CN&exportkey=n_ChQIAhIQ0AFRIglVi8jSI5OJLJiVuhL0AQIE97dBBAEAAAAAAJW6BE0EWN4AAAAOpnltbLcz9gKNyK89dVj03vxcVvQpe1OiTgKUN1oYu98ig3xYdtBSrWra6H%2FxqzJcbWpLu5Qs8WY%2FS9Y5WS19HjvHP2wfm0V%2BuV3ts%2BC5TB4cgqL1jZ04m%2B%2FIlgcNOaMLc8t9MIVIeLhM1CFJRdanhDsTm7FokvKPoeDXnBbCoSPoWmBn59XTLhIly826K3%2BhL7bDgYUPQAEEEihmXe%2B219ajUUCRMbJIkUDL4eJwdDQAs9Mv%2Fffx5rdZWBCGmu6s2I0K0NhjyM0FeYRbEVO0QHSqXJblHu82uzIXHtk%3D&acctmode=0&pass_ticket=zGYJE0TQlbim1%2BqTFi2YXzCKvfzu%2BGiyZH4oT1DF6ETJDSmhmSLYRnLPMIOP1x0jpz%2FMWyXf6%2FEd%2BUZVF5EUYA%3D%3D&wx_header=1&fontgear=2)
