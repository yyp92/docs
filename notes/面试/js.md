# js

## 类型及检测方式

**1. JS内置类型**

JavaScript 的数据类型有下图所示

![](C:\Users\Administrator\Desktop\docs\imgs\interview-js-1.png)

> 其中，前 7 种类型为基础类型，最后 `1 种（Object）为引用类型`，也是你需要重点关注的，因为它在日常工作中是使用得最频繁，也是需要关注最多技术细节的数据类型

- `JavaScript`一共有8种数据类型，其中有7种基本数据类型：`Undefined`、`Null`、`Boolean`、`Number`、`String`、`Symbol`（`es6`新增，表示独一无二的值）和`BigInt`（`es10`新增）；
- 1种引用数据类型——`Object`（Object本质上是由一组无序的名值对组成的）。里面包含 `function、Array、Date`等。JavaScript不支持任何创建自定义类型的机制，而所有值最终都将是上述 8 种数据类型之一。
  - **引用数据类型:** 对象`Object`（包含普通对象-`Object`，数组对象-`Array`，正则对象-`RegExp`，日期对象-`Date`，数学函数-`Math`，函数对象-`Function`）

> 在这里，我想先请你重点了解下面两点，因为各种 JavaScript 的数据类型最后都会在初始化之后放在不同的内存中，因此上面的数据类型大致可以分成两类来进行存储：

- **原始数据类型**：基础类型存储在栈内存，被引用或拷贝时，会创建一个完全相等的变量；占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储。
- **引用数据类型**：引用类型存储在堆内存，存储的是地址，多个引用指向同一个地址，这里会涉及一个“共享”的概念；占据空间大、大小不固定。引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。

**JavaScript 中的数据是如何存储在内存中的？**

> 在 JavaScript 中，原始类型的赋值会完整复制变量值，而引用类型的赋值是复制引用地址。

在 JavaScript 的执行过程中， 主要有三种类型内存空间，分别是`代码空间`、`栈空间`、`堆空间`。其中的代码空间主要是存储可执行代码的，原始类型(`Number、String、Null、Undefined、Boolean、Symbol、BigInt`)的数据值都是直接保存在“栈”中的，引用类型(Object)的值是存放在“堆”中的。因此在栈空间中(执行上下文)，原始类型存储的是变量的值，而引用类型存储的是其在"堆空间"中的地址，当 JavaScript 需要访问该数据的时候，是通过栈中的引用地址来访问的，相当于多了一道转手流程。

在编译过程中，如果 JavaScript 引擎判断到一个闭包，也会在堆空间创建换一个`“closure(fn)”`的对象（这是一个内部对象，JavaScript 是无法访问的），用来保存闭包中的变量。所以闭包中的变量是存储在“堆空间”中的。

JavaScript 引擎需要用栈来维护程序执行期间上下文的状态，如果栈空间大了话，所有的数据都存放在栈空间里面，那么会影响到上下文切换的效率，进而又影响到整个程序的执行效率。通常情况下，栈空间都不会设置太大，主要用来存放一些原始类型的小数据。而引用类型的数据占用的空间都比较大，所以这一类数据会被存放到堆中，堆空间很大，能存放很多大的数据，不过缺点是分配内存和回收内存都会占用一定的时间。因此需要“栈”和“堆”两种空间。

> 题目一：初出茅庐

```js
let a = {
  name: 'lee',
  age: 18
}
let b = a;
console.log(a.name);  //第一个console
b.name = 'son';
console.log(a.name);  //第二个console
console.log(b.name);  //第三个console
```

> 这道题比较简单，我们可以看到第一个 console 打出来 name 是 'lee'，这应该没什么疑问；但是在执行了 b.name='son' 之后，结果你会发现 a 和 b 的属性 name 都是 'son'，第二个和第三个打印结果是一样的，这里就体现了引用类型的“共享”的特性，即这两个值都存在同一块内存中共享，一个发生了改变，另外一个也随之跟着变化。

你可以直接在 Chrome 控制台敲一遍，深入理解一下这部分概念。下面我们再看一段代码，它是比题目一稍复杂一些的对象属性变化问题。

> 题目二：渐入佳境

```js
let a = {
  name: 'Julia',
  age: 20
}
function change(o) {
  o.age = 24;
  o = {
    name: 'Kath',
    age: 30
  }
  return o;
}
let b = change(a);     // 注意这里没有new，后面new相关会有专门文章讲解
console.log(b.age);    // 第一个console
console.log(a.age);    // 第二个console
```

这道题涉及了 `function`，你通过上述代码可以看到第一个 `console` 的结果是 `30`，`b` 最后打印结果是 `{name: "Kath", age: 30}`；第二个 `console` 的返回结果是 `24`，而 `a` 最后的打印结果是 `{name: "Julia", age: 24}`。

是不是和你预想的有些区别？你要注意的是，这里的 `function` 和 `return` 带来了不一样的东西。

> 原因在于：函数传参进来的 `o`，传递的是对象在堆中的内存地址值，通过调用 `o.age = 24`（第 7 行代码）确实改变了 `a` 对象的 `age` 属性；但是第 12 行代码的 `return` 却又把 `o` 变成了另一个内存地址，将 `{name: "Kath", age: 30}` 存入其中，最后返回 `b` 的值就变成了 `{name: "Kath", age: 30}`。而如果把第 12 行去掉，那么 `b` 就会返回 `undefined`

**2. 数据类型检测**

**（1）typeof**

> typeof 对于原始类型来说，除了 null 都可以显示正确的类型

```js
console.log(typeof 2);               // number
console.log(typeof true);            // boolean
console.log(typeof 'str');           // string
console.log(typeof []);              // object     []数组的数据类型在 typeof 中被解释为 object
console.log(typeof function(){});    // function
console.log(typeof {});              // object
console.log(typeof undefined);       // undefined
console.log(typeof null);            // object     null 的数据类型被 typeof 解释为 object
```

> `typeof` 对于对象来说，除了函数都会显示 `object`，所以说 `typeof` 并不能准确判断变量到底是什么类型,所以想判断一个对象的正确类型，这时候可以考虑使用 `instanceof`

**（2）instanceof**

> `instanceof` 可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的 `prototype`

```js
console.log(2 instanceof Number);                    // false
console.log(true instanceof Boolean);                // false 
console.log('str' instanceof String);                // false  
console.log([] instanceof Array);                    // true
console.log(function(){} instanceof Function);       // true
console.log({} instanceof Object);                   // true    
// console.log(undefined instanceof Undefined);
// console.log(null instanceof Null);
```

- `instanceof` 可以准确地判断复杂引用数据类型，但是不能正确判断基础数据类型；
- 而 `typeof` 也存在弊端，它虽然可以判断基础数据类型（`null` 除外），但是引用数据类型中，除了 `function` 类型以外，其他的也无法判断

```js
// 我们也可以试着实现一下 instanceof
function _instanceof(left, right) {
    // 由于instance要检测的是某对象，需要有一个前置判断条件
    //基本数据类型直接返回false
    if(typeof left !== 'object' || left === null) return false;

    // 获得类型的原型
    let prototype = right.prototype
    // 获得对象的原型
    left = left.__proto__
    // 判断对象的类型是否等于类型的原型
    while (true) {
        if (left === null)
            return false
        if (prototype === left)
            return true
        left = left.__proto__
    }
}

console.log('test', _instanceof(null, Array)) // false
console.log('test', _instanceof([], Array)) // true
console.log('test', _instanceof('', Array)) // false
console.log('test', _instanceof({}, Object)) // true
```

**（3）constructor**

```js
console.log((2).constructor === Number); // true
console.log((true).constructor === Boolean); // true
console.log(('str').constructor === String); // true
console.log(([]).constructor === Array); // true
console.log((function() {}).constructor === Function); // true
console.log(({}).constructor === Object); // true
```

> 这里有一个坑，如果我创建一个对象，更改它的原型，`constructor`就会变得不可靠了

```js
function Fn(){};

Fn.prototype=new Array();

var f=new Fn();

console.log(f.constructor===Fn);    // false
console.log(f.constructor===Array); // true 
```

**（4）Object.prototype.toString.call()**

> `toString()` 是 `Object` 的原型方法，调用该方法，可以统一返回格式为 `“[object Xxx]”` 的字符串，其中 `Xxx` 就是对象的类型。对于 `Object` 对象，直接调用 `toString()` 就能返回 `[object Object]`；而对于其他对象，则需要通过 `call` 来调用，才能返回正确的类型信息。我们来看一下代码。

```js
Object.prototype.toString({})       // "[object Object]"
Object.prototype.toString.call({})  // 同上结果，加上call也ok
Object.prototype.toString.call(1)    // "[object Number]"
Object.prototype.toString.call('1')  // "[object String]"
Object.prototype.toString.call(true)  // "[object Boolean]"
Object.prototype.toString.call(function(){})  // "[object Function]"
Object.prototype.toString.call(null)   //"[object Null]"
Object.prototype.toString.call(undefined) //"[object Undefined]"
Object.prototype.toString.call(/123/g)    //"[object RegExp]"
Object.prototype.toString.call(new Date()) //"[object Date]"
Object.prototype.toString.call([])       //"[object Array]"
Object.prototype.toString.call(document)  //"[object HTMLDocument]"
Object.prototype.toString.call(window)   //"[object Window]" 


// 从上面这段代码可以看出，Object.prototype.toString.call() 可以很好地判断引用类型，甚至可以把 document 和 window 都区分开来。
```

