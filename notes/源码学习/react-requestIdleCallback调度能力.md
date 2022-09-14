# 实现 React requestIdleCallback 调度能力

## 前言

Elab掘金: React Fiber架构浅析[1] 已对 React Fiber架构 实现进行了浅析。React内部实现了该方法 requestIdleCallback，即一帧空闲执行任务，但Schedular + Lane 模式远比 requestIdleCallback 复杂的多。这里我们先通过了解 requestIdleCallback都做了些什么，再尝试通过 requestAnimationFrame + MessageChannel 来模拟 React 对一帧空闲判断的实现。

## requestIdleCallback

> window.requestIdleCallback()[2]

### 概念理解

![](../../\imgs\react-requestIdleCallback-1.png)

RequestIdleCallback 简单的说，判断一帧有空闲时间，则去执行某个任务。

目的是为了解决当任务需要长时间占用主进程，导致更高优先级任务(如动画或事件任务)，无法及时响应，而带来的页面丢帧(卡死)情况。

故RequestIdleCallback 定位处理的是: **不重要且不紧急的任务。**

RequestIdleCallback 参数说明:

- window.requestIdleCallback(callback[, options]); callback为要执行的回调函数，该函数会接收deadline作为对象。

```js
// 回调函数 接收 deadline

type Deadline = {

  timeRemaining: () => number // 当前剩余的可用时间。即该帧剩余时间。

  didTimeout: boolean // 是否超时。

}



// 接收回调任务

type RequestIdleCallback = (cb: (deadline: Deadline) => void, options?: Options) => number 
```

### 实现demo

requestIdleCallback 处理任务说明:

> Demo: https://linjiayu6.github.io/FE-RequestIdleCallback-demo/

> Github: RequestIdleCallback 实验[3]

![](../../\imgs\react-requestIdleCallback-2.png)

```js
const bindClick = id => 

  element(id).addEventListener('click', Work.onAsyncUnit)

// 绑定click事件

bindClick('btnA')

bindClick('btnB')

bindClick('btnC')



var Work = {

    // 有1万个任务

    unit: 10000,

    // 处理单个任务需要处理如下

    onOneUnit: function () {  for (var i = 0; i <= 500000; i++) {} },

    

    // 处理任务

    onAsyncUnit: function () {

        // 空闲时间基准为 1ms

        const FREE_TIME = 1

        // 执行到第几个任务

        let _u = 0



        function cb(deadline) {

            // 当任务还没有被处理完 & 一帧还有的空闲时间 > 1ms

            while (_u < Work.unit && deadline.timeRemaining() > FREE_TIME) {

                Work.onOneUnit()

                _u ++

            }

            // 任务干完, 执行回调

            if (_u >= Work.unit) {

                // 执行回调

                return

            }

            // 任务没完成, 继续等空闲执行

            window.requestIdleCallback(cb)

        }

        window.requestIdleCallback(cb)

    }

}
```

以上是 window.requestIdleCallback 的实现流程。

核心: 即浏览器去在一帧有空闲的情况下，去执行某个低优先级的任务。

### 缺陷

> MAY BE OFFTOPIC: requestIdleCallback is called only 20 times per second - Chrome on my 6x2 core Linux machine, it's not really useful for UI work. requestAnimationFrame is called more often, but specific for the task which name suggests.[4]

- 实验 api，兼容情况一般。

- 实验结论: requestIdleCallback FPS只有20ms，正常情况下渲染一帧时长控制在16.67ms (1s / 60 = 16.67ms)。该时间是高于页面流畅的诉求。

- 个人认为: RequestIdleCallback 不重要且不紧急的定位。因为React渲染内容，并非是不重要且不紧急。不仅该api兼容一般，帧渲染能力一般，也不太符合渲染诉求，故React 团队自行实现。

## React requestIdleCallback 实现实验

想要实现requestIdleCallback的处理，有2个点需要解决:

- When: 如何判断一帧是否有空闲？

- Where: 如果有了空闲，在一帧中哪里去执行任务？

### requestAnimationFrame 计算一帧到期时间点

> requestAnimationFrame[5]

- > 是由系统来决定回调函数的**执行时机。** 它会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的**时间**间隔紧紧跟随屏幕的刷新频率，不会引起丢帧和卡顿。

- > 浏览器刷新率在60Hz, 渲染一帧时长控制在16.67ms (1s / 60 = 16.67ms)。

> DOMHighResTimeStamp[6]

requestAnimationFrame 参数如下:

```js
// 回调函数 接收 rafTime 即 开始执行一帧的开始时间

// 接收回调任务

type RequestAnimationFrame = (cb: (rafTime: number) => void)
```

计算一帧用到期的时间点。

```js
// 计算出当前帧 结束时间

var deadlineTime;

window.selfRequestIdleCallback = function (cb) {

    requestAnimationFrame(rafTime => {

        // 结束时间 = 开始时间 + 一帧用时16.667ms

        deadlineTime = rafTime + 16.667

        // ...... 

    })

}
```

以上使用 **requestAnimationFrame 来计算结束的时间点**。

我们暂且将空闲时间的判断放到后面去解决，先来看在时间充裕情况下，在什么时机去执行某任务。

### MessageChannel 宏任务 执行任务

> MessageChannel()[7]

> MessageChannel创建了一个通信的管道，这个管道有两个端口，每个端口都可以通过postMessage发送数据，而一个端口只要绑定了onmessage回调方法，就可以接收从另一个端口传过来的数据。

在看着方法实现之前，你可能有疑问:

1. **为什么使用宏任务处理呢？**

