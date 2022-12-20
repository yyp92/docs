# js 手写

## 基础手写

### 全排列（力扣原题）

要求以数组的形式返回字符串参数的所有排列组合。

注意：

1. 字符串参数中的字符无重复且仅包含小写字母

2. 返回的排列组合数组不区分顺序

```js
const _permute = string => {
  const result = []
  const map = new Map() 

  const dfs = (path) => {
    if (path.length === string.length) {
      result.push(path)
      return
    } 

    for (let i = 0; i < string.length; i++) {
      if (map.get(string[i])) continue
      map.set(string[i], true)
      path += string[i]
      dfs(path)
      path = path.substring(0, path.length - 1)
      map.set(string[i], false)
    }
  } 

  dfs('')
  return result
}
console.log(_permute('abc')) // [ 'abc', 'acb', 'bac', 'bca', 'cab', 'cba' ]
```

### instanceof

- 如果 target 为基本数据类型直接返回 false

- 判断 Fn.prototype 是否在 target 的隐式原型链上

```js
const _instanceof = (target, Fn) => {
  if ((typeof target !== 'object' && typeof target !== 'function') || target === null) {
    return false
  }

  let proto = target.__proto__
  while (true) {
    if (proto === null) return false
    if (proto === Fn.prototype) return true
    proto = proto.__proto__
  }
} 

function A() {}
const a = new A()
console.log(_instanceof(a, A)) // true
console.log(_instanceof(1, A)) // false
```

### Array.prototype.map

- map 中的 exc 接受三个参数，分别是: 元素值、元素下标和原数组

- map 返回的是一个新的数组，地址不一样

```js
// 这里不能直接使用箭头函数，否则无法访问到 this
Array.prototype._map = function (exc) {
  const result = []
  this.forEach((item, index, arr) => {
    result[index] = exc(item, index, arr)
  })
  return result
} 

const a = new Array(2).fill(2)
console.log(a.map((item, index, arr) => item * index + 1)) // [1,3]
console.log(a._map((item, index, arr) => item * index + 1))// [1,3]
```

### Array.prototype.filter

- filter 中的 exc 接受三个参数，与map一致，主要实现的是数组的过滤功能，会根据 exc 函数的返回值来判断是否“留下”该值。

- filter 返回的是一个新的数组，地址不一致。

```js
Array.prototype._filter = function (exc) {
  const result = []
  this.forEach((item, index, arr) => {
    if (exc(item, index, arr)) {
      result.push(item)
    }
  })
  return result
} 

const b = [1, 3, 4, 5, 6, 2, 5, 1, 8, 20]

console.log(b._filter(item => item % 2 === 0)) // [ 4, 6, 2, 8, 20 ]
```

### Array.prototype.reduce

- reduce 接受两个参数，第一个为 exc 函数，第二个为初始值，如果不传默认为 0

- reduce 最终会返回一个值，当然不一定是 Number 类型的，取决于你是怎么计算的，每次的计算结果都会作为下次 exc 中的第一个参数

```js
Array.prototype._reduce = function (exc, initial = 0) {
  let result = initial
  this.forEach((item) => {
    result = exc(result, item)
  })
  return result
} 

console.log(b.reduce((pre, cur) => pre + cur, 0)) // 55
console.log(b._reduce((pre, cur) => pre + cur, 0)) // 55
```

### Object.create

[Object.create()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create) 方法用于创建一个新对象，使用现有的对象来作为新创建对象的原型（prototype）。

```js
Object.prototype._create = function (proto) {
  const Fn = function() {}
  Fn.prototype = proto
  return new Fn()
} 

function A() { }
const obj = Object.create(A)
const obj2 = Object._create(A)
console.log(obj.__proto__ === A) // true
console.log(obj.__proto__ === A) // true
```

### Function.prototype.call

call() 方法使用一个指定的 this 值和单独给出的一个或多个参数来调用一个函数。

```js
Function.prototype._call = function (ctx, ...args) {
  // 如果不为空，则需要进行对象包装
  const o = ctx == undefined ? window : Object(ctx)
  // 给 ctx 添加独一无二的属性
  const key = Symbol()
  o[key] = this
  // 执行函数，得到返回结果
  const result = o[key](...args)
  // 删除该属性
  delete o[key]
  return result
}

const obj = {
  name: '11',
  fun() {
    console.log(this.name)
  }
}

const obj2 = { name: '22' }
obj.fun() // 11
obj.fun.call(obj2) // 22
obj.fun._call(obj2) // 22
```

### Function.prototype.bind

bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

