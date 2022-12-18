# Web前端WebRTC攻略(一) 基础介绍

> 随着互联网高速发展，以及即将到来的5G时代，WebRTC作为前端互动直播和实时音视频的利器，也是将前端开发者们不可错过的学习领域。如果你现在只是听过而已，那你可能要好好学习一番。

## 什么是WebRTC？

WebRTC 全称是（Web browsers with Real-Time Communications (RTC)  

大概2011年，谷歌收购了 GIPS，它是一个为 RTC 开发出许多组件的公司，例如编解码和回声消除技术。Google 开源了 GIPS 开发的技术，并希望将其打造为行业标准。

收购花了一大笔钱，谷歌说开源就开源，确实不得不佩服，但显然对于Googl来说，打造音视频的开源生态有着更大的价值。“浏览器 + WebRTC”就是 Google 给出的一个答案。其愿景就是可以在浏览器之间快速地实现音视频通信。

发展至今日，简单来说：**WebRTC是一个免费、开放的项目。使web浏览器通过简单的JavaScript api接口实现实时通信功能。**

## WebRTC的架构

![](../../\images\webRTC-1-1.jpg)

一般谈WebRTC架构都会拿出这张图，WebRTC从上往下架构依次是：

**Web API层**：面向开发者提供标准API（javascirpt），前端应用通过这一层接入使用WebRTC能力。

**C++ API层**：面向浏览器开发者，使浏览器制造商能够轻松地实现Web API方案。

**音频引擎（VoiceEngine）**：音频引擎是一系列音频多媒体处理的框架，包括从视频采集卡到网络传输端等整个解决方案。

1. iSAC/iLBC/Opus等编解码。

2. NetEQ语音信号处理。

3. 回声消除和降噪。

**视频引擎（VideoEngine）**：是一系列视频处理的整体框架，从摄像头采集视频、视频信息网络传输到视频显示整个完整过程的解决方案。

1. VP8编解码。

2. jitter buffer：动态抖动缓冲。

3. Image enhancements：图像增益。

**传输（Transport)**：传输 / 会话层，会话协商 + NAT穿透组件。

1. RTP 实时协议。

2. P2P传输 STUN+TRUN+ICE实现的网络穿越。

**硬件模块**：音视频的硬件捕获以及NetWork IO相关。

## WebRTC重要的类和API

### Network Stream API

**1. MediaStream（媒体流）和 MediaStreamTrack（媒体轨道）**

这个类并不完全属于WebRTC的范畴，但是在本地媒体流获取，及远端流传到vedio标签播放都与WebRTC相关。MS 由两部分构成：MediaStreamTrack 和 MediaStream。

- MediaStreamTrack 媒体轨，代表一种单类型数据流，可以是音频轨或者视频轨。

- MediaStream 是一个完整的音视频流。它可以包含 >=0 个 MediaStreamTrack。它主要的作用就是确保几个媒体轨道是同步播放。

![](../../\images\webRTC-1-2.jpg)

**2.** **Constraints 媒体约束**

关于MediaStream，还有一个重要的概念叫做: Constraints（约束）。它是用来规范当前采集的数据是否符合需要，并可以通过参数来设置。

```js
// 基本
const constraint1 = {
    // 是否捕获音频
    "audio": true,

    // 是否捕获视频
    "video": true
}

// 详细
const constraint2 = {
    "audio": {
        "sampleSize": 8,
        // 回声消除
        "echoCancellation": true
    },
    // 视频相关设置
    "video": {
        "width": {
            // 当前视频的最小宽度
            "min": "381",
            "max": "640"
        },
        "height": {
            // 最小高度
            "min": "200",
            "max": "480"
        },
        "frameRate": {
            // 最小帧率
            "min": "28",
            "max": "10"
        }
    }
}
```

**3.** **获取设备本地音视频**

其中本地媒体流获取用到的是`navigator.getUserMedia()`,它提供了访问用户本地相机/麦克风媒体流的手段。

```js
var video = document.querySelector('video');
navigator.getUserMedia({
    audio: true,
    video: true
}, function(stream) {  
    // 拿到本地媒体流
    video.src = window.URL.creatObjectURL(stream);
}, function(error) {
    console.log(error);
});
```

![](../../\images\webRTC-1-3.png)

以上这段demo，就是通过`getUserMedia`获取`stream`，浏览器弹窗向用户索要权限，当允许后才能拿到`stream`传给video标签进行播放。

`getUserMedia`的第一个参数就是`Constraint`，第二个参数传入回调函数拿到视频流。当然你可以使用如下Promise的写法：

```js
navigator.mediaDevices.getUserMedia(constraints)
    .then(successCallback).catch(errorCallback);
```

### RTCPeerConnection

RTCPeerConnection，用于实现peer跟peer之间RTC连接，继而**无需服务器**就能传输音视频数据流的连接通道。（直播的实际生产中还是需要服务器）

![](../../\images\webRTC-1-4.jpg)

