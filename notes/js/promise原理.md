# v8 - promise原理

## 例题

```js
Promise.resolve().then(() => {
    console.log(0);
    return Promise.resolve(4)
}).then(res => {
    console.log(res);
})

Promise.resolve().then(() => {
    console.log(1);
}).then(() => {
    console.log(2);
}).then(() => {
    console.log(3);
}).then(() => {
    console.log(5);
}).then(() => {
    console.log(6);
})
// 0 1 2 3 4 5 6


new Promise((resolve, reject) => {
    Promise.resolve().then(() => {
        resolve({
            then: (resolve, reject) => resolve(1)
        });
        Promise.resolve().then(() => console.log(2));
    });
}).then(v => console.log(v));
// 2 1
```

## 原理

### 基本概念

- executor：是一个函数，`Promise` 构造函数接收的参数，调用 `executor` 时传入的参数分别是 `resolve` 和 `reject`。

- PromiseReaction：是对象，表示 `Promise` 的处理函数，因为一个 `Promise` 多次调用 `then` 方法就会有多个处理函数，所以底层数据结构是个链表，每一个节点都存储着 `onFulfilled` 和 `onRejected` 函数。

### 构造函数

`executor` 的类型是函数，在 JavaScript 的世界里，回调函数通常是异步调用，但 `executor` 是同步调用。在 `Call(context, UnsafeCast(executor), Undefined, resolve, reject)` 这一行，同步调用了 `executor`。

```js
console.log('同步执行开始')
new Promise((resolve, reject) => {
  resolve()
  console.log('executor 同步执行')
})

console.log('同步执行结束')
// 本段代码的打印顺序是:
// 同步执行开始
// executor 同步执行
// 同步执行结束
```

> Promise 构造函数接收的参数 `executor`，是被同步调用的。

### then

##### PromisePrototypeThen

`PromisePrototypeThen` 函数创建了一个新的 `Promise` 对象，获取 `then` 接收到的两个参数，调用 `PerformPromiseThenImpl` 完成大部分工作。这里有一点值得注意，`then` 方法返回的是一个新创建的 `Promise`。

```js
const myPromise2 = new Promise((resolve, reject) => {
  resolve('foo')
})

const myPromise3 = myPromise2.then(console.log)

// myPromise2 和 myPromise3 是两个不同的对象
// 有不同的状态和不同的处理函数
console.log(myPromise2 === myPromise3) // 打印 false
```

> then 方法返回的是一个新的 Promise

#### PerformPromiseThenImpl

PerformPromiseThenImpl`有4个参数，因为`PerformPromiseThenImpl`是在调用`then`调用，所以它的前三个参数分别是被调用` then`方法的`Promise`对象，以及这个对象的两个处理函数`onFulfilled`和`onRejected`，最后一个参数为调用这个` then`返回的新`Promise`对象`resultPromiseOrCapability`。

##### PerformPromiseThenImpl 函数的 pending 分支

PerformPromiseThenImpl 有三个分支，分别对应 Promise 的三个状态，当被调用 then 方法的 Promise 处于 pending 状态时则进入 pending分支。pending 分支调用 `NewPromiseReaction`函数，在接收到的 onFulfilled 和 onRejected 参数的基础上，生成 `PromiseReaction` 对象，存储 Promise 的处理函数，并赋值给 `JSPromise` 的 `reactions_or_result` 字段，然后调用 `promise.SetHasHandler()` 将 `has_handler` 设置为 `true`（表示这个 Promise 对象已经绑定了处理函数）。

考虑一个 Promise 可以会连续调用多个 then 的情况，比如：

```js
const p = new Promise((resolve, reject) => {
  setTimeout(_ => {
    resolve('my code delay 2000 ms') 
  }, 2000)
})

p.then(result => {
  console.log('第 1 个 then')
})

p.then(result => {
  console.log('第 2 个 then')
})
```

p 调用了两次 then 方法，每个 then 方法都会生成一个 `PromiseReaction` 对象。第一次调用 then 方法时生成对象 PromiseReaction1，此时 p 的 `reactions_or_result` 存的是 PromiseReaction1。

第二次调用 then 方法时生成对象 PromiseReaction2，调用 `NewPromiseReaction` 函数时，`PromiseReaction2.next = PromiseReaction1`，PromiseReaction1 变成了 PromiseReaction2 的下一个节点，最后 p 的 `reactions_or_result` 存的是 PromiseReaction2。PromiseReaction2 后进入 Promise 处理函数的链表，却是链表的头结点。

#### PerformPromiseThenImpl 函数的 fulfilled 分支

fulfilled 分支逻辑则简单的多，处理的是当 Promise 处于 fulfilled 状态时，调用 then 方法的逻辑：

先调用 `NewPromiseFulfillReactionJobTask` 生成 `microtask`，然后 `EnqueueMicrotask(handlerContext, microtask)` 将刚才生成的 `microtask` 放入 `microtask 队列`，最后调用 `promise.SetHasHandler()` 将 `has_handler` 设置为 `true`。

```js
new Promise((resolve, reject) => {
  resolve()
}).then(result => {
  console.log('进入 microtask 队列后执行')
})

