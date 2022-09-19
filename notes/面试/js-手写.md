# js 手写

**建议优先掌握：**

- `instanceof` - 考察对原型链的理解
- `new` - 对创建对象实例过程的理解
- `call/apply/bind` - 对`this`指向的理解
- 手写`promise` - 对异步的理解
- 手写原生`ajax` - 对`ajax`原理和`http`请求方式的理解，重点是`get`和`post`请求的实现

## 实现防抖函数（debounce）

> 防抖函数原理：**把触发非常频繁的事件合并成一次去执行** 在指定时间内只执行一次回调函数，如果在指定的时间内又触发了该事件，则回调函数的执行时间会基于此刻重新开始计算

![](C:\Users\Administrator\Desktop\docs\imgs\handwriting-js-1.png)

防抖动和节流本质是不一样的。**防抖动是将多次执行变为`最后一次执行`，节流是将多次执行变成`每隔一段时间执行`**

> eg. 像百度搜索，就应该用防抖，当我连续不断输入时，不会发送请求；当我一段时间内不输入了，才会发送一次请求；如果小于这段时间继续输入的话，时间会重新计算，也不会发送请求。

**手写简化版:**

```js
// func是用户传入需要防抖的函数
// wait是等待时间
const debounce = (func, wait = 50) => {
  // 缓存一个定时器id
  let timer = 0
  // 这里返回的函数是每次用户实际调用的防抖函数
  // 如果已经设定过定时器了就清空上一次的定时器
  // 开始一个新的定时器，延迟执行用户传入的方法
  return function(...args) {
    let _this = this
    if (timer) clearTimeout(timer) 

    timer = setTimeout(() => {
      func.apply(_this, args)
    }, wait)
  }
}
```

**适用场景：**

- 文本输入的验证，连续输入文字后发送 AJAX 请求进行验证，验证一次就好
- 按钮提交场景：防止多次提交按钮，只执行最后提交的一次
- 服务端验证场景：表单验证需要服务端配合，只执行一段连续的输入事件的最后一次，还有搜索联想词功能类似

## 实现节流函数（throttle）

> 节流函数原理:指频繁触发事件时，只会在指定的时间段内执行事件回调，即触发事件间隔大于等于指定的时间才会执行回调函数。总结起来就是：**事件，按照一段时间的间隔来进行触发**。

![](../../\imgs\handwriting-js-2.png)

> 像dom的拖拽，如果用消抖的话，就会出现卡顿的感觉，因为只在停止的时候执行了一次，这个时候就应该用节流，在一定时间内多次执行，会流畅很多

**手写简版**

使用时间戳的节流函数会在第一次触发事件时立即执行，以后每过 wait 秒之后才执行一次，并且最后一次触发事件不会被执行

**时间戳方式：**

```js
// func是用户传入需要防抖的函数
// wait是等待时间
const throttle = (func, wait = 50) => {
  // 上一次执行该函数的时间
  let lastTime = 0 

  return function(...args) {
    // 当前时间
    let now = +new Date()
    // 将当前时间和上一次执行函数时间对比
    // 如果差值大于设置的等待时间就执行函数
    if (now - lastTime > wait) {
      lastTime = now
      func.apply(this, args)
    }
  }
}

// 测试
setInterval(
  throttle(() => {
    console.log(1)
  }, 500),
  1
)
```

**定时器方式：**

> 使用定时器的节流函数在第一次触发时不会执行，而是在 delay 秒之后才执行，当最后一次停止触发后，还会再执行一次函数

```js
function throttle(func, delay){
  var timer = null; 

  return function() {
    var context = this;
    var args = arguments; 

    if (!timer) {
      timer = setTimeout(function(){
        func.apply(context, args);
        timer = null;
      }, delay);
    }
  }
}
```

**适用场景：**

- `DOM` 元素的拖拽功能实现（`mousemove`）
- 搜索联想（`keyup`）
- 计算鼠标移动的距离（`mousemove`）
- `Canvas` 模拟画板功能（`mousemove`）
- 监听滚动事件判断是否到页面底部自动加载更多
- 拖拽场景：固定时间内只执行一次，防止超高频次触发位置变动
- 缩放场景：监控浏览器`resize`
- 动画场景：避免短时间内多次触发动画引起性能问题

**总结**

- **函数防抖**：将几次操作合并为一次操作进行。原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话，就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。
- **函数节流**：使得一定时间内只触发一次函数。原理是通过判断是否到达一定时间来触发函数。

## 实现instanceOf

思路：

- 步骤1：先取得当前类的原型，当前实例对象的原型链
- ​步骤2：一直循环（执行原型链的查找机制）
  - 取得当前实例对象原型链的原型链（`proto = proto.__proto__`，沿着原型链一直向上查找）
  - 如果 当前实例的原型链`__proto__`上找到了当前类的原型`prototype`，则返回 `true`
  - 如果 一直找到`Object.prototype.__proto__ == null`，`Object`的基类(`null`)上面都没找到，则返回 `false`

```js
// 实例.__ptoto__ === 类.prototype
function _instanceof(example, classFunc) {
    // 由于instance要检测的是某对象，需要有一个前置判断条件
    // 基本数据类型直接返回false
    if (typeof example !== 'object' || example === null) return false;

    let proto = Object.getPrototypeOf(example);

    while(true) {
        if(proto == null) return false;

        // 在当前实例对象的原型链上，找到了当前类
        if(proto == classFunc.prototype) return true;

        // 沿着原型链__ptoto__一层一层向上查
        proto = Object.getPrototypeof(proto); // 等于proto.__ptoto__
    }
}

console.log('test', _instanceof(null, Array)) // false
console.log('test', _instanceof([], Array)) // true
console.log('test', _instanceof('', Array)) // false
console.log('test', _instanceof({}, Object)) // true
```

## 实现new的过程

**new操作符做了这些事：**

- 创建一个全新的对象
- 这个对象的`__proto__`要指向构造函数的原型prototype
- 执行构造函数，使用 `call/apply` 改变 this 的指向
- 返回值为`object`类型则作为`new`方法的返回值返回，否则返回上述全新对象

```js
function myNew(fn, ...args) {
  // 基于原型链 创建一个新对象
  let newObj = Object.create(fn.prototype); 

  // 添加属性到新对象上 并获取obj函数的结果
  let res = fn.apply(newObj, args); // 改变this指向

  // 如果执行结果有返回值并且是一个对象, 返回执行的结果, 否则, 返回新创建的对象
  return typeof res === 'object' ? res: newObj;
}
```

```js
// 用法
function Person(name, age) {
  this.name = name;
  this.age = age;
}
Person.prototype.say = function() {
  console.log(this.age);
};
let p1 = myNew(Person, "poety", 18);
console.log(p1.name);
console.log(p1);
p1.say();
```

## 实现call方法

**call做了什么:**

- 将函数设为对象的属性
- 执行和删除这个函数
- 指定`this`到函数并传入给定参数执行函数
- 如果不传入参数，默认指向为 `window`

```js
// 模拟 call bar.mycall(null);
// 实现一个call方法：
// 原理：利用 context.xxx = self obj.xx = func-->obj.xx()
Function.prototype.myCall = function(context = window, ...args) {
  if (typeof this !== "function") {
    throw new Error('type error')
  }
  // this-->func  context--> obj  args--> 传递过来的参数

  // 在context上加一个唯一值不影响context上的属性
  let key = Symbol('key')
  context[key] = this; // context为调用的上下文,this此处为函数，将这个函数作为context的方法
  // let args = [...arguments].slice(1)   //第一个参数为obj所以删除,伪数组转为数组

  // 绑定参数 并执行函数
  let result = context[key](...args);
  // 清除定义的this 不删除会导致context属性越来越多
  delete context[key];

  // 返回结果 
  return result;
};
```

```js
// 用法：f.call(obj,arg1)
function f(a, b){
    console.log(a + b)
    console.log(this.name)
}
let obj = {
    name: 1
}
f.myCall(obj, 1, 2) // 否则this指向window
```

## 实现apply方法

> 思路: 利用`this`的上下文特性。`apply`其实就是改一下参数的问题

```js
Function.prototype.myApply = function(context = window, args) {
  // this-->func  context--> obj  args--> 传递过来的参数

  // 在context上加一个唯一值不影响context上的属性
  let key = Symbol('key')
  context[key] = this; // context为调用的上下文,this此处为函数，将这个函数作为context的方法
  // let args = [...arguments].slice(1)   //第一个参数为obj所以删除,伪数组转为数组

  let result = context[key](...args); // 这里和call传参不一样

  // 清除定义的this 不删除会导致context属性越来越多
  delete context[key]; 

  // 返回结果
  return result;
}
```