> 实现一个全局通用的数据类型判断方法，来加深你的理解，代码如下

```js
function getType(obj){
  let type = typeof obj;

  if (type !== "object") {    // 先进行typeof判断，如果是基础数据类型，直接返回
    return type;
  }
  // 对于typeof返回结果是object的，再进行如下的判断，正则返回结果
  return Object.prototype.toString.call(obj).replace(/^\[object (\S+)\]$/, '$1');  // 注意正则中间有个空格
}
/* 代码验证，需要注意大小写，哪些是typeof判断，哪些是toString判断？思考下 */
getType([])     // "Array" typeof []是object，因此toString返回
getType('123')  // "string" typeof 直接返回
getType(window) // "Window" toString返回
getType(null)   // "Null"首字母大写，typeof null是object，需toString来判断
getType(undefined)   // "undefined" typeof 直接返回
getType()            // "undefined" typeof 直接返回
getType(function(){}) // "function" typeof能判断，因此首字母小写
getType(/123/g)      //"RegExp" toString返回
```

**小结**

- `typeof`
  - 直接在计算机底层基于数据类型的值（二进制）进行检测
  - `typeof null`为`object` 原因是对象存在在计算机中，都是以`000`开始的二进制存储，所以检测出来的结果是对象
  - `typeof` 普通对象/数组对象/正则对象/日期对象 都是`object`
  - `typeof NaN === 'number'`
- `instanceof`
  - 检测当前实例是否属于这个类的
  - 底层机制：只要当前类出现在实例的原型上，结果都是true
  - 不能检测基本数据类型
- `constructor`
  - 支持基本类型
  - constructor可以随便改，也不准
- `Object.prototype.toString.call([val])`
  - 返回当前实例所属类信息

> 判断 `Target` 的类型，单单用 `typeof` 并无法完全满足，这其实并不是 `bug`，本质原因是 `JS` 的万物皆对象的理论。因此要真正完美判断时，我们需要区分对待:

- 基本类型(`null`): 使用 `String(null)`
- 基本类型(`string / number / boolean / undefined`) + `function`: - 直接使用 `typeof`即可
- 其余引用类型(`Array / Date / RegExp Error`): 调用`toString`后根据`[object XXX]`进行判断

**3. 数据类型转换**

我们先看一段代码，了解下大致的情况。

```js
'123' == 123   // false or true?  true
'' == null    // false or true?  false
'' == 0        // false or true?  true
[] == 0        // false or true?  true
[] == ''       // false or true?  true
[] == ![]      // false or true?  true
null == undefined //  false or true?  true
Number(null)     // 返回什么？  0
Number('')      // 返回什么？ 0
parseInt('');    // 返回什么？  NaN
{}+10           // 返回什么？  10
let obj = {
    [Symbol.toPrimitive]() {
        return 200;
    },
    valueOf() {
        return 300;
    },
    toString() {
        return 'Hello';
    }
}
console.log(obj + 200); // 这里打印出来是多少？ 400
```

> 首先我们要知道，在 `JS` 中类型转换只有三种情况，分别是：

- 转换为布尔值
- 转换为数字
- 转换为字符串

![](../../\imgs\interview-js-2.png)

**转Boolean**

> 在条件判断时，除了 `undefined`，`null`， `false`， `NaN`， `''`， `0`， `-0`，其他所有值都转为 `true`，包括所有对象

```js
Boolean(0)          //false
Boolean(null)       //false
Boolean(undefined)  //false
Boolean(NaN)        //false
Boolean(1)          //true
Boolean(13)         //true
Boolean('12')       //true
```

**对象转原始类型**

> 对象在转换类型的时候，会调用内置的 `[[ToPrimitive]]` 函数，对于该函数来说，算法逻辑一般来说如下

- 如果已经是原始类型了，那就不需要转换了
- 调用 `x.valueOf()`，如果转换为基础类型，就返回转换的值
- 调用 `x.toString()`，如果转换为基础类型，就返回转换的值
- 如果都没有返回原始类型，就会报错

> 当然你也可以重写 `Symbol.toPrimitive`，该方法在转原始类型时调用优先级最高。

```js
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  },
  [Symbol.toPrimitive]() {
    return 2
  }
}
1 + a // => 3
```

**四则运算符**

> 它有以下几个特点：
> 
> - 运算中其中一方为字符串，那么就会把另一方也转换为字符串
> - 如果一方不是字符串或者数字，那么会将它转换为数字或者字符串

```js
1 + '1' // '11'
true + true // 2
4 + [1,2,3] // "41,2,3"
```

- 对于第一行代码来说，触发特点一，所以将数字 `1` 转换为字符串，得到结果 `'11'`
- 对于第二行代码来说，触发特点二，所以将 `true` 转为数字 `1`
- 对于第三行代码来说，触发特点二，所以将数组通过 `toString`转为字符串 `1,2,3`，得到结果 `41,2,3`

> 另外对于加法还需要注意这个表达式 `'a' + + 'b'`

```js
'a' + + 'b' // -> "aNaN"
```

- 因为 `+ 'b'` 等于 `NaN`，所以结果为 `"aNaN"`，你可能也会在一些代码中看到过 `+ '1'`的形式来快速获取 `number` 类型。
- 那么对于除了加法的运算符来说，只要其中一方是数字，那么另一方就会被转为数字

```js
4 * '3' // 12
4 * [] // 0
4 * [1, 2] // NaN
```

**比较运算符**

- 如果是对象，就通过 `toPrimitive` 转换对象
- 如果是字符串，就通过 `unicode` 字符索引来比较

```js
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  }
}
a > -1 // true
```

> 在以上代码中，因为 `a` 是对象，所以会通过 `valueOf` 转换为原始类型再比较值。

**强制类型转换**

> 强制类型转换方式包括 `Number()`、`parseInt()`、`parseFloat()`、`toString()`、`String()`、`Boolean()`，这几种方法都比较类似

- `Number()` 方法的强制转换规则
  
  - 如果是布尔值，`true` 和 `false` 分别被转换为 `1` 和 `0`；
  - 如果是数字，返回自身；
  - 如果是 `null`，返回 `0`；
  - 如果是 `undefined`，返回 `NaN`；
  - 如果是字符串，遵循以下规则：如果字符串中只包含数字（或者是 `0X / 0x` 开头的十六进制数字字符串，允许包含正负号），则将其转换为十进制；如果字符串中包含有效的浮点格式，将其转换为浮点数值；如果是空字符串，将其转换为 `0`；如果不是以上格式的字符串，均返回 NaN；
  - 如果是 `Symbol`，抛出错误；
  - 如果是对象，并且部署了 `[Symbol.toPrimitive]` ，那么调用此方法，否则调用对象的 `valueOf()` 方法，然后依据前面的规则转换返回的值；如果转换的结果是 `NaN` ，则调用对象的 `toString()` 方法，再次依照前面的顺序转换返回对应的值。
  
  ```js
  Number(true);        // 1
  Number(false);       // 0
  Number('0111');      //111
  Number(null);        //0
  Number('');          //0
  Number('1a');        //NaN
  Number(-0X11);       //-17
  Number('0X11')       //17
  ```

**Object 的转换规则**

> 对象转换的规则，会先调用内置的 `[ToPrimitive]` 函数，其规则逻辑如下：

- 如果部署了 `Symbol.toPrimitive` 方法，优先调用再返回；
- 调用 `valueOf()`，如果转换为基础类型，则返回；
- 调用 `toString()`，如果转换为基础类型，则返回；
- 如果都没有返回基础类型，会报错。

```js
var obj = {
  value: 1,
  valueOf() {
    return 2;
  },
  toString() {
    return '3'
  },
  [Symbol.toPrimitive]() {
    return 4
  }
}
console.log(obj + 1); // 输出5
// 因为有Symbol.toPrimitive，就优先执行这个；如果Symbol.toPrimitive这段代码删掉，则执行valueOf打印结果为3；如果valueOf也去掉，则调用toString返回'31'(字符串拼接)
// 再看两个特殊的case：
10 + {}
// "10[object Object]"，注意：{}会默认调用valueOf是{}，不是基础类型继续转换，调用toString，返回结果"[object Object]"，于是和10进行'+'运算，按照字符串拼接规则来，参考'+'的规则C
[1,2,undefined,4,5] + 10
// "1,2,,4,510"，注意[1,2,undefined,4,5]会默认先调用valueOf结果还是这个数组，不是基础数据类型继续转换，也还是调用toString，返回"1,2,,4,5"，然后再和10进行运算，还是按照字符串拼接规则，参考'+'的第3条规则
```

**'==' 的隐式类型转换规则**

- 如果类型相同，无须进行类型转换；
- 如果其中一个操作值是 `null` 或者 `undefined`，那么另一个操作符必须为 `null` 或者 `undefined`，才会返回 `true`，否则都返回 `false`；
- 如果其中一个是 `Symbol` 类型，那么返回 `false`；
- 两个操作值如果为 `string` 和 number 类型，那么就会将字符串转换为 `number`；
- 如果一个操作值是 `boolean`，那么转换成 `number`；
- 如果一个操作值为 `object` 且另一方为 `string`、`number` 或者 `symbol`，就会把 `object` 转为原始类型再进行判断（调用 `object` 的 `valueOf/toString` 方法进行转换）。

