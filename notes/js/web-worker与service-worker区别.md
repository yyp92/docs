# web worker与service worker区别

web worker和service worker都是运行在浏览器主线程之外的线程中的脚本，它们之间主要的区别就是`应用场景和作用`不同。

## web worker

### 什么是web worker

web worker是运行在后台的JavaScript，它是独立于浏览器的。在浏览器中，主线程包含js引擎和渲染进程等。因为JavaScript是单线程的，一旦涉及到复杂的计算，就会影响到渲染进程的执行，从而造成页面卡顿，甚至崩溃。
将计算移动到web worker中，等到计算结束，再把结果返回给主线程，然后主线程再执行。这样的话页面就会很顺畅，不会卡顿。

### web worker的原理

前面我们已经介绍了webworker，现在我们来理解一下webworker的原理。webworker采用的机制就是如果遇到一些复杂的计算逻辑，主线程就会把它交给web workers子线程去处理，处理完成后，再把结果返回给主线程，然后主线程再执行。
换句话说，web worker的主要作用就是使用JavaScript创建workers线程，浏览器的主线程就会把复杂的逻辑，内耗较大的任务交给workers线程去做，主线程继续运行，同时workers线程也在后台运行，这样它们互不干扰，直到workers线程运行完成，把结果返回给主线程，主线程再继续执行相关的任务内容。

workers线程一旦创建成功了，就会一直运行，不会被主线程打断，这样就能随时响应主线程的通信了。但是这样的话workers线程也会很费资源，因此我们在实际项目开发中要适当地使用，使用完成之后要及时关闭workers线程。

### web worker的用法

我们在使用web worker的时候，需要注意两点问题：

- 同源限制： 分配给workers线程运行的脚本文件，必须与主线程的脚本文件同源。

- 文件限制： workers线程是运行在后台的，它所加载的脚本都必须是网络上的，不能读取本地文件。

- DOM限制： workers线程是不能直接操作dom对象的，如果要处理dom对象的话，应该是workers线程将内容返回给主线程，然后主线程再去操作DOM对象。

- 脚本限制： workers线程不能执行alert()和confirm等方法，但可以使XMLHttpRequest发出ajax请求。

- 通信限制： workers线程和主线程不在同一个上下文环境，它们不能直接通信，只能通过消息来完成。

#### worker的基本用法

##### 主线程

需要在主线程中创建一个worker，为了兼容性问题，我们最好判断一下当前项目是否支持worker。 向下兼容：

```js
if (window.worker) {
  // 表示支持worker
}
```

创建一个worker很简单，就是调用Worker()构造函数。制定一个脚本的URI来执行worker线程。

```js
let myWorker = new Worker('worker.js');
```

需要注意的是这个脚本必须是网络脚本，因为worker不能获取本地脚本，如果脚本下载失败，那么worker也会默默地失败。

主线程向worker线程发送消息：

```js
myWorker.postMessage('Hello world');
myWorker.postMessage({method: 'echo', args: ['work']})
```

主线程给worker线程发的消息可以是各种数据类型：基本数据类型和引用数据类型，也可以是二进制数据。

主线程监听worker线程发过来的消息：

```js
myWorker.onmessage = e => {
  console.log(e.data);
  setTimeout(() => {
    myWorker.postMessage('线程关闭');
    myWorker.terminate();
  }, 1000)
}
```

worker线程完成后，主线程需要关闭worker线程：

```js
myWorker.terminate();
```

##### worker线程

worker内部需要一个监听函数，这个监听函数是通过addEventListener绑定在worker线程上的，事件名称是message。

```js
addEventListener('message', e => {
  const { data } = e;
  console.log(data);
  setTimeout(() => {
    return postMessage('线程完成');
  }, 1000);
}, false)
```

也可以写成这样:

```js
this.addEventListener('message', e => {
  this.postMessage('线程完成');
}, false)
```

也可以用self表示:

```js
self.addEventListener('message', function (e) {
  self.postMessage('You said: ' + e.data);
}, false);
```

除了使用self.addEventListener()指定监听函数，也可以使用self.onmessage指定。监听函数的参数是一个事件对象，它的data属性包含主线程发来的数据。self.postMessage()方法用来给主线程发送消息。
这些内容其实和主线程的监听，主线程向worker线程发送消息差不多。

worker线程关闭自身：

```js
self.close();
```

##### worker加载脚本

worker内部如果要加载其他脚本，需要使用importScripts方法。