```js
function f(a, b){
    console.log(a + b)
    console.log(this.name)
}
let obj = {
    name: 1
}
f.myApply (obj, 1, 2) // 否则this指向window
```

## 实现bind方法

> `bind` 的实现对比其他两个函数略微地复杂了一点，涉及到参数合并(类似函数柯里化)，因为 `bind` 需要返回一个函数，需要判断一些边界问题，以下是 `bind` 的实现

- `bind` 返回了一个函数，对于函数来说有两种方式调用，一种是直接调用，一种是通过 `new` 的方式，我们先来说直接调用的方式
- 对于直接调用来说，这里选择了 `apply` 的方式实现，但是对于参数需要注意以下情况：因为 `bind` 可以实现类似这样的代码 `f.bind(obj, 1)(2)`，所以我们需要将两边的参数拼接起来
- 最后来说通过 `new` 的方式，对于 `new` 的情况来说，不会被任何方式改变 `this`，所以对于这种情况我们需要忽略传入的 `this`

**简洁版本**

- 对于普通函数，绑定`this`指向
- 对于构造函数，要保证原函数的原型对象上的属性不能丢失

```js
Function.prototype.myBind = function(context = window, ...args) {
  // this表示调用bind的函数
  let self = this;

  // 返回了一个函数，...innerArgs为实际调用时传入的参数
  let fBound = function(...innerArgs) { 
      // this instanceof fBound为true表示构造函数的情况。如new func.bind(obj)
      // 当作为构造函数时，this 指向实例，此时 this instanceof fBound 结果为 true，可以让实例获得来自绑定函数的值
      // 当作为普通函数时，this 指向 window，此时结果为 false，将绑定函数的 this 指向 context
      return self.apply(
        this instanceof fBound ? this : context, 
        args.concat(innerArgs)
      );
  }

  // 如果绑定的是构造函数，那么需要继承构造函数原型属性和方法：保证原函数的原型对象上的属性不丢失
  // 实现继承的方式: 使用Object.create
  fBound.prototype = Object.create(this.prototype);
  return fBound;
}
```

```js
// 测试用例

function Person(name, age) {
  console.log('Person name：', name);
  console.log('Person age：', age);
  console.log('Person this：', this); // 构造函数this指向实例对象
}

// 构造函数原型的方法
Person.prototype.say = function() {
  console.log('person say');
}

// 普通函数
function normalFun(name, age) {
  console.log('普通函数 name：', name); 
  console.log('普通函数 age：', age); 
  console.log('普通函数 this：', this);  // 普通函数this指向绑定bind的第一个参数 也就是例子中的obj
}


var obj = {
  name: 'poetries',
  age: 18
}

// 先测试作为构造函数调用
var bindFun = Person.myBind(obj, 'poetry1') // undefined
var a = new bindFun(10) // Person name: poetry1、Person age: 10、Person this: fBound {}
a.say() // person say

// 再测试作为普通函数调用
var bindNormalFun = normalFun.myBind(obj, 'poetry2') // undefined
bindNormalFun(12) // 普通函数name: poetry2 普通函数 age: 12 普通函数 this: {name: 'poetries', age: 18}
```

> 注意： `bind`之后不能再次修改`this`的指向，`bind`多次后执行，函数`this`还是指向第一次`bind`的对象

## 实现深拷贝

### 简洁版本

**简单版：**

```js
const newObj = JSON.parse(JSON.stringify(oldObj));
```

**局限性：**

- 他无法实现对函数 、RegExp等特殊对象的克隆
- 会抛弃对象的`constructor`,所有的构造函数会指向`Object`
- 对象有循环引用,会报错

**面试简版**

```js
function deepClone(obj) {
    // 如果是 值类型 或 null，则直接return
    if(typeof obj !== 'object' || obj === null) {
      return obj
    }

    // 定义结果对象
    let copy = {}

    // 如果对象是数组，则定义结果数组
    if(obj.constructor === Array) {
      copy = []
    }

    // 遍历对象的key
    for (let key in obj) {
        // 如果key是对象的自有属性
        if(obj.hasOwnProperty(key)) {
          // 递归调用深拷贝方法
          copy[key] = deepClone(obj[key])
        }
    }

    return copy
} 
```

> 调用深拷贝方法，若属性为值类型，则直接返回；若属性为引用类型，则递归遍历。这就是我们在解这一类题时的核心的方法。

**进阶版**

- 解决拷贝循环引用问题
- 解决拷贝对应原型问题

```js
// 递归拷贝 (类型判断)
function deepClone(value, hash = new WeakMap){ // 弱引用，不用map，weakMap更合适一点
  // null 和 undefiend 是不需要拷贝的
  if (value == null) { return value;}
  if (value instanceof RegExp) { return new RegExp(value) }
  if (value instanceof Date) { return new Date(value) } 

  // 函数是不需要拷贝
  if (typeof value != 'object') return value; 

  let obj = new value.constructor(); // [] {}

  // 说明是一个对象类型
  if (hash.get(value)) {
    return hash.get(value)
  }
  hash.set(value, obj);

  for (let key in value) { // in 会遍历当前对象上的属性 和 __proto__指代的属性
    // 补拷贝 对象的__proto__上的属性
    if (value.hasOwnProperty(key)) {
      // 如果值还有可能是对象 就继续拷贝
      obj[key] = deepClone(value[key], hash);
    }
  }

  return obj
  // 区分对象和数组 Object.prototype.toString.call
}
```

```js
// test
var o = {};
o.x = o;
var o1 = deepClone(o); // 如果这个对象拷贝过了 就返回那个拷贝的结果就可以了
console.log(o1);
```

### 实现完整的深拷贝

**简易版及问题**

```js
JSON.parse(JSON.stringify());
```

> 估计这个api能覆盖大多数的应用场景，没错，谈到深拷贝，我第一个想到的也是它。但是实际上，对于某些严格的场景来说，这个方法是有巨大的坑的。问题如下：

1. 无法解决`循环引用`的问题。举个例子：

```js
const a = {val: 2};
a.target = a;
```

> 拷贝`a`会出现系统栈溢出，因为出现了无限递归的情况。

2. 无法拷贝一些特殊的对象，诸如 `RegExp, Date, Set, Map`等
3. 无法拷贝`函数`(划重点)。

因此这个api先pass掉，我们重新写一个深拷贝，简易版如下:

```js
const deepClone = (target) => {
  if (typeof target === 'object' && target !== null) {
    const cloneTarget = Array.isArray(target) ? []: {};
    for (let prop in target) {
      if (target.hasOwnProperty(prop)) {
          cloneTarget[prop] = deepClone(target[prop]);
      }
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```

现在，我们以刚刚发现的三个问题为导向，一步步来完善、优化我们的深拷贝代码。

**2. 解决循环引用**

现在问题如下:

```js
let obj = {val : 100};
obj.target = obj;

deepClone(obj); // 报错: RangeError: Maximum call stack size exceeded
```

这就是循环引用。我们怎么来解决这个问题呢？

> 创建一个Map。记录下已经拷贝过的对象，如果说已经拷贝过，那直接返回它行了。

```js
const isObject = (target) => (typeof target === 'object' || typeof target === 'function') && target !== null;

const deepClone = (target, map = new Map()) => { 
  if(map.get(target))  
    return target; 


  if (isObject(target)) { 
    map.set(target, true); 
    const cloneTarget = Array.isArray(target) ? []: {}; 
    for (let prop in target) { 
      if (target.hasOwnProperty(prop)) { 
          cloneTarget[prop] = deepClone(target[prop],map); 
      } 
    } 
    return cloneTarget; 
  } else { 
    return target; 
  } 
}
```

现在来试一试：

```js
const a = {val:2};
a.target = a;
let newA = deepClone(a);
console.log(newA)//{ val: 2, target: { val: 2, target: [Circular] } }
```

> 好像是没有问题了, 拷贝也完成了。但还是有一个潜在的坑, 就是map 上的 key 和 map 构成了强引用关系，这是相当危险的。我给你解释一下与之相对的弱引用的概念你就明白了

在计算机程序设计中，弱引用与强引用相对，

> 被弱引用的对象可以在任何时候被回收，而对于强引用来说，只要这个强引用还在，那么对象无法被回收。拿上面的例子说，map 和 a一直是强引用的关系， 在程序结束之前，a 所占的内存空间一直不会被释放。

