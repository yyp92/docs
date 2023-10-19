# TypeScript 内置工具类型

## Partial（将属性变为可选）

`Partial<T>` 用于将一个类型的所有属性变为可选的。

**源码：**

```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```

- `{}`就代表是声明为一个字面量对象
- 使用`keyof`将`T`类型的`key`遍历出来，并赋值给`P`，`P`作为新的`key`值
- 通过`T[P]`就可以获取到值类型
- 这时再给键后面加上`?`就变成可选值了

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/JYOwLgpgTgZghgYwgAgKoGdrIN4FgBQyyIcAthAFzLphSgDmANAUXPZcQK6kBG0BAXwIEA9COSBSo0CYqQQQB7EDWSdMUKhiwBeHMPxD8usAE8ADig1QAjMm0AFOFDDA4AGwA8FgHwBuXWOSAsHLSgDD-gAdqgFxygHo6gOQGgBvK4YD3yoCQCYBhcrIKSirQluqq1tp4BnpAA)

```ts
interface User {
  name: string,
  age: number
}

// 报错
const user: User = {

}


type User1 = Partial<User>;

// 不报错，因为属性变为可选了
const user1: User1 = {

}
```

## Required（将属性变为必选）

`Required<T>`用于将一个类型的所有属性都定义为必不可少的。

**源代码**：

```ts
type Required<T> = {
    [P in keyof T]-?: T[P];
};
```

- `{}`就代表是声明为一个字面量对象
- 使用`keyof`将`T`类型的`key`遍历出来，并赋值给`P`，`P`作为新的`key`值
- 通过`T[P]`就可以获取到值类型
- 这时再给键后面加上`-?`就变成必选值了

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/JYOwLgpgTgZghgYwgAgKoGdrIN4FgBQyyIcAthAFzLphSgDmANAUXPRAPxUgCupARtAIBfAgQD045IFg5QKVGgTFSCCAPYgayHpihUMWALw4WxMpWQByQIuJgeB1A6SlmRY-ATABPAA4pdUAIzIDAJQgARx5gKAgAEwAeLwA+AG5HSWQFQBh-wAO1QC45QAEjQEhzQD0dQHIDQF+EwA3lTMBR-UBIBMAwuSVVdU1obx0tXwM8QmNyKktbe3xhIA)

```ts
interface User {
  name: string,
  age?: number
}

// 不报错
const user: User = {
  name: '小明'
}


type User1 = Required<User>;

// 报错，因为所有属性都变为必选了
const user1: User1 = {
  name: '小明'
}
```

## Readonly（将属性变为只读）

`Readonly<T>`用于将一个类型的所有属性都定义为只读的。

**源代码：**

```ts
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

- `{}`就代表是声明为一个字面量对象
- 使用`keyof`将`T`类型的`key`遍历出来，并赋值给`P`，`P`作为新的`key`值
- 通过`T[P]`就可以获取到值类型
- 这时再给键的前面加上`readonly`就变成只读属性了

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/JYOwLgpgTgZghgYwgAgKoGdrIN4FgBQyyIcAthAFzLphSgDmANAUXPZcQK6kBG0BAXwIEEAexA1knTFCoYsAXhwtiZDgHJAi4mB4HUDpKeuaFkbDgCYAzOcEEA9DeSBYOUClRoExUgtOgA6EuWRL1gJfpgNNe6gDcwvgEYACeAA4o8lAAjH7IAEoQcAAm4gA20QA8iQB84ZH4YhJgUjJJcrWpeEY+Gjr6hqzsVBZW+EL4dsiugDD-gAdqgFxygAJGgJDmgHo6gOQGgL8JgBvK44BXyoDf0YBhcu613mqpgSGhQA)

```ts
interface User {
  name: string,
  age: number
}

const user: User = {
  name: '小明',
  age: 233
}
// 不报错
user.name = '韩立';


type User1 = Readonly<User>;

const user1: User1 = {
  name: '小明',
  age: 233
}
// 报错，因为所有属性都变为只读了
user1.name = '韩立';
```

## Pick（挑选属性作为新类型）

`Pick<T, K extends keyof T>` 可以用来从一个类型中，挑出部分属性来组成一个新的类型。

**源代码：**

```ts
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

