# Js 更好的条件语句

## 目录

- 数组方法 Array.includes

- 提前退出 / 提前返回

- 用对象字面量或Map替代Switch语句

- 默认参数和解构

- 用 Array.every & Array.some 匹配全部/部分内容

- 使用可选链和空值合并

## 数组方法 Array.includes

使用 `Array.includes` 进行多条件选择

例如：

```js
function printAnimals(animal) {
    if (animal === 'dog' || animal === 'cat') {
        console.log(I have a ${animal});
    }
}

console.log(printAnimals('dog')); // I have a dog
```

**解决方案:**

我们可以通过使用 `Array.includes` 来重写上面的条件

```js
function printAnimals(animal) {
   const animals = ['dog', 'cat', 'hamster', 'turtle']; 

   if (animals.includes(animal)) {
     console.log(I have a ${animal});
   }
}

console.log(printAnimals('hamster')); // I have a hamster
```

## 提前退出 / 提前返回

现在的需求是：

- 如果没有动物，抛出一个异常

- 打印动物类型

- 打印动物名字

- 打印动物性别

### 例子1

```js
const printAnimalDetails = animal => {
  let result; // declare a variable to store the final value

  // condition 1: check if animal has a value
  if (animal) {

    // condition 2: check if animal has a type property
    if (animal.type) {

      // condition 3: check if animal has a name property
      if (animal.name) {

        // condition 4: check if animal has a gender property
        if (animal.gender) {
          result = ${animal.name} is a ${animal.gender} ${animal.type};;
        } else {
          result = "No animal gender";
        }
      } else {
        result = "No animal name";
      }
    } else {
      result = "No animal type";
    }
  } else {
    result = "No animal";
  }

  return result;
};

console.log(printAnimalDetails()); // 'No animal'

console.log(printAnimalDetails({ type: "dog", gender: "female" })); // 'No animal name'

console.log(printAnimalDetails({ type: "dog", name: "Lucy" })); // 'No animal gender'

console.log(
  printAnimalDetails({ type: "dog", name: "Lucy", gender: "female" })
); // 'Lucy is a female dog'
```

能用三元运算符、&&条件等语法重构上面的功能，但让我们用多个返回语句编写更清晰的代码。

```js
const printAnimalDetails = ({type, name, gender} = {}) => {
  if(!type) return 'No animal type';
  if(!name) return 'No animal name';
  if(!gender) return 'No animal gender';

// Now in this line of code, we're sure that we have an animal with all //the three properties here.

  return ${name} is a ${gender} ${type};
}

console.log(printAnimalDetails()); // 'No animal type'

console.log(printAnimalDetails({ type: dog })); // 'No animal name'

console.log(printAnimalDetails({ type: dog, gender: female })); // 'No animal name'

console.log(printAnimalDetails({ type: dog, name: 'Lucy', gender: 'female' })); // 'Lucy is a female dog'
```

### 例子2

```js
function printVegetablesWithQuantity(vegetable, quantity) {
  const vegetables = ['potato', 'cabbage', 'cauliflower', 'asparagus'];

  // condition 1: vegetable should be present
   if (vegetable) {
     // condition 2: must be one of the item from the list
     if (vegetables.includes(vegetable)) {
       console.log(I like ${vegetable});

       // condition 3: must be large quantity
       if (quantity >= 10) {
         console.log('I have bought a large quantity');
       }
     }
   } else {
     throw new Error('No vegetable from the list!');
   }
}

printVegetablesWithQuantity(null); //  No vegetable from the list!
printVegetablesWithQuantity('cabbage'); // I like cabbage
printVegetablesWithQuantity('cabbage', 20); 
// 'I like cabbage
// 'I have bought a large quantity'
```

现在，我们有：

- if/else 语句过滤非法条件

- 3级嵌套if语句 (条件 1, 2, & 3)

一个普遍遵循的规则是：在非法条件匹配时提前退出。

