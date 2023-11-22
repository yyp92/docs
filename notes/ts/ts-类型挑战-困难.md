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