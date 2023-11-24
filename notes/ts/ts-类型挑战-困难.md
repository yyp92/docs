# ts 类型体操挑战 - 困难

## 简单的 Vue 类型

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00006-hard-simple-vue/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBsELQUAHehVZUCFuEBqBXAphQ3j6DR6pPHGeSQEYCeEAggHYAuAFgPYO0BiWEAFAAEAhswBmWAJQQAxIAe1QH1pM1gEsAzjKEAHTQBtlAYyFNlHGQDdcJEtJsRAGRmA7tytRAedqAG53yAeeWw5URQPSmgIDGqIiAaMqAEk6ANOaAQAzOEIBYCYDj8YALxoDZ8oAAcoBUcoC-ioAOpgAGAMrKALa6OD75gBD-XoBxcvk+AHQ4AB5MOAwAJvmAaEb5nTiiygw4AMJsZRwdTPmAkP+AMP+AwdqAKXqAp9GAxtaAdh6ADOqAFcaAtaaAX4oQ+hOaWO2dEIAxKhAlOKxsneqAoYqAFwmo+Sqq+USxUCRABTqgBNrQCIxoAMI0AtHLzQCIRoAbuUA4gqAPuiiqVypVAKXGgHXlQBXyoAyPUAkOb9IxCfLzfKnMoXHDda75e6PZ75QDq2oBW61QAHlKAArHD6JiAHXlAFxygDdFQD0qulUIAh5Vy8zicGJTFJgHozLIoVB5JaAFfjAHtqWTSgCg5QDQcoBw0y+LDU+VQgE-tQCGMfNAGLyysABvKAA9NAKs2gMVpMhgHbgwBryiLAGtyqEAhdGAdO9AHo6gHIDHqATtN3f8ICQFZTzpd8srAGA65stwuNJo1uRt9sA2-GAGQj0oBsuQjgDc9XWAZb9ADnm0UBBchy0ALWaAJnSI4dUIAeBUAMAGAWDllXkkyn6Q92Ezx8W7fMRQXAPpyQtzP2ieUhgHvlQCncoB+6MAd6neskUs7U7p7wBTysHp4yfpDUD3UGzohBz1SM4A4OXvs5+qAjoAMCqANJygAgmoC9QMv++RtqakKAJDGgD5yoA7BY9kKE5xKiZQ6BUuBWrqA5EPuyqAN9ygDwhqgk5QPktFMKoJCnAwqhMBAwwsSI+h4AAvBAxQ4XhOB8AA3iQUCdCSfBSKJUCyRAABODxYPJDAQDJcmyUM8ksQwQj3AAXBAADkAAq1CaDgRkADRiRpOhCDpek4IZRmjCwQg6LhDAAOY4Ko1m2XJelsFgzCGQAjAADDZGkQAAvrZcUxbJaaXoZ6lyeInm6fcUlqYFsmKUwymqd8jRaY59wQAA1MZdW1WV9mVTgBUJXJSW2dBTyqOlBUWnlGUaR5ODyUwfBlVlOg5TgjRMGwAAybAAO4jaMDlCRIEitYlJBxVtNG0XEAB8ECAhAADiyisFglAQAagCn5oA0O6AFj-LBMEwmg9cAwD0foLCNNyqiNGw8necA0DAAAXiwcCjAAcmAIDAGAKOgBAAD6mNY9jWMQM6qCAMdygCAHhjONk+jEBI6jIAgBAAYnBelwQE+EDfJTyNMOZeDjF+NIACIkgAPCZJ28epADaAAKbGqQA1jg1BsKIEAmQAuoZJlS6rECtO0XTqHl3EncMogjRAABKEAAPwWxAGta2ACVgJzFkQBymjGBwgt81ZECjL7ACyvsC0qEC8XzOttB0zz8FIRsy6b8m2zbluGSMZgjaL+XiSShk+4xjM0oZowQAAZCrFqqGZFneySR3JV1zyGQHZcV2o1c4ILLflyHQitzz6b80LoxHfXjutyZlcd4L6eZyjAz6PZikQOIDD8iYqn8eiuDe77-sQAHR18GwHsbz1bun17Pt+4HR0SIZIjUCjYBo+TZMQIA0raAKvRgAUroA0fKkzfrjKmaIQasRdngYSEAACiABHLAHlfbQJaBZfk8UV7yQmMZAQEC4B-Q8l5XyqhgAXGUDofyKMt64R8CJEgEklQDVst9CAODVBwFaKgpg7D5KYPkrZCayhtJMGmkw4ALD6LsJQXyLhI1eH8Mro0XyTBzYiE6BMKSojxFsI4dI7hci5JlXoUIDRtkiolWzhpCqwinIuQ7gFWKTVrEGWMm5AhHQiH2KGiUEKYUIBRWSlANq8VkqpUuL1TKWBspOUYbFMxKkPwABJhICKEdNOKEAkmNQck4nAcV8iJWSh1KAjdz6DSUSoro6jpIFTiapAORh-ryVUVUgptl+rVNisNUa40FHBVCkwfaQ1cLdImpEqaTlZoLWWqtdaUlBlBWGWNMq5TmklDma0gxfkxodI0kxFiK8xnTTDqzBRk0RGxT2axQwqg-KGXFsgzhgs4EIJ0ILCBSsDlRPuL7Fi8lhjeVHtrXi4ttYOQgI-DZwTHZbWfq-IBuMjTpEAKbWgD4XsxfuAKAJ1ABgSoAarl8aAGPIwAKt6vXep9fS31fr-UBsDUG4NgAiFUCteSUMYbwxICdJ6xLSUfS+j9VQf0AZAxBmDCGqg2A6FIRwBiWKICABezQAWJqOB5eSylArqXCrpdDWGCMqZgCAA)