```js
null == undefined       // true  规则2
null == 0               // false 规则2
'' == null              // false 规则2
'' == 0                 // true  规则4 字符串转隐式转换成Number之后再对比
'123' == 123            // true  规则4 字符串转隐式转换成Number之后再对比
0 == false              // true  e规则 布尔型隐式转换成Number之后再对比
1 == true               // true  e规则 布尔型隐式转换成Number之后再对比
var a = {
  value: 0,
  valueOf: function() {
    this.value++;
    return this.value;
  }
};
// 注意这里a又可以等于1、2、3
console.log(a == 1 && a == 2 && a ==3);  //true f规则 Object隐式转换
// 注：但是执行过3遍之后，再重新执行a==3或之前的数字就是false，因为value已经加上去了，这里需要注意一下
```

**'+' 的隐式类型转换规则**

> '+' 号操作符，不仅可以用作数字相加，还可以用作字符串拼接。仅当 '+' 号两边都是数字时，进行的是加法运算；如果两边都是字符串，则直接拼接，无须进行隐式类型转换。

- 如果其中有一个是字符串，另外一个是 `undefined`、`null` 或布尔型，则调用 `toString()` 方法进行字符串拼接；如果是纯对象、数组、正则等，则默认调用对象的转换方法会存在优先级，然后再进行拼接。
- 如果其中有一个是数字，另外一个是 `undefined`、`null`、布尔型或数字，则会将其转换成数字进行加法运算，对象的情况还是参考上一条规则。
- 如果其中一个是字符串、一个是数字，则按照字符串规则进行拼接

```js
1 + 2        // 3  常规情况
'1' + '2'    // '12' 常规情况
// 下面看一下特殊情况
'1' + undefined   // "1undefined" 规则1，undefined转换字符串
'1' + null        // "1null" 规则1，null转换字符串
'1' + true        // "1true" 规则1，true转换字符串
'1' + 1n          // '11' 比较特殊字符串和BigInt相加，BigInt转换为字符串
1 + undefined     // NaN  规则2，undefined转换数字相加NaN
1 + null          // 1    规则2，null转换为0
1 + true          // 2    规则2，true转换为1，二者相加为2
1 + 1n            // 错误  不能把BigInt和Number类型直接混合相加
'1' + 3           // '13' 规则3，字符串拼接
```

> 整体来看，如果数据中有字符串，JavaScript 类型转换还是更倾向于转换成字符串，因为第三条规则中可以看到，在字符串和数字相加的过程中最后返回的还是字符串，这里需要关注一下

**null 和 undefined 的区别？**

- 首先 `Undefined` 和 `Null` 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 `undefined` 和 `null`。
- `undefined` 代表的含义是未定义， `null` 代表的含义是空对象（其实不是真的对象，请看下面的注意！）。一般变量声明了但还没有定义的时候会返回 `undefined`，`null` 主要用于赋值给一些可能会返回对象的变量，作为初始化。

> 其实 null 不是对象，虽然 typeof null 会输出 object，但是这只是 JS 存在的一个悠久 Bug。在 JS 的最初版本中使用的是 32 位系统，为了性能考虑使用低位存储变量的类型信息，000 开头代表是对象，然而 null 表示为全零，所以将它错误的判断为 object 。虽然现在的内部类型判断代码已经改变了，但是对于这个 Bug 却是一直流传下来。

- undefined 在 js 中不是一个保留字，这意味着我们可以使用 `undefined` 来作为一个变量名，这样的做法是非常危险的，它会影响我们对 undefined 值的判断。但是我们可以通过一些方法获得安全的 `undefined` 值，比如说 `void 0`。
- 当我们对两种类型使用 typeof 进行判断的时候，Null 类型化会返回 “object”，这是一个历史遗留的问题。当我们使用双等号对两种类型的值进行比较时会返回 true，使用三个等号时会返回 false。

## This

> 不同情况的调用，`this`指向分别如何。顺带可以提一下 `es6` 中箭头函数没有 `this`, `arguments`, `super` 等，这些只依赖包含箭头函数最接近的函数

> 我们先来看几个函数调用的场景

```js
function foo() {
  console.log(this.a)
}
var a = 1
foo()

const obj = {
  a: 2,
  foo: foo
}
obj.foo()

const c = new foo()
```

- 对于直接调用 `foo` 来说，不管 `foo` 函数被放在了什么地方，`this` 一定是`window`
- 对于 `obj.foo()` 来说，我们只需要记住，谁调用了函数，谁就是 `this`，所以在这个场景下 `foo` 函数中的 `this` 就是 `obj` 对象
- 对于 `new` 的方式来说，`this` 被永远绑定在了 `c` 上面，不会被任何方式改变 `this`

> 说完了以上几种情况，其实很多代码中的 `this` 应该就没什么问题了，下面让我们看看箭头函数中的 `this`

```js
function a() {
  return () => {
    return () => {
      console.log(this)
    }
  }
}
console.log(a()()())
```

- 首先箭头函数其实是没有 `this` 的，箭头函数中的 `this` 只取决包裹箭头函数的第一个普通函数的 `this`。在这个例子中，因为包裹箭头函数的第一个普通函数是 `a`，所以此时的 `this` 是 `window`。另外对箭头函数使用 `bind`这类函数是无效的。
- 最后种情况也就是 `bind` 这些改变上下文的 `API` 了，对于这些函数来说，`this` 取决于第一个参数，如果第一个参数为空，那么就是 `window`。
- 那么说到 `bind`，不知道大家是否考虑过，如果对一个函数进行多次 `bind`，那么上下文会是什么呢？

```js
let a = {}
let fn = function () { console.log(this) }
fn.bind().bind(a)() // => ?
```

> 如果你认为输出结果是 `a`，那么你就错了，其实我们可以把上述代码转换成另一种形式

```js
// fn.bind().bind(a) 等于
let fn2 = function fn1() {
  return function() {
    return fn.apply()
  }.apply(a)
}
fn2()
```

> 可以从上述代码中发现，不管我们给函数 `bind` 几次，`fn` 中的 `this` 永远由第一次 `bind` 决定，所以结果永远是 `window`

```js
let a = { name: 'poetries' }
function foo() {
  console.log(this.name)
}
foo.bind(a)() // => 'poetries'
```

> 以上就是 `this` 的规则了，但是可能会发生多个规则同时出现的情况，这时候不同的规则之间会根据优先级最高的来决定 `this` 最终指向哪里。

> 首先，`new` 的方式优先级最高，接下来是 `bind` 这些函数，然后是 `obj.foo()` 这种调用方式，最后是 `foo` 这种调用方式，同时，箭头函数的 `this` 一旦被绑定，就不会再被任何方式所改变。

![](../../\imgs\interview-js-3.png)

**函数执行改变this**

- 由于 JS 的设计原理: 在函数中，可以引用运行环境中的变量。因此就需要一个机制来让我们可以在函数体内部获取当前的运行环境，这便是`this`。

> 因此要明白 `this` 指向，其实就是要搞清楚 函数的运行环境，说人话就是，谁调用了函数。例如

- `obj.fn()`，便是 `obj` 调用了函数，既函数中的 `this === obj`
- `fn()`，这里可以看成 `window.fn()`，因此 `this === window`

> 但这种机制并不完全能满足我们的业务需求，因此提供了三种方式可以手动修改 `this` 的指向:

- `call: fn.call(target, 1, 2)`
- `apply: fn.apply(target, [1, 2])`
- `bind: fn.bind(target)(1,2)`

## apply/call/bind 原理

![](../../\imgs\interview-js-4.png)

> `call、apply` 和 `bind` 是挂在 `Function` 对象上的三个方法，调用这三个方法的必须是一个函数。

```js
func.call(thisArg, param1, param2, ...)
func.apply(thisArg, [param1,param2,...])
func.bind(thisArg, param1, param2, ...)
```

- 在浏览器里，在全局范围内this 指向window对象；
- 在函数中，this永远指向最后调用他的那个对象；
- 构造函数中，this指向new出来的那个新的对象；
- `call、apply、bind`中的this被强绑定在指定的那个对象上；
- 箭头函数中this比较特殊,箭头函数this为父作用域的this，不是调用时的this.要知道前四种方式,都是调用时确定,也就是动态的,而箭头函数的this指向是静态的,声明的时候就确定了下来；
- `apply、call、bind`都是js给函数内置的一些API，调用他们可以为函数指定this的执行,同时也可以传参。

![](C:\Users\Administrator\Desktop\docs\imgs\interview-js-5.png)

```js
let a = {
    value: 1
}
function getValue(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value)
}
getValue.call(a, 'poe', '24')
getValue.apply(a, ['poe', '24'])
```

> `bind` 和其他两个方法作用也是一致的，只是该方法会返回一个函数。并且我们可以通过 `bind` 实现柯里化

**方法的应用场景**

> 下面几种应用场景，你多加体会就可以发现它们的理念都是“借用”方法的思路。我们来看看都有哪些。

1. 判断数据类型

> 用 `Object.prototype.toString` 来判断类型是最合适的，借用它我们几乎可以判断所有类型的数据

```js
function getType(obj){
  let type  = typeof obj;
  if (type !== "object") {
    return type;
  }
  return Object.prototype.toString.call(obj).replace(/^$/, '$1');
}
```

2. 类数组借用方法

