# websocket

![](../../\imgs\websocket-1.gif)

看演示不过瘾，我也玩一下(http://socket.vjscoder.com/websocket-chatroom/index.html#/)  

查看源码(https://github.com/ReeceCan/websocket-chatroom)

## Websocket介绍

#### 背景

在许多场景下，用户需要得到实时的消息，比如聊天，医疗设备读数等，旧的解决方案是基于轮询的方式获取最新数据，但是并不会完全实时消息同步，并且大多数情况下请求都是没必要的，反而浪费了大量的流量和服务器资源。基于这种背景下，websocket诞生了。

#### Websocket基本概念

WebSocket 是 HTML5 开始提供的一种在**单个 TCP 连接上**进行**全双工通讯**的**协议**。WebSocket通信协议诞生于2008年， 2011年成为国际标准. WebSocket使得客户端和服务器之间的数据交换变得更加简单，**允许服务端主动向客户端推送数据**。在WebSocket API中，浏览器和服务器**只需要完成一次握手**，两者之间就直接可以创建**持久性的连接**，并进行**双向数据传输**。

- 兼容性问题(主流浏览器都支持)

![](../../\imgs\websocket-2.jpg)

##### Websocket特点

- **控制开销。** 在连接创建后，服务器和客户端之间交换数据时，用于协议控制的数据包头部相对较小。在不包含扩展的情况下，对于服务器到客户端的内容，此头部大小只有2至10字节（和数据包长度有关）；对于客户端到服务器的内容，此头部还需要加上额外的4字节的掩码。相对于HTTP请求每次都要携带完整的头部，此项开销显著减少了。

- **实时通信**。由于协议是全双工的，所以服务器可以随时主动给客户端下发数据。相对于HTTP请求需要等待客户端发起请求服务端才能响应，延迟明显更少；即使是和Comet等类似的长轮询比较，其也能在短时间内更多次地传递数据。

- **保持连接状态**。与HTTP不同的是，Websocket需要先创建连接，这就使得其成为一种有状态的协议，之后通信时可以省略部分状态信息。

- **支持二进制传输**。可以发送文本，也可以发送二进制数据。Websocket定义了二进制帧，相对HTTP，可以更轻松地处理二进制内容。

- **协议标识符是`ws`（如果加密，则为`wss`），服务器网址就是 URL**。

- 实现简单。建立在 TCP 协议之上，服务器端的实现比较容易，并且没有同源限制，客户端可以与任意服务器通信。

- 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

- 支持扩展。Websocket定义了扩展，用户可以扩展协议、实现部分自定义的子协议。如部分浏览器支持压缩等。

简单来说，websocket 具有双向通信，实时性强，支持二进制，控制开销的特点。

#### websocket与HTTP的异同

- 相同
  
  - 都是基于TCP的应用层协议。
  - 都使用Request/Response模型进行连接的建立。
  - 在连接的建立过程中对错误的处理方式相同，在这个阶段WebSocket可能返回和HTTP相同的返回码。

- 不同
  
  - 半双工通信：单向流动， 服务器不主动推送数据给客户端
  - 全双工通信：服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的**双向平等**对话，属于服务器推送技术的一种。
  - HTTP协议基于Request/Response，只能做单向传输，是**半双工通信**，而WebSocket是**全双工通信**
  
  ![](../../\imgs\websocket-3.jpg)
  
  - **http是无状态的，所以请求得到响应以后就关闭了,无状态的好处是服务器不需要存储相关会话信息**。缺点是每次http请求和响应都会发送关于请求的冗余信息；而**WebSocket** 只需要建立一次Request/Response消息对，之后都是TCP连接，避免了需要多次建立Request/Response消息对而产生的**冗余头部信息**。**节省了大量流量和服务器资源**。
  
  ![](../../\imgs\websocket-4.jpg)
  
  - WebSocket在建立握手连接时，数据是通过HTTP协议传输的，但在建立连接之后，**真正的数据传输阶段是不需要HTTP协议参与的。而http需要需要三次握手**。
  
  ![](../../\imgs\websocket-5.jpg)
  
  - WebSocket传输的数据是二进制流，是以帧为单位的，HTTP传输的是明文传输，是字符串传输。

#### Websocket和socket

- socket在**传输控制层**，websokect是**应用层协议**

- socket实际上是对TCP/IP协议的封装，socket本身不是协议，而是调用接口，通过socket我们才能使用TCP/IP

- socket是应用层与TCP/IP协议族通信的中间软件抽象层，它**是一组接口**。当两台主机通信时，让socket去组织数据，以符合指定的协议。TCP协议更依赖于底层的IP协议

软件通信有七层，下三层结构偏向于数据通信，上三层更偏向于数据处理，中间传输层则是连接上三层与下三层的桥梁，每一层都做不同的工作，上层协议依赖于下层协议。

![](../../\imgs\websocket-6.jpg)

#### Websocket应用场景

- 社交聊天：如QQ、微信、鱼耳聊天室

- 直播：如斗鱼，鱼耳直播等

- 智能客服

- 系统消息通知：货拉拉

- 其他：医疗设备读数、股价、交通图，......

## Websocket的启动和API

使用ws搭建一个简单的websocket连接。ws 是一个第三方的 websocket 通信模块，是基于 Node.js 构建的。https://www.jmjc.tech/less/114

#### 简单启动

安装 ws

```bash
npm i ws
```

服务端

```js
const Websocket = require('ws')

const wss = new Websocket.Server({ port: 3000 })

wss.on('connection', function (ws) {
    ws.on('message', function (message) {
        console.log('server receive message: ', message.toString());
    })
    ws.send('msg from server!')
})
```

客户端（这里node启动引入ws示例）

```js
const Websocket = require('ws')

const ws = new Websocket('ws://localhost:3000')

ws.on('open', function () {
   ws.on('message', function (msg) {
    console.log('client receive message: : ', msg.toString())
  })
   ws.send('msg from client')
})
```

#### Websocket API

websocket通过在客户端和服务端之间的第一次握手时将HTTP协议升级到Websocket协议，建立连接后，后面的消息直接在websocket接口定义的方法上来回传送。

##### 构造函数

首先，调用websocket构造函数创建一个websocket连接，返回websocket对象实例。

websocket协议定义了两种URL方案

- ws: 非加密

- wss ：加密（使用HTTPS采用的安全机制保证HTTP连接的安全）。

创建实例

```js
/**
  * URL: 连接的目标
  * protocols（选填）：string | string[] 一个或一组协议名称
*/

const ws = new WebSocket(URL, protocols)
```

##### WebSocket 实例

WebSocket 协议本质上是一个基于 TCP 的协议。

为了建立一个 WebSocket 连接，客户端浏览器首先要向服务器发起一个 HTTP 请求，这个请求和通常的 HTTP 请求不同，包含了一些附加头信息，其中附加头信息"Upgrade: WebSocket"表明这是一个申请协议升级的 HTTP 请求，服务器端解析这些附加的头信息然后产生应答信息返回给客户端，客户端和服务器端的 WebSocket 连接就建立起来了，双方就可以通过这个连接通道自由的传递信息，**并且这个连接会持续存在直到客户端或者服务器端的某一方主动的关闭连接。**

##### Websocket事件

Websocket是纯事件驱动的，可以通过监听Websocket对象上的事件处理数据输入和连接状态改变。

Websocket对象只有4个事件

- **onopen**: 客户端和服务器建立连接后触发。它被称为客户端和服务器之间的**初始握手**。如果接收到open, 说明已经连接成功，可以进行通信了。

- **onmessage**：接收到消息时触发。服务器发送给客户端的消息可以包括纯文本消息，二进制数据（Blob消息或者ArrayBuffer消息）

- **onerror**：响应意外故障时触发，在错误之后总是会终止连接。

- **onclose**：连接关闭时触发。一旦连接关闭后，客户端和服务端将不会再进行消息的收发。也可主动调用close()方法关闭连接。

##### Websocket方法

- send() : 在连接成功后关闭前，发送消息（onopen后和onclose前才可发送消息）。

- close() : 关闭连接

##### Websocket对象属性

- **readyState**: 只读属性，表示Websocket的连接状态。其值如下：

| 常量特性                 | 取值  | 状态                |
| -------------------- | --- | ----------------- |
| Websocket.CONNECTING | 0   | 连接进行中，但还未建立成功     |
| Websocket.OPEN       | 1   | 连接已经建立，可以正常发送消息   |
| Websocket.CLOSING    | 2   | 连接正在进行关闭握手        |
| Websocket.CLOSED     | 3   | 表示连接已经关闭或者连接不能打开。 |

备注：不同浏览器支持不同，亲测微信小程序没有这个属性。

- **bufferedAmount**：只读属性。已被 send() 放入正在队列中等待传输，但是还没有发出的 UTF-8 文本字节数。

当要检查发往服务器的缓冲数据量，特别是客户端向服务器发送大量数据。尽管调用send()连接是立即生效的，但是数据在互联网上的传输却不是这样。浏览器将为你的客户端应用程序缓存出栈数据，从而使你可以随时调用send(), 但如果你想知道数据在网络上的传输速率，Websocket对象可以告诉你缓存的大小。因此可以用bufferedAmount检查已经进入队列，但是尚未发送到服务器的字节数。

![](../../\imgs\websocket-7.jpg)

- **Protocol**: 打开握手期间使用的协议。

#### Websocket 初始握手

每个Websocket连接都始于一个HTTP请求，改请求和其他请求类似，但是包含一个特殊的首标 —— **Upgrade**。Upgrade表示客户端将**把连接升级到Websocket协议**。

在握手前，Websocket遵循HTTP/1.1协议。

客户端发送升级为Websocket的请求也称为初始握手。客户端发送HTTP升级请求后，直到服务端响应 101 状态码、Upgrade和Sec-WebSocket-Accept首标才算连接成功，否则不能连接成功。下面是拷贝的websocket握手的请求头和响应头：

```js
// 客户端发送的请求头
GET wss://www.example.cn/webSocket HTTP/1.1  // 使用的https协议, 对应的wss请求
Host: www.example.cn
Connection: Upgrade  // 带upgrade头的http1.1消息必须含有connection头，表示任何接受此消息的人都在在转发此消息之前处理掉connection中指定的域（即不转发upgrade域）
Upgrade: websocket  // 定义转换协议的header域，如果服务器支持，客户端希望使用已经建立好的http（tcp）连接
Sec-WebSocket-Version: 13 // 客户端支持的WebSocket协议的版本列表
Origin: http://example.cn // Origin为安全使用，防止跨站攻击，浏览器一般会使用这个来标识原始域。
Sec-WebSocket-Key: afmbhhBRQuwCLmnWDRWHxw== // 首标 客户端随机生成，服务器会使用此字段组装成另一个key值放在握手返回信息里。用于客户端到服务器websocket的初始握手，避免夸协议攻击。
Sec-WebSocket-Protocol: chat, superchat // 首标 告诉客户端应用程序可使用的协议
Sec-WebSocket-Extensions: permessage-deflate（协商使用传输数据压缩）; client_max_window_bits（擦采用LZ77压缩算法时，滑动窗口相关SIZE大小）// 首标


// 服务器发出的响应头
HTTP/1.1 101
Server: nginx/1.12.2
Date: Sat, 11 Aug 2018 13:21:27 GMT
Connection: upgrade
Upgrade: websocket
Sec-WebSocket-Accept: sLMyWetYOwus23qJyUD/fa1hztc= // 确认服务器是否理解websocket协议
Sec-WebSocket-Protocol: chat
Sec-WebSocket-Extensions: permessage-deflate;client_max_window_bits=15


/** 
Sec-WebSocket-Accept的生成步骤：
（1）将Sec-WebSocket-Key与协议中已定义的GUID进行拼接 
（2）将（1）中生成的字符串进行SHA1编码 
（3）将2中生成的字符串进行Base64编码
Sec-WebSocket-Accept用来确定：
（1）服务端是否理解websocket协议，如果不理解，就不会返回正确的Sec-WebSocket-Accept 
（2）返回值是本次请求的，而不是之前的缓存
*/ 
```

![](../../\imgs\websocket-8.jpg)

#### websocket消息格式

客户端和服务端相互发送的消息，在网络上用标记消息之间的边界并包括简介的类型信息的二进制语法表示。准确的说，这些二进制首标标记另一个单位--帧(frame) --之间的边界。帧是可以合并组成消息的部分数据。消息一般只有一个帧，但是它其实可以由任意数量的帧组成。

## websocket实际应用

websocket的基础概念介绍完了，开始进入正题，让我们自己也来实现一个聊天室吧。

后面的server.js是用node启动的，这里我们主要目的是实现前端功能。

#### 创建连接

home.vue

```js
this.ws = new WebSocket('ws://localhost:3000')
console.log('before open', this.ws.readyState) // 0

this.ws.onopen = () => { // 监听到连接成功
    console.log('onopen', this.ws.readyState)
    this.roomOpen = true
    this.ws.send(JSON.stringify({
      userId: this.userName,
      userName: this.nickname,
      roomId: item.roomId,
      roomName: item.name,
      event: 'login', // 像服务端发送一条登陆消息，并携带对应房间信息和用户信息
    }))
}

this.ws.onmessage = (message) => { // 收到消息的回调
  console.log('onmessage', message)
  console.log('message.data: ', message.data)
}

this.ws.onclose = () => { // 监听websocket关闭的回调
  console.log('onclose', this.ws.readyState)
}


close () {
  this.ws && this.ws.close() // 断开websocket连接
}
```

server.js

```js
const Websocket = require('ws')

const wss = new Websocket.Server({ port: 3000 })

wss.on('connection', function (ws) { // 有客户端连接进来
  ws.on('message', function (message) {
    console.log('server receive message: ', message.toString())
  })
  ws.send('msg from server!')

  ws.on('close', function (message) {
    console.log('连接断开', message)
  })
})
```

#### 多个聊天室和统计在线状态

服务端根据roomId来区分多个聊天室，有新的聊天室进来就往数组中加一个roomId, 如果已有房间，则在已有的房间的基础上在线人数加1

home.vue

```js
this.ws.onopen = () => {
    console.log('onopen', this.ws.readyState)
    this.roomOpen = true
    this.ws.send(JSON.stringify({
      userId: this.userName,
      userName: this.nickname,
      roomId: item.roomId,
      roomName: item.name,
      event: 'login',
    }))
    }, 25000)
  }
 this.ws.onmessage = (message) => { // 收到房间消息
    const data = JSON.parse(message.data)

    this.onlineNum = data.num
    if (data.event === 'login') { // 有其他用户进入房间消息
      this.msgList.push({
        content: `欢迎${data.userName}进入${data.roomName}房间～`,
      })
    } else if (data.event === 'logout') { // 有其他用户离开房间消息
      console.log('logout', data)
      this.msgList.push({
        content: `${data.userName}离开房间`,
      })
    } else { // 普通消息
      const self = this.userId === data.userId
      if (self) return
      this.msgList.push({
        name: data.userName,
        self: false,
        content: data.content,
      })
    }
  }
```

server.js

```js
ws.on('message', function (message) {
    console.log('server receive message: ', message.toString())
    const data = JSON.parse(message.toString())

    if (typeof ws.roomId === 'undefined' && data.roomId) {
      ws.roomId = data.roomId
      if (typeof group[ws.roomId] === 'undefined') {
        group[ws.roomId] = 1
      } else {
        group[ws.roomId]++
      }
    }

    data.num = group[ws.roomId]
    wss.clients.forEach(client => {
      if (client.readyState === Websocket.OPEN && client.roomId === ws.roomId) {
        client.send(JSON.stringify(data))
      }
    })
  })

ws.on('close', function (message) {
    group[ws.roomId]-- // 监听到聊天室关闭后，将在线人数减1，并将退出房间的消息推送给其他客户端，更新页面的在线人数

    wss.clients.forEach(function each (client) {
      if (client !== ws && client.readyState === Websocket.OPEN && client.roomId === ws.roomId) {
        client.send(JSON.stringify({
          ...ws.enterInfo,
          event: 'logout',
          num: group[ws.roomId],
        }))
      }
    })
  })
```

#### 首发消息debug查看面板

![](../../\imgs\websocket-9.jpg)

#### 心跳保活

https://juejin.cn/post/6844903765875621896

由于在长连接的场景下，客户端和服务端并不是一直处于通信状态，如果双方长期没有沟通则双方都不清楚对方目前的状态，所以需要发送一段很小的报文告诉对方“我还活着”。

![](../../\imgs\websocket-10.jpg)

如上图所示，在应用层通常是由客户端发送一个心跳包 `ping` 到服务端，服务端收到后响应一个 `pong` 表明双方都活得好好的。

其他目的

- 服务端检测到某个客户端迟迟没有心跳过来可以主动关闭通道，让它下线；

- 客户端检测到某个服务端迟迟没有响应心跳也能重连获取一个新的连接。

一个说明现在需要使用保活功能的常见例子是当个人计算机用户使用TCP/IP向一个使用Telnet的主机注册时。如果在一天结束时，他们仅仅关闭了电源而没有注销，那么便会留下一个半开放的连接。在图18-16中，我们看到通过一个半开放连接发送数据会导致返回一个复位，但那是在来自正在发送数据的客户端。如果客户已经消失了，使得在服务器上留下一个半开放连接，而服务器又在等待来自客户的数据，则服务器将永远等待下去。**保活功能就是试图在服务器端检测到这种半开放的连接。**

而对于服务器而言，能够及时获悉连接可用性也非常重要：一方面服务器需要及时清理无效连接以**减轻负载**，另一方面也是业务的需求，如游戏副本中服务器需要及时处理玩家掉线带来的问题。

home.vue

```js
this.ws.onopen = () => {
  if (this.heartbeatTimer !== -1) {
    clearInterval(this.heartbeatTimer)
    this.heartbeatTimer = -1
  }
  this.heartbeatTimer = setInterval(() => {
    if (this.heartBeatTimeoutJob !== -1) {
      clearTimeout(this.heartBeatTimeoutJob)
      this.heartBeatTimeoutJob = -1
    }
    this.heartBeatTimeoutJob = setTimeout(() => {
      console.log('心跳超时')
    }, 10000)

    this.ws.send(JSON.stringify({
      event: 'heartBeat',
      content: 'ping',
    }))
    console.log('send ping')
  }, 5000)
}

this.ws.onmessage = (message) => {
  console.log('onmessage', message)
  const data = JSON.parse(message.data)
  console.log('message.data: ', data)

  if (data.event === 'heartBeat' && data.content === 'pong') {
    console.log('receive server pong')
    if (this.heartBeatTimeoutJob !== -1) {
      clearTimeout(this.heartBeatTimeoutJob)
      this.heartBeatTimeoutJob = -1
    }
    return
  }
}
 this.ws.onclose = () => {
   console.log('onclose', this.ws.readyState)
   clearInterval(this.heartbeatTimer)
   this.heartbeatTimer = -1
   clearTimeout(this.heartBeatTimeoutJob)
   this.heartBeatTimeoutJob = -1
 }
```

Server.js

```js
// 服务端应该也有一个定时器校验心跳是否超时，如果超时则断开连接
ws.on('message', function (message) {
    // console.log('wss', wss)
    console.log('server receive message: ', message.toString())
    const data = JSON.parse(message.toString())

    if (data.event === 'login') {
      ws.enterInfo = data
    }

    if (data.event === 'heartBeat' && data.content === 'ping') {
      console.log('receive ping message')
      ws.isAlive = true
      ws.send(JSON.stringify({
        event: 'heartBeat',
        content: 'pong',
      }))
      return
    }

    if (typeof ws.roomId === 'undefined' && data.roomId) {
      ws.roomId = data.roomId
      if (typeof group[ws.roomId] === 'undefined') {
        group[ws.roomId] = 1
      } else {
        group[ws.roomId]++
      }
    }
    console.log('groun', group)

    data.num = group[ws.roomId]
    wss.clients.forEach(client => {
      if (client.readyState === Websocket.OPEN && client.roomId === ws.roomId) {
        client.send(JSON.stringify(data))
      }
    })
  })
```

#### 消息必达

1. 消息必达是为了处理长连过程中一些重要消息因为网络、服务器等原因，导致用户未收到消息的兼容处理。下图中，（1）直接消息丢失，无法处理，(2)(3)可通过ack手段使用户能收到重要消息，提高消息触达率。

![](../../\imgs\websocket-11.jpg)

2. 使用ack机制来触使消息必达，即当客户端收到消息后，需要发送一条ack回执，告诉服务端已经收到消息了。如果服务端未接收到客户端的ack消息，则理解为客户端未收到消息，将会重发此消息，以保证用户能够接收到消息。当客户端需要接收消息时，使用ack处理消息必达可能会有以下几种情况：
   
   - 用户收到消息后，发送ack给服务端，在发送过程中网络中断等，导致服务端误以为客户端未收到消息，重发了消息，导致客户端显示了多条重复消息。（消息必达导致的问题，需要另外做去重处理）。
   
   - 用户收到消息后，发送ack给服务端，服务端知道客户端收到消息了，服务端不再推送此消息。
   
   - 用户未收到消息，因此未发送ack给服务端，服务端未接收到ack，重发消息，用户收到了消息，消息必达完成。（消息必达的目的）
   
   - 正常情况
   
   - 故障情况
   
   ![](../../\imgs\websocket-12.jpg)
   
   ![](../../\imgs\websocket-13.jpg)

## 拓展

websocket应用还有离线消息同步，漫游消息等，有兴趣的可以多了解下。

## Demo源码

可前往查看下载demo

home.vue

```js
<template>
  <div class="container">
    <div class="navbar">
      <div v-if="roomOpen" class="back" @click="back">返回</div>
      <div class="title">{{ title }}</div>
    </div>
    <div v-if="!roomOpen"  class="openBg">
      <div class="userInfo">
        <div v-if="!nickname" class="nameInputBox">
          <input type="text" class="nameInput" v-model="inputname" placeholder="请输入昵称" @keydown="nameKeydown" />
          <div class="nameConfirmBtn" @click="certainName">确认</div>
        </div>
        <div v-else class="nickname">昵称：{{ nickname }}</div>
      </div>
      <div class="cardList">
        <div class="itemCard" @click="enterRoom(item)" v-for="(item, i) in roomList" :key="i">
          <img class="pic" :src="item.frame">
          <span class="text">{{ item.name }}</span>
        </div>
      </div>
    </div>
    <div v-else class="bgImage" >
      <div class="roomInfo">
        <div class="onLineBox">
          昵称：<span class="roomNickname">{{ this.nickname }}</span>
          在线人数：{{ this.onlineNum }}
        </div>
      </div>
      <div class="msgBox" ref="msgWrap" @scroll="listScroll">
        <div class="msgPanel" ref="msgList">
          <div :class="['msgItem', item.self && 'selfItem']" v-for="(item, i) in msgList" :key="i">
            <span v-if="!item.self && item.name" style="font-weight: 500; font-size: 15px;color: #1d688c">{{ item.name }} : </span>
            <span :style="{ color: currentRoomInfo.msgColor }">{{ item.content }}</span>
            <span v-if="item.self" style="font-weight: 500; font-size: 15px; color: #1d688c"> : 我</span>
          </div>
        </div>
      </div>

      <div class="msgFooter">
        <input class="msgInput" type="text" v-model="inputMsg" placeholder="请输入发言内容" @keydown="sendInputKeydown" />
        <div class="msgBtn" @click="sendMsg">发言</div>
      </div>
    </div>
  </div>
</template>

<script>
import { Toast } from 'mint-ui'
import smoothscroll from 'smoothscroll-polyfill'
import { throttle, debounce } from 'lodash'
import { roomList } from './config'
import { isScrollBottom } from '@/common/util.js'

smoothscroll.polyfill()

export default {
  name: 'home',
  data () {
    return {
      nickname: '',
      inputname: '',
      userId: '',
      roomList,
      msgList: [],
      roomOpen: false,
      ws: null,
      onlineNum: 0,
      inputMsg: '',
      currentRoomInfo: 0,
      heartbeatTimer: -1,
      heartBeatTimeoutJob: -1,
      msgWrapRef: null,
      scrollIsBottom: true,
      wrapperHeight: 0,
      isBindScrolled: false,
      isSending: false,
      msgListRef: null,
      scrollBottomTimeId: null,
    }
  },
  computed: {
    title() {
      return this.roomOpen ? this.currentRoomInfo.name : '首页'
    },
  },  
  watch: {
    msgList: {
      deep: true,
      handler() {
        this.msgChange()
      },
    },
    roomOpen(val) {
      if (val) {
        this.$nextTick(() => {
          this.msgWrapRef = this.$refs.msgWrap
          this.msgListRef = this.$refs.msgList
          this.wrapperHeight = this.msgWrapRef.offsetHeight
        })
      }
    },
  },
  mounted() {

  },
  methods: {
    enterRoom (item) {
      if (!this.nickname) return Toast('先起个好听的名字吧～')
      this.navbarProps = { ...this.navbarProps, title: item.name }
      this.currentRoomInfo = item

      this.connect()
      console.log('before open', this.ws.readyState)

      this.ws.onopen = () => {
        console.log('onopen', this.ws.readyState)
        this.roomOpen = true
        this.ws.send(JSON.stringify({
          userId: this.userName,
          userName: this.nickname,
          roomId: item.roomId,
          roomName: item.name,
          event: 'login',
        }))

        // 心跳定时器逻辑
        if (this.heartbeatTimer !== -1) {
          clearInterval(this.heartbeatTimer)
          this.heartbeatTimer = -1
        }
        this.heartbeatTimer = setInterval(() => {
          if (this.heartBeatTimeoutJob !== -1) {
            clearTimeout(this.heartBeatTimeoutJob)
            this.heartBeatTimeoutJob = -1
          }
          this.heartBeatTimeoutJob = setTimeout(() => {
            console.log('心跳超时')
            // 需要重连
          }, 10000)

          this.ws.send(JSON.stringify({
            event: 'heartBeat',
            content: 'ping',
          }))
          console.log('send ping')
        }, 25000)
      }

      this.ws.onmessage = (message) => {
        const data = JSON.parse(message.data)

        if (data.event === 'heartBeat' && data.content === 'pong') {
          console.log('receive server pong')
          if (this.heartBeatTimeoutJob !== -1) {
            clearTimeout(this.heartBeatTimeoutJob)
            this.heartBeatTimeoutJob = -1
          }
          return
        }

        this.onlineNum = data.num
        if (data.event === 'login') {
          this.msgList.push({
            content: `欢迎${data.userName}进入${data.roomName}房间～`,
          })
        } else if (data.event === 'logout') {
          console.log('logout', data)
          this.msgList.push({
            content: `${data.userName}离开房间`,
          })
        } else {
          const self = this.userId === data.userId
          if (self) return
          this.msgList.push({
            name: data.userName,
            self: false,
            content: data.content,
          })
        }
      }

      this.ws.onclose = () => {
        this.removeAllTimeJob()
        Toast('您已离开房间')
        this.roomOpen = false
        this.msgList = []
        this.onlineNum = 0
      }
    },
    connect () {
      this.ws = new WebSocket('ws://localhost:3000')
    },
    sendMsg () {
      if (!this.inputMsg.trim().length) return Toast('发言不能为空～')
      this.isSending = true

      this.ws.send(JSON.stringify({
        userName: this.nickname,
        userId: this.userId,
        roomId: this.currentRoomInfo.roomId,
        roomName: this.currentRoomInfo.name,
        content: this.inputMsg.trim(),
      }))
      // 本地默认显示
      this.msgList.push({
        content: `${this.inputMsg}`,
        name: this.nickname,
        self: true,
      })
      this.inputMsg = ''
      setTimeout(() => {
        this.scrollBottom()
        this.isSending = false
      }, 0)
    },
    close () {
      this.ws && this.ws.close()
    },
    back () {
      if (!this.roomOpen) return
      this.roomOpen = false
      this.msgList = []
      this.onlineNum = 0
      this.close()
      Toast({
        content: '您已退出房间',
        duration: 1000,
      })
    },
    certainName () {
      if (this.inputname.trim().length) {
        this.nickname = this.inputname.trim()
        this.userId = `${+new Date()}`
      } else {
        Toast('用户名不能为空！')
      }
    },
    // 移除所有定时器
    removeAllTimeJob() {
      clearInterval(this.heartbeatTimer)
      this.heartbeatTimer = -1
      clearTimeout(this.heartBeatTimeoutJob)
      this.heartBeatTimeoutJob = -1
    },
    msgChange() { // 监听消息列表变化以自动滚动到最新消息
      if (this.scrollBottomTimeId) {
        clearTimeout(this.scrollBottomTimeId)
        this.scrollBottomTimeId = null
      }
      setTimeout(() => {
        if (!this.scrollIsBottom) {
          this.scrollBottom()
          return
        }
        this.$nextTick(() => {
          const listHeight = this.msgListRef.offsetHeight
          const diff = listHeight - this.wrapperHeight // 列表高度与容器高度差值
          const top = this.msgWrapRef.scrollTop // 列表滚动高度
          if (diff - top > 10) {
            if (this.isBindScrolled) {
              this.isBindScrolled = false
              this.msgWrapRef.removeEventListener('scroll', this.addScroll, false)
            }
            this.msgWrapRef.scrollTo({
              top: diff + 10,
              left: 0,
              behavior: 'smooth',
            })
          } else if (!this.isSending) {
            // this.restMessage = true
            if (!this.isBindScrolled) {
              this.isBindScrolled = true
              this.msgWrapRef.addEventListener('scroll', this.addScroll, false)
            }
          }
        })
      }, 500);
    },
    addScroll() {
      debounce(this.listScroll, 200)
      this.isBindScrolled = true
    },
    scrollBottom() {
      this.msgWrapRef.scrollTo({
        top: this.msgListRef.offsetHeight,
        left: 0,
        behavior: 'smooth',
      })
    },
    listScroll() {
      const ele = this.msgWrapRef
      const isBottom = isScrollBottom(ele, ele.clientHeight, 50)
      if (isBottom) {
        this.scrollIsBottom = true
        // this.restMessage = false
      } else {
        this.scrollIsBottom = false
      }
    },
    sendInputKeydown(e) {
      let key = window.event.keyCode;  
      if (key == 13) {
        this.sendMsg()
      }
    },
    nameKeydown(e) {
      let key = window.event.keyCode;  
      if (key == 13) {
        this.certainName()
      }
    }
  },
  beforeDestroy() {
    this.removeAllTimeJob()
    this.close()
    this.msgWrapRef && this.msgWrapRef.removeEventListener('scroll', this.addScroll, false)
  }
}
</script>

<style scoped>
@import './index.scss';
</style>
```

server.js

```js
const Websocket = require('ws')

const wss = new Websocket.Server({ port: 3000 })

const group = {}
const heartBeatTime = 25000


wss.on('connection', function (ws) {
  ws.isAlive = true
  ws.heartBeatTimeIntervalObj = -1
  ws.heartBeatTimeoutObj = -1

  // 监听心跳定时器，如果长时间没有心跳，自动断开连接
  const setHeartBeatTimeout = () => {
    if (ws.heartBeatTimeoutObj !== -1) {
      clearTimeout(ws.heartBeatTimeoutObj)
      ws.heartBeatTimeoutObj = -1
    }
    ws.heartBeatTimeoutObj = setTimeout(() => {
      ws.close()
      ws.isAlive = false
    }, heartBeatTime)
  }

  setHeartBeatTimeout()

  ws.on('message', function (message) {
    console.log('server receive message: ', message.toString())
    const data = JSON.parse(message.toString())

    setHeartBeatTimeout()
    if (data.event === 'login') {
      ws.enterInfo = data
    }

    if (data.event === 'heartBeat' && data.content === 'ping') {
      ws.isAlive = true
      clearTimeout(ws.heartBeatTimeoutObj)
      ws.heartBeatTimeoutObj = -1


      ws.send(JSON.stringify({
        event: 'heartBeat',
        content: 'pong',
      }))
      return
    }

    if (typeof ws.roomId === 'undefined' && data.roomId) {
      ws.roomId = data.roomId
      if (typeof group[ws.roomId] === 'undefined') {
        group[ws.roomId] = 1
      } else {
        group[ws.roomId]++
      }
    }

    data.num = group[ws.roomId]
    wss.clients.forEach(client => {
      if (client.readyState === Websocket.OPEN && client.roomId === ws.roomId) {
        client.send(JSON.stringify(data))
      }
    })
  })

  ws.on('close', function (message) {
    group[ws.roomId]--

    wss.clients.forEach(function each (client) {
      if (client !== ws && client.readyState === Websocket.OPEN && client.roomId === ws.roomId) {
        client.send(JSON.stringify({
          ...ws.enterInfo,
          event: 'logout',
          num: group[ws.roomId],
        }))
      }
    })
  })
})
```

## 参考文档

书籍：《HTML5 WebSocket权威指南》

HTTP、socket、 Websocket的联系和区别：https://www.cnblogs.com/aspirant/p/11334957.html

长连接的心跳及重连设计：https://juejin.cn/post/6844903765875621896

负载均衡：https://juejin.cn/post/6844903689111470094

https://juejin.cn/post/6844903689581395981

websocket教程: https://juejin.cn/post/6844903977457287181

全双工和半双工：https://blog.csdn.net/a3192048/article/details/85319182

websocket教程：https://juejin.cn/post/6844903977457287181 、 https://www.jmjc.tech/less/114

websocket编写聊天室：https://www.liaoxuefeng.com/wiki/1022910821149312/1103332447876608

ws github: https://github.com/websockets/ws

websocket握手总结：https://blog.csdn.net/QQ729533020/article/details/99739827

使用.net和vue搭建websocket聊天室：https://qinyuanpei.github.io/posts/1989654282/