```js
function printVegetablesWithQuantity(vegetable, quantity) {

  const vegetables = ['potato', 'cabbage', 'cauliflower', 'asparagus'];

   // condition 1: throw error early
   if (!vegetable) throw new Error('No vegetable from the list!');

   // condition 2: must be in the list
   if (vegetables.includes(vegetable)) {
      console.log(I like ${vegetable});

     // condition 3: must be a large quantity
      if (quantity >= 10) {
        console.log('I have bought a large quantity');
      }
   }
}
```

通过这么做，我们少了一个嵌套层级。当你有一个长的if语句时，这种代码风格特别好。

我们能通过条件倒置和提前返回，进一步减少嵌套的if语句。

```js
function printVegetablesWithQuantity(vegetable, quantity) {

  const vegetables = ['potato', 'cabbage', 'cauliflower', 'asparagus'];

   if (!vegetable) throw new Error('No vegetable from the list!'); 
   // condition 1: throw error early

   if (!vegetables.includes(vegetable)) return; 
   // condition 2: return from the function is the vegetable is not in 
  //  the list 


  console.log(I like ${vegetable});

  // condition 3: must be a large quantity
  if (quantity >= 10) {
      console.log('I have bought a large quantity');
  }
}
```

通过倒置条件2，代码没有嵌套语句了。这种技术在我们有很多条件并且当任何特定条件不匹配时，我们想停止进一步处理的时候特别有用。

所以，总是关注更少的嵌套和提前返回，但也不要过度地使用。

## 用对象字面量或Map替代Switch语句

来看看下面的例子，我们想要基于颜色打印水果：

```js
function printFruits(color) {
  // use switch case to find fruits by color
  switch (color) {
    case 'red':
      return ['apple', 'strawberry'];
    case 'yellow':
      return ['banana', 'pineapple'];
    case 'purple':
      return ['grape', 'plum'];
    default:
      return [];
  }
}

printFruits(null); // []
printFruits('yellow'); // ['banana', 'pineapple']
```

相同的功能能用对象字面量以更清晰的语法实现：

```js
// use object literal to find fruits by color
const fruitColor = {
    red: ['apple', 'strawberry'],
    yellow: ['banana', 'pineapple'],
    purple: ['grape', 'plum']
};

function printFruits(color) {
  return fruitColor[color] || [];
}
```

另外，也能用 `Map` 来实现相同的功能：

```js
// use Map to find fruits by color
const fruitColor = new Map()
    .set('red', ['apple', 'strawberry'])
    .set('yellow', ['banana', 'pineapple'])
    .set('purple', ['grape', 'plum']);

function printFruits(color) {
  return fruitColor.get(color) || [];
}
```

`Map` 允许保存键值对，是自从ES2015以来可以使用的对象类型。

对于上面的例子，相同的功能也能用数组方法`Array.filter` 来实现。

```js
const fruits = [
    { name: 'apple', color: 'red' }, 
    { name: 'strawberry', color: 'red' }, 
    { name: 'banana', color: 'yellow' }, 
    { name: 'pineapple', color: 'yellow' }, 
    { name: 'grape', color: 'purple' }, 
    { name: 'plum', color: 'purple' }
];

function printFruits(color) {
  return fruits.filter(fruit => fruit.color === color);
}
```

## 默认参数和解构

当使用 JavaScript 工作时，我们总是需要检查 `null/undefined` 值并赋默认值，否则可能编译失败。

```js
function printVegetablesWithQuantity(vegetable, quantity = 1) { 
// if quantity has no value, assign 1

  if (!vegetable) return;
  console.log(We have ${quantity} ${vegetable}!);
}

// results
printVegetablesWithQuantity('cabbage'); // We have 1 cabbage!
printVegetablesWithQuantity('potato', 2); // We have 2 potato!
```

如果 vegetable 是一个对象呢？我们能赋一个默认参数吗？

```js
function printVegetableName(vegetable) { 
   if (vegetable && vegetable.name) {
     console.log (vegetable.name);
   } else {
    console.log('unknown');
   }
}

printVegetableName(undefined); // unknown
printVegetableName({}); // unknown
printVegetableName({ name: 'cabbage', quantity: 2 }); // cabbage
```