**怎么解决这个问题？**

> 很简单，让 map 的 key 和 map 构成弱引用即可。ES6给我们提供了这样的数据结构，它的名字叫WeakMap，它是一种特殊的Map, 其中的键是弱引用的。其键必须是对象，而值可以是任意的

稍微改造一下即可:

```js
const deepClone = (target, map = new WeakMap()) => {
  //...
}
```

**3. 拷贝特殊对象**

**可继续遍历**

对于特殊的对象，我们使用以下方式来鉴别:

```js
Object.prototype.toString.call(obj);
```

梳理一下对于可遍历对象会有什么结果：

```js
["object Map"]
["object Set"]
["object Array"]
["object Object"]
["object Arguments"]
```

以这些不同的字符串为依据，我们就可以成功地鉴别这些对象。

```js
const getType = Object.prototype.toString.call(obj);

const canTraverse = {
  '[object Map]': true,
  '[object Set]': true,
  '[object Array]': true,
  '[object Object]': true,
  '[object Arguments]': true,
};

const deepClone = (target, map = new Map()) => {
  if(!isObject(target)) 
    return target;
  let type = getType(target);
  let cloneTarget;
  if(!canTraverse[type]) {
    // 处理不能遍历的对象
    return;
  }else {
    // 这波操作相当关键，可以保证对象的原型不丢失！
    let ctor = target.prototype;
    cloneTarget = new ctor();
  }

  if(map.get(target)) 
    return target;
  map.put(target, true);

  if(type === mapTag) {
    //处理Map
    target.forEach((item, key) => {
      cloneTarget.set(deepClone(key), deepClone(item));
    })
  }

  if(type === setTag) {
    //处理Set
    target.forEach(item => {
      target.add(deepClone(item));
    })
  }

  // 处理数组和对象
  for (let prop in target) {
    if (target.hasOwnProperty(prop)) {
        cloneTarget[prop] = deepClone(target[prop]);
    }
  }
  return cloneTarget;
}
```

**不可遍历的对象**

```js
const boolTag = '[object Boolean]';
const numberTag = '[object Number]';
const stringTag = '[object String]';
const dateTag = '[object Date]';
const errorTag = '[object Error]';
const regexpTag = '[object RegExp]';
const funcTag = '[object Function]';
```

对于不可遍历的对象，不同的对象有不同的处理。

```js
const handleRegExp = (target) => {
  const { source, flags } = target;
  return new target.constructor(source, flags);
}

const handleFunc = (target) => {
  // 待会的重点部分
}

const handleNotTraverse = (target, tag) => {
  const Ctor = targe.constructor;
  switch(tag) {
    case boolTag:
    case numberTag:
    case stringTag:
    case errorTag: 
    case dateTag:
      return new Ctor(target);
    case regexpTag:
      return handleRegExp(target);
    case funcTag:
      return handleFunc(target);
    default:
      return new Ctor(target);
  }
}
```

**4. 拷贝函数**

- 虽然函数也是对象，但是它过于特殊，我们单独把它拿出来拆解。
- 提到函数，在JS种有两种函数，一种是普通函数，另一种是箭头函数。每个普通函数都是
- Function的实例，而箭头函数不是任何类的实例，每次调用都是不一样的引用。那我们只需要
- 处理普通函数的情况，箭头函数直接返回它本身就好了。

那么如何来区分两者呢？

> 答案是: 利用原型。箭头函数是不存在原型的。

```js
const handleFunc = (func) => {
  // 箭头函数直接返回自身
  if(!func.prototype) return func;
  const bodyReg = /(?<={)(.|\n)+(?=})/m;
  const paramReg = /(?<=\().+(?=\)\s+{)/;
  const funcString = func.toString();
  // 分别匹配 函数参数 和 函数体
  const param = paramReg.exec(funcString);
  const body = bodyReg.exec(funcString);
  if(!body) return null;
  if (param) {
    const paramArr = param[0].split(',');
    return new Function(...paramArr, body[0]);
  } else {
    return new Function(body[0]);
  }
}
```

**5. 完整代码展示**

```js
const getType = obj => Object.prototype.toString.call(obj);

const isObject = (target) => (typeof target === 'object' || typeof target === 'function') && target !== null;

const canTraverse = {
  '[object Map]': true,
  '[object Set]': true,
  '[object Array]': true,
  '[object Object]': true,
  '[object Arguments]': true,
};
const mapTag = '[object Map]';
const setTag = '[object Set]';
const boolTag = '[object Boolean]';
const numberTag = '[object Number]';
const stringTag = '[object String]';
const symbolTag = '[object Symbol]';
const dateTag = '[object Date]';
const errorTag = '[object Error]';
const regexpTag = '[object RegExp]';
const funcTag = '[object Function]';

const handleRegExp = (target) => {
  const { source, flags } = target;
  return new target.constructor(source, flags);
}

const handleFunc = (func) => {
  // 箭头函数直接返回自身
  if(!func.prototype) return func;
  const bodyReg = /(?<={)(.|\n)+(?=})/m;
  const paramReg = /(?<=\().+(?=\)\s+{)/;
  const funcString = func.toString();
  // 分别匹配 函数参数 和 函数体
  const param = paramReg.exec(funcString);
  const body = bodyReg.exec(funcString);
  if(!body) return null;
  if (param) {
    const paramArr = param[0].split(',');
    return new Function(...paramArr, body[0]);
  } else {
    return new Function(body[0]);
  }
}

const handleNotTraverse = (target, tag) => {
  const Ctor = target.constructor;
  switch(tag) {
    case boolTag:
      return new Object(Boolean.prototype.valueOf.call(target));
    case numberTag:
      return new Object(Number.prototype.valueOf.call(target));
    case stringTag:
      return new Object(String.prototype.valueOf.call(target));
    case symbolTag:
      return new Object(Symbol.prototype.valueOf.call(target));
    case errorTag: 
    case dateTag:
      return new Ctor(target);
    case regexpTag:
      return handleRegExp(target);
    case funcTag:
      return handleFunc(target);
    default:
      return new Ctor(target);
  }
}

const deepClone = (target, map = new WeakMap()) => {
  if(!isObject(target)) 
    return target;
  let type = getType(target);
  let cloneTarget;
  if(!canTraverse[type]) {
    // 处理不能遍历的对象
    return handleNotTraverse(target, type);
  }else {
    // 这波操作相当关键，可以保证对象的原型不丢失！
    let ctor = target.constructor;
    cloneTarget = new ctor();
  }

  if(map.get(target)) 
    return target;
  map.set(target, true);

  if(type === mapTag) {
    //处理Map
    target.forEach((item, key) => {
      cloneTarget.set(deepClone(key, map), deepClone(item, map));
    })
  }

  if(type === setTag) {
    //处理Set
    target.forEach(item => {
      cloneTarget.add(deepClone(item, map));
    })
  }

  // 处理数组和对象
  for (let prop in target) {
    if (target.hasOwnProperty(prop)) {
        cloneTarget[prop] = deepClone(target[prop], map);
    }
  }
  return cloneTarget;
}
```

## 实现类的继承

### 实现类的继承-简版

> 类的继承在几年前是重点内容，有n种继承方式各有优劣，es6普及后越来越不重要，那么多种写法有点『回字有四样写法』的意思，如果还想深入理解的去看红宝书即可，我们目前只实现一种最理想的继承方式。

```js
// 寄生组合继承
function Parent(name) {
  this.name = name
}
Parent.prototype.say = function() {
  console.log(this.name + ` say`);
}
Parent.prototype.play = function() {
  console.log(this.name + ` play`);
}

function Child(name, parent) {
  // 将父类的构造函数绑定在子类上
  Parent.call(this, parent)
  this.name = name
}

/** 
 1. 这一步不用Child.prototype = Parent.prototype的原因是怕共享内存，修改父类原型对象就会影响子类
 2. 不用Child.prototype = new Parent()的原因是会调用2次父类的构造方法（另一次是call），会存在一份多余的父类实例属性
3. Object.create是创建了父类原型的副本，与父类原型完全隔离
*/
Child.prototype = Object.create(Parent.prototype);
Child.prototype.say = function() {
  console.log(this.name + ` say`);
}

// 注意记得把子类的构造指向子类本身
Child.prototype.constructor = Child;
```

```js
// 测试
var parent = new Parent('parent');
parent.say() 

var child = new Child('child');
child.say() 
child.play(); // 继承父类的方法
```

### ES5实现继承-详细