> 类数组因为不是真正的数组，所有没有数组类型上自带的种种方法，所以我们就可以利用一些方法去借用数组的方法，比如借用数组的 `push` 方法，看下面的一段代码。

```js
var arrayLike = { 
  0: 'java',
  1: 'script',
  length: 2
} 
Array.prototype.push.call(arrayLike, 'jack', 'lily'); 
console.log(typeof arrayLike); // 'object'
console.log(arrayLike);
// {0: "java", 1: "script", 2: "jack", 3: "lily", length: 4}
```

> 用 `call` 的方法来借用 `Array 原型链上的 push` 方法，可以实现一个`类数组的 push` 方法，给 `arrayLike` 添加新的元素。

3. 获取数组的最大 / 最小值

> 我们可以用 apply 来实现数组中判断最大 / 最小值，`apply` 直接传递数组作为调用方法的参数，也可以减少一步展开数组，可以直接使用 `Math.max、Math.min` 来获取数组的最大值 / 最小值，请看下面这段代码。

```js
let arr = [13, 6, 10, 11, 16];
const max = Math.max.apply(Math, arr); 
const min = Math.min.apply(Math, arr);

console.log(max);  // 16
console.log(min);  // 6
```

**实现一个 bind 函数**

对于实现以下几个函数，可以从几个方面思考

- 不传入第一个参数，那么默认为 `window`
- 改变了 `this` 指向，让新的对象可以执行该函数。那么思路是否可以变成给新的对象添加一个函数，然后在执行完以后删除？

```js
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
```

**实现一个 call 函数**

```js
Function.prototype.myCall = function (context) {
  var context = context || window
  // 给 context 添加一个属性
  // getValue.call(a, 'pp', '24') => a.fn = getValue
  context.fn = this
  // 将 context 后面的参数取出来
  var args = [...arguments].slice(1)
  // getValue.call(a, 'pp', '24') => a.fn('pp', '24')
  var result = context.fn(...args)
  // 删除 fn
  delete context.fn
  return result
}
```

**实现一个 apply 函数**

```js
Function.prototype.myApply = function(context = window, ...args) {
  // this-->func  context--> obj  args--> 传递过来的参数

  // 在context上加一个唯一值不影响context上的属性
  let key = Symbol('key')
  context[key] = this; // context为调用的上下文,this此处为函数，将这个函数作为context的方法
  // let args = [...arguments].slice(1)   //第一个参数为obj所以删除,伪数组转为数组

  let result = context[key](...args); 
  delete context[key]; // 不删除会导致context属性越来越多
  return result;
}
```

```js
// 使用
function f(a,b){
 console.log(a,b)
 console.log(this.name)
}
let obj={
 name:'张三'
}
f.myApply(obj,[1,2])  //arguments[1]
```

## 变量提升

> 当执行 `JS` 代码时，会生成执行环境，只要代码不是写在函数中的，就是在全局执行环境中，函数中的代码会产生函数执行环境，只此两种执行环境。

```js
b() // call b
console.log(a) // undefined

var a = 'Hello world'

function b() {
    console.log('call b')
}
```

> 想必以上的输出大家肯定都已经明白了，这是因为函数和变量提升的原因。通常提升的解释是说将声明的代码移动到了顶部，这其实没有什么错误，便于大家理解。但是更准确的解释应该是：在生成执行环境时，会有两个阶段。第一个阶段是创建的阶段，`JS` 解释器会找出需要提升的变量和函数，并且给他们提前在内存中开辟好空间，函数的话会将整个函数存入内存中，变量只声明并且赋值为 `undefined`，所以在第二个阶段，也就是代码执行阶段，我们可以直接提前使用

- 在提升的过程中，相同的函数会覆盖上一个函数，并且函数优先于变量提升

```js
b() // call b second

function b() {
    console.log('call b fist')
}
function b() {
    console.log('call b second')
}
var b = 'Hello world'
```

> `var` 会产生很多错误，所以在 ES6中引入了 `let`。`let`不能在声明前使用，但是这并不是常说的 `let` 不会提升，`let`提升了，在第一阶段内存也已经为他开辟好了空间，但是因为这个声明的特性导致了并不能在声明前使用。

## 执行上下文

> 当执行 JS 代码时，会产生三种执行上下文
> 
> - 全局执行上下文
> - 函数执行上下文
> - `eval` 执行上下文

> 每个执行上下文中都有三个重要的属性
> 
> - 变量对象（`VO`），包含变量、函数声明和函数的形参，该属性只能在全局上下文中访问
> - 作用域链（`JS` 采用词法作用域，也就是说变量的作用域是在定义时就决定了）
> - `this`

```js
var a = 10
function foo(i) {
  var b = 20
}
foo()
```

> 对于上述代码，执行栈中有两个上下文：全局上下文和函数 foo 上下文。
> 
> ```js
> stack = [
>     globalContext,
>     fooContext 
> ]
> ```

> 对于全局上下文来说，`VO`大概是这样的
> 
> ```js
> globalContext.VO === globe
> globalContext.VO = {
>     a: undefined,
>     foo: <Function>,
> }
> ```

> 对于函数 `foo` 来说，`VO` 不能访问，只能访问到活动对象（`AO`）
> 
> ```js
> fooContext.VO === foo.AO
> fooContext.AO {
>     i: undefined,
>     b: undefined,
>     arguments: <>
> }
> // arguments 是函数独有的对象(箭头函数没有)
> // 该对象是一个伪数组，有 `length` 属性且可以通过下标访问元素
> // 该对象中的 `callee` 属性代表函数本身
> // `caller` 属性代表函数的调用者
> ```

> 对于作用域链，可以把它理解成包含自身变量对象和上级变量对象的列表，通过 `[[Scope]]`属性查找上级变量
> 
> ```js
> fooContext.[[Scope]] = [
>     globalContext.VO
> ]
> fooContext.Scope = fooContext.[[Scope]] + fooContext.VO
> fooContext.Scope = [
>     fooContext.VO,
>     globalContext.VO
> ]
> ```

> 接下来让我们看一个老生常谈的例子，`var`

```js
b() // call b
console.log(a) // undefined

var a = 'Hello world'

function b() {
    console.log('call b')
}
```

> 想必以上的输出大家肯定都已经明白了，这是因为函数和变量提升的原因。通常提升的解释是说将声明的代码移动到了顶部，这其实没有什么错误，便于大家理解。但是更准确的解释应该是：在生成执行上下文时，会有两个阶段。第一个阶段是创建的阶段（具体步骤是创建 `VO`），`JS` 解释器会找出需要提升的变量和函数，并且给他们提前在内存中开辟好空间，函数的话会将整个函数存入内存中，变量只声明并且赋值为 `undefined`，所以在第二个阶段，也就是代码执行阶段，我们可以直接提前使用。

- 在提升的过程中，相同的函数会覆盖上一个函数，并且函数优先于变量提升

```js
b() // call b second

function b() {
    console.log('call b fist')
}
function b() {
    console.log('call b second')
}
var b = 'Hello world'
```

> `var`会产生很多错误，所以在 `ES6`中引入了 `let`。`let`不能在声明前使用，但是这并不是常说的 `let` 不会提升，`let` 提升了声明但没有赋值，因为临时死区导致了并不能在声明前使用。

- 对于非匿名的立即执行函数需要注意以下一点

```js
var foo = 1
(function foo() {
    foo = 10
    console.log(foo)
}()) // -> ƒ foo() { foo = 10 ; console.log(foo) }
```

> 因为当 `JS` 解释器在遇到非匿名的立即执行函数时，会创建一个辅助的特定对象，然后将函数名称作为这个对象的属性，因此函数内部才可以访问到 `foo`，但是这个值又是只读的，所以对它的赋值并不生效，所以打印的结果还是这个函数，并且外部的值也没有发生更改。

```js
specialObject = {};

Scope = specialObject + Scope;

foo = new FunctionExpression;
foo.[[Scope]] = Scope;
specialObject.foo = foo; // {DontDelete}, {ReadOnly}

delete Scope[0]; // remove specialObject from the front of scope chain
```

**总结**

> 执行上下文可以简单理解为一个对象:
> 
> **它包含三个部分:**
> 
> - 变量对象(`VO`)
> - 作用域链(词法作用域)
> - `this`指向
> 
> **它的类型:**
> 
> - 全局执行上下文
> - 函数执行上下文
> - `eval`执行上下文
> 
> **代码执行过程:**
> 
> - 创建 全局上下文 (`global EC`)
> - 全局执行上下文 (`caller`) 逐行 自上而下 执行。遇到函数时，函数执行上下文 (`callee`) 被`push`到执行栈顶层
> - 函数执行上下文被激活，成为 `active EC`, 开始执行函数中的代码，`caller` 被挂起
> - 函数执行完后，`callee` 被`pop`移除出执行栈，控制权交还全局上下文 (`caller`)，继续执行

## 作用域

- 作用域： 作用域是定义变量的区域，它有一套访问变量的规则，这套规则来管理浏览器引擎如何在当前作用域以及嵌套的作用域中根据变量（标识符）进行变量查找
- 作用域链： 作用域链的作用是保证对执行环境有权访问的所有变量和函数的有序访问，通过作用域链，我们可以访问到外层环境的变量和 函数。

> 作用域链的本质上是一个指向变量对象的指针列表。变量对象是一个包含了执行环境中所有变量和函数的对象。作用域链的前 端始终都是当前执行上下文的变量对象。全局执行上下文的变量对象（也就是全局对象）始终是作用域链的最后一个对象。

