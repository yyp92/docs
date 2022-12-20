# WebRTC攻略(四) 媒体协商与SDP简析

## 媒体协商

在音视频通讯场景中，由于两端之间所支持的音视频编解码、传输协议、传输的速率，都需要进行彼此通知对方。

![](../../\images\webRTC-4-1.png)

我们把一个 1 对 1 的音视频通讯，比喻成双方互送快递包裹的过程。

![](../../\images\webRTC-4-2.png)

首先这里有很多问题，双方要彼此告知对方后，才能寄送包裹。 
比如：

- 我不知道包裹要寄给谁？（我要和谁建立通讯）

- 对方能否使用我的包裹？（我的媒体格式对方是否支持）

- 对方在哪里，地址是什么？（对方所处网络的位置在哪）

- 走那条路线寄送最快？（走哪种网络传输最效率）

![](../../\images\webRTC-4-3.png)

实际场景中，我们要打电话互相告诉对方一些信息。而在音视频通讯中，也需要这个“打电话”步骤，形式上一般是通过建立“信令通道”来传送信令。对于 Web 前端来说最常见以 WebSocket 来作为信令通道，通过它来交换信令并进行协商。真正的媒体数据，则是通过 RTCPeerConnection 进行传输。

![](../../\images\webRTC-4-4.png)

比如包含什么媒体流/轨，或者是我的编码是否被对方的解码器所支持等等这些问题，则通过 SDP 作为载体告诉给对方。

### 什么是媒体协商？

在没有建立 WebRTC 连接传输数据前，首先需要让本地端和远端确认彼此共同支持的媒体能力。如：音视频编解码器、使用的传输协议、IP 端口和传输速率等等。而这些信息需要通过前文所说的 SDP 来互换，这个过程称之为媒体协商。

### 媒体协商的流程

这里以在两个前端浏览器建立通讯来进行说明，我们暂且称“发起端”和“应答端”。

![](../../\images\webRTC-4-5.png)

1. 首先双方连接信令通道，（一般由业务决定如何实现），并能交换信令。

2. 发起端调用 **RTCPeerConnection.createOffer** 创建一个**offer**，并调用 **setLocalDescription** 设置本地的 SDP。

3. 然后通过信令服务器 将含有 SDP 的 offer 设置给应答端。

4. 应答端拿到此 offer 以后调用 **setRemoteDescription** 将此 SDP 信息保存。

5. 应答端调用 **RTCPeerConnection.createAnswer** 创建一个 answer，并调用 **setLocalDescription** 设置本地的 SDP。

6. 通过信令服务器将含有 SDP 的 **answer** 发送给发起端。

7. 发起端调用 **setRemoteDescription** 将此 SDP 信息保存。

简单概括就是：**发起端和应答端通过** **creatOffer** **和** **createAnswer** **创建** **offer/answerSDP**，**然后通过信令服务互换，最后调用** **setLocalDescription/setRemoteDescription** **进行设置本地和远端的** **SDP** **以完成协商。**

在双方都创建 RTCPeerConnection 之后,它们就可以开始进行媒体协商了。

### 媒体协商的前端代码实现

**呼叫方创建&发送 Offer**

```js
// local
var pc_local = new RTCPeerConnection(otps1); 

pc_local.createOffer((offer)=>{    
  pc_local.setLocalDescription(offer);  
  singalChannel.send(offer)
}, handleError);
```

**应答方收到 Offer**

```js
// remote
var pc_remote = new RTCPeerConnection(otps2);

signalChannel.on('message', (message)=>{  
  if(message.type === 'offer'){    
    pc_remote.setRemoteDescription(        
        new RTCSessionDescription(message)
    )  
  }
})
```

**应答方创建&发送 Answer**

```js
// remote
pc_remote.createAnswer((answer)=>{  
  pc_remote.setLocalDescription(answer);  
  singalChannel.send(answer);
}, handleError );
```

**呼叫方收到 Answer**

```js
// local
signalChannel.on('message',(message)=>{  
  if(message.type==='answer'){    
    pc_local.setRemoteDescription(        
        new RTCSessionDescription(message)
    )  
  }
})
```

## SDP

### 什么是SDP？

SDP 全称 Session Description Protocal，直译就是通用会话描述协议。

光看直面意思可能不太好理解，其实就是**描述双方的会话信息，以及各端所具备能力**的通用协议。

在 WebRTC 中 SDP 所描述的信息主要有：  

1. 各端所支持音视频编解码器  
2. 编解码所设定的参数  
3. 所使用的的传输协议  
4. ICE 连接候选项等

### 标准SDP规范