```js
const obj = {
  name: '11',
  fun() {
    console.log(this.name)
  }
} 

Function.prototype._bind = function (ctx, ...args) {
  // 获取函数体
  const _self = this
  // 用一个新函数包裹，避免立即执行
  const bindFn = (...reset) => {
    return _self.call(ctx, ...args, ...reset)
  }
  return bindFn
} 

const obj2 = { name: '22' }
obj.fun() // 11
const fn = obj.fun.bind(obj2)
const fn2 = obj.fun._bind(obj2)
fn() // 22
fn2() // 22
```

### New 关键字

new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。

```js
const _new = function(constructor) {
  // 创建一个空对象
  const obj = {}
  // 原型链挂载
  obj.__proto__ = constructor.prototype;
  // 将obj 复制给构造体中的 this，并且返回结果
  const result = constructor.call(obj)
  // 如果返回对象不为一个对象则直接返回刚才创建的对象
  return typeof result === 'object' && result !== null ? : result : obj
}
```

### 浅拷贝

```js
const _shallowClone = target => {
  // 基本数据类型直接返回
  if (typeof target === 'object' && target !== null) {
    // 获取target 的构造体
    const constructor = target.constructor
    // 如果构造体为以下几种类型直接返回
    if (/^(Function|RegExp|Date|Map|Set)$/i.test(constructor.name)) return target

    // 判断是否是一个数组
    const cloneTarget = Array.isArray(target) ? [] : {}
    for (prop in target) {
      // 只拷贝其自身的属性
      if (target.hasOwnProperty(prop)) {
        cloneTarget[prop] = target[prop]
      }
    } 

    return cloneTarget
  }  
  else {
    return target
  }
}
```

### 深拷贝

实现思路和浅拷贝一致，只不过需要注意几点

1. 函数 正则 日期 ES6新对象 等不是直接返回其地址，而是重新创建

2. 需要避免出现循环引用的情况

```js
const _completeDeepClone = (target, map = new WeakMap()) => {
  // 基本数据类型，直接返回
  if (typeof target !== 'object' || target === null) return target 

  // 函数 正则 日期 ES6新对象,执行构造题，返回新的对象
  const constructor = target.constructor
  if (/^(Function|RegExp|Date|Map|Set)$/i.test(constructor.name)) return new constructor(target)

  // map标记每一个出现过的属性，避免循环引用
  if (map.get(target)) return target
  map.set(target, true) 

  const cloneTarget = Array.isArray(target) ? [] : {}
  for (prop in target) {
    if (target.hasOwnProperty(prop)) {
      cloneTarget[prop] = _completeDeepClone(target[prop], map)
    }
  } 

  return cloneTarget
}
```

### 寄生组合式继承

一图胜千言

![](../../\imgs\prototype-extends.jpg)

```js
function Parent(name) {
  this.name = name
}
Parent.prototype.getName = function () {
  return this.name
}

function Son(name, age) {
  // 这里其实就等于 this.name = name
  Parent.call(this, name)
  this.age = age
}

Son.prototype.getAge = function () {
  return this.age
}
Son.prototype.__proto__ = Object.create(Parent.prototype)

const son1 = new Son('shao', 20)

console.log(son1.getName()) // shao
console.log(son1.getAge()) // 20
```

### 发布订阅者模式

```js
class EventEmitter {
  constructor() {
    // key: 事件名
    // value: callback [] 回调数组
    this.events = {}
  } 

  on(name, callback) {
    if (this.events[name]) {
      this.events[name].push(callback)
    } else {
      this.events[name] = [callback]
    }
  } 

  off(name, callback) {
    if (!this.events[name]) return;
    if (!callback) {
      // 如果没有callback,就删掉整个事件
      this.events[name] = undefined;
    }
    else {
      this.events[name] = this.events[name].filter((item) => item !== callback);
    }
  } 

  emit(name, ...args) {
    if (!this.events[name]) return
    this.events[name].forEach(cb => cb(...args))
  }
}
```

### 观察者模式

```js
class Observerd {
  constructor() {
    // 我要看看到底有多少人在观察俺
    this.observerList = []
  }
  addObserver(observer) {
    // 添加一个观察俺的人
    this.observerList.push(observer)
  }
  notify() {
    // 我要闹点动静，所有观察者都会知道这个信息，具体怎么做就是他们自己的事情了
    this.observerList.forEach(observer => observer.update())
  }
}


class Observer {
  constructor(doSome) {
    // 观察到小白鼠有动静之后，观察者做的事情
    this.doSome = doSome
  }
  update() {
    console.log(this.doSome)
  }
}

const ob1 = new Observer('我是ob1，我观察到小白鼠有反应了，太饿了，我得去吃个饭了')
const ob2 = new Observer('我是ob2，我观察到小白鼠有反应了，我要继续工作！')
const xiaoBaiShu = new Observerd()
xiaoBaiShu.addObserver(ob1)
xiaoBaiShu.addObserver(ob2)
xiaoBaiShu.notify()
```

### 节流

