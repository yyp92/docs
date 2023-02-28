# ES6 导入导出

## 命名导入导出（named exports)

### 变量

```js
// 导出
export var myVar1 = ...;
export let myVar2 = ...;
export const MY_CONST = ...; 

// 导入
import { myVar1, myVar2, MY_CONST } from 'src/mylib';
```

### 方法

```js
// 导出
export function myFunc() {
    ...
}
export function* myGeneratorFunc() {
    ...
} 

// 导入
import { myFunc, myGeneratorFunc } from 'src/mylib';
```

### 类

```js
// 导出
export class MyClass {
    ...
} 

// 导入
import { myFunc } from 'src/mylib';
```

### 对象列出所有导出内容

```js
const MY_CONST = ...;
function myFunc() {
    ...
} 

// 导出
export { MY_CONST, myFunc };

// 导入
import { MY_CONST, myFunc } from 'src/mylib';
```

### 导出改名

```js
// 导出
export { MY_CONST as THE_CONST, myFunc as theFunc }; 

// 导入
import { THE_CONST, theFunc } from 'src/mylib';
```

### 导出从其他地方导入的模块

```js
export * from 'src/other_module';
export { foo, bar } from 'src/other_module';
export { foo as myFoo, bar } from 'src/other_module';
```

## 默认导出(default export)

```js
//------ myFunc.js ------
export default function () { ... };

//------ main1.js ------
import myFunc from 'myFunc';
myFunc();
```

## 命名导出结合默认导出

```js
// 导出
export default function (obj) {
    ...
};
export function each(obj, iterator, context) {
    ...
}
export { each as forEach }; 

// 导入
import _, { each } from 'underscore';
```

### default 的别名

```js
// 相等
import { default as foo } from 'lib';
import foo from 'lib';
```

```js
//------ module1.js ------
export default 123; 

// 相等
//------ module2.js ------
const D = 123;
export { D as default };
```

## 导入导出ts类型

```ts
// type.ts
export interface Props {
    name: string
    age: number
}
```

```ts
// user.ts
export {type Props } from './type
```

```ts
// userComp.tsx
import type {Props} from './user
```