这么说过于抽象，为了帮助理解，可以用一个**不太恰当**但有助于理解的比喻：`RTCPeerConnection`就是一个**高级且功能强大**的用于**传输音视频数据**而建立类似Websocket链接通道，只不过它可以用来建立浏览器。

之所以说是高级且强大，是因为它作为WebRTC web层核心API，让你无须关注数据传输延迟抖动、音视频编解码，音画同步等问题。直接使用PeerConnection 就能用上这些浏览器提供的底层封装好的能力。

```js
var pc = new RTCPeerConnection({
    "iceServers": [
        {
            // 使用google公共测试服务器
            "url": "stun:stun.l.google.com:19302"
        },
        // 如有turn服务器，可在此配置
        {
            "url": "turn:user@turnserver.com",
             "credential": "pass"
        }
    ]
};);


pc.setRemoteDescription(remote.offer);
pc.addIceCandidate(remote.candidate);
pc.addstream(local.stream);
pc.createAnswer(function(answer) {
    // 生成描述端连接的SDP应答并发送到对端
    pc.setLocalDescription(answer);
    signalingChannel.send(answer.sdp);
});

pc.onicecandidate = function(evt) {
    // 生成描述端连接的SDP应答并发送到对端
    if (evt.candidate) {
        signalingChannel.send(evt.candidate);   
    }
}

pc.onaddstream = function(evt) {
    // 收到远端流并播放
    var remote_video = document.getElementById('remote_video');

    remote_video.src = window.URL.createObjectURL(evt.stream);
}
```

你会疑问这里ice Server配置是什么？signalingChannel又是什么？answer和offer又是什么？candidate又是什么？  

我们可以通过`new RTCPeerConnection()`创建RTCPeerConnection。以上代码只是展示RTCPeerConnection的API和设置方法，但并不能运行。

要完成一个RTCPeerConnection需要设置ICE Server（STUN服务器或TURN服务器），在**连接前**还要交换信息，为此需要借助一个信令服务器（signaling server）来进行，主要交换SDP会话描述协议和ICE candidate，我们后面段落介绍。

### Peer-to-peer Data API

RTCDataChannel可以建立浏览器之间的点对点通讯。常用的通讯方式有websocket, ajax和等方式。websocket虽然是双向通讯，但是无论是websocket还是ajax都是客户端和服务器之间的通讯，你必须配置服务器才可以进行通讯。

而由于RTCDATAChannel借助RTCPeerConnection无需经过服务器，就可以提供点对点之间的通讯，无需/(避免)服务器了这个中间件。

```js
var pc = new RTCPeerConnection();

var dc = pc.createDataChannel("my channel");

dc.onmessage = function(event) {
    console.log("received: " +event.data);
};

dc.onopen = function() {
    console.log("datachannel open");
};

dc.onclose = function() {
    console.log("datachannel close");
};
```

### 信令通道 Signaling Channel

我们说WebRTC的RTCPeerConnection是可以做到浏览器间（无服务）的通信。

但这里有个问题，当两个浏览器不通过服务器建立PeerConnection时，它们怎么知道彼此的存在呢？进一步讲，它们该怎么知道对方的网络连接位置（IP/端口等）呢？支持何种编解码器？甚至于什么时候开始媒体流传输、又该什么时候结束呢？

因此在建立WebRTC的RTCPeerConnection前，必须建立️另一条通道来交这些协商信息，这些需要即时协商的信息也被称为**信令**，这条通道成为**信令通道（Signaling Channel）**。

![](../../\images\webRTC-1-5.jpg)

两个客户端浏览器交换的信令具有以下功能：

- 协商媒体功能和设置 （交换SDP对象中的信息：媒体类型、编解码器、带宽等元数据）

- 标识和验证会话参与者的身份

- 控制媒体会话、指示进度、更改会话、终止会话等

其中主要涉及offer/answer sdp会话描述协议 ，以及ICE candidate的交换。这个过程就是**WebRTC协商**。

这里需要注意的一点：WebRTC标准本身没有规定信令交换的通讯方式，信令服务根据自身的情况实现。

在Web浏览器中，一般会使用websocket通道来做信令通道，比如可以基于socket.io来搭建信令服务。当然业界也有很多开源且稳定成熟的信令服务方案可供选择。

## WebRTC建立连接的关键-ICE连接

在交换并设置SDP（offer/asnwer）后,webrtc就开始真正的连接来传输音视频数据。这个建立连接的过程相当复杂，原因是webrtc既要保证高效的**传输性**，又要保证稳定的**连通性**。

由于浏览器客户端之间所处的位置往往是相当复杂的，可能处于同一个内网段内，也可能处于两个不同的位置，所处的NAT网关也可能很复杂。因此需要一种机制找到一条传输质量最优的道路，而WebRTC正具备这种能力。

首先简单了解以下三个概念。

- **ICE Canidate**（ICE 候选者）：包含远端通信时使用的协议、IP 地址和端口、候选者类型等信息。