- 通过`extends`进行限制，`K`只能由`T`的键类型组成
- `{}`就代表是声明为一个字面量对象
- 使用`in`遍历`K`类型，并赋值给`P`，`P`作为新的`key`值
- 通过`T[P]`就可以获取到对应的值类型

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/JYOwLgpgTgZghgYwgAgKoGdrIN4FgBQyyIcAthAFzLphSgDmA3AUXPZcQK6kBG0z+AL4ECAelHJAiMaBIBOJkUgPR1A5AaARv0AIRoAbTQCFugbx9A0eoEwATwAOKDNACMyALzIACsAQBrADwWoAGmQByEuR8APgExCUBSo0BMVIIEAHsQGmROTCgqD2s7PEI5ciofQEXEwHgdQHSUn08WZDYOACYAZhqyoRD8cWRAY2tAOw9AGH+05EAr5UBIc2zFJWi4hKSrVOT0nHL-Dnzi0oJBRiA)

```ts
interface User {
  name: string;
  age: number;
}

// 挑选 name 属性组成新的类型
type User1 = Pick<User, 'name'>;

// 报错
const user: User1 = {
  name: '小明',
  age: 233,
};

// 正确，User1 只有 name 属性
const user1: User1 = {
  name: '小明',
};
```

## Omit（排除类型中的部分属性）

`Omit<T, K extends keyof any>` 根据传入的条件，来排除掉类型的部分属性，然后组成一个新的类型。​

**源代码：** 

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

含义为：从`T`类型中挑出在`K`类型中没有的属性，然后组成一个新的类型。

- 首先使用Exclude排除掉`T`与`K`当中都有的属性，并返回结果
- 然后使用Pick从`T`中挑选出符合返回结果的属性，组成新的类型

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/JYOwLgpgTgZghgYwgAgKoGdrIN4FgBQyRyIcAthAFzLphSgDmANAccnA1SQK5kBG0FoWJwAJqKgR06arXohmBAL4ECAegBUG1sUAfboGV9QHFygLH+dRMAE8ADigzQAjMgC8OM2xLkucxgG43bDi4QXgEoP2EiFUINNQJLGzRMKEcXAHkyYDAAHjsoJmQAcjEJKXQCgD5wggQAexBaZG4k6lyU1wiPCmoCwEXEwHgdQHSUgqEAzmp7AA4R4jU1dnFJaWRAJ91AaojAJONAEzTAGH-Ab9tAAqVALHlAUqNATFS3YsWZQsB-PUA93WHlIA)

```ts
interface User {
    name: string,
    age: number,
    address: string,
}

/**
    相当于：
    type User1 = {
        name: string;
        age: number;
    }
 */
type User1 = Omit<User, 'address'>;

const user: User1 = {
    name: '小明',
    age: 18,
    // address 已被排除，添加会报错
    address: '广州',
}
```

`type User1 = Omit<User, 'address'>`的转换过程：

- ​`Exclude<keyof User, 'address'>`
  
  - `keyof User`结果为`'name' | 'age' | 'address'`
  
  - 排除掉属性`address`，返回结果为`'name' | 'age'`

- 根据上一步的结果，则有`Pick<User, 'name' | 'age'>`

返回结果为：

```ts
{
  name: string;
  age: number;
}
```

## Record（创建对象类型）

`Record<K extends keyof any, T>` 可以通过`K`作为键，`T`作为值类型来组成一个新的类型。

**源代码：**

```ts
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```

- `K`可以是任意类型。
- `{}`就代表是声明为一个字面量对象
- 使用`in`遍历`K`类型，并赋值给`P`，`P`作为新的`key`值
- 而`T`就作为值类型

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/JYOwLgpgTgZghgYwgAgKoGdrIN4FgBQyyIcAthAFzLphSgDmA3AUXPZcQK6kBG0z+AL4ECAelHJA98qBTuUAUroFNzQAppgLO1AAHKAqOUCf2oEMYwCFugOlTAcHKAeBV2BvH0DR6gTABPAA4oM0AIzIAvMgBKEBAHsoACYAPDR0IPQANGiYUAB8AgR+IDTInDFUTlCuHniEyABWiADWVLlERCTkVADkgIuJgPA6gOkp1REs5WwcAEwAzN2teYL9ggn4IvjiyAqKgGxKloBXyoC-AYD0ZvrGgCN+gAhGujrW9igA8jz57l4+-sGhDFGcIEUgvgDuIPFjE4DG1oB2Hom+yWDIvkcqIdjjk2pUONVADFygGJDQAK2tUCMNXhJAKVGgExU76-f5HZxAo4nADaAF0RkA)

示例 1：

