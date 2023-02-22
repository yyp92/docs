# TypeScript 类型 - 入门

![](../../\images\ts-simple-1%20.png)

- `keyof` 操作符的使用

- `TypeScript` 中的映射类型使用，以及映射类型结合`as` 关键字的使用

- `extends` 条件判断类型的基本使用

- `infer` 关键字的使用

## 前置知识

### 联合类型 `｜`

联合类型用 `｜` 分隔符，联合类型产生一个包含所有类型的**选择集**类型。联合类型表示一个值可以是几种类型之一，当一个变量希望传入某种类型时，可以考虑使用联合类型。

```ts
type IdType = string ｜ number ｜ boolean;

let id: IdType;
```

当变量 `id` 被 `IdType` 定义时候，`id` 值可以是 `string` 或者 `number` 或者 `boolean` 类型。

### 交叉类型 `&`

交叉类型用 `&` 分隔符，交叉类型产生一个包含**所有属性**的**新**类型。交叉类型可以更好的帮忙我们进行代码复用。

> 交叉类型在使用的时候有时候会产生一个新的类型 `never`,一般产生这种情况是两个 `interface` 使用交叉类型 `&` 进行处理，在 `interface1` 中有一个 `name:string` ,在 `interface2` 中有一个`name:number`，对于这种情况最后产生的新 `interface` ,里面的 `name` 属性类型会变为 `never` 类型 。 **因为没有一个类型即是 `string` 类型又是`number` 类型。**

### 交叉类型应用例子

联合类型大家应该很好理解，但是交叉类型可能不那么好理解，看一下下面的几个例子:

#### 例子1: 写业务，类型拆分解耦

有一个需求：做一个答题 `PK` 小程序问答记录排行榜的时候

问题记录`interface`定义

```ts
interface IQuestionRecord {
  createTime: string;
  userName: string;
  userAvatar: string;
  question: {
    title: string;
    content: string;
    picture: string[];
  };
}
```

答题记录`interface`定义

```ts
interface IAnswerRecord {
  createTime: string;
  userName: string;
  userAvatar: string;
  answer: {
    comment: string;
    audio?: {
      url: string;
    };
  };
}
```

这两个 `interface` 的定义，具有相同的片段，可以根据功能和模块拆分一下重复接口声明：

```ts
interface IUserBaseInfo {
  createTime: string;
  userName: string;
  userAvatar: string;
} 

interface IQuestionRecord {
  question: {
    title: string;
    content: string;
    picture: string[];
  };
} 

interface IAnswerRecord {
  answer: {
    comment: string;
    audio?: {
      url: string;
    };
  };
}
```

使用交叉类型进行接口混入:

```ts
type Mixin<T, X> = {
  [P in keyof (T & X)]: (T & X)[P];
}; 
// 更简单的写法
type Mixin<T, X> = T & X;

// 用泛型混入，方便之后还会出现什么数据也带有用户基础信息
// 方便做拓展和复用
type MixinUserBaseInfo<T> = Mixin<IUserBaseInfo, T>;

interface IRecordConfig {
  question?: MixinUserBaseInfo<IQuestionRecord>;
  answer?: MixinUserBaseInfo<IAnswerRecord>;
}
// 最终使用的时候输列表数据
export type RecordConfigList = IRecordConfig[];
```

#### 例子2: 处理函数参数

函数需要定义一些传参，而这些参数的定义可能用到多个函数，有时候是必填参数，有时候是可选参数

`IArgsBase` 接口

```ts
export interface IArgsBase<T>{
  name?: string;
  description?: string;
  visible?: boolean;
  execConf: T | (() => T);
}
```

`RequireArg` 类型

```ts
export type RequiredArg<T> = IArgsBase<T> & {
  required: true;
  value: T;
};
```

`OptionalArg` 类型

```ts
export type OptionalArg<T> = IArgsBase<T> & {
  required: false;
  value?: T;
};
```

`OptionalArg` 类型使用例子

```ts
nterface User {
  val: string;
} 

const testFun = (args: OptionalArg<User>): User => {
  return {
    val: '好好好',
  };
} 

testFun({ name: '123', required: true, execConf: { val: '123' } });
```

与 `例子 1` 异曲同工，只是使用场景有些不同。