节流函数（throttle）就是让事件处理函数（handler）在大于等于执行周期时才能执行，周期之内不执行，即事件一直被触发，那么事件将会按每小段固定时间一次的频率执行。

```js
function throttle(fn, wait) {
    let timer = null
    let startTime = 0

    return function() {
        let _this = this
        let args = arguments
        const remaining = wait - (Date.now() - startTime)
        clearTimerout(timer)

        if (remaining <= 0) {
            fn.apply(_this, args)
            startTime = Date.now()
        }
        else {
            timer = setTimeout(() => {
                fn.apply(_this, args)
            }, remaining)
        }
    }
}
```

节流常用方式及控制第一次和最后一次触发：

```js
// 节流 定时器 (最后一次也触发)
function throttle1(fn, wait) {
    let timer = null;
    return function () {
        if (!timer) {
            timer = setTimeout(() => {
                timer = null;
                fn()
            }, wait);
        }
    }
}

// 节流 时间戳 (第一次就触发)
function throttle2(fn, wait) {
    let pre = 0;
    return function () {
        let now = Date.now()
        if (now - pre > wait) {
            fn()
            pre = now
        }
    }
}

// 节流 控制最后一次和第一次
function throttle3(fn, wait, op = {}) {
    let timer = null;
    let pre = 0;
    return function () {
        let now = Date.now();
        if (now - pre > wait) {
            if (pre == 0 && !op.bengin) {
                pre = now
                return
            }
            if (timer) {
                clearTimeout(timer)
                timer = null
            }
            fn()
            pre = now
        }
        else if (!timer && op.end) {
            timer = setTimeout(() => {
                fn();
                timer = null
            }, wait)
        }
    }
}
```

### 防抖

事件响应函数在一段时间后才执行，如果这段时间内再次调用，则重新计算执行时间。

```js
function debounce(fn, delay = 300) {
  let timer = null 

  return function (...args) {
    // 每次进来都会清空定时器，所以在 delay 事件中重复执行之后执行最后一次
    clearInterval(timer) 

    timer = setTimeout(() => {
      fn.apply(this, args)
    }, delay)
  }
}
```

### once 函数

函数返回结果会被缓存下来，只会计算一次。

```js
const f = (x) => x;
const onceF = once(f);
//=> 3
onceF(3);
//=> 3
onceF(4);
```

```js
// 利用闭包
const once = (fn) => {
  let res
  let isFirst = true 

  return function (...args) {
    if (!isFirst) return res 

    res = fn.call(this, ...args)
    isFirst = false 

    return res
  }
}
```

### 累加函数应用

实现一个累加函数，下面的几种情况都能正确的调用。

```js
function sum(...args) {
  let params = args 

  const _sum = (...newArgs) => {
    if (newArgs.length === 0) {
      return params.reduce((pre, cur) => pre + cur, 0)
    }  
    else {
      params = [...params, ...newArgs]
      return _sum
    }
  } 

  return _sum
} 


console.log(sum(1, 2)(3)()) // 6
console.log(sum(1)(2)(3)()) // 6
console.log(sum(1, 2, 4)(4)()) // 11
```

### 图片懒加载

**概念:** 图片懒加载就是开始加载页面的时候把图片的 src 给替换,在图片出现在页面的可视区域内的时候再加载图片的 src

**思路**

1. 获取所有的 img 标签,获取并替换所有 src 数据,将 src 替换成 data-src
2. 判断当前图片是否进入可视区域内,进入后进行展示

`getBoundingClientRect` 方法返回元素的大小及其相对于视口的位置

```js
let imgList = [...document.querySelectorAll('img')]
let length = imgList.length
const imgLazyLoad = (function() {
   let count = 0

   return function() {
        let deleteIndexList = []

        imgList.forEach((img, index) => {
            let rect = img.getBoundingClientRect()
            if (rect.top < window.innerHeight) {
                img.src = img.dataset.src
                deleteIndexList.push(index)
                count++
                // 优化图片全部加载完成后移除事件监听
                if (count === length) {
                    document.removeEventListener('scroll', imgLazyLoad)
                }
            }
        })

        // 当img加载完图片后将他从imglist中移除
        imgList = imgList.filter((img, index) => !deleteIndexList.includes(index))
   }
 })()

document.addEventListener('scroll', imgLazyLoad)
```

## 进阶

### 实现 repeat 方法

```js
function repeat(fn, times, delay) {
  return async function (...args) {
    for (let i = 0; i < times; i++) {
      await new Promise((resolve, reject) => {
        setTimeout(() => {
          fn.call(this, ...args)
          resolve()
        }, delay)
      })
    }
  }
} 

const repeatFn = repeat(console.log, 4, 1000)
// 函数调用四次，每次间隔 1s 打印 hello
repeatFn('hello')
```