- 当我们查找一个变量时，如果当前执行环境中没有找到，我们可以沿着作用域链向后查找
- 作用域链的创建过程跟执行上下文的建立有关....

> 作用域可以理解为变量的可访问性，总共分为三种类型，分别为：
> 
> - 全局作用域
> - 函数作用域
> - 块级作用域，ES6 中的 `let`、`const` 就可以产生该作用域

其实看完前面的闭包、`this` 这部分内部的话，应该基本能了解作用域的一些应用。

一旦我们将这些作用域嵌套起来，就变成了另外一个重要的知识点「作用域链」，也就是 JS 到底是如何访问需要的变量或者函数的。

- 首先作用域链是在定义时就被确定下来的，和箭头函数里的 this 一样，后续不会改变，JS 会一层层往上寻找需要的内容。
- 其实作用域链这个东西我们在闭包小结中已经看到过它的实体了：`[[Scopes]]`

![](../../\imgs\interview-js-6.png)

图中的 `[[Scopes]]` 是个数组，作用域的一层层往上寻找就等同于遍历 `[[Scopes]]`。

**1. 全局作用域**

> 全局变量是挂载在 window 对象下的变量，所以在网页中的任何位置你都可以使用并且访问到这个全局变量

```js
var globalName = 'global';
function getName() { 
  console.log(globalName) // global
  var name = 'inner'
  console.log(name) // inner
} 
getName();
console.log(name); // 
console.log(globalName); //global
function setName(){ 
  vName = 'setName';
}
setName();
console.log(vName); // setName
```

- 从这段代码中我们可以看到，globalName 这个变量无论在什么地方都是可以被访问到的，所以它就是全局变量。而在 getName 函数中作为局部变量的 name 变量是不具备这种能力的
- 当然全局作用域有相应的缺点，我们定义很多全局变量的时候，会容易引起变量命名的冲突，所以在定义变量的时候应该注意作用域的问题。

**2. 函数作用域**

> 函数中定义的变量叫作函数变量，这个时候只能在函数内部才能访问到它，所以它的作用域也就是函数的内部，称为函数作用域

```js
function getName () {
  var name = 'inner';
  console.log(name); //inner
}
getName();
console.log(name);
```

> 除了这个函数内部，其他地方都是不能访问到它的。同时，当这个函数被执行完之后，这个局部变量也相应会被销毁。所以你会看到在 getName 函数外面的 name 是访问不到的

**3. 块级作用域**

> ES6 中新增了块级作用域，最直接的表现就是新增的 let 关键词，使用 let 关键词定义的变量只能在块级作用域中被访问，有“暂时性死区”的特点，也就是说这个变量在定义之前是不能被使用的。

在 JS 编码过程中 `if 语句`及 `for` 语句后面 `{...}` 这里面所包括的，就是`块级作用域`

```js
console.log(a) //a is not defined
if(true){
  let a = '123'；
  console.log(a)； // 123
}
console.log(a) //a is not defined
```

> 从这段代码可以看出，变量 a 是在 `if 语句{...}` 中由 `let 关键词`进行定义的变量，所以它的作用域是 if 语句括号中的那部分，而在外面进行访问 a 变量是会报错的，因为这里不是它的作用域。所以在 if 代码块的前后输出 a 这个变量的结果，控制台会显示 a 并没有定义

## 闭包

> 闭包其实就是一个可以访问其他函数内部变量的函数。创建闭包的最常见的方式就是在一个函数内创建另一个函数，创建的函数可以访问到当前函数的局部变量。

![](../../\imgs\interview-js-7.png)

因为通常情况下，函数内部变量是无法在外部访问的（即全局变量和局部变量的区别），因此使用闭包的作用，就具备实现了能在外部访问某个函数内部变量的功能，让这些内部变量的值始终可以保存在内存中。下面我们通过代码先来看一个简单的例子

```js
function fun1() {
    var a = 1;
    return function(){
        console.log(a);
    };
}
fun1();
var result = fun1();
result();  // 1
```

结合闭包的概念，我们把这段代码放到控制台执行一下，就可以发现最后输出的结果是 1（即 a 变量的值）。那么可以很清楚地发现，a 变量作为一个 fun1 函数的内部变量，正常情况下作为函数内的局部变量，是无法被外部访问到的。但是通过闭包，我们最后还是可以拿到a变量的值。

**闭包有两个常用的用途**

- 闭包的第一个用途是使我们在函数外部能够访问到函数内部的变量。通过使用闭包，我们可以通过在外部调用闭包函数，从而在外部访问到函数内部的变量，可以使用这种方法来创建私有变量。
- 函数的另一个用途是使已经运行结束的函数上下文中的变量对象继续留在内存中，因为闭包函数保留了这个变量对象的引用，所以这个变量对象不会被回收。

> 其实闭包的本质就是作用域链的一个特殊的应用，只要了解了作用域链的创建过程，就能够理解闭包的实现原理。

```js
let a = 1
// fn 是闭包
function fn() {
  console.log(a);
}

function fn1() {
  let a = 1
  // 这里也是闭包
  return () => {
    console.log(a);
  }
}
const fn2 = fn1()
fn2()
```

- 大家都知道闭包其中一个作用是访问私有变量，就比如上述代码中的 fn2 访问到了 fn1 函数中的变量 a。但是此时 fn1 早已销毁，我们是如何访问到变量 a 的呢？不是都说原始类型是存放在栈上的么，为什么此时却没有被销毁掉？
- 接下来笔者会根据浏览器的表现来重新理解关于原始类型存放位置的说法。
- 先来说下数据存放的正确规则是：局部、占用空间确定的数据，一般会存放在栈中，否则就在堆中（也有例外）。 那么接下来我们可以通过 Chrome 来帮助我们验证这个说法说法。

![](../../\imgs\interview-js-8.png)

> 上图中画红框的位置我们能看到一个内部的对象 `[[Scopes]]`，其中存放着变量 a，该对象是被存放在堆上的，其中包含了闭包、全局对象等等内容，因此我们能通过闭包访问到本该销毁的变量。

另外最开始我们对于闭包的定位是：假如一个函数能访问外部的变量，那么这个函数它就是一个闭包，因此接下来我们看看在全局下的表现是怎么样的。

```js
let a = 1
var b = 2
// fn 是闭包
function fn() {
  console.log(a, b);
}
```

![](../../\imgs\interview-js-9.png)

从上图我们能发现全局下声明的变量，如果是 var 的话就直接被挂到 globe 上，如果是其他关键字声明的话就被挂到 Script 上。虽然这些内容同样还是存在 `[[Scopes]]`，但是全局变量应该是存放在静态区域的，因为全局变量无需进行垃圾回收，等需要回收的时候整个应用都没了。

只有在下图的场景中，原始类型才可能是被存储在栈上。

> 这里为什么要说可能，是因为 JS 是门动态类型语言，一个变量声明时可以是原始类型，马上又可以赋值为对象类型，然后又回到原始类型。这样频繁的在堆栈上切换存储位置，内部引擎是不是也会有什么优化手段，或者干脆全部都丢堆上？只有 const 声明的原始类型才一定存在栈上？当然这只是笔者的一个推测，暂时没有深究，读者可以忽略这段瞎想

![](C:\Users\Administrator\Desktop\docs\imgs\interview-js-10.png)

因此笔者对于原始类型存储位置的理解为：局部变量才是被存储在栈上，全局变量存在静态区域上，其它都存储在堆上。

当然这个理解是建立的 Chrome 的表现之上的，在不同的浏览器上因为引擎的不同，可能存储的方式还是有所变化的。

**闭包产生的原因**

> 我们在前面介绍了作用域的概念，那么你还需要明白作用域链的基本概念。其实很简单，当访问一个变量时，代码解释器会首先在当前的作用域查找，如果没找到，就去父级作用域去查找，直到找到该变量或者不存在父级作用域中，这样的链路就是作用域链

需要注意的是，每一个子函数都会拷贝上级的作用域，形成一个作用域的链条。那么我们还是通过下面的代码来详细说明一下作用域链

```js
var a = 1;
function fun1() {
  var a = 2
  function fun2() {
    var a = 3;
    console.log(a);//3
  }
}
```

- 从中可以看出，fun1 函数的作用域指向全局作用域（window）和它自己本身；fun2 函数的作用域指向全局作用域 （window）、fun1 和它本身；而作用域是从最底层向上找，直到找到全局作用域 window 为止，如果全局还没有的话就会报错。
- 那么这就很形象地说明了什么是作用域链，即当前函数一般都会存在上层函数的作用域的引用，那么他们就形成了一条作用域链。
- 由此可见，`闭包产生的本质就是：当前环境中存在指向父级作用域的引用`。那么还是拿上的代码举例。

```js
function fun1() {
  var a = 2
  function fun2() {
    console.log(a);  //2
  }
  return fun2;
}
var result = fun1();
result();
```

- 从上面这段代码可以看出，这里 result 会拿到父级作用域中的变量，输出 2。因为在当前环境中，含有对 fun2 函数的引用，fun2 函数恰恰引用了 window、fun1 和 fun2 的作用域。因此 fun2 函数是可以访问到 fun1 函数的作用域的变量。
- 那是不是只有返回函数才算是产生了闭包呢？其实也不是，回到闭包的本质，我们只需要让父级作用域的引用存在即可，因此还可以这么改代码，如下所示