console.log('同步执行结束')
// 本段代码的打印顺序是:
// 同步执行结束
// 进入 microtask 队列后执行
```

尽管调用 then 方法时，Promise 已经处于 fulfilled 状态，但 then 方法的 onFulfilled 回调函数不会立即执行，而是进入 microtask 队列等待执行。

#### PerformPromiseThenImpl 函数的 rejected 分支

rejected 分支逻辑与 fulfilled 分支的逻辑大致相同，但是 rejected 分支中将 onRejected 处理函数加入 microtask 队列之前，会先判断当前 promise 是否已经存在处理函数，如果已经存在则会先调用 `runtime::PromiseRevokeReject(promise)`，最后调用 `promise.SetHasHandler()` 将 `has_handler` 设置为 `true`。

```js
if (!promise.HasHandler()) {
    runtime::PromiseRevokeReject(promise);
}
```

这里的`runtime::PromiseRevokeReject(promise)` 就是 ECMAScript 规范[12] 中的 `HostPromiseRejectionTracker(promise, "handle")`，`HostPromiseRejectionTracker` 是一个抽象方法，这表示没有规定它的具体的逻辑。大致的作用是标记一下 `promise` 已经绑定了 `rejected` 状态的处理函数。不用疑惑为什么要这么做，后面会单独重点说。

> 注意 1 HostPromiseRejectionTracker 在两种情况下被调用：
> 
> 当一个 promise 在没有任何处理程序的情况下被拒绝时，它的操作参数设置为“reject”。
> 
> 当第一次将处理程序添加到被拒绝的 Promise 中时，将调用它并将其操作参数设置为“handle”。
> 
> 引至 ——ECMAScript 规范[13](作者翻译)

#### 小结

- 当一个 Promise 被调用 then 方法时，会创建一个新的 Promise 对象 `resultPromise`

- 然后根据当前 `promise` 的不同状态执行不同的逻辑
  
  - pending状态：会将 `then` 传递的两个处理函数变成一个 `PromiseReaction` 节点插入到`promise.reactions_or_result` 头部（`PromiseReaction`是一个链表结构），这个步骤就是在搜集依赖，等待 `promise` 状态完成时再触发。
  
  - fulfilled状态：会创建一个 microtask 来调用传入的 onFulfilled 处理函数，并将 reactions_or_result 作为调用的参数（此时 `reactions_or_result` 是 `promise` 的值，也就是调用 `resolve` 时传入的参数 `value` ），并将其插入 microtask 队列。
  
  - rejected状态：与 fulfilled 状态类似，会将创建一个 microtask 来调用传入的 `onRejected` 处理函数，并将 `reactions_or_result` 作为调用的参数，如果当前 Promise 不存在处理函数（也就是 fulfilled 状态的Promsie 首次被调用 then 方法），会将其标记为已经绑定 `onRejected` 函数，然后将其 microtask 插入 microtask 队列。

- 调用 `promise.SetHasHandler()` 将 Promise 的 `has_handler` 设置为 `true`，表示其被调用的 then 方法绑定了处理函数。

- 最后返回新的 Promise 对象。

> 再来回顾一下 `reactions_or_result` 的3个值状态（空、链表、promise的值）:
> 
> 当 promise 刚刚被创建时，reactions_or_result的值的空，
> 
> 当promise的状态改变为 `fulfilled`/`rejected` 时，其值是调用对应 `resolve(value)`/`reject(value)` 函数传入的参数 `value`，也就是 `promise` 的值。
> 
> 当 promise 为 pending 状态且被调用 `then` 后，`reactions_or_result` 为一个链表，链表的每一项存储的是调用 `then` 时传入的处理函数。

### resolve

```js
new Promise((resolve, reject) => {
    setTimeout(_ => resolve('fulfilled'), 5000)
}).then(value => {
    console.log(value)
}, reason => {
    console.log('rejected')
})

// 上述代码 5s 后执行 resolve 函数，控制台打印 fulfilled。
```

#### FulfillPromise

`reslove(value)` 就是规范中的 `FulfillPromise(promise, value)` 函数，他的作用是将一个 Promise 的状态由 pending 改变为 fulfilled，并且将这个 Promise 的所有处理函数都变成 microtask 加入到 microtask 队列中等待执行。

`FulfillPromise` 的逻辑是获取 Promise 的处理函数到 `reactions`，`reactions` 的类型是 `PromiseReaction`，是个链表，忘记的同学可以回看上面的那张链表图片；设置 `promise`的 `reactions_or_result` 为 `value`，这个 `value` 就是 JavaScript 层传给 `resolve` 的参数；调用 `promise.SetStatus(PromiseState::kFulfilled)` 设置 `promise` 的状态为 `fulfilled`，最后调用 `TriggerPromiseReactions` 来将 `reactions` 中的处理函数添加到 microtask 队列。

#### TriggerPromiseReactions

TriggerPromiseReactions` 做了两件事：

- 反转 `reactions` 链表，前文有分析过 then 方法的实现，then 方法的参数最终存在链表中。最后被调用的 then 方法，它接收的参数被包装后会位于链表的头部，这不符合规范，所以需要反转

- 遍历 `reactions` 对象，调用 MorphAndEnqueuePromiseReaction 将每个元素放入 microtask 队列

#### MorphAndEnqueuePromiseReaction

MorphAndEnqueuePromiseReaction[17] 将 PromiseReaction 转为 microtask，最终插入 microtask 队列，morph 本身有转变/转化的意思，比如 Polymorphism (多态)。

MorphAndEnqueuePromiseReaction 接收 3 个参数，PromiseReaction 是前面提到的包装了 Promise 处理函数的链表对象，argument 是 resolve/reject 的参数，reactionType 表示 Promise 最终的状态，fulfilled 状态对应的值是 kPromiseReactionFulfill，rejected 状态对应的值是 kPromiseReactionReject。

MorphAndEnqueuePromiseReaction 的逻辑很简单，因为此时已经知道了 Promise 的最终状态，所以可以从 promiseReaction 对象得到 promiseReactionJobTask 对象，promiseReactionJobTask 的变量命名与 ECMAScript 规范相关描述一脉相承，其实就是传说中的 microtask。

