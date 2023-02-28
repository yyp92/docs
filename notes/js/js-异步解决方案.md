# JavaScript异步解决方案

## 回调

回调简单地理解为一个函数作为参数传递给另一个函数，回调是早期最常用的异步解决方案之一。

回调不一定是异步的，也不直接相关。

```js
function f1(cb) {
  setTimeout(() => {
    cb && cb();
  }, 2000);
}

f1(() => {
  console.log("1");
});
```

如上，我们在函数f1中使用setTimeout模拟一个耗时2s的任务，在耗时任务结束时抛出回调，这样我们就可以调用它，让回调函数在耗时结束时执行函数 f1 中的任务。

这样，我们就把同步操作变成了异步操作。f1不会阻塞程序，相当于先执行程序的主要逻辑，推迟执行耗时操作。

**回调的优点和缺点**

**优点：** 单，容易理解。

**缺点：** 码不优雅，可读性差，不易维护，耦合度高，层层嵌套造成回调地狱。

## 事件监听（发布订阅模式）

发布-订阅模式定义了对象之间一对多的依赖关系，这样当一个对象的状态发生变化时，所有依赖它的对象都会得到通知。

我们都使用过发布-订阅模式，例如，如果我们将事件函数绑定到 DOM 节点。

```js
document.body.addEventListener('click', function () {
  console.log('click');
})
```

但这只是发布-订阅模式最简单的使用，在很多场景下我们往往会使用一些自定义事件来满足我们的需求。

有很多方法可以实现发布-订阅模式，所以这里有一个使用类的简单实现。

```js
class Emitter {
  constructor() {
    // _listener array, key is the custom event name, value is the execution callback array - as there may be more than one
    this._listener = []
  }

  // 订阅 监听事件
  on(type, fn) {
    // Determine if the event exists in the _listener array.
    // Exists to push the callback to the value array corresponding to the event name, does not exist to add directly
    this._listener[type] 
      ? this._listener[type].push(fn) 
     : (this._listener[type] = [fn])
  }

  // Publish Trigger Event
  trigger(type, ...rest) {
    // Determine if the trigger event exists
    if (!this._listener[type]) return
    // Iterate through the array of callbacks executing the event and pass the parameters
    this._listener[type].forEach(callback => callback(...rest))
  }
}
```

如上所示，我们创建了一个 Emitter 类，并在和触发器上添加了两个原型方法，使用如下。

```js
// Create an emitter instance
const emitter = new Emitter()

emitter.on("done", function(arg1, arg2) {
  console.log(arg1, arg2)
})

emitter.on("done", function(arg1, arg2) {
  console.log(arg2, arg1)
})

function fn1() {
  console.log('I am the main program')
  setTimeout(() => {
    emitter.trigger("done", "Asynchronous parameter I", "Asynchronous parameter II")
  }, 1000)
}

fn1()
```

我们先创建一个emitter实例，然后注册事件，然后触发事件，这样也解决了异步问题。

**事件监听的优点和缺点**

**优点：** 符合模块化思想，我们在编写自己的监听器的时候可以做很多优化，从而更好的监听程序的运行。

**缺点：** 程序变成了事件驱动，或多或少影响了流程，而且每次使用都要注册事件监听器然后触发，比较麻烦，代码也不是很优雅。

## Promise

ES6 标准化并引入了 Promise 对象，这是一种异步编程的解决方案。

简单的说，就是用同步的方式写异步代码，可以用来解决回调地狱问题。

Promise对象的状态一旦改变，就不会再改变，只有两种可能的改变。

- 由待定改为已解决。

- 由Pending改为Rejected。

我们使用 setTimeout 来模拟异步操作。

```js
function analogAsync(n) {
  return new Promise((resolve) => {
    setTimeout(() => resolve(n + 500), n);
  });
}

function fn1(n) {
  console.log(`step1 with ${n}`);
  return analogAsync(n);
}

function fn2(n) {
  console.log(`step2 with ${n}`);
  return analogAsync(n);
}

function fn3(n) {
  console.log(`step3 with ${n}`);
  return analogAsync(n);
}
```

使用 Promise 来实现。

```js
function fn() {
  let time1 = 0;
  fn1(time1)
    .then((time2) => fn2(time2))
    .then((time3) => fn3(time3))
    .then((res) => {
      console.log(`result is ${res}`);
    });
}

fn();
```

**Promise 优点和缺点**

**优点：** Promise以同步的方式编写异步代码，避免了回调函数层层嵌套，可读性更强。链式操作，可以在then中继续写Promise对象并return，然后继续调用then进行回调操作。

**缺点：** Promise对象一旦创建就会立即执行，不能中途取消。如果没有设置回调函数，Promise 会在内部抛出错误，不会向外流。

## Generator

Generator其实就是一个函数，只不过是一个特殊的函数。Generator 的特别之处在于它可以中途停止。

```js
function *generatorFn() {
  console.log("a");
  yield '1';
  console.log("b");
  yield '2'; 
  console.log("c");
  return '3';
}

let it = generatorFn();
it.next();
it.next();
it.next();
it.next();
```

上面的示例是一个具有以下特征的生成器函数。与普通函数不同，Generator 函数在函数之后和函数名称之前有一个 *，该函数有一个内部 yield 字段，函数调用后的返回值使用next方法。

**Generator的优点和缺点**

**优点：** 优雅的流程控制方法，允许函数被中断地执行。

**缺点：** Generator函数的执行必须依赖executor，对于只做异步处理还是不太方便。

## async/await

ES2017标准引入了async函数，使得异步操作更加方便。async是异步的意思，await是async wait的简写，也就是异步等待。async/await 被许多人认为是 js 中异步操作的终极和最优雅的解决方案。

**异步在做什么？**

async 函数返回一个 Promise 对象。如果直接在 async 函数中返回一个直接量，async 会通过 Promise.resolve() 将直接量包装在一个 Promise 对象中。

await 是什么？

await 是一个表达式，其计算结果为 Promise 对象或其他值（换句话说，没有特殊限定，无论如何）。

如果 await 后面没有跟 Promise 对象，则直接执行。

如果 await 后面跟着一个 Promise 对象，它会阻塞后面的代码，Promise 对象解析，然后获取 resolve 的值作为 await 表达式的结果。

await 只能在异步函数中使用

上面使用setTimeout来模拟异步操作，我们使用async/await来实现。

```js
async function fn() {
  let time1 = 0;
  let time2 = await fn1(time1);
  let time3 = await fn2(time2);
  let res = await fn3(time3);
  console.log(`result is ${res}`);
}

fn();
```

输出结果和上面的 Promise 实现是一样的，但是 async/await 的代码结构看起来更清晰，几乎和同步写法一样优雅。

**async/await的优点和缺点**

**优点：** 内置执行器，语义更好，适用性更广。

**缺点：** 误用 await 可能会导致性能问题，因为 await 会阻塞代码。
