# Object.create()、new Object()和{}的区别

## 直接字面量创建

```js
var objA = {};
objA.name = 'a';
objA.sayName = function() {
    console.log(`My name is ${this.name} !`);
}
// var objA = {
//     name: 'a',
//     sayName: function() {
//         console.log(`My name is ${this.name} !`);
//     }
// }
objA.sayName();
console.log(objA.__proto__ === Object.prototype); // true
console.log(objA instanceof Object); // true
```



## new关键字创建

```js
var objB = new Object();
// var objB = Object();
objB.name = 'b';
objB.sayName = function() {
    console.log(`My name is ${this.name} !`);
}
objB.sayName();
console.log(objB.__proto__ === Object.prototype); // true
console.log(objB instanceof Object); // true
```

在[JS的指向问题](https://link.juejin.cn/?target=https%3A%2F%2Flijing0906.github.io%2Fpost%2FJSthis "https://lijing0906.github.io/post/JSthis")中讲**new绑定**时讲了`new`操作符其实做了以下四步：

```js
// 例子
function Func() {};
var func = new Func();

// 1.创建一个空对象
var obj = new Object();

// 2.设置原型链
// obj的__proto__指向构造函数Object的prototype
obj.__proto__ = Func.prototype;

// 3.让Func中的this指向obj，并执行Func的函数体。
// 把构造函数Func的this指向obj，并执行构造函数Func把结果赋值给result
var result = Func.call(obj);

// 4.判断Func的返回值类型：
// 如果是值类型，返回obj。如果是引用类型，就返回这个引用类型的对象。 
if (typeof(result) === 'object') {
    // 构造函数Object的执行结果是引用类型，就把这个引用类型的对象返回给objB
    func = result; 
}
else {
    // 构造函数Object的执行结果是值类型，就返回obj这个空对象给objB
    func = obj;
}
```

> 其实字面量创建和new关键字创建并没有区别，创建的新对象的`__proto__`都指向`Object.prototype`，只是字面量创建更高效一些，少了`__proto__`指向赋值和`this`。



## Object.create()

> `Object.create()`方法创建一个新对象，使用现有的对象来提供新创建的对象的`__proto__`。 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

```js
const person = {
  isHuman: false,
  printIntroduction: function () {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  }
};
const me = Object.create(person); // me.__proto__ === person
me.name = "Matthew"; // name属性被设置在新对象me上，而不是现有对象person上
me.isHuman = true; // 继承的属性可以被重写
me.printIntroduction(); // My name is Matthew. Am I human? true
```

> Object.create(proto[, propertiesObject])

- `proto`必填参数，是新对象的原型对象，如上面代码里新对象`me`的`__proto__`指向`person`。注意，如果这个参数是`null`，那新对象就彻彻底底是个空对象，没有继承`Object.prototype`上的任何属性和方法，如`hasOwnProperty()、toString()`等。
  
  ```js
  var a = Object.create(null);
  // {}
  console.dir(a);
  
  // undefined
  console.log(a.__proto__);
  
  // false
  console.log(a.__proto__ === Object.prototype);
  
  // false 没有继承`Object.prototype`上的任何属性和方法，所以原型链上不会出现Object
  console.log(a instanceof Object); 
  ```

- `propertiesObject`是可选参数，指定要添加到新对象上的可枚举的属性（即其自定义的属性和方法，可用`hasOwnProperty()`获取的，而不是原型对象上的）的描述符及相应的属性名称。
  
  ```js
  var bb = Object.create(null, {
      a: {
          value: 2,
          writable: true,
          configurable: true
      }
  });
  // {a: 2}
  console.dir(bb);
  // undefined 
  console.log(bb.__proto__);
  // false
  console.log(bb.__proto__ === Object.prototype);
  // false 没有继承`Object.prototype`上的任何属性和方法，所以原型链上不会出现Object
  console.log(bb instanceof Object); 
  
  // ----------------------------------------------------------
  
  var cc = Object.create({b: 1}, {
      a: {
          value: 3,
          writable: true,
          configurable: true
      }
  });
  // {a: 3}
  console.log(cc);
  // true false 说明第二个参数设置的是新对象自身可枚举的属性
  console.log(cc.hasOwnProperty('a'), cc.hasOwnProperty('b'));
  // {b: 1} 新对象cc的__proto__指向{b: 1}
  console.log(cc.__proto__);
  // false
  console.log(cc.__proto__ === Object.protorype);
  // true cc是对象，原型链上肯定会出现Object
  console.log(cc instanceof Object);
  
  ```
  
  > `Object.create()`创建的对象的原型指向传入的对象。跟字面量和`new`关键字创建有区别。

- 自己实现一个Object.create()
  
  ```js
  Object.mycreate = function(proto, properties) {
      function F() {};
      F.prototype = proto;
  
      if(properties) {
          Object.defineProperties(F, properties);
      }
  
      return new F();
  }
  
  var hh = Object.mycreate({a: 11}, {mm: {value: 10}});
  console.dir(hh);
  
  ```



## 总结

- 字面量和`new`关键字创建的对象是`Object`的实例，原型指向`Object.prototype`，继承内置对象`Object`

- `Object.create(arg, pro)`创建的对象的原型取决于`arg`，`arg`为`null`，新对象是空对象，没有原型，不继承任何对象；`arg`为指定对象，新对象的原型指向指定对象，继承指定对象