MorphAndEnqueuePromiseReaction 的功能很简单，就是根据 Promise 的状态选取 onFulfilled 还是 onRejected 放到 microtask 队列准备执行。这里走的是 fulfilled 分支，所以选取的是 onFulfilled。

```js
const myPromise4 = new Promise((resolve, reject) => {
  setTimeout(_ => {
    resolve('my code delay 1000') 
  }, 1000)
})

myPromise4.then(result => {
  console.log('第 1 个 then')
})

myPromise4.then(result => {
  console.log('第 2 个 then')
})
// 打印顺序：
// 第 1 个 then
// 第 2 个 then
// 如果把 TriggerPromiseReactions 中链表反转的代码注释掉，打印顺序为
// 第 2 个 then
// 第 1 个 then
```

#### 小结

resolve 只会处理状态为 pending 的 Promise，会将 Promise 的 `reactions_or_result` 设置为传入的 `value`，用来作为 Promise 的值，并且会将 Promise 的状态修改为 fulfilled。

因为 promise 在使用 then 收集依赖时是将最新的依赖存放到链表头部，所以还需要先对链表进行反转，然后将其挨个放入 microtask 队列中等待执行。

> resolve 的主要工作是遍历上节调用 then 方法时收集到的依赖，放入 microtask 队列中等待执行。

### reject

#### HostPromiseRejectionTracker

与 ReslovePromise 相比，RejectPromise 中多出一个判断 Promsie 是否绑定了处理函数的判断，如果没有绑定处理函数则会先执行 `runtime::RejectPromise(promise, reason, debugEvent)`，这是其实是 ECMAScript 规范中的 `HostPromiseRejectionTracker(promise, "reject")` ，这已经是第二次提到 `HostPromiseRejectionTracker`了。

> 在 **PerformPromiseThenImpl 函数的 rejected 分支** 处有提到过一次。

在 ECMAScript 规范中 HostPromiseRejectionTracker 是一个抽象方法，他甚至没有明确的执行过程，好在规范中描述了他的作用。

HostPromiseRejectionTracker 用于跟踪 Promise 的 rejected，例如全局的 `rejectionHandled`事件就是由它实现。

> 注1 HostPromiseRejectionTracker 在两种情况下被调用：
> 
> 当一个 Promise 在没有任何处理函数的情况下被调用 reject 时，调用它并且第二个参数传递 “reject”。
> 
> 当第一次为 rejected 状态的 Promise 绑定处理函数时，调用它并且第二个参数传递 “handle”。

所以在这里，当传递 `“handle”` 就相对于为这个 Promise 对象标记为已经绑定了处理函数，当传递 `“reject”` 相对于标记这个 Promise 对象还没有处理函数。

我们先来看几段代码看看他的实际作用

当我们调用一个 Promise 的状态为 reject 且未为其绑定 onRejected 的处理函数时， JavaScript会抛出错误:

```js
const myPromise1 = new Promise((resolve, reject) => {
    reject()
})
// 报错
```

并且检测是否绑定处理函数是一个异步的过程:

```js
console.log(1);
const myPromise1 = new Promise((resolve, reject) => {
    reject()
})
console.log(2);
// 1
// 2
// 报错
```

我们可以为其绑定一个 onRejected 处理函数来解决我们报错:

```js
const myPromise1 = new Promise((resolve, reject) => {
    reject()
}) // 得到一个 rejected 状态的 Promise
myPromise1.then(undefined, console.log)
```

你一定会疑惑，Promise 是在何时检测它是否绑定了 onRejected 处理函数，如何检测的？

这就是 HostPromiseRejectionTracker 的作用，在 ECMAScript 规范中还提到，当调用 `HostPromiseRejectionTracker(promise, 'reject')` 时， 如果 promsie 不存在处理函数，则会为其设置一个处理函数。

回到上面的逻辑，当一个 Promise 的 reject 函数被调用时， 如果没有 onRejected 处理函数，则会调用 `runtime::RejectPromise` 来为其添加一个处理函数，然后后面会调用 `TriggerPromiseReactions` 将这个处理函数加入到 microtask 队列，这个处理函数时做的事情就是再次检测 Promise 是否被绑定了新的 onRejected（也就是有没有在此期间执行了 `HostPromiseRejectionTracker(promise, 'handle')` ），如果没有则抛出错误，如果有则什么也不发生。

所以在对一个 reject 状态的 Promise 调用 then 方法时需要对其调用 `runtime::PromiseRevokeReject(promise)` 来表示这个 Promise 绑定了新的 onRejected，防止错误被抛出。

所以你必须要赶在这个检测的 microtask 执行之前绑定处理函数才能防止这个错误的抛出。

```js
const myPromise1 = new Promise((resolve, reject) => {
    // 同步执行
    reject()
    // 会向 microtask 队列中插入一个检查 myPromise1 
    // 是否绑定了新的 onRejected 处理函数的 microtask
})

// macrotask
setTimeout(() => {
   // 此时 microtask 已经执行，错误已经抛出，来不及了
    myPromise1.then(undefined, console.log)
}, 0)
```

#### 小结

reject 和 resolve 的逻辑基本相同，分为 4 步：

- 设置 Promise 的 reason，也就是 reject 的参数

- 设置 Promise 的状态：rejected

- 如果 Promise 没有 onRejected 处理函数，则会为其添加一个再次检测 Promise 是否绑定 onRejected 的处理函数

- 从之前调用 then/catch 方法时收集到的依赖，也就是 promiseReaction 对象，得到一个个 microtask，最后将 microtask 插入 microtask 队列

### catch