### 实现 Promise的resolve

> 实现 resolve 静态方法有三个要点:
> 
> - 传参为一个 `Promise`, 则直接返回它。
> 
> - 传参为一个 `thenable` 对象，返回的 `Promise` 会跟随这个对象，采用它的最终状态作为自己的状态。
> 
> - 其他情况，直接返回以该值为成功状态的`promise`对象。

```js
Promise.resolve = (param) => {
    // 传参为一个 Promise, 则直接返回它
    if (param instanceof Promise) return param; 

    return new Promise((resolve, reject) => {
        // 传参为一个 `thenable` 对象，返回的 `Promise` 会跟随这个对象，采用它的最终状态作为自己的状态
        if (param && param.then && typeof param.then === 'function') {
            // param 状态变为成功会调用resolve，将新 Promise 的状态变为成功，反之亦然
            param.then(resolve, reject);
        } 
        // 其他情况，直接返回以该值为成功状态的`promise`对象
        else {
            resolve(param);
        }
  })
}
```

### 实现 Promise.reject

> Promise.reject 中传入的参数会作为一个 reason 原封不动地往下传

```js
Promise.reject = function (reason) {
    return new Promise((resolve, reject) => {
        reject(reason);
    });
}
```

### 实现 Promise.prototype.finally

> 前面的`promise`不管成功还是失败，都会走到`finally`中，并且`finally`之后，还可以继续`then`（说明它还是一个then方法是关键），并且会将初始的`promise`值原封不动的传递给后面的`then`.

**Promise.prototype.finally最大的作用**

- `finally`里的函数，无论如何都会执行，并会把前面的值原封不动传递给下一个`then`方法中
- 如果`finally`函数中有`promise`等异步任务，会等它们全部执行完毕，再结合之前的成功与否状态，返回值

**Promise.prototype.finally六大情况用法**

```js
// 情况1
Promise.resolve(123).finally((data) => { // 这里传入的函数，无论如何都会执行
  console.log(data); // undefined
})

// 情况2 (这里，finally方法相当于做了中间处理，起一个过渡的作用)
Promise.resolve(123).finally((data) => {
  console.log(data); // undefined
}).then(data => {
  console.log(data); // 123
})

// 情况3 (这里只要reject，都会走到下一个then的err中)
Promise.reject(123).finally((data) => {
  console.log(data); // undefined
}).then(data => {
  console.log(data);
}, err => {
  console.log(err, 'err'); // 123 err
})

// 情况4 (一开始就成功之后，会等待finally里的promise执行完毕后，再把前面的data传递到下一个then中)
Promise.resolve(123).finally((data) => {
  console.log(data); // undefined
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('ok');
    }, 3000)
  })
}).then(data => {
  console.log(data, 'success'); // 123 success
}, err => {
  console.log(err, 'err');
})

// 情况5 (虽然一开始成功，但是只要finally函数中的promise失败了，就会把其失败的值传递到下一个then的err中)
Promise.resolve(123).finally((data) => {
  console.log(data); // undefined
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject('rejected');
    }, 3000)
  })
}).then(data => {
  console.log(data, 'success');
}, err => {
  console.log(err, 'err'); // rejected err
})

// 情况6 (虽然一开始失败，但是也要等finally中的promise执行完，才能把一开始的err传递到err的回调中)
Promise.reject(123).finally((data) => {
  console.log(data); // undefined
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('resolve');
    }, 3000)
  })
}).then(data => {
  console.log(data, 'success');
}, err => {
  console.log(err, 'err'); // 123 err
})
```

**源码实现**

```js
Promise.prototype.finally = function (callback) {
  return this.then((data) => {
    // 让函数执行 内部会调用方法，如果方法是promise，需要等待它完成
    // 如果当前promise执行时失败了，会把err传递到，err的回调函数中

    // data 上一个promise的成功态
    return Promise.resolve(callback()).then(() => data);
  }, err => {
    return Promise.resolve(callback()).then(() => {
      throw err; // 把之前的失败的err，抛出去
    });
  })
}
```

### 实现 Promise.all/race/allSettled/any

- Promise 身上的这些方法返回的都是一个 Promise

- Promise.resolve 接受一个 Promise，若非 promise 则将其变成功状态的 Promise