在上面的例子中，如果vegetable 存在，我们想要打印 vegetable name, 否则打印"unknown"。

我们能通过使用默认参数和解构来避免条件语句 if (vegetable && vegetable.name) {} 。

```js
// destructing - get name property only
// assign default empty object {}

function printVegetableName({name} = {}) {
   console.log (name || 'unknown');
}


printVegetableName(undefined); // unknown
printVegetableName({ }); // unknown
printVegetableName({ name: 'cabbage', quantity: 2 }); // cabbage
```

因为我们只需要 `name` 属性，所以我们可以使用 `{ name }` 解构参数，然后我们就能在代码中使用 `name` 作为变量，而不是 `vegetable.name` 。

我们还赋了一个空对象 {} 作为默认值，因为当执行 `printVegetableName(undefined)` 时会得到一个错误：不能从 `undefined` 或 `null` 解构属性 `name` ，因为在 `undefined` 中没有 `name` 属性。

## 用 Array.every & Array.some 匹配全部/部分内容

能使用数组方法减少代码行。想要检查是否所有的水果都是红色的：

```js
const fruits = [
    { name: 'apple', color: 'red' },
    { name: 'banana', color: 'yellow' },
    { name: 'grape', color: 'purple' }
];

function test() {
  let isAllRed = true;

  // condition: all fruits must be red
  for (let f of fruits) {
    if (!isAllRed) break;
    isAllRed = (f.color == 'red');
  }

  console.log(isAllRed); // false
}
```

能用 `Array.every` 来减少代码行数：

```js
const fruits = [
    { name: 'apple', color: 'red' },
    { name: 'banana', color: 'yellow' },
    { name: 'grape', color: 'purple' }
];

function test() {
  // condition: short way, all fruits must be red
  const isAllRed = fruits.every(f => f.color == 'red');

  console.log(isAllRed); // false
}
```

相似地，如果想测试是否有任何红色的水果，我们能用一行 `Array.some` 来实现它。

```js
const fruits = [
    { name: 'apple', color: 'red' },
    { name: 'banana', color: 'yellow' },
    { name: 'grape', color: 'purple' }
];

function test() {
  // condition: if any fruit is red
  const isAnyRed = fruits.some(f => f.color == 'red');

  console.log(isAnyRed); // true
}
```

## 使用可选链和空值合并

这有两个为编写更清晰的条件语句而即将成为 JavaScript 增强的功能。

可选链允许我们没有明确检查中间节点是否存在地处理 tree-like 结构，空值合并和可选链组合起来工作得很好，以确保为不存在的值赋一个默认值。

这有一个例子：

```js
const car = {
    model: 'Fiesta',
    manufacturer: {
        name: 'Ford',
        address: {
            street: 'Some Street Name',
            number: '5555',
            state: 'USA'
        }
    }
} 

// to get the car model
const model = car && car.model || 'default model';

// to get the manufacturer street
const street = car && car.manufacturer && car.manufacturer.address && 
car.manufacturer.address.street || 'default street';

// request an un-existing property
const phoneNumber = car && car.manufacturer && car.manufacturer.address 
&& car.manufacturer.phoneNumber;

console.log(model) // 'Fiesta'
console.log(street) // 'Some Street Name'
console.log(phoneNumber) // undefined
```

所以，如果想要打印是否车辆生产商来自美国，代码将看起来像这样：

```js
const isManufacturerFromUSA = () => {
   if (car && car.manufacturer && car.manufacturer.address && 
     car.manufacturer.address.state === 'USA') {
     console.log('true');
   }
}


checkCarManufacturerState() // 'true'
```

能清晰地看到当有一个更复杂的对象结构时，这能变得多乱。有一些第三方的库有它们自己的函数，像 lodash 或 idx。例如 lodash 有 _.get 方法。然而，JavaScript 语言本身被引入这个特性是非常酷的。

这展示了这些新特性如何工作：

```js

```