```ts
interface User {
  name: string;
  age: number;
}

// 可以用来限定一个对象的键与值的类型
type User1 = Record<string, User>;

const user: User1 = {
  jack: {
    name: '小明',
    age: 233,
  },
};
```

示例 2：

```ts
// 用来限制类型只能是键值组成的对象
type Obj = Record<string, unknown>;

// 正确
const obj: Obj = {
  name: '二愣子'
};

// 报错
const obj1: Obj = [];
```

## Exclude（排除联合类型的部分类型）

`Exclude<T, U> = T extends U ? never : T` 通过条件类型限制，将一个类型中的部分类型进行排除，然后组成一个新的类型。​

**源代码：**

```ts
type Exclude<T, U> = T extends U ? never : T;
```

- `T`可以转换成`U`类型，则返回`never`类型，否则返回`T`类型
- `never`类型表示永不存在的值的类型，所以相当于将这个类型去除了

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/C4TwDgpgBAKgDFAvFA5AQxVAPqgRpnFAYxQG4BYAKCoHoAqOqqOqQJONATNMG8fQaPUoimWgN9NYARiiAQtx6APt0DK+oDi5KKEiikUAERo12dbjUCaVJdBhjkAUQAeRADYBXACYQAPPAA0qEgD4K1SkQD2AHYAzsBQaABcJqroZFQBIWG4UaZ4cZQ0NFCApUaAmKnxQaF8KTEkpEA)

```ts
type T0 = 'a' | 'b' | 'c';

/**
 * 排除类型 c
 * 此时 T1 的类型相当于 type T1 = "a" | "b"
 */
type T1 = Exclude<T0, 'c'>;

const a:T1 = 'a';
const b:T1 = 'b';
// 报错
const c:T1 = 'c';
```

## Extract（提取联合类型的部分类型）

`Exclude<T, U>` 是根据条件排除，而`Extract`就是与其相反，根据条件提取类型，然后组成一个新的类型。​

**源代码：**

```ts
type Extract<T, U> = T extends U ? T : never;
```

- `T`可以转换成`U`类型，则返回`T`类型，否则返回`never`类型
- `never`类型表示永不存在的值的类型，所以相当于将这个类型去除了
- 最后组成的新类型，都是可以转换成`U`类型的

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/C4TwDgpgBAKgDFAvFA5AExVAPqimcoBmKA3ALABQlA9AFS2VS1SALxoGvKg3j6DR6lGlIHByUCI2aA301gBGKIBC3boA+3QMr6gOLkooSJKRQARGi3ZtELSOqU10GFOQBRAB7AATgEMAxsAA88ADSoM+lHgA+cioKZwB7ADsAZ2BeAC4LTXRSSnDo2IgEy1wUimpqKEBSo0BMVNTImKhCLKTiEiA)

```ts
type T0 = 'd' | 'e' | 'f';

/**
 * 提取类型 d 与 e
 * 时 T1 的类型相当于 type T1 = "d" | "e"
 */
type T1 = Extract<T0, 'd' | 'e'>;

const d:T1 = 'd';
const e:T1 = 'e';
// 报错
const f:T1 = 'f';
```

## NonNullable（排除null | undefined类型）

`NonNullable` 可以用来排除掉类型中的 `null` 和 `undefined` 类型，然后返回新类型。​

**源代码：**

```ts
type NonNullable<T> = T extends null | undefined ? never : T;
```

- 这个比较好理解，就是判断是否可以转换为 `null | undefined` 类型，然后进行返回

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/C4TwDgpgBAKgjFAvFA5AQxVAPqgRpnAOwFcAbU7KYwgEwgDMBLQiGgbgFgAoUSWAJiRQAcgHtCwsqTS5SEADzwAfJy7duAY3EBnYFDQAuWAmQlyqrYV1RcR+EOp0mLdtwD0bqIFO5QNBygY7lAQA9AUqNATFTAGH-AA7VALjkoMwpAGJUqWgZmVihAbx9AaPVAJ91AaojAbuVAEzTNHT0NO0FTKQtSqBoKh2TnVlUgA)

```ts
type T1 = 'a' | 'b' | null | undefined;
type T2 = NonNullable<T1>;


const a: T1 = null;
const b: T1 = undefined;
// 以下代码报错，因为 null 和 undefined 类型已被去除
const c: T2 = null;
const d: T2 = undefined;
```

## Parameters（使用函数形参创建元组）