```ts
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
};

interface Person {
    name: string;
    age: number;
    location: string;
}

type LazyPerson = Getters<Person>;
// {
//   getName: () => string;
//   getAge: () => number;
//   getLocation: () => string;
// }
```

#### 例子3 我们用其他组件库的组件，但是会在他的 props 基础上增加一些我们的 props 属性

以开发某个组件为例。

```ts
class CustomModal<T> extends React.Component<ComponentProps & T> {}
```

## TS 实现一个 Pick 功能

### Pick 的功能介绍

`Pick<T, K>`从一个复合类型 `T` 中取出几个想要的属性 `K`，构造一个新类型。

> `Pick` 的英文意思：摘；捡

```ts
// 定义一个用户信息原始类型
interface IUser {
  name: string;
  age: number;
  number: number;
}
```

现在还需要定义一个编辑用户新的类型，只需要 `name` 和 `age` 因为 `number` 不可修改。

```ts
interface IEditUser {
  name: string;
  age: number;
}
```

上面的这种写法可以实现需求，但是如果我们修改 `IUser` 中的内容，后面 `IEditUser` 也可能需要修改，并且代码量也很大。 `Pick` 就解决了以上问题。

```ts
type EditUser = Pick<IUser, 'name' | 'age'>;
```

### 源码实现

知道了`Pick`的功能，我们自己实现一个

```ts
type MyPick<T, K extends keyof T> = { [S in K]: T[S] };
```

### 从本题学到的知识点

#### keyof 操作符

`keyof` 操作符是在 `TypeScript2.1` 版本中增加的。

```ts
interface IUser {
  name: string;
  age: number;
  number: number;
} 

type UserKeys = keyof IUser; // "name" | "age" | "number" 联合类型
```

`keyof` 用于返回对应类型所有 `Key` 的`联合类型`。

工作中关于 `keyof` 常用的一个场景。一个函数，接受两个参数，参数一是一个对象，参数二是这个对象中 `key`，如何用 `TypeScript` 编写函数。

```ts
function getValue<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}
```

使用 `keyof` 可以得到所有 `key` 的联合类型，并且 `obj[key]` 会被推断为 `T[K]` 正确的返回值类型，不再是 `any` 类型。

#### 拓展 `typeof`

看到 `keyof` , 我们会想到 `typeof`，它常用来推到类型

推断一个 `Interface` 类型

```ts
interface IUser {
  name: string;
  age: number;
  number: number;
} 

type UserType1 = typeof IUser; 

let u: UserType1 = {
  name: 'xiao2',
  age: 18,
  number: 001,
};
```

推断一个 `class` 类型

```ts
class TestClass {
    constructor(public name: string, public age: number){}
} 

type Instance = InstanceType<typeof TestClass>; // 
```

官网中还有个小例子在 `ReturnType` 中使用,推导一个函数的返回值，直接传入函数是有问题的

```ts
function f() {
  return { x: 10, y: 3 };
} 

// 错误使用
type P = ReturnType<f>;
// 报错 'f' refers to a value, but is being used as a type here. Did you mean 'typeof f'?

// 正确使用
type P = ReturnType<typefo f>;
// type p = {x:number,y:number}
```

#### 映射类型语法

```ts
{[S in K]:T}
```

> 官方文档对映射类型也有解释和例子，感兴趣的看看 https://www.typescriptlang.org/docs/handbook/2/mapped-types.html

`in` 操作符用于遍历 `K` 类型中的所有类型。可以把它理解为 for...in

```ts
type InExample = 'a' | 'b' | 'c' | 'd'; 

type Obj = {
  [T in InExample]: string; // 遍历InExample，定义每一个key的类型为string
}; 

// 上面Obj等价于;
/*type Obj = {
    a: string,
    b: string,
    c: string,
    d: string
}*/
```

`T` 类型变量可以用于表示 `TS` 中的任意类型

在 `Pick` 的实现中，`{[S in K]: T[S]}` 这段实现就有用到映射类型语法,表示遍历类型 `K`中所有的类型。 `T[S]` 可以理解为属性访问的语法，用来获取对象类型某个属性对应值的类型。

#### extends

> `Pick` 的源码实现中 `extends` 不是继承的意思，我看网上好多文章写成继承，会产生误导。这里是用来约束类型。 第二个泛型输入的 `Key` 被约束在 `T` 的 `key` 内，如果超过这个范围会报错。

