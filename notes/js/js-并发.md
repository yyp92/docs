# 并发实践

## 什么是并发

因为js是单线程的，所以前端的并发指的是在极短时间内发送多个数据请求，比如说循环中发送ajax。

**举一个简单的例子：**

下面一段代码是常规的`mount`阶段执行的请求：

```js
useEffect(async () => {
    console.time();
    await TaskBizService.querySpyTaskSummary();
    await TaskBizService.querySpyTask();
    console.timeEnd();

    // time: 300ms
}, [])
```

**更换成这样：**

```js
useEffect(() => {
    console.time();
    Promise.all([
        TaskBizService.querySpyTaskSummary(),
        TaskBizService.querySpyTask(),
    ]).then((res) => {
        console.timeEnd();
    });

    // time: 120ms
}, [])
```

可以看出有很大的性能优化空间和区别，如果在页面渲染时，多个请求没有相互数据依赖性（依赖请求），直接采用并行请求会加快页面中数据展现的时间，这两个demo同时也涉及到事件循环的一些知识。

因此也可以在页面中找到一些可优化的请求，转换为并行，对于首屏渲染的优化是有很大帮助的。

## Promise.all

可以采用`Promise.all`处理并发， 当所有`promise`全部成功时, 会走`.then`，并且可以拿到所有`promise`中传进`resolve`中的值。

**看一下这段代码：**

```js
let p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
   resolve('request1 end')
  }, 1000);
})

let p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
   resolve('request2 end')
  }, 3000);
})

console.time(); // 开始计时
Promise.all([p1, p2])
.then(result => {
  console.timeEnd(); // default: 3.2s
  console.log(result); // (2) ['request1 end', 'request2 end']  
})
```

如果`Promise.all`中有实例失败，整个并发会全部挂掉。

**就像这段代码：**

```js
let p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
   resolve('request1 end')
  }, 1000);
})

let p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
   reject('request2 fail')
  }, 3000);
})

console.time(); // 开始计时
Promise.all([p1, p2])
.then(result => {
  // 不会走到这一步
  console.timeEnd(); // default: 3.2s
  console.log(result); 
})
```

### 总结

- `Promise.all`在处理并发时，如果有一个`promise`失败，就不会走.then， 但是如果只是需要在所有异步执行完成之后去执行其它副作用代码，可以把代码写在`.finally`中实现。

- 但是如果需要在有异步失败之后，获取到`promise`实例通过`resolve`传过来的值，则不行。

## Promise.allSettled

在`Promise.all`中实现不了的功能, 可以使用`Promise.allSettled`来实现， 在`Promise.allSettled`中，当其中有一个`promise`执行失败时，会继续走`.then`, 并且可以同时拿到传给`resolve、reject`的值，可以通过回调值来判断接口的请求结果来做二次处理。

代码改造起来也非常简单，如下：

```js
let p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
   resolve('request1 end')
  }, 1000);
})

let p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
   resolve('request2 end')
  }, 3000);
})

console.time(); // 开始计时
Promise.allSettled([p1, p2])
.then(result => {
  console.timeEnd(); // default: 3.2s
  console.log(result); // (2) ['request1 end', 'request2 end']  
})
```

当请求结果全部`resolve`的情况，`Promise.allSettled`和`Promise.all`没有太大区别，只是对于`catch`的情况下做了一次弥补。

同时`Promise.allSettled`的兼容性并没有`Promise.all`来得好。

### 总结

- `Promise.allSettled`在处理并发时，不怕异步执行失败，继续会走`.then`，完美解决`Promise.all`在并发有失败情况下，拿不到值的情况

- 唯一的缺点，比起`Promise.all`兼容性差一点，多了两个不支持的浏览器， 安卓端火狐浏览器（Firefox for Android）及 Samsung Internet 浏览器。

## async/await

`async/await`能处理并发吗？答案是可以的，但是代码会看起来臃肿不易读一下，先看一下常规的`async/await`的异步处理方案吧。

**代码如下：**

```js
let getData1 = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('request1 end')
    }, 2000);
  })
};

let getData2 = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('request2 end')
    }, 3000);
  })
}
let syncFn = async () => {
    console.time(); // 开始计时

    const data1 = await getData1();
    const data2 = await getData2();

    console.timeEnd(); // default: 5.8s
    console.log(data1, data2); // request1 end, request2 end
}

syncFn();
```

从耗时可以看到，`async/await`造成了异步阻塞，实际走的是串行（依赖）请求，也是普通项目中最常见的处理方式，接下来我们改造一下`async/await`并行请求的方案。

```js
let getData1 = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('request1 end')
    }, 2000);
  })
};

let getData2 = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('request2 end')
    }, 3000);
  })
} 

let syncFn = async () => {
    console.time(); // 开始计时

    const p1 = getData1();
    const p2 = getData2();

    const data1 = await p1;
    const data2 = await p2;

    console.timeEnd(); // default: 3.8s
    console.log(data1, data2); // request1 end, request2 end
}

syncFn();
```

## 总结

- 如果是在安装了`axios`或者其它请求库，在这些库本身提供支持并发的api情况下，如`axios.all、axios.spread`，建议使用他们提供的这些api，因为作为一个百万级下载量的库，提供的这些api考虑到的边界条件、兼容性肯定是比其它原生方法好用的。

- 在没有这些库的情况下，推荐使用`Promise.allSettled`，如果你喜欢用 `async、await` 也可以，结果没有区别，看个人喜好了。但是在我看来在处理并发的情况下 `async、await`的写法感觉就不那么简洁了。