### 题意

实现类似Vue的类型支持的简化版本。

通过提供一个函数 `SimpleVue`（类似于`Vue.extend`或`defineComponent`），它应该正确地推断出 computed 和 methods 内部的 `this` 类型。

在此挑战中，我们假设 `SimpleVue` 接受只带有 `data`，`computed` 和 `methods` 字段的Object作为其唯一的参数，

- `data` 是一个简单的函数，它返回一个提供上下文 `this` 的对象，但是你无法在 `data` 中获取其他的计算属性或方法。

- `computed` 是将 `this` 作为上下文的函数的对象，进行一些计算并返回结果。在上下文中应暴露计算出的值而不是函数。

- `methods` 是函数的对象，其上下文也为 `this`。函数中可以访问 `data`，`computed` 以及其他 `methods` 中的暴露的字段。 `computed` 与 `methods` 的不同之处在于 `methods` 在上下文中按原样暴露为函数。

`SimpleVue` 的返回值类型可以是任意的。

```ts
const instance = SimpleVue({
  data() {
    return {
      firstname: 'Type',
      lastname: 'Challenges',
      amount: 10,
    }
  },
  computed: {
    fullname() {
      return this.firstname + ' ' + this.lastname
    }
  },
  methods: {
    hi() {
      alert(this.fullname.toLowerCase())
    }
  }
})
```

### 题解

```ts
/** 获取 computed 中的 this */
type ComputedData<T> = {
  [P in keyof T]: T[P] extends () => infer R ? R : T[P]
}

type Option<D, C, M, Data = D extends () => infer R ? R : never> = {
  data: D,
  computed: C & ThisType<Data>,
  methods: M & ThisType<M & Data & ComputedData<C>>,
} & ThisType<never>

declare function SimpleVue<D, C, M>(options: Option<D, C, M>): any


// 使用示例
SimpleVue({
  data() {
    // @ts-expect-error
    this.firstname
    // @ts-expect-error
    this.getRandom()
    // @ts-expect-error
    this.data()

    return {
      firstname: 'Type',
      lastname: 'Challenges',
      amount: 10,
    }
  },
  computed: {
    fullname() {
      return `${this.firstname} ${this.lastname}`
    },
  },
  methods: {
    getRandom() {
      return Math.random()
    },
    hi() {
      alert(this.amount)
      alert(this.fullname.toLowerCase())
      alert(this.getRandom())
    },
    test() {
      const fullname = this.fullname
      const cases: [Expect<Equal<typeof fullname, string>>] = [] as any
    },
  },
})
```