这里先简单提一下 `extends` 关键字，下一道题会对 `extends` 详细讲解。

## 我可以一打三了

### 实现 `Partial`

> `artial` 的英文意思是：部分的，不完全的。

`Partial` 用于 修改类型中属性为可选类型

```ts
type Partial<T> = {[P in keyof T]?: T[P]}
```

### 实现 `Required`

> `Required` 的英文意思是：必须的。

`Required` 用于修改类型中可选属性为必填属性

```ts
type Required<T> ={[P in keyof T]-?: T[P]}
```

### Exclude 的功能介绍

`Exclude` 用于删除类型集合中的指定类型

> `Exclude` 英文的意思：不包括，把......排除在外

```ts
type Type1 = string | number; // 联合类型
type TypeExclude = Exclude<Type1, string>; // number
```

### 源码实现

```ts
type Exclude<T, U> = T extends U ? never : T;
```

### 从本题学到的知识点

#### extends 条件类型

```ts
T extends U ? X : Y
```

条件类型是一种条件表达式进行类型的关系检测，条件类型在理解的时候可以想到类似`JavaScript` 的三元表达式，`T`, `U`, `X` 和 `Y` 代表了任意类型, 如果 `T` 类型可以赋值个类型 `U` ，返回类型 `X` 否则返回类型 `Y`。

条件类型可以结合条件链使用，用来同时判断多种类型，看个条件链的例子：

```ts
type TypeCheck<T> = T extends string
  ? 'string'
  : T extends number
  ? 'number'
  : T extends boolean
  ? 'boolean'
  : T extends undefined
  ? 'undefined'
  : T extends Function
  ? 'function'
  : 'object'; 

type T0 = TypeCheck<string>; // "string"
type T1 = TypeCheck<'a'>; // "string"
type T2 = TypeCheck<true>; // "boolean"
type T3 = TypeCheck<() => void>; // "function"
type T4 = TypeCheck<string[]>; // "object"
```

例子中我们都是传入的一个类型，假如传入联合类型会返回什么结果呢？

```ts
T extends U ? X : Y 

T => A | B | C // 假如T是联合类型 A ｜ B ｜ C 

A | B | C extends U ? X : Y  =>
(A extends U ? X : Y) | (B extends U ? X : Y) | (C extends U ? X : Y)
```

这里也就证明了上面的联合类型的执行结果。

> `extends` 条件类型的讲解参考官网的例子进行讲解，小伙伴们也可以去官网看下条件类型

## 我继续可以一打四了

### 实现 `Exclude`

`Exclude` 用于删除联合类型中的指定类型

```ts
type Exclude<T, U> = T extends U ? never : T;
```

### 实现 `Extract`

> `Extract` 的英文意思：提取，提炼，取出

`Extract` 用于从联合类型中提取特定类型

```ts
type Extract<T, U> = T extends U ? T : never;
```

### 实现 `NonNullable`

> `Nullable` 英文意思：可为空的；可空类型

`NonNullable` 用于删除联合类型中的 `null`和 `undefined`

```ts
type NonNullable<T> = T extends null | undefined ? never : T;
```

### 实现 `Record`

`Record` 用于生成接口类型的 `Record`, `Record` 接收两个泛型参数：第一个参数作为接口类型的属性，第二个参数是接口类型的属性值

```ts
type Record<K extends keyof any, T> = {
  [P in K]: T;
}; 

// 使用 Record 例子
type testRecord = Record<string, string>
```

> 注意这里有一个 `keyof any` 。在 `TypeScript` 中，`keyof any` 可以作为对象健的属性

```ts
type T = keyof any; // => string | number | symbol
```

目前，`JavaScript` 仅支持 `string`，`number`，`symbol` 作为对象的键值。

## TS 实现一个 Omit 功能

### Omit 功能介绍

> `Omit` 的英文意思是：省去。

`Omit` 用来 **删除** 符合类型 `T` 中不需要的属性 `K`，生成新的类型。

```ts
type Person = {
  name: string;
  sex: string;
}; 

type newPerson = Omit<Person, 'name'>; // {sex: string}
```

例子中使用 `Omit` **删除** 了 `Person`的 `name` 属性。