**第一种方式是借助call实现继承**

```js
function Parent1(){
    this.name = 'parent1';
}
function Child1(){
    Parent1.call(this);
    this.type = 'child1'    
}
console.log(new Child1);
```

> 这样写的时候子类虽然能够拿到父类的属性值，但是问题是父类中一旦存在方法那么子类无法继承。那么引出下面的方法

**第二种方式借助原型链实现继承：**

```js
function Parent2() {
    this.name = 'parent2';
    this.play = [1, 2, 3]
}
function Child2() {
    this.type = 'child2';
}
Child2.prototype = new Parent2();

console.log(new Child2());
```

看似没有问题，父类的方法和属性都能够访问，但实际上有一个潜在的不足。举个例子：

```js
var s1 = new Child2();
var s2 = new Child2();
s1.play.push(4);
console.log(s1.play, s2.play); // [1,2,3,4] [1,2,3,4]
```

明明我只改变了s1的play属性，为什么s2也跟着变了呢？很简单，因为两个实例使用的是同一个原型对象

**第三种方式：将前两种组合：**

```js
function Parent3 () {
    this.name = 'parent3';
    this.play = [1, 2, 3];
}
function Child3() {
    Parent3.call(this);
    this.type = 'child3';
}
Child3.prototype = new Parent3();
var s3 = new Child3();
var s4 = new Child3();
s3.play.push(4);
console.log(s3.play, s4.play); // [1,2,3,4] [1,2,3]
```

> 之前的问题都得以解决。但是这里又徒增了一个新问题，那就是Parent3的构造函数会多执行了一次（`Child3.prototype = new Parent3()`;）。这是我们不愿看到的。那么如何解决这个问题？

**第四种方式: 组合继承的优化1**

```js
function Parent4 () {
    this.name = 'parent4';
    this.play = [1, 2 3];
}
function Child4() {
    Parent4.call(this);
    this.type = 'child4';
}
Child4.prototype = Parent4.prototype;
```

> 这里让将父类原型对象直接给到子类，父类构造函数只执行一次，而且父类属性和方法均能访问，但是我们来测试一下

```js
var s3 = new Child4();
var s4 = new Child4();
console.log(s3)
```

> 子类实例的构造函数是Parent4，显然这是不对的，应该是Child4。

**第五种方式(最推荐使用)：优化2**

```js
function Parent5 () {
    this.name = 'parent5';
    this.play = [1, 2, 3];
}
function Child5() {
    // 继承属性
    Parent5.call(this);
    this.type = 'child5';
}
// 继承方法
Child5.prototype = Object.create(Parent5.prototype);
Child5.prototype.constructor = Child5;
```

> 这是最推荐的一种方式，接近完美的继承。

## 实现Promise相关方法

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

### 实现 Promise.all

> 对于 all 方法而言，需要完成下面的核心功能:
> 
> - 传入参数为一个空的可迭代对象，则直接进行`resolve`。
> - 如果参数中有一个`promise`失败，那么`Promise.all`返回的`promise`对象失败。
> - 在任何情况下，`Promise.all` 返回的 `promise` 的完成状态的结果都是一个数组

```js
return new Promise((resolve, reject) => {
    let result = [];
    let index = 0;
    let len = promises.length;
    if (len === 0) {
      resolve(result);
      return;
    }

    for (let i = 0; i < len; i++) {
      // 为什么不直接 promise[i].then, 因为promise[i]可能不是一个promise
      Promise.resolve(promise[i]).then(data => {
        result[i] = data;
        index++; 

        if (index === len) resolve(result);
      }).catch(err => {
        reject(err);
      })
    }
  })
```

### 实现 promise.allsettle

> MDN: `Promise.allSettled()`方法返回一个在所有给定的`promise`都`已经`fulfilled`或`rejected`后的`promise`，并带有一个对象数组，每个对象表示对应的`promise`结果

当您有多个彼此不依赖的异步任务成功完成时，或者您总是想知道每个`promise`的结果时，通常使用它。

> 【译】`Promise.allSettled` 跟 `Promise.all` 类似, 其参数接受一个`Promise`的数组, 返回一个新的`Promise`, 唯一的不同在于, 其不会进行短路, 也就是说当Promise全部处理完成后我们可以拿到每个`Promise`的状态, 而不管其是否处理成功。

**用法 | 测试用例**

```js
let fs = require('fs').promises;

let getName = fs.readFile('./name.txt', 'utf8'); // 读取文件成功
let getAge = fs.readFile('./age.txt', 'utf8');

Promise.allSettled([1, getName, getAge, 2]).then(data => {
    console.log(data);
});
// 输出结果
/*
    [
    { status: 'fulfilled', value: 1 },
    { status: 'fulfilled', value: 'zf' },
    { status: 'fulfilled', value: '11' },
    { status: 'fulfilled', value: 2 }
    ]
*/

let getName = fs.readFile('./name123.txt', 'utf8'); // 读取文件失败
let getAge = fs.readFile('./age.txt', 'utf8');
// 输出结果
/*
    [
    { status: 'fulfilled', value: 1 },
    {
      status: 'rejected',
      value: [Error: ENOENT: no such file or directory, open './name123.txt'] {
        errno: -2,
        code: 'ENOENT',
        syscall: 'open',
        path: './name123.txt'
      }
    },
    { status: 'fulfilled', value: '11' },
    { status: 'fulfilled', value: 2 }
  ]
*/
```

**实现**

```js
function isPromise (val) {
  return typeof val.then === 'function'; // (123).then => undefined
}

Promise.allSettled = function(promises) {
  return new Promise((resolve, reject) => {
    let arr = [];
    let times = 0; 

    const setData = (index, data) => {
      arr[index] = data;
      if (++times === promises.length) {
        resolve(arr);
      }
      console.log('times', times)
    }

    for (let i = 0; i < promises.length; i++) {
      let current = promises[i]; 

      if (isPromise(current)) {
        current.then((data) => {
          setData(i, { status: 'fulfilled', value: data });
        }, err => {
          setData(i, { status: 'rejected', value: err })
        })
      } else {
        setData(i, { status: 'fulfilled', value: current })
      }
    }
  })
}
```

### 实现 Promise.race

> race 的实现相比之下就简单一些，只要有一个 promise 执行完，直接 resolve 并停止执行

```js
Promise.race = function(promises) {
  return new Promise((resolve, reject) => {
    let len = promises.length;
    if (len === 0) return; 

    for (let i = 0; i < len; i++) {
      Promise.resolve(promise[i]).then(data => {
        resolve(data);

        // 直接终止for循环
        return;
      }).catch(err => {
        reject(err); 

        // 直接终止for循环
        return;
      })
    }
  })
}
```

### 实现一个简版Promise

```js
// 使用
var promise = new Promise((resolve,reject) => {
    if (操作成功) {
        resolve(value)
    } else {
        reject(error)
    }
})
promise.then(function (value) {
    // success
},function (value) {
    // failure
})
```

```js
function myPromise(constructor) {
    let self = this;
    self.status = "pending"   // 定义状态改变前的初始状态
    self.value = undefined;   // 定义状态为resolved的时候的状态
    self.reason = undefined;  // 定义状态为rejected的时候的状态 

    function resolve(value) {
       if(self.status === "pending") {
          self.value = value;
          self.status = "resolved";
       }
    } 

    function reject(reason) {
       if(self.status === "pending") {
          self.reason = reason;
          self.status = "rejected";
       }
    } 

    // 捕获构造异常
    try {
       constructor(resolve, reject);
    } catch(e) {
       reject(e);
    }
}

// 添加 then 方法
myPromise.prototype.then = function(onFullfilled, onRejected) {
   let self = this; 

   switch(self.status) {
      case "resolved":
        onFullfilled(self.value);
        break; 

      case "rejected":
        onRejected(self.reason);
        break; 

      default:       
   }
}

var p = new myPromise(function(resolve, reject) {
    resolve(1)
});
p.then(function(x) {
    console.log(x) // 1
})
```

**使用class实现**

```js
class MyPromise {
  constructor(fn) {
    this.resolvedCallbacks = [];
    this.rejectedCallbacks = [];

    this.state = 'PENDING';
    this.value = '';

    fn(this.resolve.bind(this), this.reject.bind(this));
  }

  resolve(value) {
    if (this.state === 'PENDING') {
      this.state = 'RESOLVED';
      this.value = value;

      this.resolvedCallbacks.map(cb => cb(value));   
    }
  }