为了设置不同区域的 this 对象的类型，需要用到内置工具类型 [ThisType](https://www.typescriptlang.org/docs/handbook/utility-types.html#thistypetype) 。

首先了解一下 `ThisType` 的用法，下面定义了一段代码：

> [在线示例](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgCJzHZBvAUM5ADwC5kQBXAWwCNoAafZAT1IpulwF9dcB6X5GCYAHFAHlqAK2QBeHHwEEAJhjil0mBQWRwlS0gAoAlLIB8ZKrShbuQ0cgnS5eZavWrGu-cmNmL7a05kADJkABUAC2AAZzCRCAAeDThTHgQAexBosGR0qVJHWXlXTFIXbRJkAEYGbWZSACZa5E5m-h09X3L2gigIMHIoEEEo6IA6FUwxwmQAahGYidUxpgBuRnbuAi8uxl7+weGwUem5hfG1xm5OIA)

```ts
interface Data {
  x: number,
  y: number
}

type Obj = {
  data: Data
  add: () => number
}

const obj: Obj = {
  data: {
    x: 1,
    y: 2,
  },
  add() {
    // 这里 this 就是 obj 对象，类型则对应 Obj
    return this.data.x + this.data.y;
  }
}
```

但是我想改变一下这个 `this` 对象的类型，那么我可以这么做：

```ts
// 使用内置工具类型 ThisType 声明上下文 this  的类型
type Obj = {
  data: Data
  add: () => number
} & ThisType<Data>

const obj: Obj = {
  data: {
    x: 1,
    y: 2,
  },
  add() {
    // 此时 this 的类型已经是 Data 了
    return this.x + this.y;
  }
}
```

需要注意的是，使用 ThisType 需要**开启配置** **noImplicitThis**。

弄明白 ThisType 后，再回到上面的答案中，就能明白是怎么回事了：

```ts
/*
  data 是函数，computed 和 methods 都是对象。
  所以，针对不同范围的上下文，需要单独处理。

  data 的上下文对象是 options
  computed 中函数的上下文对象是 options.computed
  methods 中函数的上下文对象是 options.methods
  所以使用 ThisType 给对应的对象进行处理即可
*/
type Option<D, C, M, Data = D extends () => infer R ? R : never> = {
  data: D,
  computed: C & ThisType<Data>,
  methods: M & ThisType<M & Data & ComputedData<C>>,
} & ThisType<never>

/*
  定义泛型 D、C、M 用来获取 data、
  computed、methods 的类型
*/
declare function SimpleVue<D, C, M>(options: Option<D, C, M>): any
```

## 柯里化 1

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00017-hard-currying-1/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMDsEFoKHvzQM4mDRlGlEN37ARgJ4QCCAdgC4AWA9uSQGICuEAFAAICGVAZswEoIAYkAPaoD60kVwBO0rkWzZhyiIAyMwHduiqAD4IgRldA7DGAZa0BXgYAqlQN-+gRejAMP+Aia0Bz8RAAKAJQiBOU0Db8dcAUsYHnEwCAGLQhtQAp1QBNrQERjQAwjQFo5QC+9QDrowH95QApXABUiAAcAUwBlAGNpAEtsyggAFgA6AAYQgG0AYWZZIhLyAHMAXUAIf+pKSmyAZwAuYGBc8mqAdxKAazLcgBMSrmraaU7gOcXgFraOzsBIf8B6M0AAOUBZz0AwHUAyPUBIc0AsTUAqOUAh5UAHU0AQt0BfxXfAG3jAEbGgC45QDz1s8Huc3l9foB4vUA6EqfQABRoB6c2CUGwgGj5QBBmoAsf5CAANCZRhthCvRhhUuMtlhAALzsLijCDkZgAWwIuWkABoIAQmSz2ZyhLTdFwIABqXmk8kVGjSXK5OkQKnLNjQHkAJgEITJ5ApEEKrVKK1I1KVB2k7S6bBV2qguv1vBKADdFfTDbISibqWwtWwAMx2iCE-EhQAE8oAkBMAm374i1Wzr4n7vQD3yoBfgKeUJsgAqDQAA6YBAyMAxtaAOw9AHSpgFNFQDB2miINhIrE4jYPcblr9zoBCa0Ao3KAUuNAOvKkI+gUAYEqAWUTADHagAEje4fQBwKjYK4AUvUAK-GAPbVAG6KgGW-QA55tXsLowhAAOIlGjMAgQQBQcoBT80A0O44gZDMYTYmFajVABWww2W2AcGAAC9qAQJoADkwBAYAwEg0AIAAfTg+CEPgiBAAN5T5AGO5QBAD1gxCcJgiBwKg4AIEAEb9AFGI34wEoHJFQAWRWEo2QAHgyHlXF0WlsAyCBcgAD0oKZlmGCAGg6XhOQgRgeWqaTRPEgBRbpsCgAB+BktiZRhhV0OjViYuTWO0bAmVcABuSDllyQoABsZEVfhyEKSgSnoCA4yOZjtDYXhyCZDIBFGTjuL4gShLYaTqhkToxggWTpDILZhi0mLyDEuL3CUiBVJ0hjWUY0gEoMmsoH5XJXWkSCwGg3CcIgQBpW0AVei0gxbDqqQgiSlZbJNllaiIAAbwgOSAEdmC4KyeTkni8kciAAF8IF4aRaFZCAAHIOCovIEBfMarKmTpcmGYBmCcqzhlWyCHQqJsvWWaBzSNeM2BtJkKVKLoeT5Zk2Q5bkDSZAhaFoPaeCSyhpGYXJtSug0jVujUHsOa0XogN6jk+-kfs5HlCgBoGQfIHlljx4Hch4HlchJgmeV4V7wfRiBOipsnyDBiGocgzbFUKLhhkOpUGmwSbpsoRjhtGqzGIyrnaF4WHPRWdUGTp97OiStgvoFX71dx3l8ZZtnId3bQuSFqbLNF8WxqlqAoBluWbpWDUeRRtGunVzWsekHXmdBuldDYYm9dJv2RXYSng4J9XadR+n3f99gmcjg2E-Bo2dBNsBFIqqrWqQwBoOXOQBTaxavP8Ig0BdwgQdAGq5VDAGPIwAVbzvQYRnGYBn1fD8v22X8eGGGZOX-QCQKrm8m5bh928799P02XvYGAYZgZO5y9SrwAXs0eDRJ7bp9hhfWee+HoDQIIsAgA)