要注意的是 SDP 并不是 WebRTC 独有规范，关于标准的 SDP 规范可以查阅：IETFRFC4556规范。 
标准 SDP 规范主要包括 **SDP** **描述格式**和 **SDP** **结构**，而 SDP 结构由**会话描述**和**媒体信息描述**两个部分组成。

![](../../\images\webRTC-4-6.png)

### SDP的格式

SDP 是由多个 **<type>=<value>** 这样的表达式组成的。

```js
v=0
o=- 7017624586836067756 2 IN IP4 127.0.0.1
s=-
t=0 0...
```

- **type** 只能为一个字符，代表属性。

- **value** 为结构化文本，UTF-8 编码，代表属性值。

- **=** 两边不能有空格。

SDPLine 没有统一的 Schema 描述，也就是没有一个固定的规则能解析所有 Line，SDPGrammer 只是描述了 SDP 相关的属性，具体每个属性的表达需要根据属性定义 IETFRFC4556。 
而 SDP 的结构有一个会话描述和零至多个媒体信息描述组成。

#### 会话描述

常见属性：

**v=** SDP 协议版本

> ```js
> v=0
> ```

**o=** 会话发起者描述

> ```js
> o=<username> <sess-id> <sess-version> <nettype> <addrtype> <address>
> ```
> 
> username：用户名 
> sess-id：会话id，在整个会话中是唯一的，建议使用NTP时间戳。 
> sess-version：会话版本，每次会话数据修改后，该版本值会递增。 
> nettype：网络类型，一般为“IN”。 
> addrtype：地址类型，一般为IP4。 
> address：IP地址。

**s=** 会话名

> ```js
> s=<sessionname>
> ```
> 
> 不关注时可为-

**t=** 会话活动时间

> ```js
> t=<start-time><stop-time>
> ```
> 
> start-time：会话开始时间
> 
> stop-time：结束时间
> 
> 均为NTP时间，单位是秒，均为0时表示持久会话。

**c=** 连接信息

> ```js
> c=<nettype><addrtype><connection-address>
> ```
> 
> nettype：网路类型 
> addrtype：地址类型 
> connection-address：连接地址

#### 媒体描述

会话级别描述完成后，后面就是零到多个媒体级别描述，比如：

常见属性：

**m=** 媒体描述

> ```js
> m=<media><port> <transport> <fmt-list>
> ```
> 
> media：媒体类型（audio / video） 
> port：端口号 
> transport：传输协议 RTP/AVP（RTP/SAVP）或 UDP 
> fmt-list：媒体格式，表述 RTP 的数据负载类型(PayloadType)的列表，可以包含多个。分别代表音频和视频的编码格式，后面会跟着 rtpmap、rtcp-fb、fmtp 这些属性来做进一步的详细的描述。 
> RTP类型参考：RTPPayload

**a=** 附加描述

> 有以下两种格式：
> 
> ```js
> a=<type>
> a=<type>:<value>
> ```

SDP 解析时，每个 SDPLine 都是以 key=... 形式，解析出 key 是 a 后，可能有两种方式，可参考 RFC4566：  

> 在 m= 之前，为会话附加描述；
> 
> 在 m= 之后，为媒体附加描述。 
> 其中可以关注 rtpmap 和 fmtp。

**a=** **rtpmap** RTP参数映射表

> ```js
> a=rtpmap:<playload-type><encoding-name>/<sample-rate>/<encodingparameters可选>
> ```
> 
> playload-type：数据负载类型 
> encoding-name：编码名称 
> sample-rate：采样率 
> encodingparameters：编码参数

**a=fmtp** 格式参数

> ```js
> a=fmtp:<playload-type><specific-parameters>
> ```
> 
> playload-type：数据负载类型 
> specific-parameters：编码参数

### SDP 剖析的示例结构与说明

![](../../\images\webRTC-4-7.png)

https://webrtchacks.com/sdp-anatomy/ 这个站点给我们展示了一个详细的 SDP 例子。左侧为 SDP 文本，可以明显看出 SDP 的格式与结构，右侧则对每一行描述进行了说明。如果你不想看冗长的规范文档，这个例子是一个不错的学习材料。

### WebRTC 的 SDP **总结**

在 WebRTC 中的 SDP 相对于标准 SDP 规范中有点不一样，它对于 SDP 划分了更多部分，详情可以看下图：

![](../../\images\webRTC-4-8.png)

WebRTC 按功能将 SDP 划分成了五部分，即会话元数据、网络描述、流描述、安全描述以及服务质量描述。WebRTCSDP 中的会话元数据（SessionMetadata）其实就是 SDP 标准规范中的会话层描述；流描述、网络描述与 SDP 标准规范中的媒体层描述是一致的；而安全描述与服务质量描述都是新增的一些属性描述。SDP 作为 WebRTC 的核心部分，是你深入学习 WebRTC 前所要必须掌握的基础内容。