核心是将主进程让出，将浏览器去更新页面。

利用事件循环机制，在下一帧宏任务的时候，执行未完成的任务。

2. **为什么不是微任务？**

走远了。对一个事件循环机制来说，在页面更新前，会将所有的微任务全部执行完，故无法达成将主线程让出给浏览器的目的。

3. **既然用了宏任务，那为什么不使用 setTimeout 宏任务执行呢?**
- 如果不支持MessageChannel的话，就会去用 setTimeout 来执行，只是退而求其次的办法。

- 现实情况是: 浏览器在执行 `setTimeout()` 和 `setInterval()` 时，会设定一个最小的时间阈值，一般是 4ms。

```js
var i = 0

var _start = +new Date()

function fn() {

  setTimeout(() => {

    console.log("执行次数, 时间", ++i, +new Date() - _start)

    if (i === 10) {

      return

    }

    fn()

  }, 0)

}

fn()
```

![](../../\imgs\react-requestIdleCallback-3.png)

故，利用MessageChannel来执行宏任务，且模拟setTimeout(fn, 0)，还没有时延哦。

实现如下:

```js
// 计算出当前帧 结束时间点

var deadlineTime

// 保存任务

var callback

// 建立通信

var channel = new MessageChannel()

var port1 = channel.port1;

var port2 = channel.port2;



// 接收并执行宏任务

port2.onmessage = () => {

    // 判断当前帧是否还有空闲，即返回的是剩下的时间

    const timeRemaining = () => deadlineTime - performance.now();

    const _timeRemain = timeRemaining();

    // 有空闲时间 且 有回调任务

    if (_timeRemain > 0 && callback) {

        const deadline = {

            timeRemaining, // 计算剩余时间

            didTimeout: _timeRemain < 0 // 当前帧是否完成

        }

        // 执行回调

        callback(deadline)

    }

}

window.requestIdleCallback = function (cb) {

    requestAnimationFrame(rafTime => {

        // 结束时间点 = 开始时间点 + 一帧用时16.667ms

        deadlineTime = rafTime + 16.667

        // 保存任务

        callback = cb

        // 发送个宏任务

        port1.postMessage(null);

    })

}
```

## React 源码 requestHostCallback

> SchedulerHostConfig.js[8]

**执行宏任务(回调任务)**

- requestHostCallback: 触发一个宏任务 performWorkUntilDeadline。

- performWorkUntilDeadline: 宏任务处理。

- 是否有富裕时间, 有则执行。

- 执行该回调任务后，是否还有下一个回调任务, 即判断 hasMoreWork。

- 有则继续执行 port.postMessage(null);

```js
 let scheduledHostCallback = null;

  let isMessageLoopRunning = false;

 
  const channel = new MessageChannel();

  // port2 发送

  const port = channel.port2;

  // port1 接收

  channel.port1.onmessage = performWorkUntilDeadline;

  const performWorkUntilDeadline = () => {

    // 有执行任务

    if (scheduledHostCallback !== null) {

      const currentTime = getCurrentTime();

      // Yield after `yieldInterval` ms, regardless of where we are in the vsync

      // cycle. This means there's always time remaining at the beginning of

      // the message event.

      // 计算一帧的过期时间点

      deadline = currentTime + yieldInterval;

      const hasTimeRemaining = true;

      try {

        // 执行完该回调后, 判断后续是否还有其他任务

        const hasMoreWork = scheduledHostCallback(

          hasTimeRemaining,

          currentTime,

        );

        if (!hasMoreWork) {

          isMessageLoopRunning = false;

          scheduledHostCallback = null;

        } else {

          // If there's more work, schedule the next message event at the end

          // of the preceding one.

          // 还有其他任务, 推进进入下一个宏任务队列中

          port.postMessage(null);

        }

      } catch (error) {

        // If a scheduler task throws, exit the current browser task so the

        // error can be observed.

        port.postMessage(null);

        throw error;

      }

    } else {

      isMessageLoopRunning = false;

    }

    // Yielding to the browser will give it a chance to paint, so we can

    // reset this.

    needsPaint = false;

  };

  // requestHostCallback 一帧中执行任务

  requestHostCallback = function(callback) {

    // 回调注册

    scheduledHostCallback = callback;

    if (!isMessageLoopRunning) {

      isMessageLoopRunning = true;

      // 进入宏任务队列

      port.postMessage(null);

    }

  };

  cancelHostCallback = function() {

    scheduledHostCallback = null;

  };
```

## 参考资料

[1] Elab掘金: React Fiber架构浅析:  https://juejin.cn/post/7005880269827735566

[2] window.requestIdleCallback():  https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestIdleCallback

[3] RequestIdleCallback 实验:  https://github.com/Linjiayu6/FE-RequestIdleCallback-demo

[4] MAY BE OFFTOPIC: requestIdleCallback is called only 20 times per second - Chrome on my 6x2 core Linux machine, it's not really useful for UI work. requestAnimationFrame is called more often, but specific for the task which name suggests.:  https://github.com/facebook/react/issues/13206#issuecomment-418923831

[5] requestAnimationFrame:  https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame

[6] DOMHighResTimeStamp:  https://developer.mozilla.org/zh-CN/docs/Web/API/DOMHighResTimeStamp

[7] MessageChannel():  https://developer.mozilla.org/zh-CN/docs/Web/API/MessageChannel/MessageChannel

[8] SchedulerHostConfig.js:  https://github.com/facebook/react/blob/v17.0.1/packages/scheduler/src/forks/SchedulerHostConfig.default.js