`Parameters` 用于推断一个函数类型的形参类型，然后组合成一个有名称的元组类型。

**源代码：**

```ts
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;
```

- 首先 `T` 有约束条件，得为一个函数类型
- 在实际使用的时候，使用 `infer` 推断参数的类型并赋值给 `P`
  - `infer` 需要跟 `extends` 配合使用
- 返回的 `P` 类型是个有名称的元组

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/JYOwLgpgTgZghgYwgAgKoGdrIN4FgBQyyIcAthAFzLphSgDmANAUXPZcQK6kBG0z+AL4ECYAJ4AHFADEA9rOQBeZAApOmKFQzQAlEoB8yAG6zgAEwDcI-AHoAVHZaAAc0ATfoGW-QDnmgLH+W4qcgAFJWQAbXVoLQ0AXRY7G1FJFCDlALgoMghIKHQAHjlZfSt8AgRZEBpkcKhgvEIiYgyqAHJARcTAeB1AdJSmgXrkNg4ARgAOAWF8UvKwPqpk0KqoiyA)

```ts
interface User {
  name: string,
  age: number,
}

type Foo = (user: User) => void;

/**
 最终结果：
 type P = [user: User]
 */
type P = Parameters<Foo>;

const user = {
    name: '金小钗',
    age: 18,
}
const a: P = [user];
```

更多例子：

- [官网](https://www.typescriptlang.org/docs/handbook/utility-types.html#parameterstype)
- [相关文章](https://juejin.cn/post/6994102811218673700#heading-11)

## ReturnType（获取函数返回值类型）

`ReturnType` 可以用来获取一个函数的返回值类型。​

**源代码：**

```ts
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```

- 首先 `T` 有约束条件，得是一个函数
- 在实际使用的时候，使用 `infer` 推断返回值的类型并赋值给 `R`
  - `infer` 需要跟 `extends` 配合使用
- 如果返回值类型推断不出来则会返回 `any`

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/JYOwLgpgTgZghgYwgAgKoGdrIN4FgBQyRyIcAthAFzLphSgDmA3AccnA1SQK5kBG0FvgC+BAmACeABxQAxAPbzkAXmQAKAJQqAfGkxQhBAPQAqE6xPJAH26BlfUBxcoCx-i8kkzkAFRV7BFo+OkonqoAShBg3FAg7gEAPAry2ob4CPIgtMjc+tRBOKzEpBTUAOSAi4mA8DqA6SlFADR5RBxcAEwAzM21IgRAA)

```ts
interface User {
    name: string;
    age: number;
}

type Foo = () => User;

/**
 * 相当于：
 * type T = User;
 */
type T = ReturnType<Foo>;

const user: T = {
    name: '小明',
    age: 233,
}
```

## ConstructorParameters（使用构造函数形参创建元组）

`ConstructorParameters` 用于推断一个类的构造函数的形参类型，然后组合成一个有有名称的元组类型。​

**源代码：**

```ts
type ConstructorParameters<T extends abstract new (...args: any) => any> = T extends abstract new (...args: infer P) => any ? P : never;
```

- 首先 `T` 有约束条件，得有一个构造函数
- 在实际使用的时候，使用 `infer` 推断参数的类型并赋值给 `P`
  - `infer` 需要跟 `extends` 配合使用
- 返回的 `P` 类型是个有名称的元组

代码示例：

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/PTAEEsDsBcFMCcBmBDAxrUBVAzg0BvAWACgRRzzJYB3ACgEoAuLXeAbhLItEmQFtYzbNHhQA5h1JhuyMYJ4BXPgCMEksgF8SqADbJs2FniLFuAB1EA3ZHB795w0ZAklzVmxlnzIS1exKuFKgA9pCOCqjQwfC0vAJCIuIANKBezD4qCPQEgdzk0AAW4NgAdHEYALx2ApJ5FIXFJV6gVV61FFrEnZxg0ACeZhgAKi2gAMKh4ZHRAArI8PZw8NgAPDgIAHyS-YOgI1UTYSIRUfBzCwJLqzuwwYhG8FsBxCFHoAqszPugANoA5IBFxMA8DqAdJS-ikAIwADgAumwgA)

```ts
// interface User {
//     new(): User;
//     name: string;
//     age: number;
// }
class User {
    private name: string;
    private age: number;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }
}

// type T = ConstructorParameters<User>;
type T = ConstructorParameters<typeof User>;

const user: T = ['小明', 18];
```