```js
var fun3;
function fun1() {
  var a = 2
  fun3 = function() {
    console.log(a);
  }
}
fun1();
fun3();
```

> 可以看出，其中实现的结果和前一段代码的效果其实是一样的，就是在给 fun3 函数赋值后，fun3 函数就拥有了 window、fun1 和 fun3 本身这几个作用域的访问权限；然后还是从下往上查找，直到找到 fun1 的作用域中存在 a 这个变量；因此输出的结果还是 2，最后产生了闭包，形式变了，本质没有改变。

因此最后`返回的不管是不是函数，也都不能说明没有产生闭包`

**闭包的表现形式**

1. 返回一个函数
2. `在定时器、事件监听、Ajax 请求、Web Workers 或者任何异步中，只要使用了回调函数，实际上就是在使用闭包`。请看下面这段代码，这些都是平常开发中用到的形式

```js
// 定时器
setTimeout(function handler(){
  console.log('1');
}，1000);
// 事件监听
$('#app').click(function(){
  console.log('Event Listener');
});
```

3. 作为函数参数传递的形式，比如下面的例子。

```js
var a = 1;
function foo(){
  var a = 2;
  function baz(){
    console.log(a);
  }
  bar(baz);
}
function bar(fn){
  // 这就是闭包
  fn();
}
foo();  // 输出2，而不是1
```

4. `IIFE（立即执行函数），创建了闭包，保存了全局作用域（window）和当前函数的作用域`，因此可以输出全局的变量，如下所示

```js
var a = 2;
(function IIFE(){
  console.log(a);  // 输出2
})();
```

> IIFE 这个函数会稍微有些特殊，算是一种自执行匿名函数，这个匿名函数拥有独立的作用域。这不仅可以避免了外界访问此 IIFE 中的变量，而且又不会污染全局作用域，我们经常能在高级的 JavaScript 编程中看见此类函数。

**如何解决循环输出问题？**

> 在互联网大厂的面试中，解决循环输出问题是比较高频的面试题，一般都会给一段这样的代码让你来解释

```js
for(var i = 1; i <= 5; i ++){
  setTimeout(function() {
    console.log(i)
  }, 0)
}
```

上面这段代码执行之后，从控制台执行的结果可以看出来，结果输出的是 5 个 6，那么一般面试官都会先问为什么都是 6？我想让你实现输出 1、2、3、4、5 的话怎么办呢？

因此结合本讲所学的知识我们来思考一下，应该怎么给面试官一个满意的解释。你可以围绕这两点来回答。

- `setTimeout` 为宏任务，由于 JS 中单线程 `eventLoop 机制`，在主线程同步任务执行完后才去执行宏任务，因此`循环结束后 setTimeout 中的回调才依次执行`
- 因为 `setTimeout` 函数也是一种闭包，往上找它的`父级作用域链就是 window`，`变量 i 为 window 上的全局变量`，开始执行 setTimeout 之前变量 i 已经就是 6 了，因此最后输出的连续就都是 6。

> 那么我们再来看看如何按顺序依次输出 1、2、3、4、5 呢？

1. 利用 IIFE

> 可以利用 IIFE（立即执行函数），当每次 for 循环时，把此时的变量 i 传递到定时器中，然后执行，改造之后的代码如下。

```js
for(var i = 1;i <= 5;i++){
  (function(j){
    setTimeout(function timer(){
      console.log(j)
    }, 0)
  })(i)
}
```

2. 使用 ES6 中的 let

> ES6 中新增的 let 定义变量的方式，使得 ES6 之后 JS 发生革命性的变化，让 JS 有了块级作用域，代码的作用域以块级为单位进行执行。通过改造后的代码，可以实现上面想要的结果。

```js
for(let i = 1; i <= 5; i++){
  setTimeout(function() {
    console.log(i);
  },0)
}
```

3. 定时器传入第三个参数

> setTimeout 作为经常使用的定时器，它是存在第三个参数的，日常工作中我们经常使用的一般是前两个，一个是回调函数，另外一个是时间，而第三个参数用得比较少。那么结合第三个参数，调整完之后的代码如下。

```js
for(var i=1;i<=5;i++){
  setTimeout(function(j) {
    console.log(j)
  }, 0, i)
}
```

从中可以看到，第三个参数的传递，可以改变 setTimeout 的执行逻辑，从而实现我们想要的结果，这也是一种解决循环输出问题的途径

**常见考点**

- 闭包能考的很多，概念和笔试题都会考。
- 概念题就是考考闭包是什么了。
- 笔试题的话基本都会结合上异步，比如最常见的：

```js
for (var i = 0; i < 6; i++) {
  setTimeout(() => {
    console.log(i)
  })
}
```

这道题会问输出什么，有哪几种方式可以得到想要的答案？

## New的原理

**常见考点**

- `new` 做了那些事？
- `new` 返回不同的类型时会有什么表现？
- 手写 new 的实现过程

> new 关键词的`主要作用就是执行一个构造函数、返回一个实例对象`，在 new 的过程中，根据构造函数的情况，来确定是否可以接受参数的传递。下面我们通过一段代码来看一个简单的 new 的例子

```js
function Person(){
   this.name = 'Jack';
}
var p = new Person(); 
console.log(p.name)  // Jack
```

这段代码比较容易理解，从输出结果可以看出，p 是一个通过 person 这个构造函数生成的一个实例对象，这个应该很容易理解。

> `new` 操作符可以帮助我们构建出一个实例，并且绑定上 this，内部执行步骤可大概分为以下几步：
> 
> 1. 创建一个新对象
> 2. 对象连接到构造函数原型上，并绑定 `this`（this 指向新对象）
> 3. 执行构造函数代码（为这个新对象添加属性）
> 4. 返回新对象

在第四步返回新对象这边有一个情况会例外：

> 那么问题来了，如果不用 `new` 这个关键词，结合上面的代码改造一下，去掉 `new`，会发生什么样的变化呢？我们再来看下面这段代码

```js
function Person(){
  this.name = 'Jack';
}
var p = Person();
console.log(p) // undefined
console.log(name) // Jack
console.log(p.name) // 'name' of undefined
```

- 从上面的代码中可以看到，我们没有使用 `new` 这个关键词，返回的结果就是 `undefined`。其中由于 `JavaScript` 代码在默认情况下 `this` 的指向是 `window`，那么 `name` 的输出结果就为 `Jack`，这是一种不存在 `new` 关键词的情况。
- 那么当构造函数中有 `return` 一个对象的操作，结果又会是什么样子呢？我们再来看一段在上面的基础上改造过的代码。

```js
function Person(){
   this.name = 'Jack'; 
   return {age: 18}
}
var p = new Person(); 
console.log(p)  // {age: 18}
console.log(p.name) // undefined
console.log(p.age) // 18
```

> 通过这段代码又可以看出，当构造函数最后 `return` 出来的是一个和 `this` 无关的对象时，`new 命令会直接返回这个新对象`，`而不是通过 new 执行步骤生成的 this 对象`

但是这里要求构造函数必须是返回一个对象，`如果返回的不是对象，那么还是会按照 new 的实现步骤，返回新生成的对象`。接下来还是在上面这段代码的基础之上稍微改动一下

```js
function Person(){
   this.name = 'Jack'; 
   return 'tom';
}
var p = new Person(); 
console.log(p)  // {name: 'Jack'}
console.log(p.name) // Jack
```

可以看出，当构造函数中 `return` 的不是一个对象时，那么它还是会根据 `new` 关键词的执行逻辑，生成一个新的对象（绑定了最新 `this`），最后返回出来

> 因此我们总结一下：`new 关键词执行之后总是会返回一个对象，要么是实例对象，要么是 return 语句指定的对象`

![](C:\Users\Administrator\Desktop\docs\imgs\interview-js-11.png)

**手工实现New的过程**

```js
function create(fn, ...args) {
  if(typeof fn !== 'function') {
    throw 'fn must be a function';
  }
    // 1、用new Object() 的方式新建了一个对象obj
  // var obj = new Object()
    // 2、给该对象的__proto__赋值为fn.prototype，即设置原型链
  // obj.__proto__ = fn.prototype

  // 1、2步骤合并
  // 创建一个空对象，且这个空对象继承构造函数的 prototype 属性
  // 即实现 obj.__proto__ === constructor.prototype
  var obj = Object.create(fn.prototype);

    // 3、执行fn，并将obj作为内部this。使用 apply，改变构造函数 this 的指向到新建的对象，这样 obj 就可以访问到构造函数中的属性
  var res = fn.apply(obj, args);
    // 4、如果fn有返回值，则将其作为new操作返回内容，否则返回obj
    return res instanceof Object ? res : obj;
};
```

- 使用 `Object.create` 将 `obj 的`proto`指向为构造函数的原型`；
- 使用 `apply` 方法，将构造函数内的 `this` 指向为 `obj`；
- 在 `create` 返回时，使用三目运算符决定返回结果。

我们知道，`构造函数如果有显式返回值，且返回值为对象类型，那么构造函数返回结果不再是目标实例`

如下代码：

```js
function Person(name) {
  this.name = name
  return {1: 1}
}
const person = new Person(Person, 'lucas')
console.log(person)
// {1: 1}
```

测试

```js
// 使用create代替new
function Person() {...}
// 使用内置函数new
var person = new Person(1,2)

// 使用手写的new，即create
var person = create(Person, 1,2)
```