```js
// 有一个失败则返回失败的结果，全部成功返回全成功的数组
Promise.all = function (promiseList = []) {
  return new Promise((resolve, reject) => {
    const result = []
    let count = 0 

    if (promiseList.length === 0) {
      resolve(result)
      return
    } 

    for (let i = 0; i < promiseList.length; i++) {
      Promise.resolve(promiseList[i]).then(res => {
        result[i] = res
        count++ 

        // 不能直接通过 result.length 进行比较，因为 会存在下标大的先赋值
        // 例如 i = 3 第一个返回结果，此时数组变为[empty,empty,empty,res]
        if (count === promiseList.length) {
          resolve(result)
        }
      }).catch(e => {
        reject(e)
      })
    }
  })
} 

// 返回第一个成功或失败的结果
Promise.race = function (promiseList = []) {
  return new Promise((resolve, reject) => {
    if (promiseList.length === 0) {
      return resolve([])
    } 

    for (let i = 0; i < promiseList.length; i++) {
      Promise.resolve(promiseList[i]).then(res => {
        resolve(res)
      }).catch(e => {
        reject(e)
      })
    }
  })
} 

// 无论成功于否都返回，但是会添加一个 status 字段用于标记成功/失败
Promise.allSettled = function (promiseList = []) {
  return new Promise((resolve, reject) => {
    const result = []
    let count = 0

    const addRes = (i, data) => {
      result[i] = data
      count++
      if (count === promiseList.length) {
        resolve(result)
      }
    }

    if (promiseList.length === 0) return resolve(result) 

    for (let i = 0; i < promiseList.length; i++) {
      Promise.resolve(promiseList[i]).then(res => {
        addRes(i, { status: 'fulfilled', data: res })
      }).catch(e => {
        addRes(i, { status: 'rejected', data: e })
      })
    }
  })
} 

// AggregateError，当多个错误需要包装在一个错误中时，该对象表示一个错误。
// 和 Promise.all 相反，全部失败返回失败的结果数组，有一个成功则返回成功结果
Promise.any = function (promiseList = []) {
  return new Promise((resolve, reject) => {
    if (promiseList.length === 0) return resolve([]) 

    let count = 0
    const result = [] 

    for (let i = 0; i < promiseList.length; i++) {
      Promise.resolve(promiseList[i]).then(res => {
        resolve(res)
      }).catch(e => {
        count++
        result[i] = e
        if (count === promiseList.length) {
          reject(new AggregateError(result))
        }
      })
    }
  })
}
```

### 整数千分位加逗号

```js
function toThousands(num) {
    num = num.toString()
    let result = '' 

    // 小数部分
    const index = num.indexOf('.')
    let floatNum = ''
    if (index > -1) {
        floatNum = num.substring(index)
        num = num.substring(0, index)
    }

    while (num.length > 3) {
        result = ',' + num.substring(num.length - 3) + result
        num = num.substring(0, num.length - 3)
    } 

    result = num + result + floatNum 
    return result
} 

console.log(toThousands(1234567)) // 1,234,567
console.log(toThousands(123456)) // 123,456
console.log(toThousands(1234567.123)) // 1,234,567.123
```

### 洗牌函数

有几张牌，用 js 来进行乱序排列，要保持公平性

```js
const shuffle = (arr) => {
  // 不影响原来的数组
  const result = [...arr] 

  for (let i = result.length; i > 0; i--) {
    // 随机从 [0,i - 1] 产生一个 index, 将 i - 1 于 index 对应数组的值进行交换
    const index = Math.floor(Math.random() * i);
    [result[index], result[i - 1]] = [result[i - 1], result[index]]
  } 

  return result
} 

const arr = [1, 2, 3, 4, 5]
console.log(shuffle(arr)) // [ 3, 1, 2, 5, 4 ]
console.log(shuffle(arr)) // [ 2, 3, 5, 1, 4 ]
console.log(shuffle(arr)) // [ 4, 2, 3, 1, 5 ]
console.log(shuffle(arr)) // [ 5, 4, 2, 3, 1 ]
```

### a == 1 && a == 2 && a == 3

如何让 `a == 1 && a == 2 && a == 3` 返回 true 呢

#### 方案一

利用隐式转换会调用 valueOf

```js
const a = {
  value: 1,
  valueOf() {
    return this.value++
  }
} 

console.log(a == 1 && a == 2 && a == 3) // true
```

#### 方案二

在对象 valueOf 函数不存在的情况下会调用 toString 方法

```js
const a = {
  value: 1,
  toString() {
    return this.value++
  }
}

console.log(a == 1 && a == 2 && a == 3) // true
```

#### 方案三

利用`Object.defineProperty` 在全局 window 上挂载一个 a 属性

```js
let _a = 1
Object.defineProperty(window, 'a', {
  get() {
    return _a++
  }
})

console.log(a == 1 && a == 2 && a == 3)
```

### 手写LRU