- **STUN/TURN**：STUN实现P2P型连接，TRUN实现中继型连接。两者实现均有标准协议。（参考下图）

- **NAT穿越**：NAT即网络地址转换，由于客户端并不能分配到公网IP，需要内网IP与公网IP端口做映射才能与外网通信。而NAT穿越就是位于层层Nat网关背后的客户端之间发现对方并建立连接。

![](../../\images\webRTC-1-6.jpg)

ICE连接大致的原理及步骤如下：

1. 发起收集ICE Canidate任务。

2. 本机能收集host类型（内网IP端口）的candidate。

3. 通过STUN服务器收集srflx类型（NAT映射到外网的IP端口）的candiate。

4. 通过TUN服务器收集relay类型的（中继服务器的 IP 和端口）的candidate。

5. 开始尝试NAT穿越，按照host类型、srflx类型、relay类型的优先级去连接。

以上，WebRTC便能找到一条传输质量最优的连接道路。当然实际情况并不是这么简单，整个过程包含着更复杂的底层细节。

## WebRTC使用步骤 Demo代码

通过以上了解了，结合WebRTC的API，信令服务，SDP协商、ICE连接等内容。我们用一段代码来说明WebRTC的使用流程步骤。

```js
var signalingChannel = new SignalingChannel();

var pc = null;

var ice = {
    "iceServers": [
        {
            // 使用google公共测试服务器
            "url": "stun:stun.l.google.com:19302"
        }, 
        {
            // 如有turn服务器，可在此配置
            "url": "turn:user@turnserver.com", 
            "credential": "pass"
        }  
    ]
};

signalingChannel.onmessage = function(msg) {
    if (msg.offer) { 
        // 监听并处理通过发信通道交付的远程提议
        pc = new RTCPeerConnection(ice);
        pc.setRemoteDescription(msg.offer);

        navigator.getUserMedia({ 
            "audio": true, 
            "video": true
        }, gotStream, logError);
    }
    else if (msg.candidate) { 
        // 注册远程ICE候选项以开始连接检查
        pc.addIceCandidate(msg.candidate);
    }
}

function gotStream(evt) {
    pc.addstream(evt.stream);

    var local_video = document.getElementById('local_video');

    local_video.src = window.URL.createObjectURL(evt.stream);

    pc.createAnswer(function(answer) { 
        // 生成描述端连接的SDP应答并发送到对端
        pc.setLocalDescription(answer);

        signalingChannel.send(answer.sdp);  
    });
}

pc.onicecandidate = function(evt) {   
    if (evt.candidate) {
        signalingChannel.send(evt.candidate);    
    }
}

pc.onaddstream = function(evt) {
    var remote_video = document.getElementById('remote_video');

    remote_video.src = window.URL.createObjectURL(evt.stream);
}

function logError() { ... }
```

## WebRTC的现状

### 标准

一开始各个浏览器厂商，都会实现自己的一套API，诸如`webkitRTCPeerConnection`和`mozRTCPeerConnection` 这样的差异，对于前端开发者当然是苦不堪言。

而adapter.js正是为了消除这种差异，帮助我们可以按照规范来写我们的WebRTC代码。可以参考 https://github.com/webrtcHacks/adapter。

关于标准的另一个关键点是：W3C在2018年发布的 WebRTC 1.0标准（candidate recommendation） https://www.w3.org/TR/webrtc 使得WebRTC也将成为视频通信商业应用场景爆发的主要技术推动力。所以你能看到，目前绝大多数的实施通讯厂商，在web浏览器侧的方案基本都是WebRTC了。

### 兼容性

标准的发展，必然推动兼容支持性的提升。本人在大概2017年做H5在线夹娃娃的预研，当时发现很多浏览器，尤其移动端和IOS完全不可用的状态，因此不得不放弃了WebRTC方案。

![](../../\images\webRTC-1-7.jpg)

目前看来浏览器支持的很不错了，除了IE仍然不支持外，PC浏览器基本已经支持。移动端上IOS在11以上已经支持。

这里有个关键在于：别光看caniuse的浏览器，还要看移动端各定制浏览器是否支持，我这里没有广泛的兼容性测试数据。

但可以给出一点结论，WebRTC在最新的IOS和安卓的手Q和微信都是可以使用的。

## WebRTC学习攻略

![](../../\images\webRTC-1-8.jpg)

## 上图给的大致的学习攻略，可以从webRTC核心API开始着手，按照demo实现诸如本地音视频获取及展示。其次搭建简单信令服务，在内网实现简单的浏览器间的通讯，是个不错的尝试。当用起来后，再深入李珏其连接穿越、传输的原理和相关协议，最后再尝试深入挖掘webrtc内部音视频相关知识。

以上就是对于web前端而言比较容易理解且全面的webrtc基础介绍。

## 参考文章

1. https://webrtc.org/architecture/

2. https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API