  reject(value) {
    if (this.state === 'PENDING') {
      this.state = 'REJECTED';
      this.value = value;

      this.rejectedCallbacks.map(cb => cb(value));
    }
  }

  then(onFulfilled, onRejected) {
    if (this.state === 'PENDING') {
      this.resolvedCallbacks.push(onFulfilled);
      this.rejectedCallbacks.push(onRejected);
    }

    if (this.state === 'RESOLVED') {
      onFulfilled(this.value);
    }

    if (this.state === 'REJECTED') {
      onRejected(this.value);
    }
  }
}
```

### Promise 实现-详细

- 可以把 `Promise` 看成一个状态机。初始是 `pending` 状态，可以通过函数 `resolve`和 `reject` ，将状态转变为 `resolved`或者 `rejected` 状态，状态一旦改变就不能再次变化。
- `then` 函数会返回一个 `Promise` 实例，并且该返回值是一个新的实例而不是之前的实例。因为 `Promise` 规范规定除了 `pending` 状态，其他状态是不可以改变的，如果返回的是一个相同实例的话，多个 `then` 调用就失去意义了。
- 对于 `then`来说，本质上可以把它看成是 `flatMap`

```js
// 三种状态
const PENDING = "pending";
const RESOLVED = "resolved";
const REJECTED = "rejected"; 

// promise 接收一个函数参数，该函数会立即执行
function MyPromise(fn) {
  let _this = this;
  _this.currentState = PENDING;
  _this.value = undefined; 

  // 用于保存 then 中的回调，只有当 promise
  // 状态为 pending 时才会缓存，并且每个实例至多缓存一个
  _this.resolvedCallbacks = [];
  _this.rejectedCallbacks = [];

  _this.resolve = function (value) {
    if (value instanceof MyPromise) {
      // 如果 value 是个 Promise，递归执行
      return value.then(_this.resolve, _this.reject)
    } 

    setTimeout(() => { // 异步执行，保证执行顺序
      if (_this.currentState === PENDING) {
        _this.currentState = RESOLVED;
        _this.value = value;
        _this.resolvedCallbacks.forEach(cb => cb());
      }
    })
  };

  _this.reject = function (reason) {
    setTimeout(() => { // 异步执行，保证执行顺序
      if (_this.currentState === PENDING) {
        _this.currentState = REJECTED;
        _this.value = reason;
        _this.rejectedCallbacks.forEach(cb => cb());
      }
    })
  } 

  // 用于解决以下问题
  // new Promise(() => throw Error('error))
  try {
   fn(_this.resolve, _this.reject);
  } catch (e) {
    _this.reject(e);
  }
}