更多详细信息请看下[其他文章](https://juejin.cn/post/6994102811218673700#heading-12)。

## InstanceType（获取构造函数返回值类型）

`InstanceType` 可以用来获取一个类的构造函数的返回值类型。​

**源代码：**

```ts
/**
 * Obtain the return type of a constructor function type
 */
type InstanceType<T extends abstract new (...args: any) => any> = T extends abstract new (...args: any) => infer R ? R : any;
```

- 首先 `T` 有约束条件，得是一个构造函数
- 在实际使用的时候，使用 `infer` 推断返回值的类型并赋值给 `R`
  - `infer` 需要跟 `extends` 配合使用
- 如果返回值类型推断不出来则会返回 `any`

**代码示例**：

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/JYOwLgpgTgZghgYwgAgKoGdoGED2J1hQCuYOUyA3gLABQy9yIEA7gBQCUAXGplANy0AvrVCRYiFBmiVaDRnAC2EbgSigA5gLoM465YyIKARtC3CatAPQAqa7OvJAH26BlfUBxcoCx-+8jABPAA4oACoAjMgAvDym9pa0vgHIIeHIAJL4YHAgSIH+EAA8UlC4acSkUAB8WrQIeATIRLzciRHU2vQgivoA5ICLiYDwOoDpKZ0ANLI6etwATADMUyM0gpUWS3FBE0mpBBlZObkAolBQZEWqRAilFbRVNWDI0IdQjWvNo20d3J13wy-ISujoul1AJipgHvor7zIA)

```ts
interface UserConstrutor {
    new(): User;
}
interface User {
    name: string;
    age: number;
}

/**
 * 相当于：
 * type T1 = User;
 */
type T1 = InstanceType<UserConstrutor>;

const user: T1 = {
    name: '小明',
    age: 233,
};



type T2 = InstanceType<ErrorConstructor>;

const error: T2 = {
    name: 'err',
    message: '错误',
}
```

## ThisType（绑定上下文this）

`ThisType` 是用在字面量对象中的，可以给其中定义的函数绑定上下文对象 `this`。​

**源码：**

```ts
/**
 * Marker for contextual 'this' type
 */
interface ThisType<T> { }
```

- 使用时需要开启编译配置 `noImplicitThis: true` ，否则使用 `this` 时没有智能提示功能

`ThisType` 的实现就是个空的接口，只有在字面量对象类型中可以起到标示作用，否则直接使用也不会返回什么新的类型。

```ts
type T1 = ThisType<string>;
const a: T1 = '123';
const b: T1 = 1;
const c: T1 = { name: '小明' };
```

在上面代码中，虽然不会报错，但是变量使用起来就跟 `any` 类型一样。

**代码示例：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/C4TwDgpgBA8gRgKwgY2AEQgZ2QJwJZjAD2OAPGgDRQCyAfFALxQDeAsAFBRQAmAhsLwD8ALihoA3By4BbCMAAWRbphE0oAMigAVeXkxbwEchpq1xUAPQXthqEQBmUAOQK9TqHgB2UWQqWYPALQTag4AX0l2DnsAV09UPCJvaV4Aawh4JFRyKjoACm4sZFFMlHQi-EISHNMASlFgzWoWKShkJMxgHn5eUSJEMsYeIoA6PgEoAB9JlgjW9s9OnzlFZT6B1CHC7BHfVYDp2ciuHDkYnG9mKBGb8d4qG92V-ygwqF4gkMiwjg4Fpf6CCGKXSpVQeTYnG6AlEVwAHqIAIxUECiABMrworT2-lhrRkRAAbhAAEIgAoIqCeGLSOAQHBUbioqk0uk4WotKFcLiuTAjOFQADUTG4cPMVigAGVgDgkgBzAA2ICgoEg3BVukw+O5vJGyuFPBA4us0tlnkVytVEHVvO1YSxUPt4VqkQ4gP5Q0RAAZIu7lUw0T63YhdkTSeSAKxUCMuoA) 该代码来自[官网](https://www.typescriptlang.org/docs/handbook/utility-types.html#thistypetype)

```ts
type ObjectDescriptor<D, M> = {
  data?: D;
  // this 的类型为联合类型，就是 data 与 methods 对象
  methods?: M & ThisType<D & M>; // Type of 'this' in methods is D & M
};

function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
  const data: object = desc.data || {};
  const methods: object = desc.methods || {};
  return { ...data, ...methods } as D & M;
}

const obj = makeObject({
  data: { x: 1, y: 2 },
  methods: {
    moveBy(dx: number, dy: number) {
      // 这里的 this 是有类型的
      this.x += dx; // Strongly typed this
      this.y += dy; // Strongly typed this
    },
  },
});

obj.x = 10;
obj.y = 20;
obj.moveBy(5, 5);
```

- 在 `makeObject` 函数中，使用 `ObjectDescriptor` 限定参数的类型
  - `D` 和 `M` 用来推断实际的传入数据的类型
  - 在属性 `methods` 的定义中，使用了 `ThisType` 来标识了的函数中 `this` 的类型，这样就可以确保 `this` 是有明确的类型的

所以直接在 `makeObject` 函数中使用字面量的方式定义 `desc` 对象时，就会有类型限制和智能代码提示了。

`this` 的类型：

![](../../\imgs\ts-thistype-1.png)

代码提示：

![](../../\imgs\ts-thistype-2.png)

## 内置字符串操作类型

主要是针对内容为英文字母的操作

### Uppercase（转换为大写）

`Uppercase` 可以将字符串类型转换为大写的。​

**源码：**

```ts
/**
 * Convert string literal type to uppercase
 */
type Uppercase<S extends string> = intrinsic;
```

`intrinsic` 代表功能由 `TS` 内部实现。​

**示例代码：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/PQKhFgCgAIWwPt0Mr6g4uUFj-VbQC4E8AOAptACoCM0AvNAOQCCAQgMLUDcGIwUuhJ5VAqnkIAnAMYBDAM4EAPNXEAjUdQB8UKKID2AO0lZoe4QC5elGg2YsgA)