## 参考资料

- [SDP: Session Description Protocol](https://mp.weixin.qq.com/s?__biz=MzI1ODE4NzE1Nw==&mid=2247489577&idx=1&sn=5577ddb82e2c31a5ae644ce05640f088&chksm=ea0d52d3dd7adbc5ae2ef007f25cf38aefc2c76d0e4dab385f26586f35cd4249c1a38921cb46&scene=126&sessionid=1670824057&subscene=227&key=0c944f444cb8f50453c07fb6f82d1f990b8787468d3cdb786f21ff416e1b28c2b5ffed26b2618a242464e49d078445c6ef7873048c4c65af40ab795bda228c54f21af44fc630f9fc1b4ffcb075ab755894b8b93f7edc5e5a77de5968eb6ab849220c5a22066a208a90308932b1d9689aa2cf0c424a17e488df8427749aaa766b&ascene=14&uin=MTQxNDMzMjI0Mg%3D%3D&devicetype=Windows+10+x64&version=63080029&lang=zh_CN&exportkey=n_ChQIAhIQqAn5lZ1Dnt%2F1pbvAWpuc6hL0AQIE97dBBAEAAAAAADfRJE4tx80AAAAOpnltbLcz9gKNyK89dVj0Z8prcvuXBgGdvo027We6LO1KM4Lq8JyKUzXHu%2FYvUjH3cKu24cpw1RQJOmqvqXYGvKQHPpLgoMFS9xrV8zF7uFiPbqcjnbs21qoHroGmqo7qXxDv%2F%2BU9KiQZQB5C8EOAgl8dUuB6ML%2BWToUTz63DcUxk7aTi96KS%2FMDfKb1Agp0I6U5p%2BS63zbiO84mECKkQGeJuAebwTayPwpCe3A4z3gKDrjaTn0IuqQIqT%2BF7zxcL5taN8CvTmOUQQE3xiNvHLy4Qior1Zt89AgiiBEM%3D&acctmode=0&pass_ticket=R3B83TKIuJXVUdki8sfKWp7Q8K%2BBYL6sYbr132f5NekJInpsNKZSLFjmEOt6AamvyiRNhFKKwd0H8%2FyDontwkQ%3D%3D&wx_header=1&fontgear=2)

- [https://webrtchacks.com/](https://mp.weixin.qq.com/s?__biz=MzI1ODE4NzE1Nw==&mid=2247489577&idx=1&sn=5577ddb82e2c31a5ae644ce05640f088&chksm=ea0d52d3dd7adbc5ae2ef007f25cf38aefc2c76d0e4dab385f26586f35cd4249c1a38921cb46&scene=126&sessionid=1670824057&subscene=227&key=0c944f444cb8f50453c07fb6f82d1f990b8787468d3cdb786f21ff416e1b28c2b5ffed26b2618a242464e49d078445c6ef7873048c4c65af40ab795bda228c54f21af44fc630f9fc1b4ffcb075ab755894b8b93f7edc5e5a77de5968eb6ab849220c5a22066a208a90308932b1d9689aa2cf0c424a17e488df8427749aaa766b&ascene=14&uin=MTQxNDMzMjI0Mg%3D%3D&devicetype=Windows+10+x64&version=63080029&lang=zh_CN&exportkey=n_ChQIAhIQqAn5lZ1Dnt%2F1pbvAWpuc6hL0AQIE97dBBAEAAAAAADfRJE4tx80AAAAOpnltbLcz9gKNyK89dVj0Z8prcvuXBgGdvo027We6LO1KM4Lq8JyKUzXHu%2FYvUjH3cKu24cpw1RQJOmqvqXYGvKQHPpLgoMFS9xrV8zF7uFiPbqcjnbs21qoHroGmqo7qXxDv%2F%2BU9KiQZQB5C8EOAgl8dUuB6ML%2BWToUTz63DcUxk7aTi96KS%2FMDfKb1Agp0I6U5p%2BS63zbiO84mECKkQGeJuAebwTayPwpCe3A4z3gKDrjaTn0IuqQIqT%2BF7zxcL5taN8CvTmOUQQE3xiNvHLy4Qior1Zt89AgiiBEM%3D&acctmode=0&pass_ticket=R3B83TKIuJXVUdki8sfKWp7Q8K%2BBYL6sYbr132f5NekJInpsNKZSLFjmEOt6AamvyiRNhFKKwd0H8%2FyDontwkQ%3D%3D&wx_header=1&fontgear=2)