### 题意

> 在此挑战中建议使用TypeScript 4.0

[柯里化](https://en.wikipedia.org/wiki/Currying) 是一种将带有多个参数的函数转换为每个带有一个参数的函数序列的技术。

例如：

```ts
const add = (a: number, b: number) => a + b
const three = add(1, 2)

const curriedAdd = Currying(add)
const five = curriedAdd(2)(3)
```

传递给 `Currying` 的函数可能有多个参数，您需要正确输入它的类型。

在此挑战中，柯里化后的函数每次仅接受一个参数。接受完所有参数后，它应返回其结果。

### 题解

```ts
// 组装函数
type Medium<T, R> =
  T extends [infer F, ...infer E]
    ? (arg: F) => Medium<E, R>
    : R;

declare function Currying<T>(fn: T):
  T extends (...args: infer Args) => infer R 
    ? Medium<Args, R> 
    : never


// 使用示例：
const add = (a: number, b: number) => a + b
const three = add(1, 2)

const curriedAdd = Currying(add)
const five = curriedAdd(2)(3)
```

- 在 [条件类型](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html) 中使用 `infer` 获取函数的参数和返回值
- 然后遍历数组 `Args` ，使用递归的方式组装函数即可

一开始我是这么来获取函数返回值的类型的：

```ts
declare function Currying<T extends any[], R>(fn: (...args: T) => R): Medium<T, R>

/*
  得到的结果如下，最后的返回值不够准确，不是 true
  const curried1: (arg: string) => boolean
*/
const curried1 = Currying((a: string) => true)


// 后面我换了种方式再获取函数的返回值
declare function Currying<T extends (...args: any[]) => unknown>(fn: T): ReturnType<T>
// curried1 的类型还是 boolean..
const curried1 = Currying((a: string) => true)
```

后来是去到解答区看到了这个[解答](https://github.com/type-challenges/type-challenges/issues/1404)，才知道要把这个类型推导放到最终结果处去进行处理：

```ts
declare function Currying<T>(fn: T): 
  T extends (...args: any[]) => infer R ? R : never

// 这次 curried1 的类型是 true 了，获得了具体的字面量类型
const curried1 = Currying((a: string) => true)
```

看了解答区的说明后，认为问题是出在函数的定义上：

```ts
// 3种读取函数的返回值的测试代码：
declare function f1(a: string): true  // 函数声明
declare var f2: (a: string) => true  // 匿名函数表达式

// 第一种，通过泛型在参数中定义。
declare function test1<R>(fn: (...args: any[]) => R): R;
test1(f1) // true ✅
test1(f2) // true ✅
test1((a: string) => true) // boolean ❌

// 第二种，通过泛型约束上去定义。
declare function test2<F extends (...args: any[]) => any>(fn: F): ReturnType<F>;
test2(f1) // true ✅
test2(f2) // true ✅
test2((a: string) => true) // boolean ❌

// 第三种，在泛型最终应用结果处再做判定。可以看出只有这种方法的效果最好，也是符合测试case的要求
declare function test3<F>(fn: F): F extends (...args: any[]) => any ? ReturnType<F> : never;
test3(f1) // true ✅
test3(f2) // true ✅
test3((a: string) => true) // true ✅
```

## Union to Intersection（联合转交叉）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00055-hard-union-to-intersection/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCs0QtBAqgOwJYHsUQC4YgJIo4CmATgM4kDGOmKkC8zLjARgJ4QBaAFiQJQBzCAAoAAgC9+goQEoIAYkAPaoD60pQFc6AGwpK0KAGblGjReYiAMjMB3bqagA+CIEZXQOwxgGWtAV4GAKpUDf-oEXowBh-wCJrQDn4iAAFACUIQE5TQG34-0AKWMB5xMAgBjsIQDztQAbnQA2swHK-LTRtQG8fQGj1AANUegAVDCJSSho6LAAeJHsKjMBo+UAgzQyKwZwKRhwOAAcSQggAXmR0LAAmBvIqWnpWgHJDDAxNiAAfCAAWRcPcMg0SR2BgCBIAD0naEgATXHw2Ke3d-YAyE5nAE4S4kRiDLpQRiOQAU6hAAOJoHC8DRsCCAKDlAKfmgGh3QBY-7wcDhxhQAFy3YbUXgAOgAVhQqRgyEJgLBgNJ4ABhAByYBAwDAAtAEAA+qKxeKxRBAAbygBC3QDHcoBADxFEpVwogfIFY0m81q9WIq2aGw6swgjFESHuD1IKFeegAhiguAB+MR2pkk5AKGaOB1cD0oEgAN3ICke1ttrvdEAMxjIhC9jkDGDQr0YUBdBFNUH9QZMgpAytV4oggGlbQCr0YAKV26haLovV-LQAFtxoycLgJlMAN4QACiAEcNHbtAAaXtPZoQAC+EEMZAwjYgm3EWpI8EpQ+0JGEJAowCKuk2mo7EGodqoejmAG1GD3x7RWv3B9p2gsUHUVk11m0fntzqdziCVz2KOP7-ICEDAqC9jATed44A+A5Di+uofmsLQoK0oiiAmi47HsChHFhaAeqcOGASQcjAWI2GzI4oEKACREkYsZGgpRMEALoCvmNa1hAgDQcoAAHKAKbWvFFvWYCgNCECAGBKgDVcrKgDHkYAKt74oSxJksAFLUnSDJMiy0DAA6FAAO7kGyvCcjyDgQDiKlqUSpLkhQlK0vSjLMqyFAYNoRRYCMNmAC9mgBYmjYDkac5rm6R5FlWby-JgEAA)

### 题意

实现高级工具类型 `UnionToIntersection<U>`

例如：

```ts
// expected to be 'foo' & 42 & true
type I = UnionToIntersection<'foo' | 42 | true>
```

### 题解

```ts
type UnionToIntersection<U> = 
  (U extends any ? (arg: U) => any : never) extends (arg: infer I) => void
    ? I 
    : never

// 结果：'foo' & 42 & true
type I = Union2Intersection<'foo' | 42 | true>
```

> 答案参考自 [解答区](https://github.com/type-challenges/type-challenges/issues/122) 👍比较多的

当使用 `infer` 推断会发生 [**逆变**](https://juejin.cn/post/7138063352139350023#heading-2) 的类型中时，则推断结果会得到一个**交叉类型**：

> [在线示例](https://www.typescriptlang.org/play?#code/C4TwDgpgBAQghgJwDwBUB8UC8UBQUopQQAewEAdgCYDOUA3lHAFxQAUxLAluQGYQJQAqgEosGAG4B7TpQDcUAEYt2XXvyGjME6ZSgBfPPigB+IYZbkI4-rJw4A9PajVgCbgHMcoSAQCMWWEQkBmY2DmdXD01tGXklMJYXN3J3aKgpGX00eQcnJI8oADIocgBXAFsFfi9waBQAJgD4ZBDlcPyUtIy5RTaLCqqELp0s+SA)

```ts
type Bar<T> = 
  T extends { a: (x: infer U) => void; b: (x: infer U) => void }
    ? U
    : never;

// string
type T1 = Bar<{ a: (x: string) => void; b: (x: string) => void }>; 
// string & number
type T2 = Bar<{ a: (x: string) => void; b: (x: number) => void }>; 
```

在 [官方文档](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#:~:text=The%20following%20example%20demonstrates) 中有 `infer` 对应的说明。

所以代入到上面的解答与示例中可以这么去看：

> 注意：联合类型作用于泛型时会发生分布行为

```ts
type UnionToIntersection<'foo' | 42 | true> = 
  ((arg: 'foo') => any | (arg: 42) => any | (arg: true) => any) extends (inferArg: infer I) => void
    ? I 
    : never
```

也就符合了上面说的 `infer` 推断情况，因为 `inferArg` 的类型存在**逆变**，所以该类型必须是 `'foo' & 42 & true`，这样才能满足。但是怎么可能有类型是 `'foo' & 42 & true` 呢，所以最后会得到 `never`。

## Get Required（保留所有必填字段）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00057-hard-get-required/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCsDsEFoIHECmAXCAlVBHArgJYBOqAJpIgtTZQEYCeEAWgBaocB2A5hABQABAF7su3AJQQAxIAe1QH1p0-OkIAbAM7TCnAGapilSlKMRAGRmA7twNQAfBECMroHYYwDLWgK8DAFUqBv-0CL0YBh-wETWgOfiIAAUsCEBOU0Bt+K9ACljAecTAIAZLCEA87UAG50ANrMByvyVVQG8fQGj1AAM0dBwCEnIAHgAVK0LvQFPogsBd+UBNV0ABI0BIc0BR-UBrDUB1bUBW6yTAaPlAIM0kwqn0NUp0BgAHVAgASQgAXhQMMqJSMkqAbwhtAHsTgC4ITnwAWzo9ABoIOgBDYgB+S7V0Yi1eAF8bMBgBBUAAPJYAY3Q5Ag6BOz2WR1OFyut3uxAg-0oU0KSRsgAp1FCEdCsfB0CCAKDlAKfmgGh3QBY-6x0OgFmpzsCZpDWAA6ABWam5J2I3GAcGAIgQAGEAHJgEDAMCK0AQAD6avVGvVEEABvKAELdAMdygEAPVWa00qiDyxXzJZbUp4XZVWobA6UADaQQgWggAGtUAwTtoINUIC8NNV3QBdUFgmGcMgaHYVfa1SMQd7BCCXTioABuegjl3DQQjYGxSpAJrNGoggGlbQCr0YAKVxGlaraotCsINwWQsw1qREAAogQXiongOIahoVjjsQTjcIAByAR9hBckcqVA8VBqYA5dQLq2LZaQ0PbjYQV2UcdQ9CVIf4EeVEqJvaHY5nLPovQAbmeb0+EDfL8PBYlYTzIh+aJ3HooFgVeE7Qnew4qE+2z2kmb4opc+Bxqg2haOQv6vB82G4fh2ZkKB4HvqiOFkHhBGUYCcEloqYDKq21aANBygAAcoAptYtq27bseA1gQIAYEqANVyeqAMeRgAq3oyzKsuywCcjy-KCsKoqwMALycGoADuejiqwUqymJdLyYpLJshyahcnyApCiKYpqCcKg5Cc+mUDYgAvZoAWJrmNZyl2Q5GnOSZZlygqYBAA)

### 题意

实现高级工具类型 `GetRequired<T>`，该类型保留所有必需的属性

例如：

```ts
// expected to be { foo: number }
type I = GetRequired<{ foo: number, bar?: string }> 
```

### 题解

```ts
type GetRequired<T> = {
  [P in keyof T as T[P] extends Required<T>[P] ? P : never]: T[P]
}


// 使用示例：expected to be { foo: undefined }
type I = GetRequired<{ foo: undefined; bar?: undefined }>
```

> 答案参考自 [解答区](https://github.com/type-challenges/type-challenges/issues/285)

上面用到了工具类型 `Required`，源码如下：

```ts
type Required<T> = { [P in keyof T]-?: T[P]; }
```

首先使用到了 [as](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#key-remapping-via-as) 进行键的重新映射，然后通过 `Required` 将对象的属性变为必选的，也就是通过 `-?` 来进行处理，这样 `TS` 就能准确地识别了。

可选属性的值可不是单单多了个 `undefined` 类型而已，要不然上面的示例都没法通过了。

## Get Optional（保留所有可选字段）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00059-hard-get-optional/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?ssl=26&ssc=2&pln=24&pc=1#code/PQKgUABBCsCcEFoIHECmAXCB5ADuglgPYB2AhgDaSII21UBGAnhAFoAWqnxA5hABQABAF4cu3AJQQAxIAe1QH1p0gK4FyAZ2n5iAM1QAnKlSlGIgDIzAd24GoAPgiBGV0DsMYBlrQFeBgCqVA3-6BF6MAw-4CJrQHPxEAAKAEoQgJymgNvxXoAUsYDziYBADJYQgHnagA3OgBtZgOV+yvjkgN4+gNHqAAZo6LgEJBQAPAAqVsXegKfRRYC78oCaroACRoCQ5oD3yoCQCYDq2oCt1smA0fKAQZrJxTPoqlTojDioEACSEAC8KBgVRGTk1QDeEFqEhABcEMSKALb0egA0EPSkugD8l6roupq8AL42YDACCoAAeywAxuhUAATCDoQjPFbHF7vT7fX4QP5UGbFZI2QAU6ih8Og2Ip6BBAFBygFPzQDQ7oAsf7Y6HQOFU5yBcwhbAAdAArVTcwi6bjAODAEQIADCADkwCBgGBFaAIAB9NXqjXqiCAA3lACFugGO5QCAHqrNaaVRB5YrFsttuU8HsavVNhBDlQANpBCCaCAAa1QjEIWggtQgpHUtQ9AF0QaDocQYeoQqgAI6KfC6WF1KxRiBvK6oABuegglyCkcuEbLYGxirAyrNpoggGlbQCr0YAKVzGJobWst+BuOCFmGtyIgAFFUxQnqPwagoViTrpCDcIAByATDhBcijkVA8VCqYC5NQrq1LFYQsP751uqjTyHoarjxQ1Mq7KoHY6nC5XW73XQAbmeV4PggL4fh4LErCeFFgPRcD-isKDbxnKFHwnA5X3td8jhOM5LkUeNUC0TRYUA1EQIImEiJIuEAWgoC0QgSjqOIWFIKQyNazrEAu27c1AGg5QAAOUAU2teO7C0FVAKgbEAMCVAGq5PVAGPIwAVb0ZZlWXZYBOR5flBWFUVYGAUhiFUAB3PRxTYKVZWsCA6RUtSWTZDlVC5PkBSFEUxVUQhyFyEh5lswAXs0ALE1zEcjSXLc3TPMs6y5QVMAgA)

### 题意

实现高级工具类型 `GetOptional<T>`，该类型保留所有可选属性。

例如：

```ts
// expected to be { bar?: string }
type I = GetOptional<{ foo: number, bar?: string }> 
```

### 题解

```ts
type GetOptional<T> = {
  [P in keyof T as T[P] extends Required<T>[P] ? never : P]: T[P]
}


// 使用示例：expected to be { bar?: string }
type I = GetOptional<{ foo: number, bar?: string }>
```

一开始我还想用 `Partial` 来进行处理：

```ts
type GetOptional<T> = {
  [P in keyof T as T[P] extends Partial<T>[P] ? P : never]: T[P]
}
```

发现行不通，所以还是得用 `Required` 来进行处理。

其实就是跟上面的 `Get Required` 的处理相反，只是这次用来排除掉必选字段而已。

所以处理步骤就是：

1. 使用 [as](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#key-remapping-via-as) 键的重新映射
2. 判断当前键对应的值类型是不是必选字段
   - 是则返回 `never`
   - 否则保留当前键