```js
new Promise((resolve, reject) => {
    setTimeout(reject, 2000)
}).catch(_ => {
    console.log('rejected')
})
```

#### PromisePrototypeCatch

PromisePrototypeCatch 的源码确实只有就这几行，除了调用 InvokeThen 方法再无其它 。

#### InvokeThen

InvokeThen 方法有 if/else 两个分支，两个分支的逻辑差不多，本小节的 JS 示例代码走的是 if 分支。先是拿到 V8 原生的 then 方法，然后通过 `callFunctor.Call(nativeContext, then, receiver, arg1, arg2)` 调用 then 方法。then 方法之前有介绍，这里不再赘述。

既然 catch 方法底层调用了 then 方法，那么 catch 方法也有和 then 方法一样的返回值，catch 方法可以继续抛出异常，可以继续链式调用。

```js
new Promise((resolve, reject) => {
    setTimeout(reject, 2000)
}).catch(_ => {
    throw 'rejected'
}).catch(_ => {
    console.log('last catch')
})
```

上面的代码第 2 个 catch 捕获第 1 个 catch 抛出的异常，最后打印 last catch。

#### 小结

catch 方法通过底层调用 then 方法来实现 假如 obj 是一个 Promise 对象，JS 层面 obj.catch(onRejected) 等价于 obj.then(undefined, onRejected)。

### then 的链式调用与 microtask 队列

```js
Promise.resolve('123')
    .then(() => {throw new Error('456')})
    .then(_ => {
        console.log('shouldnot be here')
    })
    .catch((e) => console.log(e))
    .then((data) => console.log(data));
// 打印 Error: 456 和 undefined。
```

将 then 的链式调用写法改为啰嗦写法:

```js
const p0 = Promise.resolve('123')
const p1 = p0.then(() => {throw new Error('456')})
const p2 = p1.then(_ => {
    console.log('shouldnot be here')
})
const p3 = p2.catch((e) => console.log(e))
const p4 = p3.then((data) => console.log(data));
```

then 方法返回新的 Promise，所以 p0、p1、p2、p3 和 p4 这 5 个 Promise 互不相等。

> 当一个 Promise 处于 rejected 状态时，如果找不到 onRejected 处理函数则会将 rejected 的状态和其值往下传递，直到找到为止。（resolve也是一样），这个过程后面会介绍
> 
> catch 方法的作用就是绑定 onRejected 函数。

#### microtask 的执行

所有同步代码执行完毕，开始执行取 microtask 队列中的 microtask 执行，核心方法是 MicrotaskQueueBuiltinsAssembler::RunSingleMicrotask[23] ，由于 microtask 的类型由很多种，所以 RunSingleMicrotask 的分支有许多。这里就不列出代码了。

#### PromiseReactionJob

PromiseReactionJob 中会判断当前任务是否存在需要执行的处理函数，如果不存在则直接将上一个 Promise 的值作为参数调用 FuflfillPromiseReactionJob ，如果存在则执行这个处理函数，将执行结果当做参数调用 FuflfillPromiseReactionJob。

也就是说，只要一个 Promise 的 onFulfilled 或者 onRejected 在执行过程中只要没有抛出异常，这个 Promise 就会执行 FuflfillPromiseReactionJob 将状态修改为 fulfilled。如果抛出异常则执行 RejectPromiseReactionJob。

```js
let p0 = new Promise((resolve, reject) => {
    reject(123)
})
// p1 的状态为 reject

let p1 = p0.then(value => {
    console.log(value);
}, reason => {
    console.log(reason);
   return 2
})
// 将 reason => {console.log(reason)} 加入 microtask 队列

p1.then(_ => {
    console.log('p1');
})
// 为 p1 添加 PromiseReaction

// 取  microtask 队列 第一个执行，
// handler 为 reason => {console.log(reason)}，

// 成功执行 handler, 所以调用 FuflfillPromiseReactionJob 
// 执行 p1 的 resolve
```

> 注意：FuflfillPromiseReactionJob 做的事情很多，执行 resolve 只是其中的一个分支。

#### FuflfillPromiseReactionJob

FuflfillPromiseReactionJob 有3个分支，这里走的是第一个分支，调用 ResolvePromise[25]，这个方法很重要，他是规范中的 Promise Resolve Functions[26]，他的作用是同步当前处理函数的结果（值和状态）给其产生的promsie。promiseOrCapability。

> 上面例子中 promiseOrCapability 就是 p1, 值是 2

#### ResolvePromise

- **一个是调用 FulfillPromise，这个在resolve的时候已经介绍过了，作用是修改 promise 的状态为 fulfilled 并为其设置值，然后将 promise 的处理函数推到微任务队列。**
  
  ```js
  let p0 = Promise.resolve()
  let p1 = p0.then(() => {
    return 1;
  })
  
  p1.then(console.log)
  
  // p0 then 中 onFulfilled 回调进入队列
  // PromiseReactionJob 中调用 p0 的 onFulfilled ，得到结果为1
  // 调用 FuflfillPromiseReactionJob ，然后调用 ResolvePromise
  // ResolvePromise 做如下操作
  // 将 p1 变成 fulfilled, 并将 p1 的处理函数 console.log 加到队列，参数为 1
  // p1 的 onFulfilled 出队列执行，输出 1
  ```

- **当 resolution 的值是一个 Promise 对象或者是一个包含 then 方法的对象时。会调用 NewPromiseResolveThenableJobTask 生成一个 microtask，然后将其加入 microtask 队列中。**
  
  ```js
  let p0 = Promise.resolve()
  
  // 两种特殊情况
  let p1 = p0.then(() => {
      // 返回值是 Promise 对象
      return Promise.resolve(1);
  })
  
  let p2 = p0.then(() => {
      // 返回值包含 then 方法
      return {
          then(resolve, reject){
              resolve(1)
          }
      };
  })
  
  p1.then(console.log)
  
  // 1
  ```