LRU是Least Recently Used的缩写，即最近最少使用，是一种常用的[页面置换算法]( https://baike.baidu.com/item/%E9%A1%B5%E9%9D%A2%E7%BD%AE%E6%8D%A2%E7%AE%97%E6%B3%95/7626091?fromModule=lemma_inlink)，选择最近最久未使用的页面予以淘汰。该算法赋予每个[页面](https://baike.baidu.com/item/%E9%A1%B5%E9%9D%A2/5544813?fromModule=lemma_inlink)一个访问字段，用来记录一个页面自上次被访问以来所经历的时间 t，当须淘汰一个页面时，选择现有页面中其 t 值最大的，即最近最少使用的页面予以淘汰。

[力扣地址](https://leetcode.cn/problems/lru-cache/)

```js
/**
 * @param {number} capacity
 */
var LRUCache = function(capacity) {
    this.map = new Map()
    this.capacity = capacity
};

/** 
 * @param {number} key
 * @return {number}
 */
LRUCache.prototype.get = function(key) {
    if(this.map.has(key)){
        const value = this.map.get(key)
        // 更新存储位置
        this.map.delete(key)
        this.map.set(key, value) 

        return value
    } 

    return -1
};

/** 
 * @param {number} key 
 * @param {number} value
 * @return {void}
 */
LRUCache.prototype.put = function(key, value) {
    if(this.map.has(key)){
        this.map.delete(key)
    } 

    this.map.set(key, value) 

    // 如果此时超过了最长可存储范围
    if(this.map.size > this.capacity){
        // 删除 map 中最久未使用的元素
        this.map.delete(this.map.keys().next().value)
    }
};
```

## 更上一层楼

### Generator

```js
async function getResult() {
    await new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(1);
            console.log(1);
        }, 1000);
    }) 

    await new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(2);
            console.log(2);
        }, 500);
    }) 

    await new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(3);
            console.log(3);
        }, 100);
    })
}
getResult()
// 1 2 3 
```

那如何使用 Es6 中的 generator 实现类似的效果呢 ？

```js
function* getResult(params) {
    yield new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(1);
            console.log(1);
        }, 1000);
    }) 

    yield new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(2);
            console.log(2);
        }, 500);
    }) 

    yield new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(3);
            console.log(3);
        }, 100);
    })
} 

const gen = getResult()
// gen.next().value 就是每一次 yield 之后返回的 Promise
// gen.next() = {value: yeild 返回的数据,done: 迭代器是否走完}
gen.next().value.then(() => {
    gen.next().value.then(() => {
        gen.next();
    });
});// 依次打印 1 2 3
```

将 gen.next() 封装一层，让其自己能够实现递归调用

```js
const gen = getResult()

function co(g) {
  const nextObj = g.next(); 

  // 递归停止条件：当迭代器迭代到最后一个 yeild 
  if (nextObj.done) {
    return;
  } 

  nextObj.value.then(()=>{
    co(g)
  })
} 

co(gen)
```

### async-pool

JS 控制并发请求， 参考文章 mp.weixin.qq.com/s/yWOPoef9ixuSBWApZQhjIg

![](../../\imgs\async-pool.png)

aysnc-pool 的基本使用

```js
const timeout = i => new Promise(resolve => setTimeout(() => resolve(i), i));
await asyncPool(2, [1000, 5000, 3000, 2000], timeout);
```

asyncPool 这个函数接受三个参数

- poolLimit（数字类型）：表示限制的并发数；

- array（数组类型）：表示任务数组；

- iteratorFn（函数类型）：表示迭代函数，用于实现对每个任务项进行处理，该函数会返回一个 Promise 对象或异步函数。

这里提醒一下，promise.then 中的函数执行是一异步的，而赋值是同步的

```js
const a = Promise.resolve().then(()=>console.log(a))

// 等价于 此时 a 等于一个 pending 状态的 promise
const a = Promise.resolve().then()
a.then(()=>{
  console.log(a)
})
```

手写实现：

```js
async function asyncPool(poolLimit, array, iteratorFn) {
  const ret = []; // 存储所有的异步任务
  const executing = []; // 存储正在执行的异步任务

  for (const item of array) {
    // 调用iteratorFn函数创建异步任务
    const p = Promise.resolve().then(() => iteratorFn(item, array));
    ret.push(p); // 保存新的异步任务

    // 当poolLimit值小于或等于总任务个数时，进行并发控制
    if (poolLimit <= array.length) {
      // 当任务完成后，从正在执行的任务数组中移除已完成的任务
      const e = p.then(() => executing.splice(executing.indexOf(e), 1));
      executing.push(e); // 保存正在执行的异步任务
      if (executing.length >= poolLimit) {
        await Promise.race(executing); // 等待较快的任务执行完成
      }
    }
  } 

  return Promise.all(ret);
}

const timeout = i => new Promise(resolve => {
    setTimeout(() => { console.log(i); resolve(i) }, i)
});
// 当然,limit <= 0 的时候 我们可以理解为只允许一个请求存在 
asyncPool(2, [1000, 5000, 3000, 2000], timeout).then(res => {
  console.log(res)
})
```

## 编程

### JS实现树形与数组相互转换

数据：

```js
const arr =[
    {id: 2, name: '部门B', parentId: 0},
    {id: 3, name: '部门C', parentId: 1},
    {id: 1, name: '部门A', parentId: 2},
    {id: 4, name: '部门D', parentId: 1},
    {id: 5, name: '部门E', parentId: 2},
    {id: 6, name: '部门F', parentId: 3},
    {id: 7, name: '部门G', parentId: 2},
    {id: 8, name: '部门H', parentId: 4}
];
```

#### 数组转树形

```js
function toTree (data, pId) {
  const loop = parentId => {
    const res = [] 

    data.forEach(item => {
      if (item.parentId === parentId) {
        item.children = loop(item.id)
        res.push(item)
      }
    }) 

    return res
  } 

  return loop(pId)
}
```

#### 数组转树形

```js
function treeToArr(data) {
  const result = []; 

  data.forEach(item => {
      const loop = data => {
          result.push({
            id: data.id,
            name: data.name,
            parentId: data.parentId
          }); 

          let child = data.children 

          if (child) {
            for (let i = 0; i < child.length; i++) {
              loop(child[i])
            }
          }
      } 

      loop(item);
  }) 

  return result;
}
```

### 去除字符串中出现次数最少的字符，不改变原字符串的顺序

```js
// “ababac” —— “ababa”
// “aaabbbcceeff” —— “aaabbb”

const changeStr = (str) => {
  let obj = {};
  const _str = str.split(""); 

  _str.forEach(item => {
    if (obj[item]) {
      obj[item]++;
    } else {
      obj[item] = 1;
    }
  })

  var _obj = Object.values(obj).sort();
  var min = _obj[0]; 

  for (let key in obj) {
    if (obj[key] <= min) {
      var reg = new RegExp(key, "g")
      str = str.replace(reg, "")
    }
  } 

  return str;
}

console.log(changeStr("aaabbbcceeff")); // aaabbb
```

### 写出一个函数trans，将数字转换成汉语的输出，输入为不超过10000亿的数字

```js
// trans(123456) —— 十二万三千四百五十六
// trans（100010001）—— 一亿零一万零一

// 将数字（整数）转为汉字，从零到一亿亿，
// 需要小数的可自行截取小数点后面的数字直接替换对应arr1的读法就行了
const convertToChinaNum = (num) => {
    var arr1 = new Array(
        '零',
        '一',
        '二',
        '三',
        '四',
        '五',
        '六',
        '七',
        '八',
        '九'
    );
    // 可继续追加更高位转换值
    var arr2 = new Array(
        '',
        '十',
        '百',
        '千',
        '万',
        '十',
        '百',
        '千',
        '亿',
        '十',
        '百',
        '千',
        '万',
        '十',
        '百',
        '千',
        '亿'
    );

    if (!num || isNaN(num)) {
        return "零";
    }

    var english = num.toString().split("")
    var result = "";

    for (var i = 0; i < english.length; i++) {
        // 倒序排列设值
        var des_i = english.length - 1 - i;
        result = arr2[i] + result; 

        var arr1_index = english[des_i];
        result = arr1[arr1_index] + result;
    }

    // 将【零千、零百】换成【零】 【十零】换成【十】
    result = result.replace(/零(千|百|十)/g, '零').replace(/十零/g, '十');
    // 合并中间多个零为一个零
    result = result.replace(/零+/g, '零');
    // 将【零亿】换成【亿】【零万】换成【万】
    result = result.replace(/零亿/g, '亿').replace(/零万/g, '万');
    // 将【亿万】换成【亿】
    result = result.replace(/亿万/g, '亿');
    // 移除末尾的零
    result = result.replace(/零+$/, '')
    // 将【零一十】换成【零十】
    // result = result.replace(/零一十/g, '零十');//貌似正规读法是零一十
    // 将【一十】换成【十】
    result = result.replace(/^一十/g, '十');  

    return result;
}
```

### 给几个数组, 可以通过数值找到对应的数组名称

```js
// 比如这个函数输入一个1，那么要求函数返回A
const A = [1, 2, 3];
const B = [4, 5, 6];
const C = [7, 8, 9];

const test = (num) => {
  const newArr = [A, B, C];
  let i = 0; 

  while (i < newArr.length) {
    if (newArr[i].includes(num)) return newArr[i];
    i++;
  } 

  return [];
}

console.log(test(1));
```

### 不定长二维数组的全排列

```js
// 输入 [['A', 'B', ...], [1, 2], ['a', 'b'], ...]
// 输出 ['A1a', 'A1b', ....]


let res = arr.reduce((prev, cur) => {
    if (!Array.isArray(prev) || !Array.isArray(cur)) {
        return
    }

    if (prev.length === 0) {
        return cur
    }

    if (cur.length === 0) {
        return prev
    }

    const emptyVal = []
    prev.forEach(val => {
        cur.forEach(item => {
            emptyVal.push(`${val}${item}`)
        })
    })

    return emptyVal
}, [])
console.log(res); 
```

### sleep函数

- 由于js是单线程可以直接用循环阻塞进程

```js
function sleep(delay) {
  var start = (new Date()).getTime();
  while ((new Date()).getTime() - start < delay) {
    continue;
  }
}

function test() {
  console.log('111');
  sleep(2000);
  console.log('222');
}
```

- 使用定时器执行callback

```js
function sleep(ms, callback) {
  setTimeout(callback, ms)
}
sleep(2000, () => {
  console.log("sleep")
})
```

- 使用promise 微任务

```js
const sleep = time => {
 return new Promise(resolve => setTimeout(resolve, time))
} 
 sleep(1000).then(()=>{ console.log(1) })
```

- 使用generator

```js
function sleepGenerator(time) {
    yield new Promise(function(resolve, reject){
        setTimeout(resolve, time);
     }) 
} 
sleepGenerator(1000).next().value.then(()=>{console.log(1)}) 
```

- 使用asnyc/await

```js
function sleep(time) {
    return new Promise(resolve =>
      setTimeout(resolve, time)) 
} 
async function output() {
    let out = await sleep(1000); 
    console.log(1);
    return out;
} 
output();
```

### 给一个字符串, 找到第一个不重复的字符

```js
function getOnceChar(str) {
    const map = {};

    for (let i = 0; i < str.length; i++) {
        if (!map[str[i]] && str.indexOf(str[i], i + 1) === -1) {
            return str[i];
        }

        map[str[i]] = true;
    }
}
getOnceChar('aaaabbbcddcerr'); // e
```

### 虚拟dom转真实dom

```js
const vnode = {
    tag: 'DIV',
    attrs: {
        id: 'app'
    },
    children: [{
            tag: 'SPAN',
            children: [{
                tag: 'A',
                children: []
            }]
        },
        {
            tag: 'SPAN',
            children: [{
                    tag: 'A',
                    children: []
                },
                {
                    tag: 'A',
                    children: []
                }
            ]
        }
    ]
}

function render (vnode, container){
    return container.appendChild(_render(vnode));
}
function _render(vnode){
    if (typeof vnode === 'number') {
        vnode = String(vnode);
    } 

    // 处理文本节点
    if (typeof vnode === 'string') {
        const textNode = document.createTextNode(vnode)
        return textNode;
    } 

    // 处理组件
    if (typeof vnode.tag === 'function') {
        const component = createComponent(vnode.tag, vnode.attrs);
        setComponentProps(component, vnode.attrs);
        return component.base;
    } 

    // 普通的dom
    const dom = document.createElement(vnode.tag);
    if (vnode.attrs) {
        Object.keys(vnode.attrs).forEach(key => {
            const value = vnode.attrs[key];
            // 设置属性
            setAttribute(dom, key, value);    
        } );
    }  

    // 递归渲染子节点
    vnode.children.forEach(child => render(child, dom));    
    return dom ;    // 返回虚拟dom为真正的DOM
} 

// 实现dom挂载到页面某个元素
const ReactDOM = {
    render: (vnode, container) => {
        container.innerHTML = '';
        return render(vnode, container);
    }
}
```

### 实现一个函数, fetchWithRetry 会自动重试3次，任意一次成功直接返回

```js
const fetchWithRetry = async (
  url, 
  options, 
  retryCount = 0,
) => {
  const { maxRetries = 3, ...remainingOptions } = options;
  try {
    return await fetch(url, remainingOptions);
  } catch (error) {
    // 如果重试次数没有超过，那么重新调用
    if (retryCount < maxRetries) {
      return fetchWithRetry(url, options, retryCount + 1);
    }

    // 超过最大重试次数
    throw error;
  }
}

// 补充超时和取消
// 创建一个 reject 的 promise 
// `timeout` 毫秒
const throwOnTimeout = (timeout) => 
  new Promise((_, reject) => 
    setTimeout(() => 
     reject(new Error("Timeout")),
     timeout
    ),
  );

const fetchWithTimeout = (
  url, 
  options = {},
) => {
  const { timeout, ...remainingOptions } = options;
  // 如果超时选项被指定，那么 fetch 调用和超时通过 Promise.race 竞争
  if (timeout) {
    return Promise.race([
      fetch(url, remainingOptions), 
      throwOnTimeout(timeout),
    ]);
  }
  return fetch(url, remainingOptions);
}

// 取消
const fetchWithCancel = (url, options = {}) => {
  const controller = new AbortController();
  const call = fetch(
    url, 
    { ...options, signal: controller.signal },
  );
  const cancel = () => controller.abort();
  return [call, cancel];
};
```