**new 被调用后大致做了哪几件事情**

- 让实例可以访问到私有属性；
- 让实例可以访问构造函数原型（`constructor.prototype`）所在原型链上的属性；
- 构造函数返回的最后结果是引用数据类型。

## 原型/原型链

**`__proto__`和prototype关系**：`__proto__`和`constructor`是**对象**独有的。2️⃣`prototype`属性是**函数**独有的

> 在 js 中我们是使用构造函数来新建一个对象的，每一个构造函数的内部都有一个 prototype 属性值，这个属性值是一个对象，这个对象包含了可以由该构造函数的所有实例共享的属性和方法。当我们使用构造函数新建一个对象后，在这个对象的内部将包含一个指针，这个指针指向构造函数的 prototype 属性对应的值，在 ES5 中这个指针被称为对象的原型。一般来说我们是不应该能够获取到这个值的，但是现在浏览器中都实现了 proto 属性来让我们访问这个属性，但是我们最好不要使用这个属性，因为它不是规范中规定的。ES5 中新增了一个 `Object.getPrototypeOf()` 方法，我们可以通过这个方法来获取对象的原型。

当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，也就是原型链的概念。原型链的尽头一般来说都是 `Object.prototype` 所以这就是我们新建的对象为什么能够使用 `toString()` 等方法的原因。

> 特点：JavaScript 对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与 之相关的对象也会继承这一改变

- 原型(`prototype`): 一个简单的对象，用于实现对象的 属性继承。可以简单的理解成对象的爹。在 `Firefox` 和 `Chrome` 中，每个`JavaScript`对象中都包含一个`__proto__`(非标准)的属性指向它爹(该对象的原型)，可`obj.__proto__`进行访问。
- 构造函数: 可以通过`new`来 新建一个对象 的函数。
- 实例: 通过构造函数和`new`创建出来的对象，便是实例。 实例通过`__proto__`指向原型，通过`constructor`指向构造函数。

> 以`Object`为例，我们常用的`Object`便是一个构造函数，因此我们可以通过它构建实例。

```js
// 实例
const instance = new Object()
```

则此时， 实例为`instance`, 构造函数为`Object`，我们知道，构造函数拥有一个`prototype`的属性指向原型，因此原型为:

```js
// 原型
const prototype = Object.prototype
```

**这里我们可以来看出三者的关系:**

- `实例.__proto__ === 原型`
- `原型.constructor === 构造函数`
- `构造函数.prototype === 原型`

```js
// 这条线其实是是基于原型进行获取的，可以理解成一条基于原型的映射线
// 例如: 
// const o = new Object()
// o.constructor === Object   --> true
// o.__proto__ = null;
// o.constructor === Object   --> false
实例.constructor === 构造函数
```

![](../../\imgs\interview-js-12.png)

**原型链**

> 原型链是由原型对象组成，每个对象都有 `__proto__` 属性，指向了创建该对象的构造函数的原型，`__proto__` 将对象连接起来组成了原型链。是一个用来实现继承和共享属性的有限的对象链

- 属性查找机制: 当查找对象的属性时，如果实例对象自身不存在该属性，则沿着原型链往上一级查找，找到时则输出，不存在时，则继续沿着原型链往上一级查找，直至最顶级的原型对象`Object.prototype`，如还是没找到，则输出`undefined`；
- 属性修改机制: 只会修改实例对象本身的属性，如果不存在，则进行添加该属性，如果需要修改原型的属性时，则可以用: `b.prototype.x = 2`；但是这样会造成所有继承于该对象的实例的属性发生改变。

**js 获取原型的方法**

- `p.proto`
- `p.constructor.prototype`
- `Object.getPrototypeOf(p)`

**总结**

![](C:\Users\Administrator\Desktop\docs\imgs\interview-js-13.png)

- 每个函数都有 `prototype` 属性，除了 `Function.prototype.bind()`，该属性指向原型。
- 每个对象都有 `__proto__` 属性，指向了创建该对象的构造函数的原型。其实这个属性指向了 `[[prototype]]`，但是 `[[prototype]]`是内部属性，我们并不能访问到，所以使用 `_proto_`来访问。
- 对象可以通过 `__proto__` 来寻找不属于该对象的属性，`__proto__` 将对象连接起来组成了原型链。

## 继承

![](../../\imgs\interview-js-14.png)

> 涉及面试题：原型如何实现继承？`Class` 如何实现继承？`Class` 本质是什么？

首先先来讲下 `class`，其实在 `JS`中并不存在类，`class` 只是语法糖，本质还是函数

```js
class Person {}
Person instanceof Function // true
```

**组合继承**

> 组合继承是最常用的继承方式

```js
function Parent(value) {
  this.val = value
}
Parent.prototype.getValue = function() {
  console.log(this.val)
}
function Child(value) {
  Parent.call(this, value)
}
Child.prototype = new Parent()

const child = new Child(1)

child.getValue() // 1
child instanceof Parent // true
```

- 以上继承的方式核心是在子类的构造函数中通过 `Parent.call(this)` 继承父类的属性，然后改变子类的原型为 `new Parent()` 来继承父类的函数。
- 这种继承方式优点在于构造函数可以传参，不会与父类引用属性共享，可以复用父类的函数，但是也存在一个缺点就是在继承父类函数的时候调用了父类构造函数，导致子类的原型上多了不需要的父类属性，存在内存上的浪费

**寄生组合继承**

> 这种继承方式对组合继承进行了优化，组合继承缺点在于继承父类函数时调用了构造函数，我们只需要优化掉这点就行了

```js
function Parent(value) {
  this.val = value
}
Parent.prototype.getValue = function() {
  console.log(this.val)
}

function Child(value) {
  Parent.call(this, value)
}
Child.prototype = Object.create(Parent.prototype, {
  constructor: {
    value: Child,
    enumerable: false,
    writable: true,
    configurable: true
  }
})

const child = new Child(1)

child.getValue() // 1
child instanceof Parent // true
```

> 以上继承实现的核心就是将父类的原型赋值给了子类，并且将构造函数设置为子类，这样既解决了无用的父类属性问题，还能正确的找到子类的构造函数。

**Class 继承**

> 以上两种继承方式都是通过原型去解决的，在 ES6 中，我们可以使用 class 去实现继承，并且实现起来很简单

```js
class Parent {
  constructor(value) {
    this.val = value
  }
  getValue() {
    console.log(this.val)
  }
}
class Child extends Parent {
  constructor(value) {
    super(value)
    this.val = value
  }
}
let child = new Child(1)
child.getValue() // 1
child instanceof Parent // true
```

> `class` 实现继承的核心在于使用 `extends` 表明继承自哪个父类，并且在子类构造函数中必须调用 `super`，因为这段代码可以看成 `Parent.call(this, value)`。

**ES5 和 ES6 继承的区别：**

- ES6 继承的子类需要调用 `super()` 才能拿到子类，ES5 的话是通过 `apply` 这种绑定的方式
- 类声明不会提升，和 `let` 这些一致

```js
function Super() {}
Super.prototype.getNumber = function() {
  return 1
}

function Sub() {}
Sub.prototype = Object.create(Super.prototype, {
  constructor: {
    value: Sub,
    enumerable: false,
    writable: true,
    configurable: true
  }
})
let s = new Sub()
s.getNumber()
```

> 以下详细讲解几种常见的继承方式

**1. 方式1: 借助call**

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

> 这样写的时候子类虽然能够拿到父类的属性值，但是问题是父类原型对象中一旦存在方法那么子类无法继承。那么引出下面的方法。

**2. 方式2: 借助原型链**

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
console.log(s1.play, s2.play);
```

可以看到控制台：

![](C:\Users\Administrator\Desktop\docs\imgs\interview-js-15.png)

> 明明我只改变了s1的play属性，为什么s2也跟着变了呢？很简单，因为两个实例使用的是同一个原型对象。

那么还有更好的方式么？

**3. 方式3：将前两种组合**

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
console.log(s3.play, s4.play);
```

可以看到控制台：

![](../../\imgs\interview-js-16.png)

> 之前的问题都得以解决。但是这里又徒增了一个新问题，那就是`Parent3`的构造函数会多执行了一次（`Child3.prototype = new Parent3();`）。这是我们不愿看到的。那么如何解决这个问题？

**4. 方式4: 组合继承的优化1**

```js
function Parent4 () {
    this.name = 'parent4';
    this.play = [1, 2, 3];
}
function Child4() {
    Parent4.call(this);
    this.type = 'child4';
}
Child4.prototype = Parent4.prototype;
```

这里让将父类原型对象直接给到子类，父类构造函数只执行一次，而且父类属性和方法均能访问，但是我们来测试一下：

```js
var s3 = new Child4();
var s4 = new Child4();
console.log(s3)
```

![](../../\imgs\interview-js-17.png)

> 子类实例的构造函数是Parent4，显然这是不对的，应该是Child4。

**5. 方式5(最推荐使用): 组合继承的优化2**

```js
function Parent5 () {
    this.name = 'parent5';
    this.play = [1, 2, 3];
}
function Child5() {
    Parent5.call(this);
    this.type = 'child5';
}
Child5.prototype = Object.create(Parent5.prototype);
Child5.prototype.constructor = Child5;
```

这是最推荐的一种方式，接近完美的继承，它的名字也叫做寄生组合继承。

**6. ES6的extends被编译后的JavaScript代码**