```js
importScripts('script1.js');
// 加载多个脚本
importScripts('script1.js', 'script2.js');
```

##### 错误处理

主线程可以监听worker是否发生错误。如果发生错误，worker会触发主线程的error事件。

```js
myWorker.onerror = e => {
  console.log('error:', e);
}

// 也可以这样实现：
myWorker.addEventListener('error', function(e) {
  console.log('error:', e);
})
```

worker线程内部也可以监听error事件，这点和发送消息等一样。

##### 关闭worker

这点我们上面已经介绍过，主要分为两种。

- 在主线程内关闭worker线程

```js
myWorker.terminate();
```

- 在worker线程内关闭自身

```js
self.close();
```

#### 在Vue中使用web Worker

##### 安装 worker loader

```bash
npm install -D worker-loader
```

##### 在vue.config.js中配置

```js
// 在webpack中配置worker-loader
// web-worker相关配置
chainWebpack: config => {
  config.module.rule('worker').test(/\.worker\.js$/).use('worker-loader').loader('worker-loader').options({
    inline: true
  }).end();
},
```

上述配置的意思是以worker.js结尾的文件将被worker-loader加载，也就是说我们在写worker脚本的时候可以直接是worker.js命名，也可以是xxx.worker.js命名。
其中inline: true是将worker和blob进行内联，这种模式下将额外为浏览器创建chunk，即使对于不支持worker的浏览器来说也是这样。

也可以这样配置:

```js
module.exports = {
  chainWebpack(config) {
    // set worker-loader
    config.module
      .rule('worker')
      .test(/\.worker\.js$/)
      .use('worker-loader')
      .loader('worker-loader')
      .end();

    // 解决：worker 热更新问题
    config.module.rule('js').exclude.add(/\.worker\.js$/);
  },
  parallel: false,
  chainWebpack: config => {
    // 解决：“window is undefined”报错，这个是因为worker线程中不存在window对象，因此不能直接使用，要用this代替
    config.output.globalObject('this')
  }
}
```

##### 创建worker.js文件

在测试过程中是直接在最外层创建了一个test.worker.js文件，里面的内容如下：

```js
/ 测试worker的使用
addEventListener('message', e => {
  const { data } = e;
  console.log(data);
  setTimeout(() => {
    return postMessage('线程完成');
  }, 1000);
})
export default { }
```

##### 在页面中引入

在Vue页面中引入脚本，然后创建一个worker实例，也就能创建出一个worker线程了

```js
import TestWorker from '../../test.worker';
// 创建worker线程
const worker = new TestWorker();
worker.postMessage('开启线程');
worker.onmessage = e => {
  console.log(e.data);
  setTimeout(() => {
    worker.postMessage('线程关闭');
    worker.terminate();
  }, 1000)
}
```

❓❓❓也许你会有疑问：new Worker(url) 里面不是说脚本必须是处于网络上的吗？为什么我们这里引入的是本地的脚本？
首先需要说明的是vue 里面的web worker是封装之后的，我们这里调用的时候不能使用原生的写法，而且通常我们的worker脚本是写在一个单独的文件夹中，需要使用import引入，引入的是什么变量，那么创建实例的时候，NEW关键字后面就是什么变量。比如上面我们引入的worker的脚本变量是TestWorker，那么我们创建实例的时候就应该是new TestWorker();需要前后保持一致。
其实在worker loader底层使用的也是原生来实现的。只是我们vue项目在启动的时候，所有的资源文件都会编译到服务器上，其实最后解析的时候也是从服务端拿到worker脚本的，只是这个服务比较特殊，是我们本地服务器(ip)。

##### 测试

页面控制台会依次执行，打印出一下内容：

```js
开启线程
线程完成
线程关闭
```

### web worker的应用场景

因为web worker在使用的时候是会浪费一些资源的，所以不到必须使用的使用还是不要使用的好，所以这里我们就介绍几种web workr的应用场景, 具体实现这里不做过多介绍

- 加密数据
  有些加解密的算法比较复杂，或者在加解密很多数据的时候，这会非常耗费计算资源，导致UI线程无响应

- 预取数据
  有时候为了提升数据加载速度，可以提前使用worker线程获取数据，因为worker是可以使用XMLHttpRequest的

- 预渲染
  在某些渲染场景下，比如渲染复杂的canvas的时候需要计算的效果比如反射，折射，光影，材料等都可以使用worker线程来处理。

- 复杂数据处理场景
  某些检索、排序、过滤、分析会非常耗费时间，这时可以使用web worker来进行，不占用主线程

