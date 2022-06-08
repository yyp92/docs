# js-基础知识

## 找到==和===的算法细节

> “The comparison x == y, where x and y are values, producestrue or false.”

上面这句话的意思是，相等运算符用于比较两个值，返回true或false。

算法细节：

> 1. ReturnIfAbrupt(x).
> 2. ReturnIfAbrupt(y).
> 3. If Type(x) is the same as Type(y), then  
>    Return the result of performing Strict Equality Comparisonx === y.
> 4. If x is null and y is undefined, return true.
> 5. If x is undefined and y is null, return true.
> 6. If Type(x) is Number and Type(y) is String,  
>    return the result of the comparison x == ToNumber(y).
> 7. If Type(x) is String and Type(y) is Number,  
>    return the result of the comparison ToNumber(x) == y.
> 8. If Type(x) is Boolean, return the result of the comparisonToNumber(x) == y.
> 9. If Type(y) is Boolean, return the result of the comparison x == ToNumber(y).
> 10. If Type(x) is either String, Number, or Symbol and Type(y)is Object, then  
>     return the result of the comparison x == ToPrimitive(y).
> 11. If Type(x) is Object and Type(y) is either String, Number, or Symbol, then  
>     return the result of the comparison ToPrimitive(x) == y.
> 12. Return false.

翻译：

> 1. 如果x不是正常值（比如抛出一个错误），中断执行。
> 2. 如果y不是正常值，中断执行。
> 3. 如果Type(x)与Type(y)相同，执行严格相等运算x === y。
> 4. 如果x是null，y是[undefined](https://www.zhihu.com/search?q=undefined&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A132231109%7D)，返回true。
> 5. 如果x是undefined，y是null，返回true。
> 6. 如果Type(x)是数值，Type(y)是字符串，返回x == ToNumber(y)的结果。
> 7. 如果Type(x)是字符串，Type(y)是数值，返回ToNumber(x) == y的结果。
> 8. 如果Type(x)是[布尔值](https://www.zhihu.com/search?q=%E5%B8%83%E5%B0%94%E5%80%BC&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A132231109%7D)，返回ToNumber(x) == y的结果。
> 9. 如果Type(y)是布尔值，返回x == ToNumber(y)的结果。
> 10. 如果Type(x)是字符串或数值或Symbol值，Type(y)是对象，返回x == ToPrimitive(y)的结果。
> 11. 如果Type(x)是对象，Type(y)是字符串或数值或Symbol值，返回ToPrimitive(x) == y的结果。
> 12. 返回false。

## 0==null为何是false

要比较相等性之前，不能将null和undefined转换成其他任何值。就是undefined和null与其他数在进行 ***相等判断*** 时不进行类型转换。

```js
null > 0 // null转化为number，为0，所以0>0结果为false。

null >= 0 // null转化为number，为0>=0，所以结果为true。

null == 0 // null在做相等判断时，不进行转型，所以null和0为不同类型数据，结果为false。
```

## 闭包

> 当函数能够记住并访问所在的词法作用域时，就产生了闭包。

> 闭包其实是一种特殊的函数，它可以访问函数内部的变量，还可以让这些变量的值始终保持在内存中，不会在函数调用后被垃圾回收机制清除。