### 源码实现

使用 `Exclude` 结合 `Pick` 实现：首先使用 `keyof` 获取 `T` 中的所有 `key`生成一个`key联合类型`，`Exclude` 删除 `key联合类型`中不需要的 `key`，获取一个新的 `key联合类型`。 然后使用 `Pick` 从 `T` 中捡出需要的 `key`属性，构成一个**新类型**。

```ts
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;
```

我们不使用 `Pick` 和 `Exclude`，自己实现一下

```ts
// 删除
type MyExclude<T, U> = T extends U ? never : T;
type MyPick<T, K extends keyof T> = { [S in K]: T[S] };
type Omit<T, K extends keyof T> = MyPick<T, MyExclude<keyof T, K>>;
```

再换一种写法(这种写法可能不理解，继续看下面的知识点讲解就懂了)

```ts
type MyOmit<T, K extends keyof T> = {
  [P in keyof T as P extends K ? never : P]: T[P]
}
```

### 学到的知识点

#### as 语句

`as` 语句的作用：会对**映射类型**中的键进行**重新映射**（TypeScript4.1版本中新增加的语法）

`as` 语句后面新映射类型必须是 `string|number|symbol` **联合类型**的子类型。

上面的 `MyOmit` 例子

```ts
type MyOmit<T, K extends keyof T> = {
    [P in keyof T as P extends K ? never : P]: T[P]
}
```

`as P` 键重新映射时，`extends` 语法返回 `never` 类型时，该键就会被删除。

`as` 语句的一些应用场景，可以利用 `as` 语句定义自己的工具类型,我们可以定义一个 `Getters` 工具类型，用于为对象类型生成对应的 `Getter` 类型： (注：此函数借鉴**阿宝哥**TS 映射类型文章中的一个函数)。

```ts
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
};

interface Person {
    name: string;
    age: number;
    location: string;
}

type LazyPerson = Getters<Person>;
// LazyPerson 打印结果
// {
//   getName: () => string;
//   getAge: () => number;
//   getLocation: () => string;
// }
```

## 学完 Omit 和 前面的映射类型 我打一个

### 实现 CustomRequired

有一个如下接口，我想要把其中**某个属性**修改为必填(非可选)。

```ts
interface User {
  name?: string;
  age?: number;
  hobby?: string;
}
```

使用 TS 的类型实现

```ts
type CustomRequired<T, K extends keyof T> = Omit<T, K> & {
    [P in K]-?: T[P];   // 映射类型
};
```

使用方式

```ts
type SpecialUser = CustomRequired<User, 'name'>

// 打印结果
// interface User {
//  name: string;
//  age?: number;
//  hobby?: string;
// }
```

- `CustomRequired` 先使用 `Omit` 删除要改为必填的属性返回一个新的 `interface`。例子中先从 `User` 中却掉 `name` 属性。

- 再与一个不可选的 `name` 接口进行 `&` 合并。

## TS 实现一个 ReturnType

### ReturnType 的功能介绍

`ReturnType` 接收一个函数类型作为参数，推断出函数的`返回值类型`。 

```ts
type ReturnType1 = ReturnType<() => number>; // number
type ReturnType2 = ReturnType<(s: string) => void>; // void
type ReturnType3 = ReturnType<typeof Math.random>; // number
type ReturnType4 = ReturnType<typeof Array.isArray>; // boolean
```

### 源码实现

```ts
/**
 * Obtain the return type of a function type
 */
type ReturnType<T extends (...args: any[]) => any> = T extends (...args: any[]) => infer R ? R : any;
```

源码实现中 `ReturnType` 的泛型参数约束为 **函数类型**，如果 `T` 满足约束，会通过 `infer` 关键字推断出函数返回类型为 `R`，否则返回 `any` 类型。

### 学到的知识点

#### infer

`infer` 的英文意思：推断`infer` 是 `typescript 2.8`中新增的关键字。可以**结合** `extends`条件语句推断待推断的类型。

`ReturnType` 的源码实现直接看好像有些复杂, 我们先从简单的内容来，`infer` 在没有出现之前，我们想推断一个数组中元素的类型怎么做？