- 预加载图片
  有时候一个页面中有很多图片，或者有几个很大的图片的时候，如果业务不考虑懒加载，也可以使用web worker来加载图片

## service worker

### 什么是service worker

service worker是继appcache之后出现的解决浏览器离线缓存问题的技术，因为appcache有很多坑，所有人们就基于web worker推出了service worker。service worker是在web worker的基础上实现了持久离线缓存的能力。

> Service workers 本质上充当 Web 应用程序、浏览器与网络（可用时）之间的代理服务器。这个 API 旨在创建有效的离线体验，它会拦截网络请求并根据网络是否可用来采取适当的动作、更新来自服务器的资源。它还提供入口以推送通知和访问后台同步 API。

### service worker的原理

- service worker是一个注册在指定源和路径下的事件驱动worker。采用JavaScript控制相关的页面或网站，拦截并修改访问和资源请求，细粒度地缓存资源。

- service worker是基于web worker的，运行在worker上下文，所以它的很多原理都是和web worker是一样的。因此它也不能访问DOM，相对于驱动应用的JavaScript主线程，它是运行在其他线程中，所以不会造成阻塞。它的设计是完全异步的，所以同步API是不能使用的。

- service worker只能由HTTPS承载。是因为https比http安全，毕竟修改网络请求的能力暴露给中间人攻击会非常危险。

#### 特点

- 一个独立的worker线程，独立于当前网页进程，有自己独立的worker context

- 一旦被install，就永远存在，除非被手动unregister

- 用到的时候就可以直接唤醒，不用的时候自动睡眠

- 可编程拦截代理请求和返回，缓存文件，缓存文件可以被网页进程取到（包括网络离线状态）

- 离线内容开发者可控

- 能向客户推送消息

- 不能直接操作DOM

- 必须在HTTPS环境下才能工作

- 异步实现，内部大都是通过Promise实现
  所以service worker的主要作用就是让缓存做到优雅和极致，让Web App相对于原生APP的缺点更加弱化，也为开发者提供了对性能和体验的无限遐想。

#### 生命周期

service worker的生命周期完全独立于网页。主要是以下几个过程：install -> waiting -> activate -> fetch

### service worker的用法

#### 注册worker

```js
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register(
    '/sw-test/sw.js',
    { scope: '/sw-test/'}
  ) 
  .then(function(reg) {
    console.log('注册成功', reg.scope)
  }).catch(function(err) {
    console.log('注册失败', err); 
  });
}
```

首先要判断一下当前浏览器是否支持service worker, 如果支持的话就用ServiceWorkerContainer.register()函数来注册站点的service worker。第一个参数是url，是相对于于根目录的url.第二个参数是scope，这个参数是选填的。可以用来指定想要让service worker控制的子目录。在这个例子中我们写的是'/sw-test/'，表示的是根目录下的所有内容，如果不写的话，也默认是根目录下的所有内容。这个注册函数执行完成后返回的是一个promise对象，可以执行.then()和。catch()函数分别去执行回调函数和捕获异常。
需要注意的是：单个service worker可以控制很多页面。每个你的scope里的页面加载完的时候，安装在页面的service worker可以控制它。牢记你需要小心service worker脚本里的全局变量：每个页面不会有自己独特的worker。

##### 安装和激活

serviceWorker注册之后，浏览器会尝试为你的页面或站点安装或激活它。
install事件会在注册完成之后触发。install事件一般是被用来填充你的浏览器的离线缓存能力。主要使用到的API就是cache，一个service worker上的全局对象，它使我们可以存储网络响应发来的资源，并且根据它们请求生成key。这个缓存是一直存在的，直到你让它不再存储。

```js
this.addEventListener('install', function(e) {
  e.waitUntil(
    cache.open('V1').then(function(cache) {
      return cache.addAll([
        '/sw-test/',
        '/sw-test/index.html',
        '/sw-test/style.css',
        '/sw-test/app.js',
        '/sw-test/image-list.js',
        '/sw-test/star-wars-logo.jpg',
        '/sw-test/gallery/',
        '/sw-test/gallery/bountyHunters.jpg',
        '/sw-test/gallery/myLittleVader.jpg',
        '/sw-test/gallery/snowTroopers.jpg'
      ]);
    })
  )
})
```