#### NewPromiseResolveThenableJobTask

NewPromiseResolveThenableJobTask 的目的是调用 resolution 的 then 方法，在回调函数中同步状态给 promise。这可能不是很好理解，我把他转化为js来大致就是这样的。

```js
microtask(() => {
    resolution.then((value) => {
        ReslovePromise(promise, value) 
    })
})
```

这个任务中会调用 resolution.then ，然后同步到 promsie。但是这个整体的过程需要加入 microtask 队列中等待运行，当这个任务运行时，如果 resolution 也是一个 Promise 的话，则 `(value) => {ReslovePromise(promise, value) }` 又会被作为一个 microtask 加入 microtask 队列中等待运行。

你可以会疑惑为什么要这样做？为什么不同步调用 `resolution.then((value) => {ReslovePromise(promise, value) })`，而是把他封装为一个 microtask 呢？我一开始也感到疑惑，好在规范中给出了一个原因。

> 注意: 此作业使用提供的 thenable 及其 then 方法来解决给定的 Promise。此过程必须作为作业进行，以确保在对任何周围代码的评估完成后对 then 方法进行评估。
> 
> 引至 ECMAScript NewPromiseResolveThenableJobTask 规范[27](作者翻译)

> **什么是 thenable：**
> 
> Javascript 为了识别 Promise 产生的一个概念，简单来说就是所有包含 then 方法的对象都是 thenable。

『以确保在对任何周围代码的评估完成后对 then 方法进行评估』指的是什么呢？我唯一能想到的就是下面这种情况。

```js
const p1 = new Promise((resolve, reject) => {
    const p2 = Promise.resolve().then(() => {
        resolve({
            then: (resolve, reject) => resolve(1)
        });
        const p3 = Promise.resolve().then(() => console.log(2));
    });
}).then(v => console.log(v));
// 2 1
```

上面 p2 的 onFulfilled回调 会先进入 microtask 队列，等待其执行时 调用 p1 的 resolve，但是参数是一个包含 then 方法的对象。这时 p1 不会立即改变为 fulfilled，而是创建一个 microtask 来执行这个then方法，然后将 p2的 onFulfilled 加入 microtask 队列。这时 microtask 队列中有两个 microtask，一个是执行 resolve 返回值中的 then函数，另一个则是 p3的 onFulfilled 函数。

然后取出第一个 microtask 执行（取出后 microtask 队列中只剩下 p3的 onFulfilled），执行后 p1 的状态变为 fulfilled，然后 p1 的 onFulfilled 进入队列。后面可想而知是相继输出 2和1（因为 p1 的 onFulfilled 函数在 p3 的 onFulfilled 函数之后进入 microtask 队列）。

如果没有将 NewPromiseResolveThenableJobTask 作为一个 microtask。也就变成了 p2.then 中的回调执行时同步触发 resolve 参数中的 then 方法，fulfilled 的状态会立即同步到 p1,这时 p1 的 onFulfilled 就会先进入 microtask，导致结果变为 12。这样的执行结果可以会让JavaScript开发者感到疑惑。

所以 ECMAScript 将其作为一个异步任务来执行。

> 似乎 返回 Promsie 对象会产生两个 microtask 似乎会更让人感到疑惑。

#### RejectPromiseReactionJob

PromiseReactionJob 中如果处理函数 handler 执行时抛出异常则会执行 RejectPromiseReactionJob，也就是下面这种情况:

```js
let p0 = Promise.resolve()
let p1 = p0.then(() => {
    throw 'error'; // handler 执行时出错
})
```

RejectPromiseReactionJob 与 FuflfillPromiseReactionJob 是类似的，就是调用 RejectPromise 来调用 Promsie 的 reject 方法，这个在上面 reject 的地方介绍过了。

##### PromiseReactionJob的handler == Undefined分支

PromiseReactionJob 中还有一个 handler == Undefined 的分支也很重要，当一个 task 中的 handler 为 undefined时会进入这个分支。

进入分支后会直接获取上一个 Promise 对象的 value 和 状态 同步到当前 promise 来，我们来通过一段js了解他。

```js
let p0 = new Promise((resolve, reject) => {
    reject(123)
})
// p0 的状态为 rejected

let p1 = p0.then(_ => {console.log('p0 onFulfilled')})
// p0 的 onRejected 作为 handler 进入 microtask 队列
// 但是因为 then 没有传递第二个参数
// 所以 onRejected 是 undefined，那么 handler 也是 undefined

let p2 = p1.then(_ => {console.log('p1 onFulfilled')})
/*
为p1绑定 
PromiseReaction{
  onFulfilled:_ => {console.log('p1 onFulfilled')}, 
  onRejected:undefined
}
*/

let p3 = p2.then(_ => {console.log('p2 onFulfilled')}, _ => {
    console.log('p2 onRejected')
})
/*
为p2绑定 
PromiseReaction{
  onFulfilled:_ => {console.log('p2 onFulfilled')}, 
  onRejected:_ => {console.log('p2 onRejected')
}
*/

let p4 = p3.then(_ => {console.log('p3 onFulfilled')}, _ => {
    console.log('p3 onRejected')
})
/*
为p3绑定 
PromiseReaction{
  onFulfilled:_ => {console.log('p3 onFulfilled')}, 
  onRejected:_ => {console.log('p3 onRejected')
}
*/

//p2 onRejected
//p3 onFulfilled
```