> ES6的代码最后都是要在浏览器上能够跑起来的，这中间就利用了babel这个编译工具，将ES6的代码编译成ES5让一些不支持新语法的浏览器也能运行。

那最后编译成了什么样子呢？

```js
function _possibleConstructorReturn(self, call) {
    // ...
    return call && (typeof call === 'object' || typeof call === 'function') ? call : self;
}

function _inherits(subClass, superClass) {
    // ...
    //看到没有
    subClass.prototype = Object.create(superClass && superClass.prototype, {
        constructor: {
            value: subClass,
            enumerable: false,
            writable: true,
            configurable: true
        }
    });
    if (superClass) Object.setPrototypeOf ? Object.setPrototypeOf(subClass, superClass) : subClass.__proto__ = superClass;
}


var Parent = function Parent() {
    // 验证是否是 Parent 构造出来的 this
    _classCallCheck(this, Parent);
};

var Child = (function (_Parent) {
    _inherits(Child, _Parent);

    function Child() {
        _classCallCheck(this, Child);

        return _possibleConstructorReturn(this, (Child.__proto__ || Object.getPrototypeOf(Child)).apply(this, arguments));
    }

    return Child;
}(Parent));
```

> 核心是`_inherits`函数，可以看到它采用的依然也是第五种方式————寄生组合继承方式，同时证明了这种方式的成功。不过这里加了一个`Object.setPrototypeOf(subClass, superClass)`，这是用来干啥的呢？

答案是用来继承父类的静态方法。这也是原来的继承方式疏忽掉的地方。

**追问: 面向对象的设计一定是好的设计吗？**

> 不一定。从继承的角度说，这一设计是存在巨大隐患的

## 面向对象

**编程思想**

- 基本思想是使用对象，类，继承，封装等基本概念来进行程序设计
- 优点
  - 易维护
    - 采用面向对象思想设计的结构，可读性高，由于继承的存在，即使改变需求，那么维护也只是在局部模块，所以维护起来是非常方便和较低成本的
  - 易扩展
  - 开发工作的重用性、继承性高，降低重复工作量。
  - 缩短了开发周期

> 一般面向对象包含：继承，封装，多态，抽象

**1. 对象形式的继承**

浅拷贝

```js
var Person = {
    name: 'poetry',
    age: 18,
    address: {
        home: 'home',
        office: 'office',
    }
    sclools: ['x','z'],
};

var programer = {
    language: 'js',
};

function extend(p, c){
    var c = c || {};
    for( var prop in p){
        c[prop] = p[prop];
    }
}
extend(Person, programer);
programer.name;  // poetry
programer.address.home;  // home
programer.address.home = 'house';  //house
Person.address.home;  // house
```

> 从上面的结果看出，浅拷贝的缺陷在于修改了子对象中引用类型的值，会影响到父对象中的值，因为在浅拷贝中对引用类型的拷贝只是拷贝了地址，指向了内存中同一个副本

深拷贝

```js
function extendDeeply(p, c){
    var c = c || {};
    for (var prop in p){
        if(typeof p[prop] === "object"){
            c[prop] = (p[prop].constructor === Array)?[]:{};
            extendDeeply(p[prop], c[prop]);
        }else{
            c[prop] = p[prop];
        }
    }
}
```

> 利用递归进行深拷贝，这样子对象的修改就不会影响到父对象

```js
extendDeeply(Person, programer);
programer.address.home = 'poetry';
Person.address.home; // home
```

**利用call和apply继承**

```js
function Parent(){
    this.name = "abc";
    this.address = {home: "home"};
}
function Child(){
    Parent.call(this);
    this.language = "js"; 
}
```

**ES5中的Object.create()**

```js
var p = { name : 'poetry'};
var obj = Object.create(p);
obj.name; // poetry
```

> `Object.create()`作为new操作符的替代方案是ES5之后才出来的。我们也可以自己模拟该方法：

```js
// 模拟Object.create()方法
function myCreate(o){
    function F(){};
    F.prototype = o;
    o = new F();
    return o;
}
var p = { name : 'poetry'};
var obj = myCreate(p);
obj.name; // poetry
```

目前，各大浏览器的最新版本（包括IE9）都部署了这个方法。如果遇到老式浏览器，可以用下面的代码自行部署

```js
if (!Object.create) {
　　　　Object.create = function (o) {
　　　　　　function F() {}
　　　　　　F.prototype = o;
　　　　　　return new F();
　　　　};
}
```

**2. 类的继承**

> `Object.create()`

```js
function Person(name, age){}
Person.prototype.headCount = 1;
Person.prototype.eat = function(){
    console.log('eating...');
}
function Programmer(name, age, title){}

Programmer.prototype = Object.create(Person.prototype); // 建立继承关系
Programmer.prototype.constructor = Programmer;  // 修改constructor的指向
```

**调用父类方法**

```js
function Person(name, age){
    this.name = name;
    this.age = age;
}
Person.prototype.headCount = 1;
Person.prototype.eat = function(){
    console.log('eating...');
}

function Programmer(name, age, title){
    Person.apply(this, arguments); // 调用父类的构造器
}


Programmer.prototype = Object.create(Person.prototype);
Programmer.prototype.constructor = Programmer;

Programmer.prototype.language = "js";
Programmer.prototype.work = function(){
    console.log('i am working code in '+ this.language);
    Person.prototype.eat.apply(this, arguments); // 调用父类上的方法
}
```

**3. 封装**

- 命名空间
  - js是没有命名空间的，因此可以用对象模拟

```js
var app = {};  // 命名空间app
// 模块1
app.module1 = {
    name: 'poetry',
    f: function(){
        console.log('hi robot');
    }
};
app.module1.name; // "poetry"
app.module1.f();  // hi robot
```

> 对象的属性外界是可读可写 如何来达到封装的额目的？答：可通过`闭包+局部变量`来完成

- 在构造函数内部声明局部变量 和普通方法
- 因为作用域的关系 只有构造函数内的方法
- 才能访问局部变量 而方法对于外界是开放的
- 因此可以通过方法来访问 原本外界访问不到的局部变量 达到函数封装的目的

```js
function Girl(name,age){
	var love = '小明';//love 是局部变量 准确说不属于对象 属于这个函数的额激活对象 函数调用时必将产生一个激活对象 love在激活对象身上   激活对象有作用域的关系 有办法访问  加一个函数提供外界访问
	this.name = name;
	this.age = age;
	this.say = function () {
		return love;
	};

	this.movelove = function (){
		love = '小轩'; //35
	}

} 

var g = new Girl('yinghong',22);

console.log(g);
console.log(g.say());//小明
console.log(g.movelove());//undefined  因为35行没有返回
console.log(g.say());//小轩



function fn(){
	function t(){
		//var age = 22;//声明age变量 在t的激活对象上
		age = 22;//赋值操作 t的激活对象上找age属性 ，找不到 找fn的激活对象....再找到 最终找到window.age = 22;
				//不加var就是操作window全局属性
	
	}
	t();
}
console.log(fn());//undefined
```

**静态成员**

> 面向对象中的静态方法-静态属性：没有new对象 也能引用静态方法属性

```js
function Person(name){
    var age = 100;
    this.name = name;
}
// 静态成员
Person.walk = function(){
    console.log('static');
};
Person.walk();  // static
```

**5. 私有与公有**

```js
function Person(id){
    // 私有属性与方法
    var name = 'poetry';
    var work = function(){
        console.log(this.id);
    };
    // 公有属性与方法
    this.id = id;
    this.say = function(){
        console.log('say hello');
        work.call(this);
    };
};
var p1 = new Person(123);
p1.name; // undefined
p1.id;  // 123
p1.say();  // say hello 123
```

**6. 模块化**

```js
var moduleA;
moduleA = function() {
    var prop = 1;

    function func() {}

    return {
        func: func,
        prop: prop
    };
}(); // 立即执行匿名函数
```

**7. 多态**

> 多态:同一个父类继承出来的子类各有各的形态

```js
function Cat(){
	this.eat = '肉';
}

function Tiger(){
	this.color = '黑黄相间';
}

function Cheetah(){
	this.color = '报文';
}

function Lion(){
	this.color = '土黄色';
}

Tiger.prototype =  Cheetah.prototype = Lion.prototype = new Cat();//共享一个祖先 Cat

var T = new Tiger();
var C = new Cheetah();
var L = new Lion();

console.log(T.color);
console.log(C.color);
console.log(L.color);


console.log(T.eat);
console.log(C.eat);
console.log(L.eat);
```

**8. 抽象类**

> 在构造器中 `throw new Error('')`; 抛异常。这样防止这个类被直接调用

```js
function DetectorBase() {
    throw new Error('Abstract class can not be invoked directly!');
}

DetectorBase.prototype.detect = function() {
    console.log('Detection starting...');
};
DetectorBase.prototype.stop = function() {
    console.log('Detection stopped.');
};
DetectorBase.prototype.init = function() {
    throw new Error('Error');
};

// var d = new DetectorBase();
// Uncaught Error: Abstract class can not be invoked directly!

function LinkDetector() {}
LinkDetector.prototype = Object.create(DetectorBase.prototype);
LinkDetector.prototype.constructor = LinkDetector;

var l = new LinkDetector();
console.log(l); // LinkDetector {}__proto__: LinkDetector
l.detect(); // Detection starting...
l.init(); // Uncaught Error: Error
```