首先新增一个install的监听器，然后在事件上接了一个Extendable.waitUntil()方法----保证service worker不会在waitUntil里面的代码执行完成之前安装完成。在waitUntil()内，调用了cache.open()创建了一个新的缓存V1, 返回了一个创建缓存的promise对象。当resolves时会调用cache上的addAll()方法，第一个参数是一组相对根目录的url组成的列表，也就是我们想缓存的资源的列表。如果被rejected, 安装就会失败。这个worker就不不会起作用。

`waitUntil 机制`：ExtendableEvent.waitUntil()方法告诉事件分发器该事件仍在进行。这个方法也可以用于检测进行的任务是否成功。在服务工作线程中，这个方法告诉浏览器事件一直进行，直至promise.resolve，浏览器不应该在事件中的异步操作完成之前终止服务进程。

##### 自定义请求

到这一步之前，你的页面或站点资源缓存了，接下来你需要告诉service worker让它用这些缓存内容来做点什么，前面我们讲过离线缓存就是在有缓存的情况下拦截我们的请求，直接将缓存里面的内容加载出来。
每次任何被service worker控制的资源被请求到时，都会触发fetch事件，这些资源主要包含上面注册的指定的scope内的文档，和这些文档内引用的其他任何资源。通过给service worker添加一个fetch的事件监听器，接着调用event上的respondWith()方法来劫持我们的HTTP响应，然后更新他们。

```js
this.addEventListener('fetch', function(e) {
  e.respondWith(
    // ....
  )
})
```

##### serviceWorker更新

**自动更新**

```js
this.addEventListener('install',function(event){
  this.skipWaiting();
});
this.addEventListener('activate', function (event) {
  this.clients.claim();
});
```

`skipWaiting`：Service Worker 一旦更新，需要等所有的终端都关闭之后，再重新打开页面才能激活新的 Service Worker，这个过程太复杂了。通常情况下，开发者希望当 Service Worker 一检测到更新就直接激活新的 Service Worker。如果不想等所有的终端都关闭再打开的话，只能通过 skipWaiting 的方法了。
Service Worker 在全局提供了一个 skipWaiting() 方法，skipWaiting() 在 waiting 期间调用还是在之前调用并没有什么不同。一般情况下是在 install 事件中调用它。

`clients.claim() 方法`：如果使用了 skipWaiting 的方式跳过 waiting 状态，直接激活了 Service Worker，可能会出现其他终端还没有受当前终端激活的 Service Worker 控制的情况，切回其他终端之后，Service Worker 控制页面的效果可能不符合预期，尤其是如果 Service Worker 需要动态拦截第三方请求的时候。
为了保证 Service Worker 激活之后能够马上作用于所有的终端，通常在激活 Service Worker 后，通过在其中调用 self.clients.claim() 方法控制未受控制的客户端。self.clients.claim() 方法返回一个 Promise，可以直接在 waitUntil() 方法中调用

**手动更新**
手动更新主要是调用在注册serviceWorker时registration的update方法。它会获取worker的脚本URL，如果新的worker和当前的worker并不是完全相同的则按照新的worker。

```js
navigator.serviceWorker.register('./sw.js').then(function(registration){
  registration.update();
});
```

如何处理 Service Worker 的更新。

- 如果目前尚未有活跃的 SW ，那就直接安装并激活。

- 如果已有 SW 安装着，向新的 swUrl 发起请求，获取内容和和已有的 SW 比较。
  
  - 如没有差别，则结束安装。
  
  - 如有差别，则安装新版本的 SW（执行 install 阶段），之后令其等待（进入 waiting 阶段）如果老的 SW 控制的所有页面 「全部关闭」，则老的 SW 结束运行，转而激活新的 SW（执行 activated 阶段），使之接管页面。

从上述用法可以看出，注册service worker是发生在页面的，其他的安装，激活，自定义请求等都是写在SW.js中(也就是service worker脚本文件)。

### service worker的应用场景

- 离线缓存
  配合CacheStorage可以将应用中不变化的资源或者很少变化的资源长久的存储在用户端，提升加速速度、降低流量消耗、降低服务器压力，提高请求速度，让用户体验更加丝滑

- 消息推送
  激活沉睡的用户，推送即时消息、公告通知、激发更新等。如web资讯客户端、web即时通讯工具、h5游戏等运营产品。

- 事件同步：确保web端产生的任务即使在用户关闭了web页面也可以顺利完成。如web邮件客户端、web即时通讯工具等

- 定时同步：周期性的触发service worker脚本中的定时同步事件，可借助它提前刷新缓存内容

- 结合CacheStorage、Push API和Notification API