同步代码执行完毕后（执行过程大致如注释），开启取 microtask 执行，此时 microtask 队列中只有一个 handler 为 undefined 的任务。进入 PromiseReactionJob 的 handler == Undefined 分支。

因为此时 p0 状态为 rejected，所以执行 `RejectPromiseReactionJob(context, promiseOrCapability, argument, reactionType)`，其中 promiseOrCapability 就是p1, argument 就是 p0 的值 123，reactionType 为 rejected。

执行后 p0 的状态也变为 reactionType 也就是 rejected，p1 的值为 argument（相当于吧p0的状态和值都转移到了p1）。

然后执行 p1 的 reject 函数（`FulfillPromise(p1, 123)`），会把 p1 绑定的 PromiseReaction 链表中的 onRejected（还是undefined） 当做 handler 进入microtask 队列（因为 p1 的状态是 rejected，所以是onRejected）。

同样还是取 microtask 任务执行，handler 还是 undefined，后面就和上面一样，把状态 rejected 和值 123 继续同步给 p2，..........

再次取 microtask 执行，因为 p2 绑定了 onRejected 函数，所以 handler 不是 undefined，则不走 handler == Undefined 分支，另一个分支的逻辑刚刚已经描述过了。大概就是执行 onRejected(123)，然后将其结果设置到 p3 的value，p3 变为 fulfilled 状态。

> 输出 p2 onRejected

因为 onRejected(123) 的返回值是 undefined，所以 p3 变为 fulfilled 状态，且值为 undefined。

后面还是一样的，但是 handler 就是 onFulfilled 了，因为 p3 的状态是 fulfilled嘛，这里就相当于 `onFulfilled(undefined)`(因为 p3 的值的 undefined)。

> 输出 p3 onFulfilled

而后 p4 的状态也变成了 fulfilled，值也是 undefined，因为 p3 的 onFulfilled 返回值是 undefined

然后 p4 的 onFulfilled 变成 handler 队列，因为 p4 没有调用 then 绑定过 onFulfilled 处理函数。但是因为没有调用 then 方法，所以也没有产生新的 Promsie 对象，这次在执行 FuflfillPromiseReactionJob 方法的时候进入 promiseOrCapability 为 Undefined 分支就结束了

至此所有相关的任务全部执行完成。

如果上面你看懂了，那么下面这段代码我想你也应该能知道结果：

```js
Promise.resolve('123')
    .then(() => {throw new Error('456')})
    .then(_ => {
        console.log('shouldnot be here')
    })
    .catch((e) => console.log(e))
    .then((data) => console.log(data));

// Error: 456
// undefined
```

> catch(onRejected) 的本质是 then(undefined, onRejected)

这就是 Promise 的 rejected 传递机制，不断向下传递直到遇见 onRejected 处理函数为止。

## Promise 的几个高难度题目

### 题目1

```js
Promise.resolve().then(() => {
    console.log(0);
    return Promise.resolve(4);
}).then((res) => {
    console.log(res)
})

Promise.resolve().then(() => {
    console.log(1);
}).then(() => {
    console.log(2);
}).then(() => {
    console.log(3);
}).then(() => {
    console.log(5);
}).then(() => {
    console.log(6);
})

// 0 1 2 3 4 5 6
```

> 主要考察 当 Promise 的值是 promsie 对象时会如何处理，在本文 的 **then 的链式调用与 microtask 队列**> **ResolvePromise** 目录末尾处开始介绍
> 
> 关键字：**thenable**、**NewPromiseResolveThenableJobTask**

#### 题解

为了方便描述，我们将上面的代码转化为下面这样：

```js
let p1 = Promise.resolve()
let p2 = p1.then(() => {
    console.log(0);
    let p3 = Promise.resolve(4)
    return p3;
})
let p4 = p2.then((res) => {
    console.log(res)
})

let p5 = Promise.resolve()
let p6 = p5.then(() => {
    console.log(1);
})
let p7 = p6.then(() => {
    console.log(2);
})
let p8 = p7.then(() => {
    console.log(3);
})
let p9 = p8.then(() => {
    console.log(5);
})
let p10 = p9.then(() => {
    console.log(6);
})
```

先执行所有的同步代码，执行过程如下面的注释:

```js
let p1 = Promise.resolve()
// 1. p1 的状态为 fulfilled

let p2 = p1.then(() => {
    console.log(0);
    let p3 = Promise.resolve(4)
    return p3;
})
// 2. 因为 p1 的状态已经是 fulfilled，所以调用 then 
// 后立即将 onFulfilled 放入 microtask 队列
// 此时 microtask 只有p1的 onFulfilled： [p1.onFulfilled]

let p4 = p2.then((res) => {
    console.log(res)
})
// 3. p2的状态还是 pending，所以调用 then 后是为 p2 收集依赖，
// 此时 p2 的 reactions 如下
/*{
    onFulfilled: (res) => {console.log(res)},
    onRejected: undefined
}*/


let p5 = Promise.resolve()
// 4. p5 的状态为 fulfilled

let p6 = p5.then(() => {
    console.log(1);
})
// 5. 同第2步，将 onFulfilled 加入 microtask 队列
// 此时 microtask 是： [p1.onFulfilled, p5.onFulfilled]

let p7 = p6.then(() => {
    console.log(2);
})
// 6. 同第3步，是给 p6 添加 reactions

let p8 = p7.then(() => {
    console.log(3);
})
// 7. 同上，是给 p7 添加 reactions

let p9 = p8.then(() => {
    console.log(5);
})
// 8. 同上，是给 p8 添加 reactions

let p10 = p9.then(() => {
    console.log(6);
})
// 9. 同上，是给 p9 添加 reactions
```