MyPromise.prototype.then = function (onResolved, onRejected) {
  var self = this; 

  // 规范 2.2.7，then 必须返回一个新的 promise
  var promise2; 

  // 规范 2.2.onResolved 和 onRejected 都为可选参数
  // 如果类型不是函数需要忽略，同时也实现了透传
  // Promise.resolve(4).then().then((value) => console.log(value))
  onResolved = typeof onResolved === 'function' ? onResolved : v => v;
  onRejected = typeof onRejected === 'function' ? onRejected : r => throw r;

  if (self.currentState === RESOLVED) {
    return (promise2 = new MyPromise(function (resolve, reject) {
      // 规范 2.2.4，保证 onFulfilled，onRjected 异步执行
      // 所以用了 setTimeout 包裹下
      setTimeout(function () {
        try {
          var x = onResolved(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (reason) {
          reject(reason);
        }
      });
    }));
  }

  if (self.currentState === REJECTED) {
    return (promise2 = new MyPromise(function (resolve, reject) {
      setTimeout(function () {
        // 异步执行onRejected
        try {
          var x = onRejected(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (reason) {
          reject(reason);
        }
      });
    }));
  }

  if (self.currentState === PENDING) {
    return (promise2 = new MyPromise(function (resolve, reject) {
      self.resolvedCallbacks.push(function () {
        // 考虑到可能会有报错，所以使用 try/catch 包裹
        try {
          var x = onResolved(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (r) {
          reject(r);
        }
      });

      self.rejectedCallbacks.push(function () {
        try {
          var x = onRejected(self.value);
          resolutionProcedure(promise2, x, resolve, reject);
        } catch (r) {
          reject(r);
        }
      });
    }));
  }
}; 

// 规范 2.3
function resolutionProcedure(promise2, x, resolve, reject) {
  // 规范 2.3.1，x 不能和 promise2 相同，避免循环引用
  if (promise2 === x) {
    return reject(new TypeError("Error"));
  } 

  // 规范 2.3.2
  // 如果 x 为 Promise，状态为 pending 需要继续等待否则执行
  if (x instanceof MyPromise) {
    if (x.currentState === PENDING) {
      x.then(function (value) {
        // 再次调用该函数是为了确认 x resolve 的
        // 参数是什么类型，如果是基本类型就再次 resolve
        // 把值传给下个 then
        resolutionProcedure(promise2, value, resolve, reject);
      }, reject);
    } else {
      x.then(resolve, reject);
    } 

    return;
  } 

  // 规范 2.3.3.3.3
  // reject 或者 resolve 其中一个执行过得话，忽略其他的
  let called = false;
  // 规范 2.3.3，判断 x 是否为对象或者函数
  if (x !== null && (typeof x === "object" || typeof x === "function")) {
    // 规范 2.3.3.2，如果不能取出 then，就 reject
    try {
      // 规范 2.3.3.1
      let then = x.then;
      // 如果 then 是函数，调用 x.then
      if (typeof then === "function") {
        // 规范 2.3.3.3
        then.call(
          x,
          y => {
            if (called) return;
            called = true;
            // 规范 2.3.3.3.1
            resolutionProcedure(promise2, y, resolve, reject);
          },
          e => {
            if (called) return;
            called = true;
            reject(e);
          }
        );
      } else {
        // 规范 2.3.3.4
        resolve(x);
      }
    } catch (e) {
      if (called) return;
      called = true;
      reject(e);
    }
  } else {
    // 规范 2.3.4，x 为基本类型
    resolve(x);
  }
}
```

### 实现 Promisify

> Promisify作用：将原本需要通过传入回调参数来实现回调执行（或者叫同步执行）改为利用`promise`的`.then`的方式来调用,从而实现逻辑上的同步操作。

```js
const fs = require('fs')
const path = require('path')

// node中使用
// const fs = require('fs').promises 12.18版
// const promisify = require('util').promisify

// 包装node api promise化 典型的高级函数
const promisify = fn=>{
  return (...args)=>{
    return new Promise((resolve,reject)=>{
      fn(...args, (err,data)=>{
        if(err) {
          reject(err)
        } 
        resolve(data)
      })
    })
  }
}

// const read = promisify(fs.readFile)

// read(path.join(__dirname, './promise.js'), 'utf8').then(d=>{
//   console.log(d)
// })

// promise化node所有api
const promisifyAll = target=>{
  Reflect.ownKeys(target).forEach(key=>{
    if(typeof target[key] === 'function') {
      target[key+'Async'] = promisify(target[key])
    }
  })
  return target
}

// promise化fs下的函数
const promisifyNew = promisifyAll(fs)

promisifyNew.readFileAsync(path.join(__dirname, './promise.js'), 'utf8').then(d=>{
  console.log(d)
})

module.exports = {
  promisify,
  promisifyAll
}
```

### 完整实现Promises/A+规范

```js
/**
 * Promises/A+规范 实现一个promise
 * https://promisesaplus.com/
*/

const EMUM = {
  PENDING: 'PENDING',
  FULFILLED: 'FULFILLED',
  REJECTED: 'REJECTED'
}

// x 返回值
// promise2 then的时候new的promise
// promise2的resolve, reject
const resolvePromise = (x, promise2, resolve, reject)=>{
  // 解析promise的值解析promise2是成功还是失败 传递到下层then
  if(x === promise2) {
    reject(new TypeError('类型错误'))
  }
  // 这里的x如果是一个promise的话 可能是其他的promise，可能调用了成功 又调用了失败
  // 防止resolve的时候 又throw err抛出异常到reject了
  let called
  // 如果x是promise 那么就采用他的状态
  // 有then方法是promise
  if(typeof x === 'object' && typeof x!== null || typeof x === 'function') {
    // x是对象或函数
    try {
      let then = x.then // 缓存，不用多次取值
      if(typeof then === 'function') {
        // 是promise，调用then方法里面有this，需要传入this为x才能取到then方法里面的值this.value
        then.call(x, y=>{// 成功
          // y值可能也是一个promise 如resolve(new Promise()) 此时的y==new Promise()
          // 递归解析y，直到拿到普通的值resolve(x出去)
          if(called) return;
          called = true;

          resolvePromise(y, promise2, resolve, reject)
        },r=>{// 一旦失败直接失败
          if(called) return;
          called = true;
          reject(r)
        })
      } else {
        // 普通对象不是promise
        resolve(x)
      }
    } catch (e) {
      // 对象取值可能报错，用defineProperty定义get 抛出异常
      if(called) return;
      called = true;
      reject(e)
    }
  } else {
    // x是普通值
    resolve(x) // 直接成功
  }

} 

class myPromise {
  constructor(executor) {
    this.status = EMUM.PENDING // 当前状态
    this.value = undefined // resolve接收值
    this.reason = undefined // reject失败返回值

    /**
     * 同一个promise可以then多次(发布订阅模式)
     * 调用then时 当前状态是等待态，需要将当前成功或失败的回调存放起来（订阅）
     * 调用resolve时 将订阅函数进行执行（发布）
    */
    // 成功队列
    this.onResolvedCallbacks = []
    // 失败队列
    this.onRejectedCallbacks = [] 

    const resolve = value =>{
      // 如果value是一个promise，需要递归解析
      // 如 myPromise.resolve(new myPromise()) 需要解析value
      if(value instanceof myPromise) {
        // 不停的解析 直到值不是promise
        return value.then(resolve,reject)
      }

      if(this.status === EMUM.PENDING) {
        this.status = EMUM.FULFILLED
        this.value = value

        this.onResolvedCallbacks.forEach(fn=>fn())
      }
    } 

    const reject = reason =>{
      if(this.status === EMUM.PENDING) {
        this.status = EMUM.REJECTED
        this.reason = reason

        this.onRejectedCallbacks.forEach(fn=>fn())
      }
    } 

    try {
      executor(resolve,reject)
    } catch(e) {
      reject(e)
    }
  } 

  then(onFulFilled, onRejected) {
    // 透传 处理默认不传的情况
    // new Promise((resolve,reject)=>{
    //   resolve(1)
    // }).then().then().then(d=>{})
    // new Promise((resolve,reject)=>{
    //   resolve(1)
    // }).then(v=>v).then(v=>v).then(d=>{})
    // new Promise((resolve,reject)=>{
    //   reject(1)
    // }).then().then().then(null, e=>{console.log(e)})
    // new Promise((resolve,reject)=>{
    //   reject(1)
    // }).then(null,e=>{throw e}).then(null,e=>{throw e}).then(null,e=>{console.log(e)})
    onFulFilled = typeof onFulFilled === 'function' ? onFulFilled : v => v
    onRejected = typeof onRejected === 'function' ? onRejected : err => {throw err}

    // 调用then 创建一个新的promise
    let promise2 = new myPromise((resolve,reject)=>{
      // 根据value判断是resolve 还是reject value也可能是promise
      if(this.status === EMUM.FULFILLED) {
        setTimeout(() => {
          try {
            // 成功回调结果
            let x = onFulFilled(this.value)
            // 解析promise
            resolvePromise(x, promise2,resolve,reject)
          } catch (error) {
            reject(error)
          }
        }, 0);
      }
      if(this.status === EMUM.REJECTED) {
        setTimeout(() => {
          try {
            let x = onRejected(this.reason)
            // 解析promise
            resolvePromise(x, promise2,resolve,reject)
          } catch (error) {
            reject(error)
          }
        }, 0);
      }
      // 用户还未调用resolve或reject方法
      if(this.status === EMUM.PENDING) {
        this.onResolvedCallbacks.push(()=>{
          try {
            let x = onFulFilled(this.value)
            // 解析promise
            resolvePromise(x, promise2,resolve,reject)
          } catch (error) {
            reject(error)
          }
        })
        this.onRejectedCallbacks.push(()=>{
          try {
            let x = onRejected(this.reason)
            // 解析promise
            resolvePromise(x, promise2,resolve,reject)
          } catch (error) {
            reject(error)
          }
        })
      }
    })

    return promise2
  } 

  catch(errCallback) {
    // 等同于没有成功，把失败放进去而已
    return this.then(null, errCallback)
  } 

  // myPromise.resolve 具备等待功能的 如果参数的promise会等待promise解析完毕在向下执行
  static resolve(val) {
    return new myPromise((resolve,reject)=>{
      resolve(val)
    })
  } 

  // myPromise.reject 直接将值返回
  static reject(reason) {
    return new myPromise((resolve,reject)=>{
      reject(reason)
    })
  } 

  // finally传入的函数 无论成功或失败都执行
  // Promise.reject(100).finally(()=>{console.log(1)}).then(d=>console.log('success',d)).catch(er=>console.log('faild',er))
  // Promise.reject(100).finally(()=>new Promise()).then(d=>console.log(d)).catch(er=>)
  finally(callback) {
    return this.then((val)=>{
      return myPromise.resolve(callback()).then(()=>val)
    },(err)=>{
      return myPromise.resolve(callback()).then(()=>{throw err})
    })
  } 

  // Promise.all
  static all(values) {
    return new myPromise((resolve,reject)=>{
      let resultArr = []
      let orderIndex = 0
      const processResultByKey = (value,index)=>{
        resultArr[index] = value 
        // 处理完全部
        if(++orderIndex === values.length) {
          resolve(resultArr) // 处理完成的结果返回去
        }
      }
      for (let i = 0; i < values.length; i++) {
        const value = values[i];
        // 是promise
        if(value && typeof value.then === 'function') {
          value.then((val)=>{
            processResultByKey(val,i)
          },reject)
        } else {
          // 不是promise情况
          processResultByKey(value,i)
        }
      }
    })
  } 

  static race(promises) {
    // 采用最新成功或失败的作为结果
    return new myPromise((resolve,reject)=>{
      for (let i = 0; i < promises.length; i++) {
        let val = promises[i]
        if(val && typeof val.then === 'function') {
          // 任何一个promise先调用resolve或reject就返回结果了 也就是返回执行最快的那个promise的结果
          val.then(resolve,reject)
        }else{
          // 普通值
          resolve(val)
        }
      }
    })
  }
}


/**
 * =====测试用例-====
 */
// let promise1 = new myPromise((resolve,reject)=>{
//   setTimeout(() => {
//     resolve('成功')
//   }, 900);
// })

// promise1.then(val=>{
//   console.log('success', val)
// },reason=>{
//   console.log('fail', reason)
// })

/**
 * then的使用方式 普通值意味不是promise
 * 
 * 1、then中的回调有两个方法 成功或失败 他们的结果返回（普通值）会传递给外层的下一个then中
 * 2、可以在成功或失败中抛出异常，走到下一次then的失败中
 * 3、返回的是一个promsie，那么会用这个promise的状态作为结果，会用promise的结果向下传递
 * 4、错误处理，会默认先找离自己最新的错误处理，找不到就向下查找，找打了就执行
 */

// read('./name.txt').then(data=>{
//   return '123'
// }).then(data=>{

// }).then(null,err=>{

// })
// // .catch(err=>{ // catch就是没有成功的promise

// // })

/**
 * promise.then实现原理：通过每次返回一个新的promise来实现（promise一旦成功就不能失败，失败就不能成功）
 * 
 */

// function read(data) {
//   return new myPromise((resolve,reject)=>{
//     setTimeout(() => {
//       resolve(new myPromise((resolve,reject)=>resolve(data)))
//     }, 1000);
//   })
// }

// let promise2 = read({name: 'poetry'}).then(data=>{
//   return data
// }).then().then().then(data=>{
//   console.log(data,'-data-')
// },(err)=>{
//   console.log(err,'-err-')
// })

// finally测试
// myPromise
//   .resolve(100)
//   .finally(()=>{
//     return new myPromise((resolve,reject)=>setTimeout(() => {
//       resolve(100)
//     }, 100))
//   })
//   .then(d=>console.log('finally success',d))
//   .catch(er=>console.log(er, 'finally err'))


/**
 * promise.all 测试
 * 
 * myPromise.all 解决并发问题 多个异步并发获取最终的结果
*/

// myPromise.all([1,2,3,4,new myPromise((resolve,reject)=>{
//   setTimeout(() => {
//     resolve('ok1')
//   }, 1000);
// }),new myPromise((resolve,reject)=>{
//   setTimeout(() => {
//     resolve('ok2')
//   }, 1000);
// })]).then(d=>{
//   console.log(d,'myPromise.all.resolve')
// }).catch(err=>{
//   console.log(err,'myPromise.all.reject')
// })


// 实现promise中断请求
let promise = new Promise((resolve,reject)=>{
  setTimeout(() => {
    // 模拟接口调用 ajax调用超时
    resolve('成功') 
  }, 10000);
})

function promiseWrap(promise) {
  // 包装一个promise 可以控制原来的promise是成功 还是失败
  let abort
  let newPromsie = new myPromise((resolve,reject)=>{
    abort = reject
  })
  // 只要控制newPromsie失败，就可以控制被包装的promise走向失败
  // Promise.race 任何一个先成功或者失败 就可以获得结果
  let p = myPromise.race([promise, newPromsie])
  p.abort = abort

  return p
}

let newPromise = promiseWrap(promise)

setTimeout(() => {
  // 超过3秒超时
  newPromise.abort('请求超时')
}, 3000);

newPromise.then(d=>{
  console.log('d',d)
}).catch(err=>{
  console.log('err',err)
})


// 使用promises-aplus-tests 测试写的promise是否规范
// 全局安装 cnpm i -g promises-aplus-tests
// 命令行执行 promises-aplus-tests promise.js
// 测试入口 产生延迟对象
myPromise.defer = myPromise.deferred = function () {
  let dfd = {}
  dfd.promise = new myPromise((resolve,reject)=>{
    dfd.resolve = resolve
    dfd.reject = reject
  })
  return dfd
}

// 延迟对象用户
// ![](http://img-repo.poetries.top/images/20210509172817.png)
// promise解决嵌套问题
// function readData(url) {
//   let dfd = myPromise.defer()
//   fs.readFile(url, 'utf8', function (err,data) {
//     if(err) {
//       dfd.reject()
//     }
//     dfd.resolve(data)
//   })
//   return dfd.promise
// }
// readData().then(d=>{
//   return d
// })

module.exports = myPromise
```

## 实现发布订阅模式

**简介：**

发布订阅者模式，一种对象间一对多的依赖关系，但一个对象的状态发生改变时，所依赖它的对象都将得到状态改变的通知。

**主要的作用(优点)：**

1. 广泛应用于异步编程中(替代了传递回调函数)
2. 对象之间松散耦合的编写代码

**缺点：**

- 创建订阅者本身要消耗一定的时间和内存
- 多个发布者和订阅者嵌套一起的时候，程序难以跟踪维护

**实现的思路：**

- 创建一个对象(缓存列表)
- `on`方法用来把回调函数`fn`都加到缓存列表中
- `emit` 根据`key`值去执行对应缓存列表中的函数
- `off`方法可以根据`key`值取消订阅

```js
class EventEmiter {
  constructor() {
    // 事件对象，存放订阅的名字和事件
    this._events = {}
  } 

  // 订阅事件的方法
  on(eventName, callback) {
    if (!this._events) {
      this._events = {}
    } 

    // 合并之前订阅的cb
    this._events[eventName] = [...(this._events[eventName] || []), callback]
  } 

  // 触发事件的方法
  emit(eventName, ...args) {
    if (!this._events[eventName]) {
      return
    } 

    // 遍历执行所有订阅的事件
    this._events[eventName].forEach(fn => fn(...args))
  } 

  off(eventName, cb) {
    if (!this._events[eventName]) {
      return
    } 

    // 删除订阅的事件
    this._events[eventName] = this._events[eventName].filter(fn => fn != cb && fn.l != cb)
  } 

  // 绑定一次 触发后将绑定的移除掉 再次触发掉
  once(eventName, callback) {
    const one = (...args) => {
      // 等callback执行完毕在删除
      callback(args)
      this.off(eventName, one)
    } 

    one.l = callback // 自定义属性
    this.on(eventName, one)
  }
}
```

**测试用例**

```js
let event = new EventEmiter()

let login1 = function(...args) {
  console.log('login success1', args)
}
let login2 = function(...args) {
  console.log('login success2', args)
}
// event.on('login',login1)
event.once('login',login2)
event.off('login',login1) // 解除订阅
event.emit('login', 1,2,3,4,5)
event.emit('login', 6,7,8,9)
event.emit('login', 10,11,12)  
```

**发布订阅者模式和观察者模式的区别？**

- 发布/订阅模式是观察者模式的一种变形，两者区别在于，**发布/订阅模式在观察者模式的基础上，在目标和观察者之间增加一个调度中心。**
- **观察者模式**是由具体目标调度，比如当事件触发，`Subject` 就会去调用观察者的方法，所以观察者模式的订阅者与发布者之间是存在依赖的。
- **发布/订阅模式**由统一调度中心调用，因此发布者和订阅者不需要知道对方的存在。

## 实现观察者模式

> 观察者模式（基于发布订阅模式） 有观察者，也有被观察者

**观察者需要放到被观察者中，被观察者的状态变化需要通知观察者** 我变化了 内部也是基于发布订阅模式，收集观察者，状态变化后要主动通知观察者

```js
class Subject { // 被观察者 学生
  constructor(name) {
    this.state = 'happy'
    this.observers = []; // 存储所有的观察者
  }

  // 收集所有的观察者
  attach(o){ // Subject. prototype. attch
    this.observers.push(o)
  }

  // 更新被观察者 状态的方法
  setState(newState) {
    this.state = newState; // 更新状态
    // this 指被观察者 学生
    this.observers.forEach(o => o.update(this)) // 通知观察者 更新它们的状态
  }
}

class Observer{ // 观察者 父母和老师
  constructor(name) {
    this.name = name
  }

  update(student) {
    console.log('当前' + this.name + '被通知了', '当前学生的状态是' + student.state)
  }
}

let student = new Subject('学生'); 

let parent = new Observer('父母'); 
let teacher = new Observer('老师'); 

// 被观察者存储观察者的前提，需要先接纳观察者
student.attach(parent); 
student.attach(teacher); 
student.setState('被欺负了');
```

## 实现单例模式

> 核心要点: 用闭包和`Proxy`属性拦截

```js
function proxy(func) {
    let instance;
    let handler = {
        constructor(target, args) {
            if (!instance) {
                instance = Reflect.constructor(fun, args);
            } 

            return instance;
        }
    } 

    return new Proxy(func, handler);
}
```

## 实现Ajax

**步骤**

- 创建 `XMLHttpRequest` 实例
- 发出 HTTP 请求
- 服务器返回 XML 格式的字符串
- JS 解析 XML，并更新局部页面
- 不过随着历史进程的推进，XML 已经被淘汰，取而代之的是 JSON。

了解了属性和方法之后，根据 AJAX 的步骤，手写最简单的 GET 请求。

### 原生实现

```js
function ajax() {
  let xhr = new XMLHttpRequest() // 实例化，以调用方法
  xhr.open('get', 'https://www.google.com')  // 参数2，url。参数三：异步
  xhr.onreadystatechange = () => {  // 每当 readyState 属性改变时，就会调用该函数。
    if (xhr.readyState === 4) {  // XMLHttpRequest 代理当前所处状态。
      if (xhr.status >= 200 && xhr.status < 300) {  // 200-300请求成功
        let string = request.responseText
        // JSON.parse() 方法用来解析JSON字符串，构造由字符串描述的JavaScript值或对象
        let object = JSON.parse(string)
      }
    }
  }
  request.send() // 用于实际发出 HTTP 请求。不带参数为GET请求
}
```

### Promise实现

基于`Promise`封装`Ajax`

- 返回一个新的`Promise`实例
- 创建`HMLHttpRequest`异步对象
- 调用`open`方法，打开`url`，与服务器建立链接（发送前的一些处理）
- 监听`Ajax`状态信息
- 如果`xhr.readyState == 4`（表示服务器响应完成，可以获取使用服务器的响应了）
  - `xhr.status == 200`，返回`resolve`状态
  - `xhr.status == 404`，返回`reject`状态
- `xhr.readyState !== 4`，把请求主体的信息基于`send`发送给服务器

```js
function ajax(url) {
  return new Promise((resolve, reject) => {
    let xhr = new XMLHttpRequest()
    xhr.open('get', url)
    xhr.onreadystatechange = () => {
      if (xhr.readyState == 4) {
        if (xhr.status >= 200 && xhr.status <= 300) {
          resolve(JSON.parse(xhr.responseText))
        } else {
          reject('请求出错')
        }
      }
    }
    xhr.send()  //发送hppt请求
  })
}

// 测试
let url = '/data.json'
ajax(url).then(res => console.log(res))
  .catch(reason => console.log(reason))js
```

## 实现JSONP方法

> 利用`<script>`标签不受跨域限制的特点，缺点是只能支持 `get` 请求

- 创建`script`标签
- 设置`script`标签的`src`属性，以问号传递参数，设置好回调函数`callback`名称
- 插入到`html`文本中
- 调用回调函数，`res`参数就是获取的数据

```js
function jsonp({url, params, callback}) {
  return new Promise((resolve, reject) => {
    let script = document.createElement('script')

    window[callback] = function (data) {
      resolve(data)
      document.body.removeChild(script)
    } 

    var arr = [] 
    for (var key in params) {
      arr.push(`${key}=${params[key]}`)
    }

    script.type = 'text/javascript'
    script.src = `${url}?callback=${callback}&${arr.join('&')}`
    document.body.appendChild(script)
  })
}
```

```js
// 测试用例
jsonp({
  url: 'http://suggest.taobao.com/sug',
  callback: 'getData',
  params: {
    q: 'iphone手机',
    code: 'utf-8'
  },
}).then(data=>{console.log(data)})
```

- 设置 `CORS: Access-Control-Allow-Origin：*`
- `postMessage`

## 实现async/await

**分析**

```js
// generator生成器  生成迭代器iterator

// 默认这样写的类数组是不能被迭代的，缺少迭代方法
let likeArray = {'0': 1, '1': 2, '2': 3, '3': 4, length: 4}

// // 使用迭代器使得可以展开数组
// // Symbol有很多元编程方法，可以改js本身功能
// likeArray[Symbol.iterator] = function () {
//   // 迭代器是一个对象 对象中有next方法 每次调用next 都需要返回一个对象 {value,done}
//   let index = 0
//   return {
//     next: ()=>{
//       // 会自动调用这个方法
//       console.log('index',index)
//       return {
//         // this 指向likeArray
//         value: this[index],
//         done: index++ === this.length
//       }
//     }
//   }
// }
// let arr = [...likeArray]

// console.log('arr', arr)

// 使用生成器返回迭代器
// likeArray[Symbol.iterator] = function *() {
//   let index = 0
//   while (index != this.length) {
//     yield this[index++]
//   }
// }
// let arr = [...likeArray]

// console.log('arr', arr)


// 生成器 碰到yield就会暂停
// function *read(params) {
//   yield 1;
//   yield 2;
// }
// 生成器返回的是迭代器
// let it = read()
// console.log(it.next())
// console.log(it.next())
// console.log(it.next())

// 通过generator来优化promise（promise的缺点是不停的链式调用）
const fs = require('fs')
const path = require('path')
// const co = require('co') // 帮我们执行generator

const promisify = fn=>{
  return (...args)=>{
    return new Promise((resolve,reject)=>{
      fn(...args, (err,data)=>{
        if(err) {
          reject(err)
        } 
        resolve(data)
      })
    })
  }
}

// promise化
let asyncReadFile = promisify(fs.readFile)

function * read() {
  let content1 = yield asyncReadFile(path.join(__dirname,'./data/name.txt'),'utf8')
  let content2 = yield asyncReadFile(path.join(__dirname,'./data/' + content1),'utf8')
  return content2
}

// 这样写太繁琐 需要借助co来实现
// let re = read()
// let {value,done} = re.next()
// value.then(data=>{
//   // 除了第一次传参没有意义外 剩下的传参都赋予了上一次的返回值 
//   let {value,done} = re.next(data) 
//   value.then(d=>{
//     let {value,done} = re.next(d)
//     console.log(value,done)
//   })
// }).catch(err=>{
//   re.throw(err) // 手动抛出错误 可以被try catch捕获
// })



// 实现co原理
function co(it) {// it 迭代器
  return new Promise((resolve,reject)=>{
    // 异步迭代 需要根据函数来实现
    function next(data) {
      // 递归得有中止条件
      let {value,done} = it.next(data)
      if(done) {
        resolve(value) // 直接让promise变成成功 用当前返回的结果
      } else {
        // Promise.resolve(value).then(data=>{
        //   next(data)
        // }).catch(err=>{
        //   reject(err)
        // })
        // 简写
        Promise.resolve(value).then(next,reject)
      }
    }
    // 首次调用
    next()
  })
}

co(read()).then(d=>{
  console.log(d)
}).catch(err=>{
  console.log(err,'--')
})
```

**整体看一下结构**

```js
function asyncToGenerator(generatorFunc) {
    return function() {
      const gen = generatorFunc.apply(this, arguments)
      return new Promise((resolve, reject) => {
        function step(key, arg) {
          let generatorResult
          try {
            generatorResult = gen[key](arg)
          } catch (error) {
            return reject(error)
          }
          const { value, done } = generatorResult
          if (done) {
            return resolve(value)
          } else {
            return Promise.resolve(value).then(val => step('next', val), err => step('throw', err))
          }
        }
        step("next")
      })
    }
}
```

**分析**

```js
function asyncToGenerator(generatorFunc) {
  // 返回的是一个新的函数
  return function() {
    // 先调用generator函数 生成迭代器
    // 对应 var gen = testG()
    const gen = generatorFunc.apply(this, arguments)

    // 返回一个promise 因为外部是用.then的方式 或者await的方式去使用这个函数的返回值的
    // var test = asyncToGenerator(testG)
    // test().then(res => console.log(res))
    return new Promise((resolve, reject) => {
    
      // 内部定义一个step函数 用来一步一步的跨过yield的阻碍
      // key有next和throw两种取值，分别对应了gen的next和throw方法
      // arg参数则是用来把promise resolve出来的值交给下一个yield
      function step(key, arg) {
        let generatorResult
        
        // 这个方法需要包裹在try catch中
        // 如果报错了 就把promise给reject掉 外部通过.catch可以获取到错误
        try {
          generatorResult = gen[key](arg)
        } catch (error) {
          return reject(error)
        }

        // gen.next() 得到的结果是一个 { value, done } 的结构
        const { value, done } = generatorResult

        if (done) {
          // 如果已经完成了 就直接resolve这个promise
          // 这个done是在最后一次调用next后才会为true
          // 以本文的例子来说 此时的结果是 { done: true, value: 'success' }
          // 这个value也就是generator函数最后的返回值
          return resolve(value)
        } else {
          // 除了最后结束的时候外，每次调用gen.next()
          // 其实是返回 { value: Promise, done: false } 的结构，
          // 这里要注意的是Promise.resolve可以接受一个promise为参数
          // 并且这个promise参数被resolve的时候，这个then才会被调用
          return Promise.resolve(
            // 这个value对应的是yield后面的promise
            value
          ).then(
            // value这个promise被resove的时候，就会执行next
            // 并且只要done不是true的时候 就会递归的往下解开promise
            // 对应gen.next().value.then(value => {
            //    gen.next(value).value.then(value2 => {
            //       gen.next() 
            //
            //      // 此时done为true了 整个promise被resolve了 
            //      // 最外部的test().then(res => console.log(res))的then就开始执行了
            //    })
            // })
            function onResolve(val) {
              step("next", val)
            },
            // 如果promise被reject了 就再次进入step函数
            // 不同的是，这次的try catch中调用的是gen.throw(err)
            // 那么自然就被catch到 然后把promise给reject掉啦
            function onReject(err) {
              step("throw", err)
            },
          )
        }
      }
      step("next")
    })
  }
}
```

## 基于Generator函数实现async/await原理

> 核心：传递给我一个`Generator`函数，把函数中的内容基于`Iterator`迭代器的特点一步步的执行

```js
function readFile(file) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(file);
    }, 1000);
    })
};

function asyncFunc(generator) {
  const iterator = generator(); // 接下来要执行next
  // data为第一次执行之后的返回结果，用于传给第二次执行
  const next = (data) => {
        let { value, done } = iterator.next(data); // 第二次执行，并接收第一次的请求结果 data
    
    if (done) return; // 执行完毕(到第三次)直接返回
    // 第一次执行next时，yield返回的 promise实例 赋值给了 value
    value.then(data => {
      next(data); // 当第一次value 执行完毕且成功时，执行下一步(并把第一次的结果传递下一步)
    });
  }
  next();
};

asyncFunc(function* () {
    // 生成器函数：控制代码一步步执行 
  let data = yield readFile('a.js'); // 等这一步骤执行执行成功之后，再往下走，没执行完的时候，直接返回
  data = yield readFile(data + 'b.js');
  return data;
})
```