```ts
type Uuids = number[];
type Names = string[];
// 等等数组类型 Symbol[] 等 

// 请注意，如果有多种数组类型，这个条件语句可能要一直?下去
type Unpacked<T> = T extends Uuids ? number : T extends Names ? string : T;  

type UuidType = Unpacked<Uuids>; // UuidType 的类型为 number
type NameType = Unpacked<Names>; // NameType 的类型为 string
```

通过上面这种 `Unpacked`，我们需要条件语句判断各种不同的类型，一直写下去，呜呜呜！

有了 `infer` 关键字之后，修改一下代码:

```ts
type Uuids = number[];
type Names = string[];

type Unpacked<T> = T extends (infer R)[] ? R : T;

type UuidType = Unpacked<Uuids>; // UuidType 的类型为 number
type NameType = Unpacked<Names>; // NameType 的类型为 string
```

`T extends (infer R)[] ? R : T` 使用条件语句，如果 `T` 是某个待推断类型的数组，则返回推断的类型，否则返回 `T`。 看完这个例子再回去看 `ReturnType<T>` 的实现，`ReturnType<T>` 只是将 `infer P` 从参数位置移动到返回值位置，因此此时 `P` 即是表示待推断的返回值类型。

使用 `infer` 获取 `Promise<xxx>` 类型中 `xxx` 类型, 也比较常用:

```ts
type Res = Promise<number[]>;
type Unpacked<T> = T extends Promise<infer R> ? R : T;
type resType = Unpacked<Res>; // resType 类型为number[]
```

##### `infer` 使用条件

官方文档中的有说明：

> Within the extends clause of a conditional type, it is now possible to have infer declarations that introduce a type variable to be inferred. Such inferred type variables may be referenced in the true branch of the conditional type. It is possible to have multiple infer locations for the same type variable.

`infer` 需要在条件类型的 `extends` **子句**中，推断的**类型变量**需要可以在条件类型的 `true` 分支中引用。

本文对 `infer` 的常用场景进行了讲解，感兴趣的同学可以去官方文档阅读更详细的内容 (TypeScript2.8)[https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html]

## 最后打三个

### 实现 PropertyType

`PropertyType` 用于推断出类型中键的类型。

```ts
type UserInfo = {
  id: number;
  userName: string;
  userAvatar: string;
}
type PropertyType<T> = T extends {id: infer U, userName: infer R, userAvatar: infer K} ? [U,R,K]:T;

// 使用PropertyType
type TestProperty = PropertyType<UserInfo>;// [number,string,string]
```

通过 `infer` 声明了类型变量 `U R K` ，分表表示对象类型中`id userName userAvatar` 属性要推断的类型。若类型匹配，满足条件语句的 `true` 条件，会以**元组**的形式返回 `id userName userAvatar` 属性的类型 `[number,string,string]`。

### 实现 ConstructorParameters

`ConstructorParameters` 用于获取类中构造函数的参数类型 代码实现。

```ts
type ConstructorParameters<T extends new (...args: any[]) => any> = T extends new (...args: infer P) => any
  ? P
  : never;

class TestClass {
  constructor(public name: string, public age: number) {}
}
type Params = ConstructorParameters<typeof TestClass>; // [string, number]
```

`ConstructorParameters` 的参数 `T extends new (...args: any[]) => any` ，是对构造函数的类型约束。作用于构造函数的参数位置`T extends new (...args: infer P) => any` ，可推断构造函数参数类型。

```ts
// 构造函数
type Constructor = new (..args:any[]) => any;
```

### 实现 InstanceType

`InstanceType` 用于获取实例类型。

```ts
type InstanceType<T extends new (...args: any[]) => any> = T extends new (...args: any[]) => infer R ? R : any;

class TestClass {
  constructor(public name: string, public age: number) {}
}

type Instance = InstanceType<typeof TestClass>; // TestClass
```

作用于构造函数的返回值位置`new (...args: any[]) => infer P;` ，可推断构造函数实例类型。

## 参考文章

- [TypeScript 官方手册](https://www.typescriptlang.org/docs/handbook) 

- [TypeScript 官方手册-中文版](https://www.tslang.cn/docs/handbook/advanced-types.html)

- [阿宝哥的 TypeScript 讲解](https://juejin.cn/post/7096265620445986823) 

- [ts 入门教程](https://ts.xcatliu.com/basics)