10.当同步代码执行完成后，microtask 队列只有:

```js
[p1.onFulfilled, p5.onFulfilled]
```

11.然后取出 p1.onFulfilled 来执行，此时输出 `0`，但是发现 p1.onFulfilled 返回值的 p3 是一个 Promise 对象。所以会执行 ResolvePromise 的 Enqueue 代码块，里面会调用 NewPromiseResolveThenableJobTask 产生一个微任务，这个微任务的要做的事情上面已经介绍过，大致就是下面这样:

```js
let promiseResolveThenableJobTask = () => {
    p3.then((value) => { 
        ReslovePromise(p2, value) 
    })
}
```

然后将其加入 microtask 队列， 这时 microtask 队列就变成了 :

```js
[p5.onFulfilled, promiseResolveThenableJobTask]
```

12.继续取出 p5.onFulfilled 执行，此时输出 `1`，因为 p5.onFulfilled 返回值 undefined，所以就将 undefined 作为 p6 的值，然后将 p6 的状态变为 fulfilled。

因为 p6 的状态被改变，所以它的 reactions 也会加入 microtask 队列，这时 microtask 队列就变成这样：

```js
[promiseResolveThenableJobTask，p6.onFulfilled]
```

13.同样是取 promiseResolveThenableJobTask 执行，因为promiseResolveThenableJobTask 的内容是下面这样：

```js
let promiseResolveThenableJobTask = () => {
    p3.then((value) => { 
        ReslovePromise(p2, value) // ReslovePromise 的作用上面有介绍
    })
}
```

所以执行 promiseResolveThenableJobTask 时就相当于执行了 `p3.then((value) => {ReslovePromise(p2, value)})`

因为 p3 的状态是 fulfilled ，所以会将其 onFulfilled 加入 microtask 队列（参数就是 p2 的值 undefined），这时 microtask 队列就变成这样：

```js
[p6.onFulfilled，p3.onFulfilled]
```

14.同样是取 p6.onFulfilled 执行，然后输出 `2` 并将其返回值 undefined 设置为 p7 的值，并将 p7 变为 fulfilled 状态，所以 p7 的 reactions 也会加入 microtask 队列，这时 microtask 队列就变成这样：

```js
[p3.onFulfilled，p7.onFulfilled]
```

15.p3.onFulfilled 出队执行，p3.onFulfilled 是 `(value) => {ReslovePromise(p2, value)}`, 参数 value 是 undefined，所以此时就执行 `ReslovePromise(p2, undefined)`,这就相当于调用了 p2 的 resolve。

所以此时 p2 的 值变为 undefined， 状态为变 fulfilled，然后将其 reactions 挨个加入 microtask 队列，这时 microtask 队列就变成这样：

```js
[p7.onFulfilled，p2.onFulfilled]
```

16.p7.onFulfilled 出队列执行，输出 `3`，p8 状态变为 fulfille，值变为 undefined，然后 p8.onFulfilled 加入队列:

```js
[p2.onFulfilled，p8.onFulfilled]
```

17.p2.onFulfilled 出队列执行，输出 `4`，因为 p2 没有被在此调用 then 方法，所以就没有产生下一个 Promise 对象，所以也就没有后序了。

```js
[p8.onFulfilled]
```

18.后面就不用说了。

### 题目2

```js
Promise.resolve().then(() => {
    console.log(0);
    return {then(resolve){resolve(4)}};
}).then((res) => {
    console.log(res)
})

Promise.resolve().then(() => {
    console.log(1);
}).then(() => {
    console.log(2);
}).then(() => {
    console.log(3);
}).then(() => {
    console.log(5);
}).then(() => {
    console.log(6);
})

// 0 1 2 3 4 5 6
```

> 与上题知识点一致，考察的是 Promise 的值是一个包含 then 方法的对象时发生的逻辑
> 
> 关键字：**thenable**、**NewPromiseResolveThenableJobTask**

### 题目3

```js
const p1 = new Promise((resolve, reject) => {
    reject(0)
})
console.log(1);
setTimeout(() => {
    p1.then(undefined, console.log)
}, 0)
console.log(2);
// 1
// 2
// 输出报错 UnhandledPromiseRejection: This error originated either


const p1 = new Promise((resolve, reject) => {
    reject(0)
})
console.log(1);
p1.then(undefined, console.log)
console.log(2);
// 1
// 2
// 0
```

> 为什么第一种方式会报错？
> 
> 考察的是规范中的 HostPromiseRejectionTracker，当一个没有绑定处理函数的 Promsie 被调用 reject 则会创建一个 微任务来再次检测这个 Promise 是否存在处理函数，如果此时还不存在则输出报错，setTimeout回调执行在微任务之后。
> 
> 本文 **reject** > **HostPromiseRejectionTracker** 目录处有详细介绍。

### 题目四

```js
async function async1() { 
    console.log("async1 start"); 
    await async2(); 
    console.log("async1 end"); 
} 
async function async2() { 
    console.log("async2"); 
    return Promise.resolve().then(() => { 
        console.log("async2-inner"); 
    }); 
} 

console.log("script start"); 
setTimeout(function () { 
    console.log("settimeout"); 
}); 

async1(); 
new Promise(function (resolve) { 
    console.log("promise1"); 
    resolve(); 
}) 
.then(function () { 
    console.log("promise2"); 
}) 
.then(function () { 
    console.log("promise3"); 
}) 
.then(function () { 
    console.log("promise4"); 
}); 
console.log("script end");

// script start
// async1 start
// async2
// promise1
// script end
// async2-inner
// promise2
// promise3
// async1 end
// promise4
// settimeout
```