```ts
/**
 * 相当于：
 * type T1 = 'ABC';
 */
type T1 = Uppercase<'abc'>

const str: T1 = 'ABC';
```

### Lowercase（转换为小写）

`Lowercase` 可以将字符串类型转换为小写的。​

**源码：**

```ts
/**
 * Convert string literal type to lowercase
 */
type Lowercase<S extends string> = intrinsic;
```

`intrinsic` 代表功能由 `TS` 内部实现。​

**示例代码：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/PQKhFgCgAIWwPt0Mr6g4uUFj-VbQC4E8AOAptACoCM0AvNAOQCGARgMbUDcGIwUuhJ5VAMgHsA7gQBOjWgGcCAHmoBBAEIBhagD4oURoIB2UrNANiAXL0o0GzFkA)

```ts
/**
 * 相当于：
 * type T1 = 'abc';
 */
type T1 = Lowercase<'ABC'>

const str: T1 = 'abc';
```

### Capitalize（首字母转换为大写）

`Capitalize` 可以将字符串类型的首字母转换成大写的。​

**源码：**

```ts
/**
 * Convert first character of string literal type to uppercase
 */  
type Capitalize<S extends string> = intrinsic;
```

`intrinsic` 代表功能由 `TS` 内部实现。​

**示例代码：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/PQKhFgCgAIWwPt0Mr6g4uUFj-VbQC4E8AOAptACoCM0AvNAOQASBANgwPbQDuzATgwCbUDcGEMCi5CJclQDCAQzwBLLDIbyAXgQA81ABaMW7Lr2oA+KFADGzAHYBnLNDucAXBMo16TVh259+QA)

```ts
/**
 * 相当于：
 * type T1 = 'Hello world';
 */
type T1 = Capitalize<'hello world'>

const str: T1 = 'Hello world';
```

### Uncapitalize（首字母转换为小写）

`Uncapitalize` 可以将字符串类型的首字母转换成小写的。​

**源码：**

```ts
/**
 * Convert first character of string literal type to lowercase
 */
type Uncapitalize<S extends string> = intrinsic;
```

`intrinsic` 代表功能由 `TS` 内部实现。​

**示例代码：**

> [在线示例](https://www.typescriptlang.org/play?&q=457#code/PQKhFgCgAIWwPt0Mr6g4uUFj-VbQC4E8AOAptACoCM0AvNAOQAWBANgwPbQDqzATgwCbUDcGEMCi5CJclQCqAOwDGAQzwBLLAobKAXgQA81ABKMW7Lr2oA+KFDnMZAZyzQHnAFwTKNek1YduffkA)

```ts
/**
 * 相当于：
 * type T1 = 'hello World';
 */
type T1 = Uncapitalize<'Hello World'>

const str: T1 = 'hello World';
```
