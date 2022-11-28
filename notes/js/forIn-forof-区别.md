# for in 和 for of的区别

## 区别

### for in

- 一般用于遍历对象自身的和继承的可枚举属性。以及对象从构造函数原型中继承的属性。对于每个不同的属性，语句都会被执行。
- 不建议使用for... in 遍历数组，因为输出的顺序是不固定的。
- 如果迭代的对象的变量值是null或者undefined, for... in不执行循环体，建议在使用for... in循环之前，先检查该对象的值是不是null或者undefined。

### for of

- for…of 语句在可迭代对象（包括 Arry，Map，Seta，String，TypedArray，arguments 对象等）上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句。
- for…of 语句遍历可迭代对象定义要迭代的数据（非自定义属性）。
- for…of循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象、Generator 对象，以及字符串。

## for in

```javascript
// for in 应用于数组
Array.prototype.sayHello = function(){
    console.log("Hello")
}
Array.prototype.str = 'world';
var myArray = [1, 2, 10, 30, 100];
myArray.name = '数组';

for(let index in myArray){
    console.log(index);
}
// 输出结果如下
0,1,2,3,4,name,str,sayHello


// for in  应用于对象中
Object.prototype.sayHello = function(){
    console.log('Hello');
}
Obeject.prototype.str = 'World';
var myObject = {name: 'zhangsan', age: 100};

for(let index in myObject){
    console.log(index);
}
// 输出结果
name,age,str,sayHello

// 首先输出的是对象的属性名，再是对象原型中的属性和方法，
// 如果不想让其输出原型中的属性和方法，可以使用hasOwnProperty方法进行过滤或者设置成不可枚举的
for(let index in myObject){
    if(myObject.hasOwnProperty(index)){
        console.log(index)
    }
}
// 输出结果为
name,age

// 你也可以用Object.keys()方法获取所有的自身可枚举属性组成的数组。
Object.keys(myObject)


/*
    总结：
        for in 应用于数组循环返回的是数组的下标和数组的属性和原型上的方法和属性。
        for in 应用于对象循环返回的是对象的属性名和原型中的方法和属性。

    for in 遍历数组有以下问题：
        1.index索引为字符串型数字，不能直接进行几何运算
        2.遍历顺序有可能不是按照实际数组的内部顺序
        3.使用for in会遍历数组所有的可枚举属性，包括原型。例如上栗的原型方法method和name属性
*/
```

## for of

```javascript
// 对象
Object.prototype.sayHello = function(){
    console.log('Hello');
}
var myObject = {
    name: 'zhangsan',
    age: 10
}

for(let key of myObject){
    consoloe.log(key);
}
// 输出结果
// typeError


// 数组
Array.prototype.sayHello = function(){
    console.log("Hello");
}
var myArray = [1, 200, 3, 400, 100];
for(let key of myArray){
    console.log(key);
}
// 输出结果
1,200,3,400,100


/*
    总结：
        for in 遍历的是数组的索引（即键名）。
        for of 遍历的是数组元素值。 

        所以for in更适合遍历对象，不要使用for in遍历数组。
*/
```