#### 题解

这个问题，这里面涉及 async 中使用 await 会产生多个Promise链路的问题以及 resolve 值为 Promise 对象的问题，重点看 async2，我把他转化为这样（setTimeout对于本题悬念不大，就删除了）。

```js
async function async1() {
    console.log("async1 start");
    let a2 = async2();
    await a2
    console.log("async1 end");
}
async function async2() {
    console.log("async2");
    let p1 = Promise.resolve()
    let p2 = p1.then(() => {
        console.log("async2-inner");
    })
    return p2;
}

let a1 = async1();

let p3 = new Promise(function (resolve) {
    console.log("promise1");
    resolve();
})

let p4 = p3.then(function () {
    console.log("promise2");
})
// 为 p3 添加 reactions
let p5 = p4.then(function () {
    console.log("promise3");
})
// 为 p4 添加 reactions
let p6 = p5.then(function () {
    console.log("promise4");
});
// 为 p5 添加 reactions
console.log("script end");
```

想必你也发现了 async 函数中 await 语句之前的代码都是同步执行的（相当于Promsie的executor）。

1.先调用 async1，输出 `async1 start`， 同步执行到 `async2()` 的位置，然后去同步执行 async2。

然后输出 `async2`，里面创建一个 fulfilled 状态的 p1，然后为 p1 绑定 then，因为 p1 是 fulfilled 状态，所以 p1.onFulfilled 会立即进入 microtask 队列。这时 microtask 队列就变成这样：

```js
[p1.onFulfilled]
```

然后返回 p2（resolve(p2)），又因为 p2 是一个 Promise 对象，所以创建一个如下的 promiseResolveThenableJobTask：

```js
let promiseResolveThenableJobTask = () => {
    p2.then((value) => { 
        ReslovePromise(a2, value) // ReslovePromise 的作用上面有介绍
    })
}
```

```js
[p1.onFulfilled，promiseResolveThenableJobTask]
```

2.然后执行 await a2，这里很关键，await 会等待一个 Promsie 进入 fulfilled 状态后才执行后面的代码，其实就相当于下面这样:

```js
a2.then(_ => {
 // 这里是 async1 中 await 后的代码
})
// 为 a2 绑定了 reactions，这里的 onFulfill 暂时就叫 『async1后半段』吧
// 其实这里面做的事情很多，我后面可能会单独讲解 async/await 的原理
```

注意此时 a2 还不是 fulfilled 状态，因为他需要等待 promiseResolveThenableJobTask 执行时来调用他的 resolve 才会变成 fulfilled。

3.这时 async1() 触发的同步代码才执行完毕，继续执行后面的 new Promise:

同步执行这段代码:

```js
function (resolve) {
    console.log("promise1");
    resolve();
}
```

输出 `promise1`, 执行 resolve， 然后 p3 状态变为 fulfilled，p3.onFulfilled 进入队列，后面的 then 都是给对应的 promsie 绑定 reactions 这个就不说了，最后输出 `script end`

到此时所有同步代码执行完成， microtask 队列是这样的：

```js
[p1.onFulfilled，promiseResolveThenableJobTask，p3.onFulfilled]
```

4.至此所有同步代码执行完成，开始取 microtask 执行，首先是 p1.onFulfilleed ，执行输出 `async2-inner`, 然后 将其返回值 undefined 作为 p2 的值，并将 p2 变成 fulfilled 状态。因为 p2 此时没有 reactions （也就是没有被调用过then方法），所以不会发生什么事情:

```js
[promiseResolveThenableJobTask，p3.onFulfilled]
```

5.promiseResolveThenableJobTask 出队列执行， 其内容如下，上面已经说过了:

```js
let promiseResolveThenableJobTask = () => {
    p2.then((value) => { 
        ReslovePromise(a2, value) // ReslovePromise 的作用上面有介绍
    })
}
```

执行是执行 p2 的 then 方法为其绑定 onFulfilled 处理函数，但是 p2 已经是 fulfilled 状态，所以会直接将 p2.onFulfilled 加入 microtask 队列。

```js
[p3.onFulfilled, p2.onFulfilled]
```

6.p3.onFulfilled 出队执行，输出 `promise2` ，将 p4 的状态变为 fulfilled，p4的值为其1返回值也就是undefined，然后 p4 的 onFulfilled 也会加入 microtask 队列。

```js
[p2.onFulfilled, p4.onFulfilled]
```

7.p2.onFulfilled 出队列执行， p2.onFulfilled 的内容如下，这个上面说过:

```js
(value) => { 
    ReslovePromise(a2, value) // 也就是 a2 的 resolve(value)
}
```

所以执行 ReslovePromise 后，a2 会变成 fulfilled 状态，a2.onFulfilled 也就是 『async1后半段』 也理所当然的进入 microtask 队列>

```js
[p4.onFulfilled, async1后半段]
```

8.后面的结果就没有什么难点了，p4.onFulfilled 出队执行，输出 `promise3`，然后 p5.onFulfilled 入队:

```js
[async1后半段, p5.onFulfilled] 
```

9.async1后半段 出队执行，输出 `async1 end`， 然后 a1 状态变为 fulfilled，但是没有绑定任何处理函数，所以 a1 就没有后续了:

```js
[p5.onFulfilled]
```

10.p5.onFulfilled 出队执行输出 `promise4`，同理 p6 没有绑定任何处理函数，至此所有代码执行完成。

## 资料

[Promises/A+ 规范](https://juejin.cn/post/6844903767654023182)

https://mp.weixin.qq.com/s/JBQ9WtNMYzSFVWqlmnUBpA
