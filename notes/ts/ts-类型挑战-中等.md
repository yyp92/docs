# ts 类型体操挑战 - 中等

## 获取函数返回类型

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00002-medium-return-type/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMELQUO3Bg15UL+KgHU0Cvxg9tUN4+ho9UnjhNKICMBPCAQQDsAXACwHs7qAxAVwgAoABAIaMAZlwCUEAMSBaOUCS3tICWdEQFMATtPJdFAGwZxlRIlNMRAGRmA7t2NRAsHKB-eUAUrhAAGAJVUMu6ugBVKAA6qLhCAedqADc4Q-kEAygDG6ooBDBCAIW6uHl4+0aoAPL4AfCGA2zb4gEAMNhCA0fKAQZqAWP+VLk0MAM5EcWwtKSJ0EAC8fABuAFwQ5CwsuqrCkn0FEADeRFCKIkPiy1AQ6p7evQCMm6q6LaqbUDtZvdBEAL6VDIGqEIL9EACylJl7ObmPQSw1j15sBgBBACl6gArjQA-2oAvxQgACJ9hAAD4wBFEJouSrzQAU6hAAOKKZhccgQQBQcoBT80A0O51JgMBgBFrDUGtOJMAB0ACsWhyWOoAObAaDAABeTDgAGEAHJgEDAMCK0AQAD6avVGvVEEABvKpQDHcoBAD1VmpNKog8sV-2en2+2Se+QgqgAHgxVHQACYtPgcn2CQWjYSUADaAF1ZvNA-MBr5HS63Z7vb7-S92KHwxBlGpNG4IAB+CA50Z0VSDDQAbkVYGVppNEEA0raAVejHFVjTWtRbFABbAL8lILCAAUQAjlxBLoADSDp1BOIpW4QETqFidiAAcn4Vrg7LHUzoAtULWAXAYehaq8tTwgcUEpy9AyDRAH09Us9yw9HulyXUSe8nNt2dpBLkvDpt+ygCgUkHjo+z6vu+Y65Ps0AAMx-l8AF+PaIH9PMSHIZBBTQVAT4zgwb4jghkrLgEUxOgA8uQXIvgwaG2phQHYXMEBUd2tEMUxs4EURU6keRH65AACkunaKKcuTjJM0x0IRHzoVcvycfMUnLrJeQKVMwgEYRMGifBn6aWuIgTKurEYRp6YWauVksKuQkmcxYkIciaLQLZ6n2lagILspUHuXBFGft5MB+T8AVPEFPT7G5IYXkE3HUXxjHMW8SxQIIoxBkhk5OdZKVQOQfqjKuTDHLoLlEAEOyDCBRZcJ25AaGA9xgB0dBdMFby8CMYwTAZdDpoMeYQMiow3L1-WJYNw36Upk4AO4BuwE1TTNMCVlWICtm2ZqANBygAAcoAptZHW25oKqARDzIAYEqANVyuqAMeRgAq3nSDJMiywBspyPJ8oKwrAMILRrRoYoSjKD0QDSn3fYyzKsi07Lcry-JCiKLSTMeiidHDgAvZoAWJpWEjv2o+jQNY9DUqyhaYBAA)

### 题意

不使用 `ReturnType` 实现 TypeScript 的 `ReturnType<T>` 泛型。

例如：

```ts
const fn = (v: boolean) => {
  if (v)
    return 1
  else
    return 2
}

type a = MyReturnType<typeof fn> // 应推导出 "1 | 2"
```

### 题解

```ts
type MyReturnType<T extends (...arg: any[]) => any> = T extends (...arg: any[]) => infer R ? R : never;
```

- 跟`Parameters`一样，反正只要是想获取函数类型使用时的一个实际类型，就得用`extends`+`infer`进行推导

## 实现 Omit

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00003-medium-omit/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMELQUHnagG5wgeQLYEsAul5yFH4BGAnhAIIB2OAFgPbUUBiArhABQACAhrQDM2ASggBiQLRygSW9xbalibiSbLABsccLNXz4xeiIAyMwHduOqIFg5QP7ygClcIAA0y5bSVABUyABwCmAZQDGAJyx3HAhAELc7BxwAHhcAGggAaQA+J0Btm0Bo9UAgBlMI7BwnQCx5QGwlQC+9QAA5QCo5QEB3QFNXOwSnCUB1bUBW63DbFydAT+1AQxjsqHxAaPlAIM1ALH+c20mcAGd8LRxPfwFeX08IFwYAEwYIAG98KBxcVU8ALghpnEDqAHMDiE3PaYCgo6Zzy+u7qChfBgx3CcFptziQGAwTvx8ABfHI4DxrDbbAAK-k8ADcsJ4AO4QAC8EAAsmRIjEtgx4gByR7PQLBBTUSkQAA+EEpRxwJ0pSRyf2olwgOHJ5yRDFRGKxuIJ+x+ED+AKBnhBEGWqmmnliMPwk1sOSSEEAFOoQADiuDobBIEEAUHKAU-NANDuozoOBw7mmp2AwBmvjoADoAFbTH0Mfw3YDQYAALzocAAwgA5MAgYBgFOgCAAfUzWezWYggAN5UKAY7lAIAeGZz5fTECTqeAEEAScaAEzT1hBAN4+6Qk4QSrfSYHhXiJZAAogAPXyqNiPGLxZL45ueYcLaibaaJCAAfgg1AxiwgIoA3Cm+2tiaS4qv54vlxcyBgwaoWRcrlobg-qGxb4t9dL8ABtZEQLQBxHMcJ08KIAGtPDIBgBHWackgAXRFP8EIPWFUxAMsK2zCBAGlbQBV6KsQYsOwzMq2TLAAWDEJdggQcAEc2F4VR4hHLxfBCaEVX8f42W4I84G9ZiTluJ5gDYI41UpQ8ETlXh1RXAkf3wNjPA4qIGKY1QNOHdjgQARniE88jJbYqRpF56SYbkkiSTUoFU9TNOYnS9KVAAmIySRM0VzKeSy3kZB9KXlQFPGBGyeQQmsIH46Y4HnPSEv8Hj-F7WTFlS2djNwUyKTZCy6UCplWUpLR0WYrBNm5FN5kWZZVnWck9nwDkTg+J9bnwQrXgZDqvnwULFWVMEIU8KF0LqpYVjWRyDJaw5jjOR8Bt+f4wuBUFwUhbRJtoeqZro3S1OBdyFsFJb+ufMB0IwkjSIgQBoOTKQBTa3u7DyLAUB8H1QAwJUAarkC0AY8jABVvR1nVdd1PWeX0AyDEMw2AfhpmxRZI2jeMfoge1QfBl03Q9L1YcDYNQ3DaYIQkhlZigfVABezQAsTWMPHIcJmH-RJhGo1jBNqzAIA)

### 题意

不使用 `Omit` 实现 TypeScript 的 `Omit<T, K>` 泛型。

`Omit` 会创建一个省略 `K` 中字段的 `T` 对象。

例如：

```ts
interface Todo {
  title: string
  description: string
  completed: boolean
}

type TodoPreview = MyOmit<Todo, 'description' | 'title'>

const todo: TodoPreview = {
  completed: false,
}
```

### 题解

```ts
// 排除 T 类型中的 K 类型
type MyExclude<T, K> = T extends K ? never : T;

type MyOmit<T, K extends symbol | string | number> = {
  [P in MyExclude<keyof T, K>]: T[P];
}
```

- 对象键的类型只能是`symbol | string | number`
- `MyExclude<keyof T, K>`就是将`K`类型从`T`的键类型中进行剔除
- 然后就进行常规的对象类型组装

## Readonly 2

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00008-medium-readonly-2/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAcELQQEoFMCGATA9gOwDYE8IAmSeOci0gI0IEFsAXACx0IDEBXCACgAFVGAMw4BKCAGJAtHKBJbwkAnNFjyFxmKgCtkAYwZwA1snwBnUqXHmIgDIzAd26moAPgiBGV0DsMYBlrQFeBgCqVA3-6BF6MAYf8Aia0A5+IgABUQIQE5TQG34-0AKWMB5xMAgBjsIQDztQAbnQAA5QCo5QCwEwApXAAMAWXwUDBwCIgAeABUAGggAaXsSgMBg7UAyPUBIc0ASOUBZz0BvH0Bo9UAQt0Ah5UAHUxKGksAYlRLWkrSoUhWSwHBjQCztQBS9QD7owDt-QC45KqUCCfmSicA9HUByA0AxtJTAIM1AHPNAKnNABeNAbPktgKATCV9oB-eUAAkZ9R6AX4TABvKp0AV8qAb+iAoBGHUA8wqAOzMCtcLjV8I0OjlAOwW6wgpEA0fKvdIlGkMExQACWjGQckEqC0yAgDUwWAgAG9SFAGAyGLhkAAuCBGBhyJkAc0FEHQyCMWllAAdhThJdLZdgFVAoFpMABbdVihjIdCSqiYTBigSkAC+6WN2GlEAYPMwkoquOU9W5WBaAHJhaLkCGIAAfCAh5WqjVa7AhxwAXn5ivDYslACIABKGXNNRUJtUMzUM7UQXOCO1UVByYuK41mi1WyVs3BGZAlqAujZC70AOmznIzBeQuFwmFzEGAwAgAFE5HJMHJJVoBNhMAwIApUEYjAy5dgIKh94o8RB1Wv1SyGPhSF6sMOy0mq2eJw25Gw7XOF2XVd103bdd0vQ9j1Pc8IMuQhb0we85EfZ8R1bc1kEtdAIAzGUOE5QCAHlWk2Gl0kcQAKdQgABxEUmA4KgIEAKDlAFPzQBod0ALH+mAYBh1SMcUFzpLQmGHdQjGHdc5WAaBgAALyYOAAGEADkwBAYAwE00AIAAfT0-SDP0iBAAN5CZAGO5QBAD10wybJ0iB1M0x97wgP0rwDRoWlaCBkAAD0tbB0CMCADHwTBBC5HDgsMMKuXTJA3IIOpwgZLQ9A8tp7EcAAyCBCJNEV0vaTSwG02ybIgQBpW0AVeiinJayyqMhyGTNdc9z5CBaFwBkDBaJcfPvHQICdCBBDXE0414JzkDgYTUGnZB9RVYAOGFbsQ0c-BnK3HsgozABtUg+oGhg6k67rkDqVzqncoNMAARnsFp-US26Hsyvtl367QTrOgxLsqBL8EDb07tDMco1jeMVXLSscFTXqvp0K13sOxGfq6v6rrg4GsCIMGRTFCG43fCtk3hz7juRx6wAAXU0plLVZdlOVezMhQJiUpRleVSBJ2HsAAfh1bn9VIdD22tCBbXtNBsDAF0wAZlk2Q5LlvSINnYOvMdhb1A0lWhj8cCFrm9bF00MKwm07QdOWFaVpnVaO76rU1g84M9DndZ5qB3evPnkxN3UfYgcXMI7KWbdl+XNLdD0Xx9FyAeu2pGhB-GIyJqHE1Jz9U0igV2YjPNC3wZsoADz88zrNRG3L0OLYlzs5p7EsFYT0cOciydp1nedFxXNcN1DsC9wPI8TzPC8-eUG87wfJ8O8rnBu5-P8+8AweQJH7AdzHtAJ+g6fAbnxCF7ADuw6wyK8IIxdiK0kB6oauzAGg5HJAFNrZ+GvsjTQFIRwgAwJUANVyplADHkYAFW8uI8T4gJYAQkRJiQknIKSMkBBGAAO4sjkgpFSACIDsUgdA3i-FBKqkQeJSS0lgBGHtCtT89IICOEAC9mgAsTRsMQ2BZDhKiUoSgnBSlVIOTAEAA)

### 题意

实现一个泛型`MyReadonly2<T, K>`，它带有两种类型的参数`T`和`K`。

类型 `K` 指定 `T` 中要被设置为只读 (readonly) 的属性。如果未提供`K`，则应使所有属性都变为只读，就像普通的`Readonly<T>`一样。

例如:

```ts
interface Todo {
  title: string
  description: string
  completed: boolean
}

const todo: MyReadonly2<Todo, 'title' | 'description'> = {
  title: "Hey",
  description: "foobar",
  completed: false,
}

todo.title = "Hello" // Error: cannot reassign a readonly property
todo.description = "barFoo" // Error: cannot reassign a readonly property
todo.completed = true // OK
```

### 题解

```ts
type MyReadonly2<T, K extends keyof T = keyof T> = Readonly<Pick<T, K>> & Omit<T, K>
```

- 首先使用`Pick`将`K`类型的键挑选出来组成新的类型，然后通过`Readonly`变为只读的对象，这样就达到了将部分属性变为`readonly`的目的了
- 那么另外一部分属性就使用`Omit`来创建新的类型
- 最后通过`&`操作符组成交叉类型

## 深度 Readonly

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00009-medium-deep-readonly/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCcELQUI+2gyvQgJQKYEMAmB7AOwBsBPSeOSq8gIxIgEECAXAC0PoDEBXCACgACWFgDNuASggBiQLRygSW9pAJ2z5i9KXhoArDAGNmcANYYSAZ2k4MGAA7lyUhxEAZGYDu3O1AB8EQIyugdhjAMtaAV4GAFUqA3-6Ai9GAMP+ARNaAc-EQAApoEICcpoDb8RGAFLGA84mAQAzuEIB52oANzoAAcoBUcoBYCYAUroAhbgAGACJW1pi4hKQAPAAqHvWRgMHagGA6gJ-agIYxtYDz1uWAQ8qADqaAU8qAboqACtrjgEgJgEr6gAzqgH3RgFxygFfKgN-R+VDkgBUGgPfKgKdygOIKu4AU6oAm1oCIxoAYRjKAiEaAN3KAo3KAEE1AGAu41yc0AI36RQC-inNIoBvHzkgF+EwAHpoAAdMAwAGARTDcoAxeUA9GaRC6AWblAGyOt0qgHH4wBhkYA1t0AvDpXQC-AYAsTVqgFg5QAwKoAJC0A0fKAU3MPoFAI+6Zwg5B5gCDNAr1GXMUzkZgkawYCAADQgAF4IABvMVQKAADwAXDq9fqoFgTQBGcjmmgmgDkrAAlg7bRAAL7ukiO1gmN1QL3nKCK5UQACiBuV+gwOE1pvdynaaggxoT5qgSdUpAglogNozECzHXo9ogTtd7qD+uLKZ95b9JAdZurCqVKu6eHw8eaNja2ZInVVXmAwAgpnY3CIcZoKtMWAAtiqsOZ6pHo8xY-VyDLt8GIF5HhAAOLOtjcGgQQBQcoBT80A0O6ALH-WMxmNZTEbR3LdKwAHRaUw-ngigAObANAwAAF6sHAADCAByYAgMAYAoaAEAAPqYVh2FYRAgAG8rUgDHcoAgB4YTh5HoRASEoaGKq9q0Kglj0Xhatq5C1jmADaCQQM6BAQMYJB4CIEDdAAuia3TcWJEAYAam4EDg5iYLoQE4J0pjMIofHAQANBA3AEIYBB4AA7gQXgAPwQPR-ZMVJCRiV4knSQA3GAXooahIBkRR2EQIA0raAKvR1Q8r5fmYVRyHOgu1hAcwprhgAjtwWBEPp656AlHoQCIih4Au5YCLRcDfmlRAYAQwEYKYwDcMwzpEKYbpgLREC6CuNXxpx5CZfonTJalRCdLZjFqEOHgZVGWWxh4k1gGJNHtmq8ZsRaJp8JIGpeAATDttAmpp2lVeQugmmt+o4CaNB4HgFXCO6GDne6UDAc9hZQKw70fVAzomlp3AYC9GZaI6R06QGH3VoWhi+hgRBEHgkPmtDnrkF6nltX1m5xqx7FjTmeabZqu37ZmBOlodWk6fjyY5mdOqJhTEBXRAN13dgBBM3T9BPYzhYcfQb38x9gsQF9Is-WLf0QADQM-eTPMQKD5bg1VyMZqj5pi7DDbw4jGuBlW6MeV5YBoRF-mANBypSAKbW4URVF5vgJ4ECAGBKgDVcgRgDHkYAKt5Pi+b4fsAX6-v+gEgWBwDCKYpkYIokHQfB5BePefsB6+76fqY35-gBQGgeBph3fVzqEPKruAC9mzKuBnQfZ7n4cF4nsEIdRYBAA)

### 题意

实现一个泛型 `DeepReadonly<T>`，它将对象的每个参数及其子对象递归地设为只读。

您可以假设在此挑战中我们仅处理对象。不考虑数组、函数、类等。但是，您仍然可以通过覆盖尽可能多的不同案例来挑战自己。

例如:

```ts
type X = { 
  x: { 
    a: 1
    b: 'hi'
  }
  y: 'hey'
}

type Expected = { 
  readonly x: { 
    readonly a: 1
    readonly b: 'hi'
  }
  readonly y: 'hey' 
}

type Todo = DeepReadonly<X> // should be same as `Expected`
```

### 题解

```ts
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends Record<string, unknown> ? DeepReadonly<T[P]> : T[P];
}
```

- 主要在于判断`T[P]`的值类型是不是对象，是的话要进行递归处理

## 元组转合集

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00010-medium-tuple-to-union/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAMEFoKGFFQI36Bt4wECqDG0yiEGF4BGAnhAIIB2ALgBYD2V5AYgK4QAUAAgIa0AzNgEoIAYkC0coElvcQEsqAgKYAncTTYAHADaLxbKrKZ48Y0xEAZGYDu3Y1EB52oAbnQNs2gaPUABgBVNO9wwCqBpgAedwA+V0AYf8Bg7UAV+MA9tTRAASNASHNAHgVAELccQCAGGwhAaPlAIM0c12KaAGc8GlINXQplVQBeCABtAHJoFoAaCBaAJk7ugGYWgF0cyuqId0VSmghGz21FH39DKkDa5RCIYGAIRQAPaoBjGkUAEwgaBghiXTaWiAAfbr6nwZa8YtccrcAKdQgAOKyehsYgQQBQcoBT80A0O6ALH+6DQaBpSgAuHZlI50AB0ACtSliGMoAObAODAABedAQAGEAHJgEDAMDM0AQAD6HM5XM5EEABvJpQDHcoBAD3Z3LFbIgjOZ410AFleBoNPIicE9vtTlQzqUIMpFLwzkwtOR+KQmsMto0AN54JoABQg8kmTSobAAtrdlMNhiiILaANxgAC+YBlky8Sz8ATW7jVGq1Or1BqoRogJrNFogAGtFKQGAIIPLFcrgiEA2AdpcqroFt5I6sS3NJrHFJrtRteKRAvIlKoAJKnV1bAD8EH7ildEB9VEUADcVAGK6GaxGVkEYwc49rdfrDcbmOnG+5nW6PcMy+WQKLxVyIIBpW0Aq9GACldclfrxzJUzZK6NITZpaIAAogAjmwvBaF0AGHIoJwQIGEACMoDATi03AyggmJgToVBEtMwBsDQshaKUHwhlWEBHLwpTTI2TR4JBxw0IEwGgVowThssUaBE00A9AMXQtAALAArAAbP0NDKGwijml0PEDG8gmiQ8zwSVJIQhB0dFQScTEgWBbGLBx9bcbxMkwLx6maaMzIXq+b4QIA0HKAABygCm1nZ14fheeBbIAYEqANVy-KAMeRgAq3vCiLImiwAYtieIEsSpKwMA-ClAA7ioFJUnS3kQDCIVhUiqLoqUmK4vihIkmSpQMFo+GrOUUBbIAL2aAFiaVj5RFRUlbF5UZTS9JSmAQA)

### 题意

实现泛型`TupleToUnion<T>`，它返回元组所有值的合集。

例如:

```ts
type Arr = ['1', '2', '3']

type Test = TupleToUnion<Arr> // expected to be '1' | '2' | '3'
```

### 题解

```ts
type Mapping<T extends readonly any[]> = {
  [P in T[number]]: P;
}

type TupleToUnion<T extends readonly any[]> = keyof Mapping<T>;
```

- 先将数组中的元素组装成一个对象类型，也就是`Mapping`
- 然后通过`keyof`就可以获取到数组元素的联合类型了

我在解答区看到的👍比较多的答案：

```ts
type TupleToUnion<T> = T extends Array<infer Item> ? Item : never;

type TupleToUnion<T extends readonly any[]> = T[number];
```

## 可串联构造器

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00012-medium-chainable-options/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMBMEFoKHvlQTHKBUAwIeaAIEwFmqUQSOIICMBPCAQQDsAXACwHsbKAxAVwgAoABAQ3oAzDgEoIAYkC0coElvSfwAOCgDYBLAMb86qlgQISDEQBkZgO7c9UQBTqEAFL8AbvwDK6gE6qFdCFMCIRoBu5QBH6gBx6gFjygP7ygBSuaOiAEP8Awgz8qjT8pMoApsAACu5pajRpgJD-gCFugL+KgA6mOIAAcoBUcoCf2oCGMYAw-4Bi8lYAKuQKac5uHl6NgAbygL8BgBAqgGAuRYCbfoBrcoAiaYBQcoDePoDR6oDoKoD4-+YQFoCb8dWAiMaAGEZSg8iAp3IRgN9ygPCGA4A5qoBE1kWAnaaA8PqAedqADc57K4gQAJL0NIuIT8dRpAA0EA6XQggDQjCCxZT8ADOKIggF+EwAyEYAgBgGgAB0wCBkYAF40A2fKAEjlquUIAADJgeHQ0bgAazS5ChDmUHDSohpEEAMSq0gDmaTo3F5OKsdIZLD5UgJhIiZKKEFZlEFnO5EEAlkaAVR1AMr6gFklIoNFaNQBYCYBx+OFor5gHbgwBryoAAc0AE36AZb9ADnmOK2gGj5QBBmlsaUG6CiCAATNLqZEuNIQdQsFGeeM0ISqIUALkRCSSKXSW2TiYgMZRHGUngAvHGWKmhQQoAA6enaFjcADkQiYTFbULgAGZRHWII2ZUzW8kALZpbsQVt0TppBDqBLKdI0EUo1sDqANpuMtukfguacAbwgmrSmdbAAlcsomBAAOpMFzKMOtiAAXy32-rIrF34IYBgAgQB8c0AbHMizSEsywgIoVkAejNACx-ggkjoYFQXBCAACUoNLTxj0HDsmEzGgOHHUhgUHCcLwgRM3DXQcDxcTMCO3bdz0zOiklrNiPwIPioCDGktgGQAr5SJCwVkAIR1ACN0r49kAfKUhj+QBYOSJL5LkAVXloUcYBrEcWD3kAUTTAG4EwBE+O9KACAGc5AHEFQA+6NpNU+VEwBS40AdeVAHVtQAyb1QQAYANpc8+Q8rSzTEolABazQAmdMAYO1AAJ5QAkBLg5Y-LUwlAG34rEtMAEE0xhxQAYFUAdgsVRpZyIFE4JAGqIiJKkAQmtLIgAgAD5tggABxVRGA4UgIHmQBT80AaHdEIYOg6AUFF0yAkMl3rAArFFGxcIVgDgYAAC8GAQWIADkwBAYAwEO0AIAAfTO86LvOiABiKQBjuUAQA9Tsu56TogfbDrnWF4kSZJUjSAAeNoIErY8PxakGCF3Fh-oAaQgNIAA80JoMN0S4tcoQANSalk2UzABRBGow4CNYahNUmCEaEmo5fguRozHREzb6cz+wGIAAMmwyNnzDMmIGxmmCD-cVMzaABuMA+KOkAnpei6IEAaVtAFXo8IfTl+Wzreg7VHHBRn3w6g1FZKFCa6dRPA-CAhBcJhxxnXhPoXJc6dXddgA4bRlA3Q6IyjQ9YwLTx+GZ7NfrzMAg8g6C6GgYGIH4Ahh2bUciOnPtv2TvdWyYk8zzp7lLxvFd7yfF830-TOodHajp1nedF2XN2oM3JORYHSOE08Ys8PgStE53Ec21rqFW0EJhGGBCBa+-ICIEdlEEER826CXlxbZcJPq+H-hJzr5FCxntvRXFD75zjVEoPjgBtAgzcjOh-qoY2Aadyno7w6BTYRle0jDaAmpCygPfC2T8X7-TflTHuZZYDf1-mGWAgCIRgAALpn1hCAtC-946sWtp2EiZEKKbygExFig4OK0ToPRHin4CDUU4lQ7iUt0GxkwX-PuEBcH0ModQ5hh0wDHU1grQA0HKVEAKbWGtNbawEeAKALVABgSoAarkbqAGPIwAKt7DVGuNSawBpoMDmgtZ8y1VqCBRAAd2BOtTaO1moQEGuozRY0JpTRRDNeai1jGwGACiJgXIU6hjkRAQAL2aACxNUwjjtEuLcYYpaVitq7XemAIAA)

### 题意

在 JavaScript 中我们经常会使用可串联（Chainable/Pipeline）的函数构造一个对象，但在 TypeScript 中，你能合理的给它赋上类型吗？

在这个挑战中，你可以使用任意你喜欢的方式实现这个类型 - Interface, Type 或 Class 都行。你需要提供两个函数 `option(key, value)` 和 `get()`。在 `option` 中你需要使用提供的 key 和 value 扩展当前的对象类型，通过 `get` 获取最终结果。

例如:

```ts
declare const config: Chainable

const result = config
  .option('foo', 123)
  .option('name', 'type-challenges')
  .option('bar', { value: 'Hello World' })
  .get()

// 期望 result 的类型是：
interface Result {
  foo: number
  name: string
  bar: {
    value: string
  }
}
```

你只需要在类型层面实现这个功能 - 不需要实现任何 TS/JS 的实际逻辑。

你可以假设 `key` 只接受字符串而 `value` 接受任何类型，你只需要暴露它传递的类型而不需要进行任何处理。同样的 `key` 只会被使用一次。

### 题解

```ts
type Chainable<T = {}> = {
  option<K extends string, V>(key: Exclude<K, keyof T>, value: V): Chainable<T & Record<K, V>>,
  get(): T;
}
```

分成几步来进行分析：

```ts
type Chainable<T = {}> = {
  option<K extends string, V>(key: K, value: V): Chainable<T & Record<K, V>>,
  get(): T;
}
```

- 每次调用`option`方法时，需用将`key`与`value`的类型组成一个对象类型，为了获取到这两个字段的类型，所以要给`option()`添加泛型`K`和`V`，再用`Record`进行组合。
- 泛型`T`是用来累加键值对类型的，而每次调用时，需要跟上次的类型进行合并，所以使用了操作符`&`变为交叉类型
- 最后就是每次调用`option()`返回的都是当前键值对累加的对象类型，这样就可以持续将该对象类型顺着链条传递下去了

由于同样的`key`只能使用一次，所以要对泛型`K`进行约束，也就有了最终的形式：

```ts
type Chainable<T = {}> = {
  // 使用 Exclude 进行排除重复的 key
  option<K extends string, V>(key: Exclude<K, keyof T>, value: V): Chainable<T & Record<K, V>>,
  get(): T;
}
```

## 最后一个元素

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00015-medium-last/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=30&ssc=79&pln=30&pc=1#code/PQKgUABBCMCsEFoKABzQcCqAA5QVHKGFFQBL6UQSOIICMBPCAQQDsAXACwHsbKAxAVwgAoABAQ3oAzDgEoIAYkC0coElvSfwBOC-uQIEJGiIAyMwHduaqAD4IgRldA7DGAZa0BXgYAqlQN-+gRejAMP+Aia0Bz8RAAKAJQiBOU0Db8Q6AFLGA84mAQAz6EAaAFOqAJtaAiMaAGEZSgF96gHXRgP7ygBSuACrkAA4ApgDKAMYKAJb5dBAALAB0AAyRgHnagA3OWIBYCVkABgAy-ADOdAA8OQY9joDB2oClxoDryliADqaAI349OT2AbnqAK-GAe2qAbopoWHiAIW6A3j6A0eoRUASA0fKAQZqRPU90gwR0BYUQigrQEAC8EAA2gByfjAgA0EGBpAhUNKwIAum8Pl8lAAmf5AgDMkLRkOgSOuUHeRQgdH4FQANr8AQNhiNvtAjMBgBBCgAPIqlOiFAAmZKYEFIn2B8ORpPJVIxtKGo2+aOZrI5XJ5-LoguFMAITx6kSM0QgAHEKowOKQIIAoOUAp+aAaHdAFj-DDodHygwAXCyXqUGPUAFaDepMBQAc2AcGAAC8GAgAMIAOTAIGAYGToAgAH0M5ms5mIIADeWOgGO5QCAHuns2W0xBE8mSZ86aMcmz2TyaLzBhAFIV+LyWJTKIJyICEUYAQ2Oc3W0D6lOKjQhIUFBAvJCZ3OF30ERAAPwQPoQV0QHIAbjALLJKLrY0b47bHa7Pb7rEHw6B-chU-qOQRgJyIMphRoQaMIiCLHimICluWWYQIA0raAKvRWQ3BBkEZpWSYVAAtvkgY1AA3hAACiACOHD8JSkL4ZyhTchAAC+EBCAoTDoVCvA1ggXqkX+AGFIMwAcHQVKDMC1YoqUQw8ZigIEBRKojERJGUiMF6AjiEB4jAQ74gYBjgtJlHcnJxGkUpsojIC3DiH8RjQGiql4fw+7DJUAG0ZpED2Y5dDOUGtHabpSLJie4HIVBgDQcuggCm1khyGoUFBBGIAYEqANVy+aAMeRgAq3g6Touu6wCet6foBsGoawMAgiDAA7vOEZRnG8UQLaGVZc6boeoMXq+v6gYhmGgxMJS-EVCwryGBAgAvZoAWJq6M1OVtR1hXdTVMbxlWYBAA)

### 题意

实现一个`Last<T>`泛型，它接受一个数组`T`并返回其最后一个元素的类型。

例如:

```ts
type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type tail1 = Last<arr1> // 应推导出 'c'
type tail2 = Last<arr2> // 应推导出 1
```

### 题解

```ts
type Last<T extends readonly any[]> = T extends [...infer R, infer L] ? L : T;
```

- 因为元组支持 `rest` 元素，所以通过 `extends` 搭配 `infer` 就可以获取到最后一个元素的类型了

在[解答区](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ftype-challenges%2Ftype-challenges%2Fissues%2F100 "https://github.com/type-challenges/type-challenges/issues/100")看到一个👍比较多的答案，我的答案简直`low`爆了：

```ts
type Last<T extends any[]> = [any, ...T][T["length"]];
```

- 首先通过`[any, ...T]`组成了一个新的元组类型
- 使用索引访问类型可以获取对应的属性，只要是数组上的属性，都可以使用：

而 `TS` 是可以推断出元组类型的长度的，所以 `T["length"]` 是可以获得具体数值的，从而获取最后一个元素的类型。

**例子：**

```ts
type A = Last<[1, 2, 3]>

// 相当于
type A = [any, 1, 2, 3][3]
```

## 出堆

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00016-medium-pop/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMBsEFoKC-FQYBqUQr2MCMCeEAggHYAuAFgPYmEBiArhABQACAhuQGYMCUEAYkC0coElvQewBOE9vgwYBCiIAyMwHducqAD4IgRldA7DGAZa0BXgYAqlQN-+gRejAMP+Aia0Bz8RAAKAJQiBOU0Db8RcAUsYHnEwEAM6hAagBTqgCbWgIjGgBhGQoBfeoB10YD+8oAUrgAq+AAOAKYAygDGEgCW6WQQACwAdAAMgYB52oANzoAAcoBUcoBYCUkABg5U6QA8KRrtloDB2oClxoDrys2ADqaAI37tKe2AbnqAK-GAe2rNgIU2gJDmgADmgHAqzYDCioAEvoAhbjMBUBiA0fKAQZqB7Q9kAM4YZBmZEJIS0BAAvBAAbQA5OwgQAaCBA3DgyG5GFAgAmQIAuq93p8pAAmP6AgDMEMxEOgqMuUDeWQgEkyP3+XV6X2gWmAwAgmQAHllcmRMgiIGQqBBcB9gaCIVD4XCSXz0VTsbTuj0vpimSz2Zzubz+YLhfiIJipQ92oEQCBALkZgDRNE2ALH-ADAqgHYLSyACoNAPpygHvlQCncvV2tkKAUuGRBp0GE8KO1ADEq7QAqiQQ36A4B0FUA+P+BLTBCAAcQKlAYuAggCg5QCn5oBodytFDIZHSTwAXMznrkKOUAFZPcpUCQAc2AcGAAC8KAgAMIAOTAIGAYAnoAgAH1Z3P53OIIADeROgGO5QCAHjOF9vpxAxxPyR86X1WWzuSQEU9PrQAcitP8Uqfz5fAeU3wUSFxMhIIE4IZx8GRCAAH5fwgKsIBSABuScWQtE4wEPCAfTjE92WfK8ANve9IKfTILyvAEAIhN9yg-L8fycIDQJcCDoMQ9EHGDCg0LPfCXyw5EIQAaRwgFSJSHjkRgpDo1jf1WIw698FvHi+O4ki3xSYSJzAKcd23CBAGlbQBV6KSK4tw0xd9wKABbdI2xKABvCAAFEAEcGHYAAbCFbI5TIuQgABfCAuAkKhTMhVhDwQesXOc-D20yJ5gAYMgCmcp4gQPdFcnYJ4YpxAEMHc9UegcpznJ6Y8AV1QkYDvCEyoJO8NDBXKPK5ArHJckqFRFeFoTFOExSRCEqsBEEuolFENHqxr8sKtqUIkzqxW62Exuq8UerGiaoDyzyyBaoqSuYnoar1IkuIgSp6rxAkiQhSo6oazamp26birE305uGhbRtOxEgQu4F+shUVIUWoFJXGhrUVU9SjMXQBoOQaQBTa0MmG93HUAMC0QAwJUAarkV0AY8jABVvUty0rGtgDrBtm1bDsu1gYBOCeAB3b9e37YcMYgYtCeJitq1rJ56ybFs207bsnioZz4oKGgXk0CBABezQAsTVUHnSf5wWqZF1nBxHfcwCAA)

### 题意

实现一个泛型`Pop<T>`，它接受一个数组`T`，并返回一个由数组`T`的前 N-1 项（N 为数组`T`的长度）以相同的顺序组成的数组。

例如:

```ts
type arr1 = ['a', 'b', 'c', 'd']
type arr2 = [3, 2, 1]

type re1 = Pop<arr1> // expected to be ['a', 'b', 'c']
type re2 = Pop<arr2> // expected to be [3, 2]
```

### 题解

```ts
type Pop<T extends any[]> = T extends [...infer R, any] ? R : T;
```

- `extends`是跟`infer`搭配使用的
- 通过`rest`元素组成新的元组

额外的：实现`Shift`、`Push`、`Unshift`。

```ts
type Shift<T extends any[]> = T extends [any, ...infer R] ? R : T;
type Push<T extends any[], K> = [...T, K];
type Unshift<T extends any[], K> = [K, ...T];
```

- 道理就跟上面差不多了，利用的都是元组的特性

## Promise.all

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00020-medium-promise-all/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMAMEFoIAUBOB7AtgSwM4FMA6AQwBtTJEFqbKAjATwgEEA7AFwAt1WmAxAK4QAFAAFiHAGYCAlBADEgWjlAkt4LiqVMSby6A7KXYJsrSpXnmIgDIzAd26moAPgiBGV0DsMYBlrQFeBgCqVA3-6BF6MAYf8Aia0A5+JQAJQhATlNAbfj-QApYwHnEwCAGOwhAOlTAU0VAX8VAB1MAAzQsPHxmcnyAwGDtQFLjQHXlIpwCABlsAGt8QE-tQEMY3MARvwDAFfjAPbVAHgVAFL1ALjlCjEb8AB4AFXsKwDdFRXyF-MB6M0BjyMAE80AQt0Blv0Ac8z7UqEp86-ZcSgBjHlx2CAAHGZKARggAXhQPgiEVD4XDoUgAN3wwgAzDIANwPJ4vd7FAjQX4QAAs0ARUEerGebwB+GhGNY+AA7v9UfNnqhjABzezCYTA0EQ-AAGggwIAVvh7uw5D9HABvShQAjsBbYTD4dACdiskFgyHcz6wWDcgDkknQ6G18MoAF8jZcoMBgBB8AAPV4C9j4AAmEHY6AgdHwEGmNLmAG1WAJMJ7UNzsdy6YyALrLREE5EYholEjkYR+lGzT7cjMlaDZ4nQqMQYi4CD454yK7XNKOQAU6hAAOLYLgCOgQQBQcoBT80A0O6ALH-OOx2K9cAAuS23e6cQi83CEdCoBnAODAABenAQAGEAHJgEDAMAH0AQAD6p7P57PEEABvIHQDHcoBADxPF+fx4ge4P7AY9ogCwEr1I+ALF+8wLNaNqOqwTqlhIDB+jGGLilAfrIBAxgQO0DDoJIP5RiOP7IUWtoQVB1KzHMxiSPgqAQOEjgAPw0RAeELARYDGhAB5OgKpDqF60isIK2A8KRJRlKQixgcR0G8HBzLgmQAggnhwLEE6PCkEwfqENpCxRjIeFJgQix-gBQH2os9j2AeYBHi+z4QIA0raAKvRgAUroA0fJPnZl7vrKrzzi8ooQAAogAjgIZDckFdoOhA7GSDMEDaqIn72ggk5kABrAMiCwCKvouDage5bIsSYkLCC7DfH8hmlKmfpZjA3KFsWpbFZWxVEjSZUVei1WlXVDV5iJgJsqqUKwkWJZlki7VIp1szdc8pJ9V1A3ckNNVAiqHIwjIekfsBZYliCGJ+pQUX2oKcyheF4kpfK2E5gQi2VdyNX+oNTUxpZnLndFV03WQcz3Vh82ieQ5XPBtxIfet3KBsGVHffYv1QBdDrXWFQMg49-WkJD7DQm9MMBkGIbw2TVEU4jqDI2AUbWTZICeV5r6ANBygAAcoAptYs15b77qAlCOIAYEqANVyN47IAKt79oOw5jsAE5TjOc4LkusDABIuAUlRq7rtuQsQD2Usy0Oo7jrgk7TrO86Lsu7J5U8BuAC9mgBYmjYJty+blvKzbuubju75gEAA)

### 题意

给函数`PromiseAll`指定类型，它接受元素为 Promise 或者类似 Promise 的对象的数组，返回值应为`Promise<T>`，其中`T`是这些 Promise 的结果组成的数组。

```ts
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise<string>((resolve, reject) => {
  setTimeout(resolve, 100, 'foo');
});

// 应推导出 `Promise<[number, 42, string]>`
const p = PromiseAll([promise1, promise2, promise3] as const)
```

### 题解

```ts
type TupleType<T extends any[]> = {
  [P in keyof T]: T[P] extends Promise<infer R> ? R : T[P]
} 

declare function PromiseAll<T extends any[]>(values: readonly [...T]): Promise<TupleType<T>>
```

- `[...T]` 的目的就是使用 `rest` 元素创建一个元组类型，看个例子：

```ts
type A<T extends any[]> = [...T]

type B = A<[1, 2, 3]>
// 相当于：
type B = [1, 2, 3]
```

- 而使用 `readonly` 修饰 `[...T]` 就变成了只读元组类型
- 最后就是通过 `TupleType` 来遍历元组，把元组中的元素类型按照条件进行处理

## Type Lookup

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00062-medium-type-lookup/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFBsBMUC0UAqBPADgUygGQHsCBrAV00hiWpqUqgCN0oBBAOwBcALAt5gMVJQAFAAEAhpwBmpAJRQAxIFo5QJLei0mwCWvRQFtxFaFHoLTUQBkZgO7d69AHxRAjK6B2GMAy1oCvAwBVKgb-9Ai9GAYf8Aia0A5+KgABQAlKEBOU0Bt+N9ACljAecTAIAYbI0BIc0A30z9ACoNAe+VAX4DAGD1AbHNATgtAO2NAN0VAPR1AcgNACnVANz1AMbSlQFPzQD8jQG8fQGj1VKN6BsATa0BEY0AMIyU-QEQjQBu5QGeDQCwEwHH4hsAVAMAIFQADAGFxDigAHygAEQIAcy2lQBwTQCJfQBtFQEdFLY4sbC3AdW1AVutAU3NAduDAGvKgA+3QApeoAQtz6yUARsaAU+VALvRgBfovwNQCncoBoOUAXJ6AaPkpnNAPjmJS2hBIAFVMAAeM7nI5QPYcAA0UAA5AATC5M2xbP6AdP0tlStn4iURiGTKRcaXTGUyAMb7Dlc3l0rYDGD0LbqjgAZ3omk42AATlJxNLcHSoABvegwV44ABczNlHCZVsY+uw2BZmvtTJYTE1mt1mkkTJpTIAyjx9dxxJp9SHjkydqR9QAbePMgBC2DY53EafoAF80jBdRwDUaTadxZajNa3t62ednbXXe7Pd6ABIEDQs9NMjP6zQcDiSdB9jOkFMpxvjggADwNzZgMGlBBTBH13oY+oIAHc2H3d1wh9g+wwU8biEuoEXBkYbbgALLoKlQAC8+GForNxypUpn9jAMAUDYHOODSmWLJQBwBCMLgfIXFsarqsWUD2A0UAAOJDlwpAMFAgBQcu0gDQ7oAWP9cMOmBekBWrSlwAB0ABWmr0Ru5zAAgwAAF5cEgOwAHKQCAwCQKJECgFAAD60kybJMlQIABvJgoAx3KAIAeUlyZpklQMJYkPp+pIUiSjKoCBc5lmwnpQJqHCDjm9gfiSZkWVZ5rQfWaA3lAAD8UBOfabDYAAbgaADcYkReJIAaVpslQIA0raAKvRgAUrliMWxdJOkiRAmi6JgG4HG5ACiACOpB5oyRVgdgEFeVIO66MyogPkgdF5im2bnNgmrAKQHCaCmmrNpApblsapr7Ba9APt6jrXtubZesyvroP6gbBqGEYFVwMZxqGSapuOnV5s2t4jXqhrjVW1I1nWdrMjO9ALR6S1Ml2PbjoOw6juOk7TuyoYZvOi70Ku66bsy257geoZHieZ4XtKV6QGdED6ewuV5u+tKTb+Fx6W8UCypq3XYwA2vQVXgRw5KleVKbksSIoUhj+gpv+7K2IyVK2FzlPVRBtNlXmjNfizWhs1Kc1czjHC8-SkAALqRZAEkZXFaKAABygCm1ulGVZar4BGPYgBgSoA1XJKYAx5GACre5GUdRwC0QxzGsfq7GcZImq7ga3G8QJdhQCRNt2xwVG2jRmp0UxLFsRx8DAJqa59dobDasbUCAC9mgBYmlYIdhxHUcu7HPF8YJEC6RAkBAA)

### 题意

有时，您可能希望根据某个属性在联合类型中查找类型。

在此挑战中，我们想通过在联合类型`Cat | Dog`中通过指定公共属性`type`的值来获取相应的类型。换句话说，在以下示例中，`LookUp<Dog | Cat, 'dog'>`的结果应该是`Dog`，`LookUp<Dog | Cat, 'cat'>`的结果应该是`Cat`。

```ts
interface Cat {
  type: 'cat'
  breeds: 'Abyssinian' | 'Shorthair' | 'Curl' | 'Bengal'
}

interface Dog {
  type: 'dog'
  breeds: 'Hound' | 'Brittany' | 'Bulldog' | 'Boxer'
  color: 'brown' | 'white' | 'black'
}

type MyDog = LookUp<Cat | Dog, 'dog'> // expected to be `Dog`
```

### 题解

```ts
type LookUp<U, T extends string> = U extends { type: T } ? U : never;
```

**例子：**

```ts
type Animal = Cat | Dog

LookUp<Animal, 'dog'>
```

因为使用泛型 `U` 的时候传入联合类型，所以搭配上 `extends` 就变成了分布类型，相当于：

```ts
Cat extends { type: T } ? Cat : never | Dog extends { type: T } ? Dog : never
```

类型结构之间，参数少的兼容参数多的，所以通过 `{ type: T }` 这个结构就可以获得想要的结果了。

## Trim Left

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00106-medium-trimleft/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAMBsEC0EAqAnAlgWwgGQFMAzAF0mSUqvICMBPCAQQDsSALAe2YYDEBXCAAoAAgENWRPgEoIAYkC0coElvOSQLYADgBtRqpJsyr0ozeXKzzEQBkZgO7dTUQHnagBucIAAww5CpADyoAfC4hAGH-AYO1AUuNANlNAOw9ALO1AELdAdW1AMm9AJjlAbx9AaPVANz1AFfjAPbVAADlAKjlABtMElMDAN0V5Yryy5MACJUATNMAwuUB85STkwAB9QBZNWMAvL0BfNw7AIAY7CEBo+UAgzTGXWZIAZ3ISOnUCCBIsbAIAEwgAXjRNzxIvAHIoAAkCTU0OCAB1DnRNXYhT3whgYAhAFL1ACuNAD-agC-FN5XG53R7PV6ncizFxjD6ACnUIABxAxsPg0CCAKDlAKfmgGh3QBY-2wSCR1PMAFxfBYAYzYADoAFbzelPADmwDg8GAAC82EgAMIAOTAIGAYAloAgAH1ZXL5XKIIADeVigGO5QCAHjKFdrpRAxRLlqsIABldSiGlrA7nc4AHzeAB1mLaHSRTgBuMCGtbubDHLzGiAEAAeqmY23mEHmG0wzDZHwOAeDofDrgAJABvU3mggAXwzMaIBHQEAASjmAgB+Q4eYgnEsfCkmj0SsBSnXaiCAaVtAKvRgApXcZa9uK-U4dRPEgQdMQACiAEc+MYADQzoOrGkTnMQIjoDi4U7CL1IOnGTQEWMEebAPgkTCaeYwz0rNY00TzC-7CAAbXI09XBHXXhzgumg+EctZnFG6DvMupyQe8viLj+f4AUBxigTW3jnHBCFvNhCFIWuJyoSBPp+ucUBQNhMF4YhUC-oRgHzmhpHgeRFGQRRUDQbhGycW8vj4XRyFEUxJFgZhFGOvaE5EBwdw0KIxbcacsnyYp-GCSuDHEehvqscp8G0Vp-4icBulkRAUmujhpyGWAAC6LatiAg5DrqgDQcvkgCm1q5Q56uKoDkB8gBgSoA1XIqoAx5GACrexKkuSVLALSDLMqy6AclywDiPMADuRa8vywpBRABLRbFZKUtS8x0kyLLspyCDAPMHCaNemBcIsUAfIAL2aAFiaNhlfFlXVSldV8oKIr6mAQA)

### 题意

实现 `TrimLeft<T>` ，它接收确定的字符串类型并返回一个新的字符串，其中新返回的字符串删除了原字符串开头的空白字符串。

例如:

```ts
type trimed = TrimLeft<'  Hello World  '> // 应推导出 'Hello World  '
```

### 题解

```ts
type Space = ' ' | '\n' | '\t';

type TrimLeft<S extends string> = S extends `${Space}${infer R}` ? TrimLeft<R> : S;
```

- 主要就是利用模板字符串来进行拼接，然后作为判断条件，从而推断类型
- 通过递归的方式，将空格`\n\t`等字符排除掉，就能获取到最终结果了

关于模板字符串的文档请看[Template Literal Types](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)。

## Capitalize

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00110-medium-capitalize/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCMsAYoFooGECGAHAlgFwwBscAvAU0hmWpuUqgCMBPKAQQDs8ALAe3ZYBiAVygAKAAIZOAMyEBKKAGIAtmQAmOIcqV4yyrIQy7kxXQCci9eoptQAikLIBnPDj5XoUAJL7CespxQAAaYuATE5AA8ACoAfEFQAO5cOADGXFCpfABuZGZ4TlDcZFDSOGYuUH545lA80lAYUC5mOOwA5kU8UEJYWHmpGE4lUmpVZBi5RVwlZs54jU7IOE4AdB4wAjxmUGQAHhi+FJ70QWcF9HhM-ZnY+ESk6lAAvOh34Y+RAOQzhITdiW2hDUX1iUGAwF2e36qV0Yzw3QYJS+AAkyH8AUCQaczhsoGCAGo4MiJOrsKAAcXwKKEDAAXFAuDUsE46RCCulVgArNbbdrAeAISAgYCQMUQUBQAD6MtlctlUAAmjwhDs0Dw1CU0XNpfK9VKoCLxVcbgBZbC4DovKAAb3oXwwXwZX1YXwANPaGE6oF8AELu+2pb1fNABzxfEHOgAiYZgXzIwYAorGfdJgwIU192sGKZmuMGUZmcMGvJmucGAFKZgDWwYA0pnCMGADKZ5TB02Z9jBgBymZ4wYA8pmsMGAAqZgCOwbsmbMwYASpmnMGAMqZvDB6KZoTBgCqmeywYJmcSwYA6pm9sGABqZpjBxWZkjBgBaYYAvsbriVTUxQvcERkJEq5Qro7BqIULRtO0YKvFAoH7OBkHBAAJDabTSHkUACB+6GYdhC4fgkAD8aE2gIYEBCh1ZkEw9RQOafQwVAZFMZa7QANoCAAulADK4ehREJAyq4ANzipAkr6nqUDRPMbzDIUMnyoaooQDg+jbAsNpQImk5CEQbp6dCZCwlAH6lGYPDaF84gmmQyDpEQfgdM4wBCK4hArt+NyDEp1qcfQiambCkT6YZhCRH+AEfFEXzSDwPAMBg86xMZXxbMlqWgulwWhXg4UGUQ0X-u8DzxQIg6Dr6rBLulPpVTVdW5R6nghTChURSVMXlUB3yJYiOUNZlSWMMNeXtQVRWRaVsUVcBXyghlrX5Z1M09WVYQLd8joja6sSTTAHVmV1xVRb1239V8Xojf6h1tcd03dRdW2AZ8XxBiNoYPWtp0ba983XSCI0xr9U3rS9c19R9CYjcm4NPZD53Q1dH1piNGaIyZyOzZd73xdmI25tjJ1hVD+NxYt+YjYWpPPSjlM7V8xYjaW9O45tQMfeWI1Vhz-0U29VPfLWI0NgL5OM8LzNNiNraS2deMy9d7YjZ2isA6jBOLd2I19prQvc-FA4jcOhvS8bi2jiNE4W8rVvfNOI2zvbXMw-FaUZfVR044Llse4tK4jeubuA4H3ybiN25h9rItfLuI0HrHTPXUeI0ninKsfWeI2XlnjtfNeI13gXEdfA+I1PmXaPxS+I3vuDPGSRAUkgLqKkyjhqrFDsq66CyHed2pbf0GCq5cKlJT0aqzQ8IQnluOwrKMsyrLsk4nI8qsfICogwBSE4iR5GPUBEiSc8L64fAr0yeAsmywAclw3K8mY-KCsATjz4vN+n6a2wShoEnn8AI7RnAMjvg-DeW837tGFOpSAQA)

### 题意

实现 `Capitalize<T>` 它将字符串的第一个字母转换为大写，其余字母保持原样。

例如：

```ts
type capitalized = Capitalize<'hello world'> // expected to be 'Hello world'
```

### 题解

```ts
type Mapping = {
  'a': 'A',
  'b': 'B',
  'c': 'C',
  'd': 'D',
  'e': 'E',
  'f': 'F',
  'g': 'G',
  'h': 'H',
  'i': 'I',
  'j': 'J',
  'k': 'K',
  'l': 'L',
  'm': 'M',
  'n': 'N',
  'o': 'O',
  'p': 'P',
  'q': 'Q',
  'r': 'R',
  's': 'S',
  't': 'T',
  'u': 'U',
  'v': 'V',
  'w': 'W',
  'x': 'X',
  'y': 'Y',
  'z': 'Z',
}

type MyCapitalize<S extends string> =  
    S extends `${infer F}${infer R}` 
        ? `${F extends keyof Mapping ? Mapping[F] : F}${R}` 
        : S;
```

- 通过模板字符串 `` `${infer F}${infer R}` `` 可以获取到首字母
- 事先定义好一个大小写字母的映射类型 `Mapping`，`F` 作为键就可以获取到对应的大写字母了

## Replace

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00116-medium-replace/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCM0GwQLQQEoFMAOAbAhgYzUiURNKICMBPCAQQDsAXACwHs7qAxAVwgAoABHIwBmXAJQQAxIFo5QJLeUhmgC22HIsRYAlooBOOLESKTjEQBkZgO7dDUQHnagBucIAA3SqCAHgDKAGggcdLJd4AKiwAfA4QgGA6gOragGTegExyju7h0oAhboA03oAAcoBUcoAK2rEJDr7+4YD-ZoBGxoBcco7B4YBADFYQgNHygEGaDQ7tDADORAyUGGgQOpi4BAAmEAC8qMP4aK4A5L39nRA4QxCidACE897zm7sQ8zgA7mid-mjzIRDAwBCA+OaA2OaA9GZHS+er66fnlztE7QcDRugAp1CAAcW0TC45AggCg5QCn5oBod0AWP9MBgMDCdABcdy6eCYADoAFadQksHQAc2AsDgwAAXkxEABhAByYBAwDA3NAEAA+gLBULBRBAAbyKUAx3KAQA9+cK5XyIJzuR9pi45u4IGgAB6KOijFadBg6TR0SneIpKTU6tB6g1Gk1miDBK26-UQQ3G003KZgC0um1u+bzCBgAD8EA1YGxEf9tscABIAN4m4RoHQ+AC+SYtWeTdFT6YAohnwkRww5s7ngrni0CoNH3ABublgXnyuUQQDStoBV6MAFK6NWXtkVKzQqCkMCCJiCFgCOXH03kLWv6eAnGY2fkt834H0QBP0WBtlPOwC4DE0WE682VfQGeBwnU+UwA2kQlyuGK5Z-OsK5nCM5n2FgWHINZDnmUCdHA4RgOuPYYJYBDrhCTw32XNBVy-Od9D-GY3CAkC1kg8DiPg2CUKOBCEOIkIULQj8sJ-XC1QWaiiLAvZoPIsjCJ0Gi6Kgd8MM-b8cP-WZWOAyDSKOcg9C4lg4MoqT2Kg2jUME9DMNE39xPw8CDIooN1LAABdFtWxAQchwVQBoOQyQBTa2sodFS5UAiBuQAwJUAarlxUAY8jABVvNEMSxXFgHxIlSXJKkaXgYAhE6M4dAZJk2Q8iBkUC4LMRxPFOgJEkyQpalaWAC4sDPTQ2G6KAbkAF7NACxNCxstCvKCqi4qUpZdklTAIA)

### 题意

实现 `Replace<S, From, To>` 将字符串 `S` 中的第一个子字符串 `From` 替换为 `To` 。

例如：

```ts
type replaced = Replace<'types are fun!', 'fun', 'awesome'> // 期望是 'types are awesome!'
```

### 题解

```ts
type Replace<S extends string, From extends string, To extends string> = 
    From extends '' 
        ? S 
        : S extends `${infer F}${From}${infer E}` 
            ? `${F}${To}${E}`
            : S;
```

- 主要还是基于模板字符串，然后搭配条件类型来进行处理，跟`Capitalize` 是一样的道理

## ReplaceAll

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00119-medium-replaceall/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCM0JwQLQQEoFMAOAbAhgYzQEEstIlELKyAjATwkIDsAXACwHtH6AxAVwgAUAARwsAZrwCUEAMSBaOUCS3rOZoAtthwrEWAJYqATjlJQyMsxEAZGYDu3MmUB52oAbnCAAN0GgsSwAeAMoAaCG59dlUAgBV2AD5nCEAwHUAAOUAqOUB1bUAyb0AmORcfGLlAELdAASNASHNABW00zOcgkJjAf7NAI2NALjkXCOdAIAZbKEBo+UAgzXaXZ2dmAGcyZloMNAh9TFwCABMIAF5UafwiEi8AcmYIegwICcGNgI2II9ONyIhgYAhAfHNAbHNAejNT0fHDsn7nXsvACnUIAHE9KxeNQIIAoOUAp+aAaHdAFj-rGYzAwgwAXNchnhWAA6ABWg0x7H0AHNgLA4MAAF6sRAAYQAcmAQMAwMzQBAAPoczlczkQQAG8rlAMdygEAPdncsVsiCM5mvCZuGZrbw+fYADxUjFmgwgg2Y+h0jEJAUqqhVao1Wp1eoNEAiJrQ6s12t1+suCzARtt9vOEDAAH4IEqwMj-R6zc4ACQAbz1YjQ+kCAF9I0bE1HGDG4wBReMxMh+8MR7gpiIpuWrTxeDOG4Kha1RbNkIM+ADczLArPFYoggGlbQCr0YAKVw6oo7PKlOnUBO2EYgGYAjrwjAEM8rxnhtvGIGJq6chDLEBijFg7YS0INgLxmDosO8wDKIHgcIMT4sIABtMhLlfMCtzoxeUsedYNjEdh2GoHB9DODYwIg45gPYC5YJAuCLkiPx32XNBV2-edvH-BVNjg6DILAwlIOQ1DTkI8CULQqAP0wr9Zxwv8VgA7wgJA6CiOObjKJAhC+PYODyNQ9DP2w388PLLYdggPYDkg05jgErYxhPGixIYiTcNY-CONA8DeI2Mj+Io-SuOoyJRLojCsKYyTdOk6DkJ46jEPgszhNM6zp1sxifx09w9Ko-QQsg0DiJU4DnIMiCrNo3zxPswL5Wk6KQNC0D2Dc05qHgnioqyzKiPizS7ICligukyCarMjSAF1WzbEAh2HCVAGg5eJAFNrVrh0lJlQDIS5ADAlQBquX5QBjyMAFW84QRJFUWAdEsVxfEiRJeBgFEQYAHdYwpKk6SGiBoWm2bERRNFBgxHE8QJYlSWAQZ2Cwc8dE4YYoEuQAXs0ALE1rDO+bLuula7v2ml6SlMAgA)

### 题意

实现 `ReplaceAll<S, From, To>` 将一个字符串 `S` 中的所有子字符串 `From` 替换为 `To`。

例如：

```ts
type replaced = ReplaceAll<'t y p e s', ' ', ''> // 期望是 'types'
```

### 题解

```ts
type ReplaceAll1<S extends string, From extends string, To extends string> =
    From extends '' 
        ? S 
        : S extends `${infer F}${From}${infer E}` 
            ? `${F}${To}${ReplaceAll<E, From, To>}`
            : S;
```

- 跟 `Replace` 是类似的，只不过不要进行递归处理，将所有的字符进行替换
- 但需要注意的是，匹配后的字符，不应该再传递下去进行递归处理，所以就有了 `` `${F}${To}${ReplaceAll<E, From, To>}` `` ，这样就会将匹配到的字符一个接一个地进行替换了

## 追加参数

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00191-medium-append-argument/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMCc0QLQUL-xgCpUEPKgHU0kx+DcAjATwgFkBDAYwEsBTAKwgGVaBrAewCdKIAKAAIBbGg0YBnDj0oBKCAGJAtHKBJb0WVuAcwCuw+gDsALhNy4F5iIAyMwHdupqAD4IgRlcIgykYBm2iIG--QIvRdhCAedqADc6AAHKAVHKA2zaA0eoQAAYAggAOyQYAJolaugaGADwAYvoANBCJ9vGAMP+An9qAcXKAm36AWdqAIW6Av4pYgN4+cfFFVYCncoBTypGA33KA8IZdCYlVgCvxgHtqkYANpm1YCQDi8YBADPHrEICnRoCQ5gm9EM2AAkb72IBueoAU6oDU5oB8OuhdgFxyk-En2BuB8T+GJGkSajcWjJQy4P5pCBFCAAXgElAAXBB9LoiPRuKUiEiJIZgfpNPIYY4UcI0dxAhD6BAAEr0CTaAA2hlhZVSGSyOj0RkKJQgRE4nAZ9HcjlwwGAEEA+OaAbHNAPRm8KRJLJmOxuNo+NKAA8kfzBcL9ITiaj0bgfvFAo5ADTmgERjQAYRoBTc0AV4G1CAAbREYiYUi4vAAuvwABaGQzJCQI8WaWiGAPaIgAOmonGEwFEdC90l48muEAAIvQAG5x2icCCAKDlmq7AOGmgAOvf1BkNh8XpAtxwycFM0b0yYCUdL59zUejpRCUwHA0GIeia9F0CR0xAAR20dMMxf0iAALJvYABWaiyC0QbOrKMxohlwCn5oBod0AWP-10Ph4DGagBuOSOM8TTAODQYAALwDRAAGEADkwBAYAwCg0AIAAfXghDEIQiBAAN5ZpAGO5QBADzgpDcNgiAIKgylWTSfRMmyLl8iKUpyhZaEp0MDIJAEONWI0TQwwgdUPHRCAAAVDS4-QeO4GkIAAfhY1jtTdVi4z4mjfUE6kICRIoAG4oK0mC8NwiBAGlbQBV6MACldAGj5HDdOQwjaGEZIeGZABvCAAFEl0oBlSmczU0moZkAF8IA8bgkwgAByQRKUQF93KFfE6WAbRVwZCRQqI-4qSAyg5wQOEUlI8jOVyPJ+ERZFjQxPlVTxAlYSNUl0UxAUhRFMBiNpekmRyhUyvqiqsQgHFqq1HUmv1QSlRNVr0ogTK5wAJhZPL2Qoor+EE-NOFodJSm0Mj6A8dUh3sKbIXaxlDAWuF+Bk3bmwO-Qh3Wzb0jSyFqCyukWVdXAvJ8-JXO0dy8lm+hoFKM7Ovsexih+7z6F8vIAaBkG5vBulzrmqGYd9LSwB0yzkMAaDkwkAU2sLIJgjINAXBHEAMCVAGq5NDAGPIwAVbzvYMH3FZ9X3fT9v3gHt9AkAB3dF-0A0CaYga9WfZhtH25t8JA-LR+d-CRBUStcTAcCBABezQAsTRsOXOafQEeeVvmAOAsDCLAIA)

### 题意

实现一个泛型 `AppendArgument<Fn, A>`，对于给定的函数类型 `Fn`，以及一个任意类型 `A`，返回一个新的函数 `G`。`G` 拥有 `Fn` 的所有参数并在末尾追加类型为 `A` 的参数。

```ts
type Fn = (a: number, b: string) => number

type Result = AppendArgument<Fn, boolean> 
// 期望是 (a: number, b: string, x: boolean) => number
```

### 题解

```ts
type AppendArgument<Fn, A> = Fn extends (...args: infer P) => infer R ? (...x: [...P, A]) => R : Fn;
```

- 先利用条件判断推断出 `Fn` 的参数与返回值类型
- 然后通过 `rest` 参数来将新函数的参数组合起来就行了

## Permutation

- [ ] 待仔细思考？

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00296-medium-permutation/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMCcBsEC0EAKBTATgWwK4BcBDfASwHsA7SZJWu6gIwE8IA5Qs-MiASQGtcFbgAoAAgAdCFACZSyJTAEoIAYmzppJXNlWDyVKNRXGIARVzoAzqUrVqPbOIA26dRXwRxWPERsUI+ExeAQAWxAGYUpYAZmQ4lhB6lAFBVhAk7tz4IegQhJiRLNnhGQDGTrjSaV44BMT6CWTRiRQNAHR2UAAGPYFelqWYJOL41H25NToAvGjedX4APADkAIJLEAA+EEsAQutbSwDCSwB8ANwQwMAQANqrSwA023tPR0sAupu396-Hr3ufLZ3F7bNa-D5fYGPbZ-UEQoFvV5g57w76w3bQ+7vag9LqdCAnCAANRI6AA7hBkgBxEj4AASuAYAC4ICF8PhxJYmVd8AMQm0AFaWNpxADmwDg8DAIGAYDloAgAH1lSrVSqIABNMi4TAQQ5kKoQOlYXJqs3KiAyuXjWa1Xz6BYAFSeAGkIDNHYSZtQbo7PugAB74dAyBI3CjoABuWGxUAA-LdYxAWW7A8HQxA3dR47cXU82gWMHb6pQFgBRAPlSroJ2uk4nJNQFkR6OYM5y+UgJXmtUQR1WDyHQiWNI93tWkiOOIeADeEDLAEdcIQnE8K15Sh4AL4QaKYMg6JaicZIUphJwuCiiqzAAgkJyWJbW1IQUrDtIzG7UdfoTflpcrgsRY+CWFDLGsJxPHcazvPWDzfgGG74P+y5OEBcz2qW9xfBiOHHJB3zIhi4KAoRmLogCkLEXCJFUSCiJwqRdzokRlEIhRmIwXBCFIShgHAfMDq4Qcax4acUE-CitEIkRHFMdRklvPJ9EsaizGcZibFoppGmwZBPG-shi6oehxaLC2WAETcenwdiHYKmOqoQAAYjq2RYBAADKwact2jmKpasqgNQhKeWEmC5Ew2q6pYZAVH4XKsuynLcsAvJnoKwpihKCDAFEZJYCFxKkhSsXxQ0LJshyXI8nymUipg4qSsAZV3pQlhFQAsnEuSHOel7XolVUpbVGVCg1orSrKYBAA)

### 题意

实现联合类型的全排列，将联合类型转换成所有可能的全排列数组的联合类型。

```ts
// ['A', 'B', 'C'] | ['A', 'C', 'B'] | ['B', 'A', 'C'] | ['B', 'C', 'A'] | ['C', 'A', 'B'] | ['C', 'B', 'A']
type perm = Permutation<'A' | 'B' | 'C'>; 
```

### 题解

```ts
type Permutation<T, K = T> = 
    [T] extends [never]
        ? []
        : K extends K 
            ? [K, ...Permutation<Exclude<T, K>>]
            : never;
```

- 为什么是 `[T] extends [never]` 而不是用 `T extends never` 呢？

由于泛型作用于条件类型的时候，会发生分布，而 `never` 很多时候我们是用来排除掉某个类型的。

看个例子：

```ts
type Exclude<T, U> = T extends U ? never : T;

type T = Exclude<'a' | 'b', 'a'>;
// 相当于
type T = 'a' extends 'a' ? never : 'a' | 'b' extends 'a' ? never : 'b';
type T = never | 'b'
```

`never` 是永远不存在的值，最后的结果是 `type T = 'b'`。

所以在使用 `Permutation` 的时候，当发生分布时，`never` 类型的值是捕捉不到的，所以得在发生分布前确定 `T` 的值为 `never` ，方法有两种：

```ts
// 元组
[T] extends [never]

// 数组
T[] extends never[]
```

再来看看`K extends K ? [K, ...Permutation<Exclude<T, K>>] : never`这段代码。

首先一定要记住这一点：**当条件类型作用于泛型类型时，当给定一个联合类型时，它们就变成了**[**分布型**](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types)

直接通过示例来看：假如有一个`Permutation<1 | 2 | 3>`，由于`T`为一个联合类型，所以发生了分布：

```ts
/*
  T = 1 | 2 | 3
  T 就先从 1 开始吧
*/
[1] extends [never]
? []
: K extends K
  ? [K, ...Permutation<Exclude<T, K>>]
  : never;

/*
  K = 1 | 2 | 3
  由于 K extends K，又会触发分布，那么 K 就先从 1 开始吧
*/
[1] extends [never]
? []
: 1 extends 1
  ? [1, ...Permutation<Exclude<1 | 2 | 3, 1>>]
  : never;
```

按照条件判断就会进入 `[K, ...Permutation<Exclude<T, K>>]` ，就会有 `[1, ...Permutation<2 | 3>]` ，

而 `Permutation<2 | 3>` 又会触发分布：

```ts
/*
  T = 2 | 3
  K = 2 | 3
  T 和 K 就先从 2 开始吧（K = 3 的时候这个我就先忽略了，后面也是直接给出结果）
*/
[2] extends [never]
? []
: 2 extends 2 
  ? [2, ...Permutation<Exclude<2 | 3, 2>>]
  : never;
```

按照条件判断就会进入 `[K, ...Permutation<Exclude<T, K>>]` ，就会有 `[2, ...Permutation<3>]` ：

```ts
/*
  T = 3
  K = 3
*/
[3] extends [never]
? []
: 3 extends 3 
  ? [3, ...Permutation<Exclude<3, 3>>]
  : never;
```

按照条件判断就会进入 `[K, ...Permutation<Exclude<T, K>>]` ，就会有 `[3, ...Permutation<never>]` ，然后结果就会是：

```ts
[3] extends [never]
? []
: 3 extends 3 
  ? [3, ...[]]
  : never;
```

好了，继续往上返回：

```ts
[2] extends [never]
? []
: 2 extends 2 
  ? [2, ...[3, ...[]]]
  : never;
```

直到最开始 `T` 和 `K` 都等于 1 的时候，这里省略了部分推导，主要是看第一个类型获得的结果：

```ts
[1] extends [never]
? []
: 1 extends 1
  ? [1, ...[2, ...[3, ...[]]]]
  : never;

// T 和 K 的第一个类型都推断完了后，则结果就是
[1, 2, 3]
```

主要就是利用分布类型来对联合类型进行迭代，这样就能达到全排列组合。

如果你能理解这个条件分布类型是怎么处理的，那么下面这个表就能看懂了：

```ts
type P = Permutation;
type X = Exclude
```

| Iteration | T   | K in K extends K | X<T, K> | [K, ...P<X<T, K>>] | Result          |
| --------- | --- | ---------------- | ------- | ------------------ | --------------- |
| 1         | 1   | 2                | 3       | 1                  | 2               |
| 1.1       | 2   | 3                | 2       | 3                  | [1, 2, ...P<3>] |
| 1.1.1     | 3   | 3                | never   | [1, 2, 3, ...[]]   | [1, 2, 3]       |
| 1.2       | 2   | 3                | 3       | 2                  | [1, 3, ...P<2>] |
| 1.2.1     | 2   | 2                | never   | [1, 3, 2, ...[]]   | [1, 3, 2]       |
| 2         | 1   | 2                | 3       | 2                  | 1               |
| 2.1       | 1   | 3                | 1       | 3                  | [2, 1, ...P<3>] |
| 2.1.1     | 3   | 3                | never   | [2, 1, 3, ...[]]   | [2, 1, 3]       |
| 2.2       | 1   | 3                | 3       | 1                  | [2, 3, ...P<1>] |
| 2.2.1     | 1   | 1                | never   | [2, 3, 1, ...[]]   | [2, 3, 1]       |
| 3         | 1   | 2                | 3       | 3                  | 1               |
| 3.1       | 1   | 2                | 1       | 2                  | [3, 1, ...P<2>] |
| 3.1.1     | 2   | 2                | never   | [3, 1, 2, ...[]]   | [3, 1, 2]       |
| 3.2       | 1   | 2                | 2       | 1                  | [3, 2, ...P<1>] |
| 3.2.1     | 1   | 1                | never   | [3, 2, 1, ...[]]   | [3, 2, 1]       |

答案参考自[解答区](https://github.com/type-challenges/type-challenges/issues/614)，这个大佬说的很详细，不过都是英文。。。

## Length of String

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00298-medium-length-of-string/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMCcAcEC0EAyBTAdgcwC4AsIB7AMwgGVcAnASx0mSSeYYCMBPCABRuwgDEAhjggAKAALYkABwA2w9AEoIAYgC26ACY0ArmtW50auYMNJZNQ1UGyGDFQ4gBFHegDOuGkUx2oAYSJjHUMIAnQIWSw8QlIIQQgPWhELKxsAGggAd3waAGNCVnR8QQA3dwiaAGtwgANKJOwVSJwCGoA6XwgAPggANRp0TOJMCABxSwAJHVYALgh8XFxpNxngYFw3fLaAKzc2oipsYDh4MBBgMEvQCAB9O-uH+4gATSIdKggAzXCJ9Cpwx6Au4Qc6XXDsaThDAtfAAeRI9To2AAPOQIOgAB6GTCaNwJahIjIAaXRWKwuLimHYAG0ALoQAC8EDpPSZDDRmOxFJqABIAN50Eh-AQAX35guFAFERTUGAB+NBRAjwxE4ZGSjLUtraokZfi0roMOZE6kAcma0VNtIA3JcwNcgYCIAAVdy4T6CNzlR2PEEXGjGA7uvkQSUARx06VDGMhuXdIogJCogQgpvE4MhSHyNgt7mAwRosjcprBEPCuU95SZ1IYkpj6Dj6ojNmR0OiKoJatNpq6GQADF1e7X643w5HZK2lXCEZ2UabKnoqkQexkAGyDtLD2O4JvjycwjsNZGm-50QQriAAVg3W4bO7HLbbypnR9N5DeOIAhKGdNJ8N5dDUC8AEZ1yHWk7XtEBbh9J5+HeMIPkodBlhg2Cbj9KCGB6chin+CB2DeD43CIWQC28FZ5kWZZVnWTZ8B2PYDiOE5gGENxMj+bC+gGIYSLIzwKLmBYlhWNYNi2XZ9kOY4EGAfjyMwNxuIAWQOcI-GKWRc0okSaPE+jGOk7AzguMAgA)

### 题意

计算字符串的长度，类似于 `String#length` 。

### 题解

```ts
type LengthOfString<S extends string, K extends any[] = []> = 
    S extends `${infer F}${infer E}`
        ? LengthOfString<E, [...K, F]>
        : K['length'];
```

首先需要明确一点，只有[元组类型](https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types)才可以推断出具体的长度，因为元组中的元素个数是已知的。

使用模板字符串进行推断是可以获得字符串的第一个字符的，所以依赖这个特性，将字符串遍历到底，并将每个字符放到一个数组中，这样当循环结束，就可以通过元组类型获取长度了。

## Flatten

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00459-medium-flatten/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAsCsCcEC0EBiAbAhgF2wUwDtJklSziAjATwgC8ALAewFcqBLAWzYIHMIAKAAIBjehQBOjAJQQAxIFo5QJLeczOPGYqxYrJ0RAGRmA7ty1RAFOqBN+MBUcoERjQBhG8wDD-gA3lAAOmBAyMCZioAA5C4FLjQOvKgA6mgCN+gCFugN4+gNHq9oBueoAocoAr8YB7aoCCRoDOeoBoyiHBkYBADMYQgNHygEGaAFz5AAaV2ADOxNhUAA54EABmWLiEEAC8aO34BAA8ANoAjAA0EABME0MAzBPQALozQ0OwixuLAHwQwMAQoxPTEPMwE+vEleX5OyYQAOJs2PTMFBCAUHKAp+aA0O6AWP-0uAa1RKexqogAdAArargxjiHjAODwYAMJAAYQAcmAQMAwHjQBAAPrEkmkkkQRwhQDHcoBADyJZIZhIgOLx9SavRw-QGABUIHgAB79AAm1QgmAIVCG226EGIvIFwtFQ24LTw4jQE3BWpVaogAFFFrKoAB+A5a8H8VB8wWEEViiVSiCmjCcwgDVA7YglA6oRZSTVal0dQZ6raG71SvFgAmMhkQQDStoBV6MAFK4Femx8kszgNOHYCAAb31AEdmJh0BM9fymsI8wBfVqSDgQADkgjZeCQojL6EIPDw1WAzGwbHQ1WbrMazWEmGq-ZlQ2Iler2AGepLZfdfTdUq2M22u8XVbwNdX6-Qm9dg0OUwmpyWu4O4xvJwW+7Gh+Xp9L56DXOvQ0mDYH2vQCtgPKAl2PFc12-C9g2GJ9jjmV8VjWTZthmRDbwWc43w-KCvw3X9t3zFpGEYb1mwoVRmwAbimb0RgABlrCZmzIxhqPEZsMIOUjyMori6IYiBmNYlsOKEvDFijaMQHTDMmUAaDlPEAU2sFIzZlcVAYgdkAMCVAGq5SlAGPIwAVb3+QFgVBaoIWhWF4URBBgHFaoAHc1RReh0SxKAdh+MyLOwIEQWAMF6ChGE4QRJFgGqRh0CHNhGAIWpfIgQAXs0ALE1DEC4LrNsyKHNRTFsVxMAgA)

### 题意

在这个挑战中，你需要写一个接受数组的类型，并且返回扁平化的数组类型。

例如:

```ts
type flatten = Flatten<[1, 2, [3, 4], [[[5]]]]> // [1, 2, 3, 4, 5]
```

### 题解

```ts
type Flatten<T extends any[]> = 
  T extends [infer F, ...infer E] 
    ? [
      ...(
        F extends any[]
          ? Flatten<F> 
          : [F]
      ),
      ...Flatten<E>
    ]
    : []
```

- 要数组元素进行扁平化，那么就要对数组进行遍历，所以可以通过 `rest` 特性来进行迭代，也就是 `[infer F, ...infer E]`
- 迭代数组元素时，还要判断该元素是否为数组，是就通过 `Flatten` 继续处理
- 通过对数组元素进行递归处理，然后又通过 `...` 运算符进行解构，就可以达到扁平化的效果了

## Append to object

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00527-medium-append-to-object/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCsBMB2KBaKBBADpgpgOwBMoAXAeylICMArHAY2MhhRdZSakoE8NCAnHDwDSfAIYBnUgDdxAax4AKAAKUBAZgAMssQDYAnHXEBKKAGJAtHKBJbzNVaDFLMHiOHU26iAMjMB3bi+hRAedqADc6AAHKAVHKAXHKApcaAx8qA37aABUphgA2mgOragK3WgCFugN4+gNHqgEAMgKfRuZGAbKaAkHKhgEPKgA6mgDD-gCvxgHtqgGR6gJDmqZkxuXm+MIDR8oBBmgBcvVAABhPEzn7EXLhQACo44sRQALxQAN5QAJYEQ1AA5ACMh1AAvhyz8wBKywCuADarG1i4hAukAPI09MQAPEsVgAaI5SUSPe44Q6ggAsAD4oMBgFAcAAPXAMHBEMicHBbXb7I6nUHgyE4A6wi4cCZjUaIwAU6lAAOI7YgAC3ulCggCg5QCn5oBod0AWP-s4jETDiIbIqZ0dkAOmo4jlpD4AHNgAhEMAAF7slAAYQAcpAQMBIOaIKAoAB9W12+12qCAA3kMoBjuUAgB42h3e61QU0W674t74AifH52AELUFCVFo4gh8RQFZ8HZ4VWggBqiI2mw4AG0AAq7PBQRxcUgAM0WUAAPlAhABdA5F9HxwiJsuV6sAfkWhabUAzAG5IJcIBbIFafd6oIBpW0Aq9GAClc+l7p47-RAdgBbTAq1bbACiAEd7hDQQeMX8LlAK3xSFujkpAyhZRDHvhVctgPdiDtHuJDgDOZ8XjFZjnWLYODLA5DjoURiEAvwyShGDVQEfBELHSBAxIZZiAvTFiHAnMoMEGC4IQjhkIpI40JwDCOHZe8aMoUhSHfUQ8FHID5lA4h4Ag3M-Ggo4CFIVUzjre5CBwCtU2xKiIRQo4AHd2TZaEOHEaSDmIPgoW48cIBwviCL+ASSOEsjRPEySoGkghZPkghFPJGC1I0xCYG0vBdP0nBGOYg5jkM7DgNwlY1EE0iuHI0gVK8qBqJgrgcEeR54sSnyDgrCFxACiAsOM8LTMvBgossmARNgzLXOUw5UvS2q-Gym88oKmAdnEABZUgORwPgbnucRxB2UQcva2t7JkuS8AUwqePxOD8sTDY8w4MyGH+Y9T0ef5gw+b5fi2vjjlBQ4mK3aFQVY9icE4+FQVKwjjnhR6NrKgEdohfbsBDMNjoBPj4HOy7rqgV6nrwzb+Le4EPsI7aTx+g7QyOiN-j4tRzq63r+sG4bRtEGE2v-fEpJm5zHoi-DPrUOHIAbCcjKnNdHUAaDlgkAU2tVzZv0zUtcA-ERQAwJUAarkXUAY8jABVvEUxQlKVgBleVFWVNUNSQYBOPEFSBp1PUjQ4RFBRluXxUlaVxFlBUlRVdVNWASRIV-Ug8GmGBEUAF7NACxNbwzYVy3rdVu39YNY0IA3SAgA)

### 题意

实现一个为接口添加一个新字段的类型。该类型接收三个参数，返回带有新字段的接口类型。

例如:

```ts
type Test = { id: '1' } 

// expected to be { id: '1', value: 4 }
type Result = AppendToObject<Test, 'value', 4>
```

### 题解

```ts
type AppendToObject<T, K extends string, V> = {
    [P in keyof T | K]: P extends keyof T ? T[P]: V;
}
```

- 遍历对象类型 `T` ，然后将 `K` 和 `T` 的 `key` 联合起来，也就有了 `keyof T | K`
- 而设置值类型时，只要对 `key` 进行判断就好了

## Absolute

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00529-medium-absolute/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCsBMCcEC0ECCAjAzgewDYFcAXAU0mSQsrPQE80A7AEwCdi6BpZgQxwDdMA1nQAUAAXSsAzAAYB3AGzwAxpgCUEAMSBaOUCS3poC2XQgAtNJfQAdcR4klwBLEt1xkyG9xEAZGYDu3V1EB52oANzoAAcoBUcoClxoBsppiEzPb0AOYANPT4+ujEzIBoRuj2CQCS9ISA3j6A0eqAQ8qADqaAIW4ABhg4BCS1ZUmAK-GAe2phgMbWlYDq2oBk3oBMcoBADH4QgNHygEGa47VzhJhkhDQWxBAAKsQxEAC8yACM0tIA3EsrawBKW-i4hLtoWHhExAA8mzEAfMcQwMAQxAAPVZKEiMCCEbAQDIQABEh2kMLIc1q4w+EEAFOoQADijmM+HQEEAUHKAU-NANDugCx-4yEQgWTAALl+CyUxgAdAArTAs7DMBLAODwYAAL2MSAAwgA5MAgYBgWWgCAAfSVypVyoggAN5aqAY7lAIAeitVBoVEGlsuWqwejWeb3+AJITEwEFS6UyEAAPhAYnFEm6oXl4oQkhALvdagASADe6wAvrU0Xtg4C7YwHbUkBH4gAzF0AURjEAA-BBsxA6UHTrKwPLDQaIIBpW0Aq9GAClcJvrq2qTfZLNy7uGiwBHfBcXCB7NA4ggiBRiAZ5jYfQQADkojNtmZQ9wxESW2ARHsuEwC9N5wgSh4W3uAG0yKPgYQXtmB0OXg0niQXtIPoGF9IFx9P9exxBe9H1wZ9HiaV4kA-L8fz-JIANvYDB1Al8IJeQ5P0XQ5f3-KAb3HO8H2QsDLTfJBoEwhdoBw+C8MAwiQJI19Xm-X8YJohCCKQp9UKtBcoLYxdYNwot6O4lDwL47DKOkkT8KAoieMkt9+Ooyi1LksTFIk0jIP2BUjmkAyjnoGTDMMji6MQ7SmLQ+AFXgRzTK-RzHI4gBdCtKxAVs2yNQBoOWCQBTa18ttjRlUAyDRQAwJUAarlNUAY8jABVvSlqVpBlgCZVkOS5Hk+QQYAuHoTAAHdMiFEUJSiiAyWS1KaXpRlMGZdlOW5Xl+WAUj7GwYrqsAF7NACxNHx6vSpqWpy9qKrFSUTTAIA)

### 题意

实现一个接收 `string,number 或 bigInt` 类型参数的 `Absolute` 类型,返回一个正数字符串。

例如：

```ts
type Test = -100;
// expected to be "100"
type Result = Absolute<Test>; 
```

### 题解

```ts
type Absolute<T extends number | string | bigint, R = `${T}`> = R extends `-${infer E}` ? E : R;
```

- 已经确定要返回的结果为字符串类型，所以可以通过定义一个泛型`R`通过字符串模板进行转换。这样使用泛型还可以简化代码，相当于占个位嘛
- 之后就是通过条件类型将`-`去掉就好啦

## String to Union

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00531-medium-string-to-union/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCsDMCMEC0EDKAXATgSwHYHMJ0B7CAVV22N0mSXodoCMBPCAQVwBNMBTNgNKYAhgGdiAN1EBrNgAoAAkz6wADNJEA2AJwBjUQEoIAYkC0coElvEwFdK1E6Kx58tWsbcRAGRmA7txdRAedqADc6AAHKAVHKAYDqApcaAbKaADEqAIW4YOASAQ8qADqaANvGARsaAXHKhgOragLPWFFS4cYDePoDR6oBADL4QgNHygEGatQAGreiitOgsAA68EAAqvA4QALwQAOTwAEyw4wDcnT19AEpDVgA26KNojgT9xMXUADyDDgB8cxDAwBC8AB69uui8XESkTH0ARPCfEAA+EE+U1+AM+sE+tFazVqZwggAp1CAAcWw6AAFlYmBBAFBygFPzQDQ7oAsf9R6HQ3VEAC5ru1dKiAHQAK1EtOImHwwDg8GAAC9UUgAMIAOTAIGAYDFoAgAH1pTLZTKIIADeTigGO5QCAHlK5ZrJRAReKbl1egMDrZcCdbndntxRBBhLgWABtAC6sLG0n4xAAZhAAN5ga5QCD2gAKEDwA3tuCsAFsPphHY7yRAgwt-QBfP36pY7JL4faHU39c2WrjWhw5gA0EGW2ydLoGRd4VogzQAJN68B7eJgIAAxVNtjtdiAAUVTzQgAH4jfmjvae5XaYv7YknHmTUdh2d47DE7heBIuwswAa+iu9saSmb7sXS7t8JXq2Nvam64Xr42S82B7hO92+9-fxHMdJ2zVcL2OYdK29YNQ1wXsE17VMIAAMirHcIDdFhPSrI8xT9EANS1WUIEAaVtAFXowAKVzqQiiOlHVRWwKNuhZLZvRHABHKxhHWSthweXgnggJCPUwYgowmBQTyQGluPWRt8CGYArHQbB1lEcYxRPCBdDEIYa1oPjHnQDdOO4o4z1zcDTU+T4zkrPcD0wM47IM-inhMrj1nMu810vT50Fsyt-NslyoEMgTjOHUyvIs3zjk+VFeHWdZiECwFURBQFeEyz51hyvL-kBVLnPLVyjI8szYqso5Pl0FlqGECRsEwKxRDS2qctSwrPkwTqctwHLhByiQcuwHLeu6qwcrakqwEdPD8Jo2iIEAaDkgkAU2slqI+j8NoWFADAlQBquSVQBjyMAFW8iRJMlKWAak6UZZlWXZBBgFtUQAHcu25XlBT2iB8XOy7SQpKlRBpBkmRZNkOWAcR1mUkoOigWFABezQAsTW8IHrtB8GHqh77+SFXUwCAA)

### 题意

实现一个将接收到的String参数转换为一个字母Union的类型。

例如：

```ts
type Test = '123';
// expected to be "1" | "2" | "3"
type Result = StringToUnion<Test>; 
```

### 题解

```ts
type StringToUnion<T extends string, R = {}> = 
  T extends `${infer F}${infer E}` 
    ? StringToUnion<E, { [P in F]: F } & R> 
    : keyof R;
```

- 我这里是把每个字符遍历出来，并组装成一个对象类型
- 最后使用`keyof`来获取最终对象类型的键值的联合类型

我的写得复杂了点，在[解答区](https://github.com/type-challenges/type-challenges/issues/537)看到一个简洁一些的：

```ts
type StrintToUnion<T extends string> = T extends `${infer Letter}${infer Rest}`
  ? Letter | StrintToUnion<Rest>
  : never;
```

- 就是直接用联合类型的运算符`|`来拼接所有的字符类型

## Merge

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00599-medium-merge/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCsCcsQLQQLIFMBOBzNkmIMLwCMBPCALQE0BlAL1IGcIAKAAWvqYEoIBiQLRygSW9+Ae2IArNAGMALnjx8lEQBkZgO7cFUQGA6gEjlAVHKBvH0DR6oAgVQG56gBCNAAHKGjgGH-ANN6AYuVuAQt0B0qYCx5QGGRgNbcHNsYegEAMmhCA0fKAQZphAAZxsox4sqQADmgQAGaiohAAvBAA3nhQAHYAhgC2aABcEIyyGACWJVgA3MUQZTi19U0t7VAAvmHJaRDS2XmFHV01ECUArhXEmANQdWgAHj0NzVh4w1BJqekASmiMCwA2slPo2GgAPFmiADQTogB8rRDAwBBbNJyNAAEwgshyK0K5SqOz6WFenW68yWKwwiMYWzhe0OEDiMTCnwggAp1CAAcUasgAFgtiBBAFBygFPzQDQ7oAsf6pslkKUY1T+CWkVIAdBJGILRNhgHBYMA6FTEABhAByYBAwDA6tAEAA+jrdXrdRBAAbyrkAx3KAQA9tfqrVqIKr1aN0vccI8AGKImhE-JFKAAbQAChBmhAANZoUiiDKsF0QABkEBo3AAurUA1tZGgSiDmKHw5GaBAAPzx-3JqOx+PcEtgYbqsCa61WiCAaVtAKvRgApXcKWhsGu2NCopcW3AoQACiAEcFmUroiR5sgbdBpkMKIKhAAORsB2IAVTq4ZnCMYALWSNK6MNf2k4QF2TL14Mq1RbLVYkbH9avtB0QABCZQwU29CBiEfVEXygaRamIbI9zKEp2kGdowC-aQykxZh8h9PBZ3nR5x0nK5HidJ4bzeH8-0+REigASAfFFnwwdoqOAui0UYiCgOgtBYLWQZPk+MBE1rOsQC7bsbUAaDkrEAU2tRO7W01VAPAiUAMCVAGq5Y1AGPIwAVb3ZTluV5YB+SFEUxQlKVgFgxgAHdMBlOUlSUiAWW03SuR5PlGAFYVRXFLBJXgYBGFEK5j0aUQSkSKAiUAF7NACxNdRXP0jyvJM3y7IVZU7TAIA)

### 题意

将两个类型合并成一个类型，第二个类型的键会覆盖第一个类型的键。

例如：

```ts
type foo = {
  name: string;
  age: string;
}

type coo = {
  age: number;
  sex: string
}

// expected to be {name: string, age: number, sex: string}
type Result = Merge<foo,coo>; 
```

### 题解

```ts
type Merge<F, S> = {
  [P in keyof (F & S)]: P extends keyof S ? S[P]: (F & S)[P]
}
```

- 首先通过交叉类型操作符 `&` 把 `F` 和 `S` 变为交叉类型，然后通过 `keyof` 就可以拿到键的联合类型了
- 由于 `S` 需要覆盖 `F` 的 `key` ，所以在设置对象的值类型时，需要进行判断，优先使用 `S` 的值 

## CamelCase

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00114-hard-camelcase/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=21&ssc=7&pln=16&pc=1#code/PQKgUABBBsCMAMEC0EDCBDAtgUwDYYGdtJkkzySAjATwgCkB7ACwDsCGW0mBXCACgACAK2ZsOAYwYATbEwDWASggBiHFICW3TCoAu2TAAdc6PUlzq9AJ3S4SJZQ4gBFbtgI71HO1AAGAMwZLJEp0IJCALx9kAD4If0CAIVCkyO8IWIA1dWwAdwgOCABxCwAJbkoALggmHR0DAgrgYB0CcSYAOiECdsCAc2A4eDAQYDAx0AgAfWmZ2ZmIAE0Gbks0aWwIEuxLDbm96YgRsZ1qAw2MHHx0IgAeAGUIbAAPPRYpAgh3S3UWXtiAXhID2er3ecQAJABvH5+bYQABiAF8kFCYXCAKKInwkAD8EMhSKh6MeL2wbw+GAMFhs6nC2Bu6OiJCgeJ8KMhFzwhHpjKxzIgVU5V1ulOp5jpDOi0T5UCqdwA3GMwBN9nsIAAVNw6NDXNxTVWzQ6jdSGQLayEQdEAR24NgANJanmdxNrERA-JYGNoAOQCE5nJBtGy4Mm9NzAbgeXAEb3HU4bcS6j7-CAAbRI6Kd2BdDJtNhuQu5N29AQYwVC5fC3uiDpLDAYSUsKWrNYzWZz1ttuALWC5uuLpaQjaH6CrNYgdbLw+bUrtbedOlzXZ7lyLk5HYVH1drg8bM9bUEzC6X+cL-fXIUs24npcbLbnh-bi87p97wvpk8ml+vn+-s-n2bPnm3Znrc67lkE5ARD+u4VmQ+4Po6x4viBb5rugwSBj+6AJKg94AR2wErn2YEYZQgZINhuGUf+j7IURoEfgAgrhP4sXhtFIYBJ6oau540bWNEHlxhHLoxxY-vhdHcShxHvsWgC8G4AcHs-sp+EALpKsqID6gaBzwisOhMHCdx6PUul6Ua2kkLEdxMKEGzUMsqzsLgkaeGwVQ1HUDRNC0bSdN0fQDAgwDoGwOTbDZEBZLknwMG5HgcA01S1PUjTNK0HRdD0lj9IMwCue5yXRQAsoE5z2bgIa-G4Xlpb5mUBTlfTDKMYBAA)

### 题意

实现 `CamelCase<T>` ，将 `snake_case` 类型的表示的字符串转换为 `camelCase` 的表示方式。

例如：

```ts
// 预期为 'helloWorldWithTypes'
type camelCase1 = CamelCase<"hello_world_with_types">

// 期望与前一个相同
type camelCase2 = CamelCase<"HELLO_WORLD_WITH_TYPES"> 
```

### 题解

```ts
type CamelCase<S extends string> = S extends Lowercase<S>
  ? S extends `${infer F}_${infer RF}${infer R}`
    ? `${F}${Uppercase<RF>}${CamelCase<R>}`
    : S
  : CamelCase<Lowercase<S>>
```

- `CamelCase` 是内置的工具类型，用来将字符串的首字母变为大写

## KebabCase

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00612-medium-kebabcase/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBsCMBMEC0EDSBTARgQ0wYWwGd1JklyLTMBPCAKQHsALAO0IZYjyYFcIAKAAIArZmw4BjBgBN0TANYBKCAGIAtumkBLHmtUAXdGoAOAG2yGkprYYBO2U6VIqXEAIo90hfVo5OoAAYAYgwMAELYthEAXgHIAHwQAQBmoUg4tunYsf4QiQBqWugA7hAcEADiNgASPJgAXBBM+vrGhPXAwPqEEkwAdMKEfQy2AObAcPBgIMBgc6AQAPrLK6srEACaDDy2XDLoENXotgdrZ8sQM3P61MYHGDj4ROgAPADKEOgAHoYs0oQQby2LQsUaJAC8EFIH2+v3+SQAJABvEHJY4QIIAX2RqPRAFFMQFSAB+CB4z4-dB-AEAVRYEmwxhsDi00VeePipCgpICyLpDKZ+hZbJeQXi2KRD1wBGILw5hK5EEavKR-MZzOsIrFmKQyKlT1l8qJUEabwA3HNLQtzmcIAAVLz6LjPAE2taXWZaEwjJ1IskARx4DgANGSvncJE7MRBkrYGHoAOSCG53JC9BymKmjLzAHg+UyEBPXW4HBnEAGQgDapDx4fQkblgYcL31MteCZC4UiMQT8VDCdSDCymRw0V7fZrdYbeKbphbWGlzxeA9CESi2V7-cHw6yY-iE6gtYj+kbQbnraXK6HGU3ECvw-Hwcnx9PzYvsqvixvfbvg6-kUfZ96xPGcz3nR422XTskDXW97wffcn0PKcQNncDFw-ABBMI8Dg7B0jTQDkJfUC3wXA12yQOCqMQoDpzQ992zgoiwxIhjyMghNAF4NwA4Pbg3jAIAXUtMBrTddYgh2fQmHRN5DDaJZxIuK5QFIRI3iYSIDmobZdnYUw818NhGmaVp2k6bpegGIYRnGSZgGwNhimONSIEKEpAQYAyfA4domhaNoOi6Hp+kGYYxgmBBgH0wzfNcgBZEYDm4DMsy8EyAvM4KrLC2zplmMAgA)

### 题意

将camelCase或PascalCase字符串替换为烤肉串大小写。

例如：

```ts
type FooBarBaz = KebabCase<"FooBarBaz">
const foobarbaz: FooBarBaz = "foo-bar-baz"

type DoNothing = KebabCase<"do-nothing">
const doNothing: DoNothing = "do-nothing"
```

### 题解

```ts
type KebabCase<S extends string> = 
  S extends `${infer F}${infer E}`
    ? E extends Uncapitalize<E>
      ? `${Uncapitalize<F>}${KebabCase<E>}`
      : `${Uncapitalize<F>}-${KebabCase<E>}`
    : S
```

> Uncapitalize 用于将字符串的首字母变为小写

- 因为要处理的字符没有特别的规律，所以就要对字符串进行遍历，使用条件就是 `` `${infer F}${infer E}` ``

泛型 `E` 为什么是使用 `Uncapitalize` 进行判断处理，而不是 `Capitalize` 呢？看下代码示例：

```ts
type T<S extends string> = S extends Capitalize<S> ? true : false;
// type A = true
type A = T<'-'>;
```

因为用`Capitalize`没法准确排除 `-` 、 `''` 、😎 等字符，所以通过 `Uncapitalize` 才能准确判断出当前字符串是不是一个大写字母开头的字符串。

搞清楚后，拼接字符串就根据是不是大写开头来加个 `-` 就行了。

## Diff

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00645-medium-diff/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBsAsCsEC0EAiBLAZpyyn4NwCMBPCALQE0BlALxIGcIAKAASrsYEoIBiQWjlAkt58A9kQBWAUwDGAF1y5eSiIAyMwHduCqIHbgwGvKgEjlAVHKBS40DHyoG8fQNHq-QCFugO91APAqA9HUDkBoCAGTRAAG32Q1yySAAdJCAAxEREIAF4IAG9cKABDAC4IBlkAJ3QAOwBzAG4EiCJU7IBXAFsiSQzCqABffyCQgCFEjOi4opS0zJyCoulUogiAG0lE7NxGqCbgiAAlSQYy0dkARk6MbAAecJEAGjaMgD4IYGA44tLK6oyDiCHisYnsiBmIAPmllbWAJi2WEwO2OB32ZwuVxKEHKVRqDyeIxE40m7w83k8HjOgAp1CAAcXQsgAFmUiBBAFBygFPzQDQ7oAsf6JslkgQYyQuvmkRIAdOIGJyRBlcsA4PBgLQiUgAMIAOTAIGAYAVoAgAH1VWr1WqIIADeWsgGO5QCAHiqNcblRA5QqviFtsCAPIPG3rM4xG0VQk7G0QABkEAdDwA1pISCJMCwPQAfH3rLgnQoKsBKk3GiCAaVtAKvRgApXQDR8kbE5rzegKoF+bIrgBRACOZUSowepYAHsE5O8IJgMiIKhAAOSsS1IDnV8Z5ZbAMqydCjBidi3NMIRTrxKDZRIVSSpdJZPK4RK5Ve9De5MCNS0QY7z3BLldrvqbpI7q-73A77IAExqNzhGUPYGPErnMQXMLLru679I+kgvm+MK3DUX7fjO0iJAwyydAA2rg9aNrIOwVlWow7NaewRA8xwnA8sQQE+r4ZO+dzvCcpHoQ2MhYTh1b4UCILtA84JkRR4FUTRNR0QxUAYcx2GVmxBH7A8v4iKRVzbsB165A8lGQbCtH1PRByMZhEm4exuxydxEQKeRSn3v0an8Rp0EdNpDEALpxvGIA5rmpqANBygAAcoAptYebmZryqAuBnIAYEqANVyOqAMeRgAq3vSjLMqywDslyPJ8gKQoIMAkwMAA7jUoritKYUQDS8WJUyLJsgwHLcry-KCsKwAMMio7oCI2R+FAZyAC9mgBYmuoVXJbV9UZU1xWSjK5pgEAA)

### 题意

获取两个接口类型中的差值属性。

```ts
type Foo = {
  a: string;
  b: number;
}
type Bar = {
  a: string;
  c: boolean
}

type Result1 = Diff<Foo,Bar> // { b: number, c: boolean }
type Result2 = Diff<Bar,Foo> // { b: number, c: boolean }
```

### 题解

```ts
type Diff<O, O1> = Omit<O & O1, keyof (O | O1)>;
```

> Omit<T, K>：从 T 对象类型中排除类型为 K 的键，返回新的对象类型

首先来看个例子：

```ts
// 结果：type a = "toString" | "valueOf"
type a = keyof (string | number);
```

使用联合类型运算符 `|` 将两个集合关联起来时，那么使用 `keyof` 取到的值是 `string` 和 `number` 都有的属性，这是取的并集。

所以使用 `keyof (O | O1)` 就能获取两个对象中都有的属性，那么剩下的属性就是我们想要的了，通过 `Omit` 就可以得到新的对象类型了。

> 答案参考自[解答区](https://github.com/type-challenges/type-challenges/issues/3014)，里面也有相关的解

## AnyOf

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00949-medium-anyof/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCcAs0QLQQIIDsCeB5AZpJihR+ARhhGgK4A21EAFAAIDWGaApjpcwJQQDEAW3YATAJaVBAgIYAnWdIz58-VRACKldgGcALmID2aZVACSggA7V2wtLogAFDLoAWRiNTHNREAAbTMXwguNABjfXcxNAhXdhiMCzjtDD0bADpUeMSY6W9tGJc4lHlFCACRCFl2XUpZNHzfXVktILEcMswIdmtbewN22NQS8jF8pq0M0wHCoYUR-JsLXQwAGkrq2ujfHGlqbXZfNJMIADEDWS6AD2lLawAuY98n3W18ZeyAZRurdgBGCAAvKhMLgADwAbV+awARNC1js9uw1uCALprADeAF8UQA+ADcEGAwCuiXCPl0BggJDi43YRyg7ziX1u7AATIDgdgcBCAAwwuHBXb7ZFoiBY3EEokk9hkioUqlxBH7el+J7HHEQABqYnYAHcIO4AOJiXQACUoJDuEBcul0Fm0dyJL1CLjSACttGlzgBzYBwaBgEDAMAh0AQAD6kaj0ajEAAmgZahAAMIGERxU3sKoRmO58MQIMhxmcsEAFSuunYaBE+Sq0hERmo5ACGFRGqBpfBVEE1NkKIrVZrDAA5MOIAAfCA8ieCxEz1Ez9EQcHeDBWvSyKLelFWjgANyzEExfAA-LP9hArbS8SGwGG87mIKWdPZk9J9vkHzGC8GxJZzvYS4AKIAI6ULsaxAZcpL2JiwSyAYUjDowjKIC6uzWGg3o6MAlD6Hsw5FgkcShO+OgcuC+BQTBoKgeB1CgugXIQlCEDDpWejDmstLIr8opLmgNzsFa7EvmOmIYhAvwiRxujibi3HNOwOI4isVHQTKui0WBuyMSC3KQmso7wkKSLLvx2KqTESkqWpUDUZp2n0XpzHgnybGyVx55maiGKWYpWi2epNF0bpTFgm5RleTx5l+Qp1mBapwWOaFDHhQZ7nGd5vEWfFtJBfZGnhE5YX6byUUmYiIqSYJwgyWJR55TZSWFSFOlpWVkVsV5So+fxUn1ZxjVWflLUQA5xWpS5EWZT1pnVWKFBCYNclHpJ0keQ1-kJcpY0TVpU3peV3WVcKsVittvUFeNRUHe100GfFV1JSit53iAOZfpGpy1LEFwfJW9qfV9P7vfgGofC4chxBgiYXNoBjUHhhj1FaNp2g6TraC67qej6frwMAATaLqWbg1qOr6gjSMRKj1q2vajrAM6roel6si+v6wDU8jRivFAGoALLnHEyZQ7QVbYQ69MY0zLO4+z3qBsGYBAA)

### 题意

在类型系统中实现类似于 Python 中 `any` 函数。类型接收一个数组，如果数组中任一个元素为真，则返回 `true`，否则返回 `false`。如果数组为空，返回 `false`。

例如：

```ts
type Sample1 = AnyOf<[1, '', false, [], {}]> // expected to be true.
type Sample2 = AnyOf<[0, '', false, [], {}]> // expected to be false.
```

### 题解

```ts
type AnyOf1<T extends readonly any[]> = 
  T[number] extends ('' | 0 | false | [] | { [key: string]: never }) 
    ? false 
    : true
```

通过[索引访问类型](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)可以获取数组中所有元素的联合类型，例如：

```ts
type a<T extends readonly any[]> = T[number]
// type b = 3 | 1 | 2
type b = a<[1, 2, 3]>
```

为了识别出一个空的字面量类型，所以通过声明一个对象类型，并且在里面将值的类型设置为 `never` ，也就是：

```ts
{ 
  [key: string]: never
}

// 也可以使用内置类型来声明
Record<string, never>
```

## IsNever

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/01042-medium-isnever/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAMAsAmCBaCBJAzgOQKYDdcAnSVFci0gIwE8IALASyIHsaBDCRxgLwFcA1pwAUAASasO3fkICUEAMQBbXABNGfJYr4A7Rix3aALowA2mUqQXWIART65MJg5ajolAB1O4VOoxE4jGg9cDBwCYgAaCAB3JgBjeggjdgFHLh0PPn8gkIgAAwAVfIA6UnQAM2T6UNzQliqiRxZTQkxklgKdCKJ86KajPiJDfKMiBz6IFiMaohjGTFD8ivZzXFLXCAAxFiIIXAAPdk9vAC5N-MujCyg6iABBCABeMLxCIgAebveAPihgYD7A4heJGNQdCBUWrjXCkO4AIWerx6H10qlwFUY3VUfwBQJBYNUEKhEBWazhwVCAGEkVg3sQvnxTKZcYDDgTwUZOiSyYsKXkACK08LvD4AbQAuqz8bhQZzuaFebDbpSIABRYX0z46TRQojS9mywnExWrPlQS75TZ-ABqjFwMSmhgA4owjAAJPhUU4MIxGDyYU4A66JEoAK0wJV2AHNgHAkGAQMAwCnQBAAPqZrPZrMQACaLCGECpLHREHdxFCOermYgSZTdzpKMKfxeYsKEqBYJ0qnaYu+xE7AH5kjCID6lQBuFNgNM16sQQqOfxU9iLdrznN15OMTy7fwAb3VAEc+KtomrgUaIABfUmsLQAclEdRQiVW3h00ccwGyZkwj4Nqq8RrukbakJeBIfGqp6rB8TaigO+rRGMDg-D8kQQVeoLQbBpjwSKDJIRAAA+EBOEQWLRhhpJmrg6GYVAkFGrhZ74QhDIAEScTRSoMVhUEwWxBFaqiPYYliai8XR-FMdhRisXBHHakyLLRHxGECSxQlKYRnyStJayyeq8mKexekfAeN6GYs-ESjOs4gBmm65lsQwzMQEAAMpggGzkuem26OaQfxefQ7BNBANCFnsmAtH+BiBr6-qBsGmChhGUZELG8aIMA7A6JgMTECFEB2g65Hxc4hU+vQfoBkGwAhvQ4aRjGcYIHlcWmAlhWlQAsrs1LhcyuBfo4tX1alTXpS1mUxomyZgEAA)

### 题意

实现一个类型 `IsNever`，它接受输入类型 `T` 。如果的类型解析为 `never` ，则返回 `true` ，否则为 `false` 。

例如：

```ts
type A = IsNever<never> // expected to be true
type B = IsNever<undefined> // expected to be false
type C = IsNever<null> // expected to be false
type D = IsNever<[]> // expected to be false
type E = IsNever<number> // expected to be false
```

### 题解

```ts
type IsNever<T> = [T] extends [never] ? true : false;
```

使用泛型 + 条件判断时，如果传入的值是联合类型，会出现[分布](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types)，例如：

```ts
type IsNever<T> = T extends never ? true : false;
type a = IsNever<never | string>;

// 相当于
type a = never extends never ? true : false | string  extends never ? true : false
```

上面发生分布的时确定的值是 `never | string` ，`never` 是永远不存在的值，它会丢失，为了不让 `IsNever` 发生分布的行为，就需要在 `extends` 左右两侧的类型上加上 `[]` ，让它变为一个元组类型，或者直接变成一个数组类型也可以（`T[]`）。

## IsUnion

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/01097-medium-isunion/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAMCcB2CBaCBJAzgVQHYEsB7XSVFci0gIwE8JcBXAGyYgAoABKgU1wGNCAJwCUEAMQBbbgBN8DCRFKkxKiAEUG3TABciJKKXQSADk25Tc2iAEMI2mse4QABljx7nAGggB3ABb4fH521gDWWja4EPi4xgxW9o4uACrOkdIQgtzaDIK4mL5+2UWCKWlZmIRMAG4R2oQ2EAwExHYO3AB0SlAAYkIQ3AAe1iZmAFykBlBQzrPamJNtSXzWmNzQEAC8GDgtuAA8OoIxAOYAfFDAwBAAZtZMa4uJTitrAExbO+7Eh9rHuCcAD6MCQ8QQXCBXOyCTRPdoQV7cADMnzce32AG0jqdgfIwQBdCFQu4PbiLWbOboQC4ANXw3B8EFaAHF8NoABIMKhjCB+bTaYyYMZXeZBDoAK0wHSEJ2AcCQYBAwDAKtAEAA+pqtdqtRAAJqEXIQADChGkTnZ3CyGp1tvVECVKueX3RyW8ACFPskLttkgNBtpeNICp6APwQDHu-H+wO4YMR5LR8MktYQHl-TRp+jcWqCADcKsLarttogyS0VmNqwiJZ1DuV+BMQisAG8IABRACODHu3nbg0cfCsAF9boJCAoAOQcZ4oIL3MwArTAeL4B6Tp3wxEFbYY0j9wfafZdntMfZovS-f7nbwp7hnM6efcD7hD4-d+7n3aX7EAiCA+g8StR9oU0B8nygA9XyPE9PwvH5J2sSd-wgScqGQgDJz4DDUOkScQIze9H2fQ931PL9vgOZpzRuGIZBQxgWBQ6pCHwDJMPw7xCPAkjoLIuDvx+NtrB5X8TggUcAOEnkQTBCSCJhIiII7F831gs94IOaSIDEhigNKYcQLvHjINUmCPw0wSDixP5Tj00ErUJW97jWEzIWuKs1gKfwrScP0KiqWoCnqRpcGIFBmj0JZOl4tSLIo9FdIA3Ac2A5zSTcqC4vIzSrzsgDmlCMKfFwIyXKU2LzJyqy8r-ADrFwGgyoy4jTNI9SEp-Wy6tQpDmtc4j8ULMBi1rXUelybQSggABlQNBRtMb7UdUBSAuGa-Gsa0aENUpKiYVdiCFXl+UFYVgFFPwJSlGU5QQRBgAazAfCtNaIDpBkdKqQ78h5PkBSFEVMDFSVpUEWV5Qe-afoWKALgAWSEJxjU2lheBOLQ-tOwGLuBq7QZlRVlTAIA)

### 题意

实现一个类型 `IsUnion` ，它接受一个输入类型 `T` 并返回 `T` 是否解析为联合类型。

例如：

```ts
type case1 = IsUnion<string> // false
type case2 = IsUnion<string | number> // true
type case3 = IsUnion<[string | number]> // false
```

### 题解

```ts
type IsUnion1<T, B = T> = T extends B
  ? [B] extends [T]
    ? false
    : true
  : never
```

答案参考自[解答区](https://github.com/type-challenges/type-challenges/issues/1227)，👍比较多的一个，也比较简洁。

刚开始看到这个答案还是有点懵的，然后把每个`case`都分析了一遍就明白了。里面的知识主要涉及到[条件类型](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types)。

我们通过挑战中的`case`来看看：

**第一种**：`T` 不是联合类型的。

```ts
// case 如下，期望 false
Expect<Equal<IsUnion<string>, false>>

// 1.代入到类型中
type IsUnion<string, B = string> = 
  string extends string 
    ? [string] extends [string] 
      ? false 
      : true
    : never;

// 2.string extends string 为 true，进入第二个表达式：
[string] extends [string] ? false : true

// 3.第二步的表达式成立，所以最后返回结果为：true
IsUnion<string> == true
```

**第二种：** `T` 为一般的联合类型：

```ts
// case 如下，期望 true
Expect<Equal<IsUnion<'a' | 'b'>, true>>

// 1.由于 T 为联合类型，所以会发生分布的行为
'a' extends 'a' | 'b' 
  ? ['a' | 'b'] extends ['a'] 
    ? false 
    : true 
  : never
|
'b' extends 'a' | 'b' 
  ? ['a' | 'b'] extends ['b'] 
    ? false 
    : true 
  : never; 

// 2.执行完第 1 步后的结果为：
true | true
true // 最终就是 true
```

**第三种：** `T` 为特殊的联合类型：

```ts
// case 如下，期望 false
Expect<Equal<IsUnion<string | never>, false>>

// never 是永远不存在的值，在联合类型中，它不是一个有效的值
// 例如下面 T  的类型就是 stirng
type T = string | never;

// 所以上面的 case 可以看做：
Expect<Equal<IsUnion<string | never>, false>> == Expect<Equal<IsUnion<string>, false>>
```

## ReplaceKeys

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/01130-medium-replacekeys/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCMsGYAMUC0UBKBTADgGwEMBjLAaSwE8BnSGVeh1WqAIwqkEZYjqACgAE8AVzyE2AK0EA7LAEooAYgC2WACYBLQYqjNm8vVACKgrFQAuagPaSd0KAElF+LMsmmoBKKYo4smXIRJyagAaTwALAjcAJ39iXwBrSiooNUkoKUs0rx8qULUAMygqC2VPb18I5MkLN1MwtWTEilCJMyL4tRwoGPxiVIBzYOYAQTKfTwJE5LqY3wIo-s0sVyoAOm1bGxgAMQsoqCwADwJHPCwALi2oAANb0xpbbN8AOQsVLFGAXigAb2YYJ7nKAAcmGwP+UEkJwuRVMUQGEPyhH6QMkmhYWCizAAvlcnlBXu8AEJQb5-WwA8pA4FE8EUlIqVHozGI5FMxQYrG2XGbR7lAlvLAAYVJvwhgJBQrpMBgUOUQLM8Mk-VZBBRkOZXJgPJgeP5hJMooNowAPgLiVAzQahXrxtheiQVAbkt97QEyEkADzO0LAuVYYGWkFItXA0I-f3szmhEPqxUDbEAPigwGAvwloLDkOhUcxMbZsKV-WxQZ+GdpeUZGo5eagsYVcITpYzUtCker0brBfjypLqagAHcLMIVN1YiRs6V8lESoWBp4LB3az13V21XWZ1o0TX9qZFz3+qtbb43XEnTUAKKHBqmIIuvwOj3Ub2C3Ig-1Zn4EAi5qJJlNpmWVIgmCbY5pCWAAG61vWS5-kGBokmaQE+NSraTjC0jQVE+Zqr+fZpkOI5jo+GELhB2HMLc1xXMmABqahYAOUBWFAADiaimAAEoILBAmEpimDgVDnKm9xEGEqxiGsez9MA8DIJAIDAJAqkQKAUAAPraTpuk6VAACaw77EKgpQFxmK+Hp1naVAylqfip6BF6ACqoQACoHIcpjLCoyQHqEBnJt8kAuV5PmSH5UBhZAAD8Yr0gA2gACikaRNBYhQuQAukCqVHBFUWefFPD5d5vmNJQmWGVA8UGSl2VQKiUGYnIQIuQ1OIbO1ADcamQBpNnWVA7kmG4QoEFQhpDXpdkqRAaiOHstT8j8UAXgAjoIBB4KEV4+EQbgltOs7AnwTyoBJO1nMqJjAII5h4FQdKQPixqiuSlKoSB0oYQ2RbMLB26cpAPKvfqgokmSzDlr9ahVsDLK2EDmqgw5EPvCK0N8t9wJSsw7YHoDBaI1yYMQI546qO92NfTCmYE+BpPE3hc7KmjEDg3aVPnhatNjPTtLMPDv4s3GjbsxA5OU4+vPCh9MPAXjv3tszyPdhLKpS+j4yvM80I0wldPUmCYui7YqstWTOsvBY+vKNaCs4-T+Pq6zauykzVsc1ztvvPe71WpDCGCjaFP8k51OvqKkdy6aD7unLSEJ2e1q++aJh69CRp2wbgrx1nDuhyHxI21ARCTYa3yJcw+1YIdnqbdteCepHd4vv7vofkGwKxp+f1wT1a7i0WUBJqEsfOomiZDLYdcN03O2t1T7c+u+0L99+v5jzPGdUIXWDT7P2X9Zz6kgFpM36dsghRHUmJQAAyj5wmX1fmlzQN4C2Mmj8RDEUAKDGSKBYIQ5grAiSgAJISIkxJUAklJGSCx5KICQMAAgkgqADiRjAeijFmLFDAZkSB0DhKiWAOJSS0lViyRQcgYAhCHrEOYMmAAsnsXwQoIgiGWP0Ew-FBJkLgQg6hsklLzUgEAA)

### 题意

实现一个类型 `ReplaceKeys` ，用于替换联合类型中的键，如果某个类型没有这个键，只需跳过替换，一个类型需要三个参数。

例如：

```ts
type NodeA = {
  type: "A"
  name: string
  flag: number
}

type NodeB = {
  type: "B"
  id: number
  flag: number
}

type NodeC = {
  type: "C"
  name: string
  flag: number
}

type Nodes = NodeA | NodeB | NodeC

// {type: 'A', name: number, flag: string} | {type: 'B', id: number, flag: string} | {type: 'C', name: number, flag: string}
// would replace name from string to number, replace flag from number to string.
type ReplacedNodes = ReplaceKeys<
  Nodes,
  "name" | "flag",
  { name: number; flag: string }
>  

// {type: 'A', name: never, flag: number} | NodeB | {type: 'C', name: never, flag: number} // would replace name to never
type ReplacedNotExistKeys = ReplaceKeys<Nodes, "name", { aa: number }> 
```

### 题解

```ts
type ReplaceKeys<U, T extends string, Y> = 
  U extends U 
    ? {
        [P in keyof U]:
          P extends T
            ? (
                P extends keyof Y
                  ? Y[P]
                  : never
              )
            : U[P]
      } 
    : U
```

- `U` 将是一个联合类型，我们要对其中的每一个对象类型都进行处理，所以要让其发生分布行为，用 `U extends U` 就可以实现

- 具体要处理的是对象中键对应的值类型，所以要遍历每个对象

```ts
/*
  P extends T：用于确认是否对要当前键进行处理
  (P extends keyof Y ? Y[P] : never)：因为不能直接使用 Y[P]，所以要进行类型推断
*/
{
  [P in keyof U]: P extends T
    ? (
      P extends keyof Y
        ? Y[P]
        : never
    )
    : U[P]
}
```

## Remove Index Signature

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/01367-medium-remove-index-signature/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCMDMA2A7FAtFASgUwLYHsA3bKASQDsATbADygGUBLAc3IEMAXAVwCdtIYaIcLQCoAIwCeUABaMe+SWyiNGALy4BrZQAoAAnIVLVG7QEooAYlzZKjLrihixl11ACKXbAGcOjfOTO0GS4AA4ANnjY5BxQAAY4BMQU1DRMrJy82AA8ACoAfHFQADRQtADG4VzUUBwyJIxUtFDeLOzcfFAAZgqO+OIAVtjlsRySoT4AdEEwAGL4PGU0bGGRAFwz8XFxm2MTUPP4UAC8UADeYjAA2prYkmstHDyNzAC6D2zkkgDcl934+B0ZgehHwjEov2CAF9duMSABBE5YPBEbApWjpdpZbKHfIwYDAJYTEa2c7-QHAqCg8FQGHBMTbHb04J4gBqjGwAHcoAEoABxRgcAASXHEDxkHA4oW8awJHG85RkkwG3kmC2YwAQKEgIGAkH1EFAUAA+qazeazVAAJr4XhQADC+BqQuwnQt7tNUF1Br2JESqPRaTamT4eTxpwuwSuAAUVOQoLdJPgulBclA2N4oDpY7QONFKJm4gASM6NLquqAAaShRQA-FWoA9yNhiDwzO9UzHXpA6QbIEaPe7Uz5YvaMz4TYPzV69RBGGEFqM4WSAKIARy4bHCpRXNGJsSh3V6UAA5HpfWhFVvIuRmD5gFw-OFvCefcvDkjI9dEw9fM9bx2nySGIXQAkCIJgpQPZvvsABCbCLBGYg3HcTYOOIrqAV8YjiAh4FUpB0EQJAvpQPBaifjheGUtSUHBLhai-k8LxESRy7lOOmanFcYi7vu2Trpu4TZP6yRNEGGQdDkuKlGc5L4bRtL5PkxS8XuwwcAJG5biJKJiakmIhjk8E8CpZK4TwCmQUpKlqfxgk6aJaLiYZUnZORZlyRZVngt8EhsIxjz-swNmqRA3bEZF-YgJOU6erMvB1BW9B5tKsVxTO0ViHi9AyAhJBJna3j4FUfgBDKsiStKsrAPKirKqq6qakgyDAJ83icq62VQOyXItCVj7+OQFUSlKMpygqSoqmqPAalqrXFaVQ3eN1ACyCwkPaeXhDed4jVV421ZNDUzcwOqzpAQA)

### 题意

实现 `RemoveIndexSignature＜T＞`，从对象类型中排除索引签名。

例如：

```ts
type Foo = {
  [key: string]: any
  foo(): void
}

// expected { foo(): void }
type A = RemoveIndexSignature<Foo> 
```

### 题解

```ts
type RemoveIndexSignature<T> = {
  [P in keyof T as (P extends `${infer K}` ? K : never)]: T[P];
}
```

- 键是可通过 `as` 进行[重新映射](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#key-remapping-via-as)的，所以这时可以使用模板字符串进行扩展或条件判断
- 而通过 `${infer K}` 可以推断出键的字面量类型，所以能够排除掉索引类型，获取到普通的键

答案参考自[解答区](https://github.com/type-challenges/type-challenges/issues/3542)，大佬也有详细的解释，并对 `case` 进行了扩展。

## Percentage Parser

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/01978-medium-percentage-parser/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCMCcB2AHFAtFACgUwE4GNsA7AFwEMBzbLM3AZz0hjRdbSagCMBPKAZQEALAK5kAlgnjwoACgACAviPGT4ASigBiALbYAJmOHaoHDpvNQAisOx0SYgPZFT0KAEltABwA22XaSw8QlJKbExaBlwAHgAVKGwADxJiPTooO1wxIgoAPgA6DgBBfHwHXANsqBIHKsFqAANgAD0ZAB0AagAfVrQ1AH42vRB+toBSfoASYHqoXGwKYW9aMRJeaqhtMhJ8QSg4siI9KCoSWrnqTe26ugLXDhi69JJcYXwSYTn0wQdFo85sABcUAA2vUfMI0mUNlkIfUADRQepEIz-XDwxHCIgreoAXQ4bgAZlAVsS0kQHKd8GRPO85noESRHnpsASyItTmI0gd4l5Vk9MtlbjAOAAxKGJMheXwAjj1OUkOgcVaeaiYPjPLIUWBQAC8UAA5PqldwVVh1QKKAAmXUG9rIACsoyNrmVqvNmoAzDb9WgHU7jaa1RrsgAWb1+50wV1m4MUe3h+2Rkwuk3UABK2r1OAIxHIVHC9DwUSDFtgORgwGA8QSKre+hBhoRjYN+rxKdNaetWaCudCBcixfd2Ut5aglerteSR2BACJ2jOETOHQuoDPRjO21HU1A017uzmQvmIkWS57R+PEpP67O0Cul-a7+vN1Vt2mw-vgnmwsfoqfQ+eq0vbA62nGc72XRcnwDdN4w-Xsj0LX8hzjACJ2AqcQTAxcINXDdZTlFwYHLAA1MRsAAdygJwoAAcRWAAJYROCBQQSBITw6ABSsFR2PIACsbjKChgAQFBIBAYBICkiBQCgAB9BTFKUxSoAATR+XAoAAYQcZkoHovBqGU4yFKgCTpOjLS6nwABrTA5gJMQEliatkkONIMk1cs9TiRI3NSW19SgToDTQIK+j2KAgSIbAADc8AAbkgSzrJsvhhAJRznN8pIUg82NvMivy8sRCYAG8sgJPAdwAX1GGYIuBNMmydHEopBGIm1bJKIGjbNPz7H8okKVySs87JCpG4r3NKsqrOAuyHKcqJKuqkUchq8rVs0gBRGqGpBEUETyE75ts9LMuWnacjaoFgWbE68jOtKMqy4abp6yBZJM4y9lsU4tLIBg0h+5SzMkiAxC8MpTmjMqoB2gBHURvARHaa3QqAaqgAlcAcYx9TkV00B2MhvF8bJbGAYR7G8OhnWS7dAYYTMG31LqurbSygewLsG3admW05xnTWZ7A935wX9VgKXWxF6gxffSWm1gAAGVWpda+XtJ52C2ZVjWWrl3qmZ5gA2G17rCpspE143tapYHLY4dHJyiJGUeLHtD2-RCokNHIETFstA5djG3nd5Gya9g8v37It9QFwOdYYEdQ9cV30Mjz3+vg32B0TmXk7Fj0cnTmBM4jj3o9zn34+iQv1adYueZDMu4TDt3q+8GOBoQgu1eboPdfbzus+73u8-r-20CkIeU+wM3R4gNtpK+kB5NBlSRQ+RlqvVbAOM3re5PB9eOHLJRaGobgNPSBxvBpxwiE4qBWPYzjuLoXiBLyISRKQMgYABw6DkUYK4EiZFKJ0Afk-Jwr934cS4sAHigh+KCVwMJUSQCYGP3sPAi+UAACyZQFaCDJhTKgCC2JIK-j-DBFBxIQ0gEAA)

### 题意

实现类型 PercentageParser。根据规则 `/^(\+|\-)?(\d*)?(\%)?$/` 匹配类型 T。

匹配的结果由三部分组成，分别是：[`正负号`,  `数字`,  `单位`]，如果没有匹配，则默认是空字符串。

例如：

```ts
type PString1 = ''
type PString2 = '+85%'
type PString3 = '-85%'
type PString4 = '85%'
type PString5 = '85'

type R1 = PercentageParser<PString1> // expected ['', '', '']
type R2 = PercentageParser<PString2> // expected ["+", "85", "%"]
type R3 = PercentageParser<PString3> // expected ["-", "85", "%"]
type R4 = PercentageParser<PString4> // expected ["", "85", "%"]
type R5 = PercentageParser<PString5> // expected ["", "85", ""]
```

### 题解

```ts
// 检查字符串前缀
type CheckPrefix<T extends string> = T extends '+' | '-' ? T : never;

// 检查字符串中的数值与 %
type CheckSuffix<T extends string> = T extends `${infer R}%` ? [R, '%'] : [T, ''];

type PercentageParser<A extends string> = A extends `${CheckPrefix<infer F>}${infer E}`
  ? [F, ...CheckSuffix<E>]
  : ['', ...CheckSuffix<A>]
```

- 首先使用 `CheckPrefix<infer F>` 将字符串的第一个字符进行处理，从而判断出是不是指定的前缀
- 之后就是根据不同的判断结果组装数组，`CheckSuffix` 专门用来处理字符串中的数值与百分比符号

## Drop Char

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02070-medium-drop-char/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMAMDssIFoIBEBOB7ADhAwgBYCGGkKylV5ARgJ4HE4AuxAlgHYDyAZgAqEAQhAAUAAXxNWnXgMEBKCAGIAtgFMAJmwCuK5czUqcAG2IHkxtgYzFjyzjzVko5JW4gBFbWoDOzNlgc5OSYuBDEED44agDGbDxsmhAxJBgQPNh6EX4YnADmAHTBUABiWGlqAB7ERsZqAFzFEAAGrcw+5Mx00RCC2szWPMYMALzo2DhEpAA8AOQQNBDaEMwrEGoQaTwQdgwAhBCzADSHhwB8EMDAhzT9g8N7s+StzU0XAGqJAO4QgRAA4lYABLaGj1CCEAY4Hz1K7tFIFABWPgK5TywDgiDAIGAYDxoAgAH1iSTSSSIABNLDaNL4LAaDZApwbMms4kQHF4ro9UKTVLTADKJ3wFzG5AF60qBg4Gh8LQAJABvBxOCAlAC+SpVaQAourmhByAB+BWKkqS6WyggQE2zebgtWaxW8qYYaY64VnfWGqDggUAbjxYAJbNZEAAKr5VpIfL4iaHSRzcWwjOVVtyNoqIDqAI7aWwnHWVaIxVbq9KZQ5iDPIFK2OocPK+YD9NjGHxPMAZ5LEWNysYAbXIVwg1Z8yCqJeYE4w2Gc2eLsWY7rztmmLv5s1uA1VQzojxOdrOh+3933szOx-IRanK-zxnXE1dc1Pu4ex1OF5Pdyce8el6Oa9F1LO81w3GYtx-LZ30Pf9vx3aC6AvK8oBvJdQIfcC3XmKAFig9IHlwojDkPc54LPf8UIXW9c3vR9cGfeZFmWdN1k2dIdggfYSM-Y8bigv9kMA1DgOXWiwKfTcFiWNZVg2LZOO4j8ty-U4ZNY+SON2CADiEoCaNXTDJIg6SWLWTTtm03TD2YVSmJkqALMUnTzivABdINgxAeME3ZEoaWYQhVQFAxoR83yky88gLgFVINjoak0h8LBjFbQIYQhKEYThHwEWRVEMHRTFYGAYgOB8L4nGiiBPjUH5ktS-x0vBSFmGhWFgHhQgkRRNEMQQEqGrS8rqoAWXKDYpmMBsmwy1r2pyvLesK7FcTAIA)

### 题意

从字符串中剔除指定字符。

例如：

```ts
type Butterfly = DropChar<' b u t t e r f l y ! ', ' '> // 'butterfly!'
```

### 题解

```ts
type DropChar<S, C> = S extends `${infer F}${infer E}` 
  ? `${F extends C ? '' : F}${DropChar<E, C>}` 
  : S
```

- 遍历字符串 `S`，然后对第一个字符 `F` 进行处理，字符串 `E` 则使用 `DropChar` 进行处理

## MinusOne

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02257-medium-minusone/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBM0KwHYIFoIFkCWA7ArgZwHksBTSFZCysgIwE918AXAQwDMB7CAMWYwC8IACgACrZrT6SAtgEoIAYinEAJhhxSFU5owAWZMvMMQAijmJ5GGdln1QA4hgBuxLBGYRcU6sQBOQ5gA2AO7ieBAADux4GJbOcsxh7oy04cQAdBAAmuw4fsmpEHg6OQHKED7EjLmuusQe6t5+ysQAxhUJKhB0ENbpttzsfsQAHsxS4QHEAFz9AAbzjHhk+XUAWr6cALzo2PhExAA8AIwAfBDAwBAADMspdVwYrMlcOX7bmLiEJAdwcGcXEDgABYyPNZv0zgA1DDEII9VwORgACRw1CmEB0jEY4TwUwuixaOjSACs8GlBgBzYCwRBgEDAMCM0AQAD6bPZHPZWVeEAAwuxmhAkb46pyxWyIPSmZdAA6mgB4FQDkmoB4HUAcHKAYUVACN+gH9UwBleoAPt0Akt6AELdAARmgBAdQCf2oBDGLAKwgABEMBSYgAVdgAQR8PnEEG2AG8yAAiK6B9EAbQAugAaINHUMQMM4LAAayw7CCWGjQeg8cTKbTGajECTqfTmZjUEDAGZcyWC1gi3Wy4382Ws5WgbXW4Xi92G73Sz2mxn2xBA3Au4P+8Pp32W1P5-XR4GAGyT+uL5sDjfbrczzcjitjhDrvdz3dD8-7i+zqfLgAcp8vC5vB9vO+vn77y4AnE-32eL5fkBV6gSBd5gAAvgA3NKEAAHIQIA9GaAABygBUcoANvGAEbGgBccoA6tqAGTegBMcka8qADD-gCcFoAdsaAMHagDYSoAX3robqOEIRARqaradx8u0jDEJ63q0AAQrQAAyLgUroByISMfFYMoYQWD42AUkWABKECyS4Ck3pGvoJhGZzbDJwxyTpswACR+tgrC+NwGA+BYkFWTZdlqeYjCQeCAD89mOYwmmmdpYTJsQtDsKwDpOq6HpeuIZC+byvH8XFwliRJUnuRYRZhmkeVqUWeVpAVEBFSVZWFfllXFdV5VVaV9UVQ1aSOs6jBugJ4hhg8-kRoZZDoiQzg+GA6JqbBcGANxygDePoA0epQBAXEFB8ezfC6gVmWEniNEZPHENoKWCSJ4lYJJOgHJZfoul5ZxafJYRhq5fg9dlzVPRAWWMBGYC+Z9YaBpMp26IG32DcQw0TWAzLimKEAuh5fIdGEMOcpKDIYOMgwBXafoQAAogAjjggRFnjwypC0AWQRArA+OwGgAOTCCsyCEoEgMUuYwA4JYAR4AzjJ2i0SP6WGZBkxTjAHITxMBAcK1fIcpxFlcJwnEeEutFLMuBPLuyKz8fxFsCasa+TWvS0TusK-sBxVurMCm+L5uU5bst658ttHFcqtFj+P5O1Amuuzrcs298Rze8rECRz7TvfYyUMgKyKNclwuS1H4ADKfE4inqcsmjSdkGcWc6MwFQQOFuSFOwAQ81YWC4hiWI4niwAEkSpLkj4VI0ggwDME3QS+CXEDQrCtf15Y1jN5i2K4vieCEiSZKUtS8AD3gdcN7PY9oIMdS8uXAQc+Y6Lz23S8r93lJ0gyYBAA)

### 题意

给定一个正整数作为类型的参数，要求返回的类型是该数字减 1。

例如:

```ts
type Zero = MinusOne<1> // 0
type FiftyFour = MinusOne<55> // 54
```

### 题解

```ts
// 数值大小与元组长度相等的映射对象
type DigitToArray = {
  "0": [],
  "1": [unknown],
  "2": [unknown, unknown],
  "3": [unknown, unknown, unknown],
  "4": [unknown, unknown, unknown, unknown],
  "5": [unknown, unknown, unknown, unknown, unknown],
  "6": [unknown, unknown, unknown, unknown, unknown, unknown],
  "7": [unknown, unknown, unknown, unknown, unknown, unknown, unknown],
  "8": [unknown, unknown, unknown, unknown, unknown, unknown, unknown, unknown],
  "9": [unknown, unknown, unknown, unknown, unknown, unknown, unknown, unknown, unknown]
};

// N 是一个转换为字符串的数值，根据它创建一个长度为 N 的元组
type CreateArrayByLength<N extends string, R extends unknown[] = []> = N extends `${infer First}${infer Rest}`
  ? First extends keyof DigitToArray
    ? CreateArrayByLength<Rest, [...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...DigitToArray[First]]>
    : never
  : R;


// 主类型   
type MinusOne<T extends number> = CreateArrayByLength<`${T}`> extends [infer First, ...infer Rest]
  ? Rest["length"]
  : never;
```

我们根据实际的 `case` 进行剖析理解：

**1. `Expect<Equal<MinusOne<1>, 0>>`**

```ts
// 1. T = 1，代入主类型得到：
type MinusOne = CreateArrayByLength<`${1}`> extends [infer First, ...infer Rest]
    ? Rest["length"]
    : never
```

接着看 `CreateArrayByLength` 类型：

```ts
// 1.1 代入类型中，此时 N = 1，R = []，所以得到：
type CreateArrayByLength = '1' extends `${infer First}${infer Rest}`
? First extends keyof DigitToArray
  ? CreateArrayByLength<Rest, [...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...DigitToArray[First]]>
  : never
: R;

// 1.2 '1' extends `${infer First}${infer Rest}` 与 First extends keyof DigitToArray 都是成立的，所以有：
CreateArrayByLength<'', [...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...DigitToArray['1']]>
// 相当于
CreateArrayByLength<'', [unknown]>;

// 1.3 此时 N = ''，R = [unknown]，所以得到：
type CreateArrayByLength = '' extends `${infer First}${infer Rest}`
? First extends keyof DigitToArray
  ? CreateArrayByLength<Rest, [...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...DigitToArray[First]]>
  : never
: R;
// 空字符串的判断条件不成立，最后直接返回 R，也就是 [unknown]
[unknown]         
```

根据 1.3 得到的结果，我们代入到主类型中：

```ts
type MinusOne = [unknown] extends [infer First, ...infer Rest]
    ? Rest["length"]
    : never

// 判断条件成立，则有：
type MinusOne = []["length"]
```

元组类型是允许推断出长度的，所以可以通过 `length` 属性获取到长度值，最后结果就是 **0。**

**2. `Expect<Equal<MinusOne<55>, 54>>`**

我这里就直接跳过第一步了，进入到 1.1 步骤：

```ts
// 1.1 代入类型中，此时 N = 55，R = []，所以得到：
type CreateArrayByLength = '55' extends `${infer First}${infer Rest}`
? First extends keyof DigitToArray
  ? CreateArrayByLength<Rest, [...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...DigitToArray[First]]>
  : never
: R;

// 1.2 两个判断条件都是成立的，所以有：
CreateArrayByLength<'5', [...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...DigitToArray['5']]>
// 相当于, 第二个参数的数组长度此时为 5
CreateArrayByLength<'5', [unknown, unknown, unknown, unknown, unknown]>;

// 1.3 根据1.2得到的结果，再次调用CreateArrayByLength
// 此时 N = '5'，R = [ 这里面是 5 个unknown]，并且两个条件判断都会成立，所以得到：
type CreateArrayByLength = '5' extends `${infer First}${infer Rest}`
? '5' extends keyof DigitToArray
  ? CreateArrayByLength<'', [...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...R, ...DigitToArray['5']]>
  : never
: R;
// 相当于
type CreateArrayByLength = CreateArrayByLength<'', [<这里有 50 个 unknown>, <这里是 5 个 unknown>]>

// 1.4 空字符串的判断条件不成立，最后直接返回 R，也就是：
[<这里有 50 个 unknown>, <这里是 5 个 unknown>]    
```

根据 1.4 得到的结果，我们代入到主类型中：

```ts
type MinusOne = [<这里有 50 个 unknown>, <这里是 5 个 unknown>] extends [infer First, ...infer Rest]
    ? Rest["length"]
    : never

// 判断条件成立，则有：
type MinusOne = [<这里有 54 个 unknown>]["length"]
```

最终结果就是 **54。**

**总结：**

- 第一步就是先将数值转为字符串，然后遍历每一个字符串，得到长度等于该数值的一个元组
  - 每一次将调用 `CreateArrayByLength` 的时候，都会将当前的 `R` 的数量 * 10 再加上当前 `N` 值获取的元组数量，形成一个累加，再进行传递
- 第二部就是通过条件判断 + `rest` 参数，从而达到元组长度 - 1 的效果
- 最后根据通过新元组就能获取到对应的长度了

> 参考自[解答区](https://github.com/type-challenges/type-challenges/issues/5547)，有些答案非常复杂，这个比较好理解。

## PickByType

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02595-medium-pickbytype/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMCsCcsIFoIAUCWBjA1gIQE8AVAgBwFNIVkbaqAjAiAKwwEMA7AcwGcALThAAUAAVadeAjgFtyAFzYBKCAGJZAEwwBXaaoD29ZuSxyqVFRYgBFLeR5yMejmagAxAE57dAAyLeANBCk2DgQbBA88hB6AGZBnhTuDnYQAO58epEQcmTkYe55bDw8GFwcbPQANnlyehDeAKreAHQuEK567hAAogAebNKk1W3eozkUPFjuGKSmUON5APIclQR4enrVggC86CGEJBQAPADeVFDlsgBcEXLT3OcQWHpaHHI3HDr05O6PGDwAJXIbHUTlWN3oGy2zigUH+3XKVXIEKhwJhEAAvoFIZs0QA+CDAYAQE4Qf5AkFgggo3GcADcZJ4CIq1Rp0IZGKoo28bQJADUMORUtEOBAAOIYOQACS09BufDkclIPCuRLkkz4zWYPGanS4wDgiDAIGAYDNoAgAH1rTbbTaIABNF5dADCenUeSlPzydt91ogJrNCx6vSwlS0HoA0uQCEciIEGoFIxByL05OQOOoeBAcDHYhAiBBdrmCPmiATdsnU+nM9nkwB+AsAbUjAF0U2mM1mIA0II3kx9yAA3H4QQcj9x0oO5Pa4A65OMJiskqhNtBk0V9MMR8jR2Pxnt41s3Ihr1tTznmkBWv12gt2OQQF1FFK3u+BjCDTqP4Ok7oARy0NhKkCPoKBMTEIBiTxdAAchEBZkCwARKmqbg7GALQHEqHhYLNDA3h+GI2CwPIAFl3XISoVwuAZkVue4uCoZ5XneCBPmkb5fjhQFgVBFZqQgHFoSoeFEVZITUU4MBL2DLAX2zXYmyoMDjDkI4AKAyojkwOdiAXCiPRAyTaQ4PFAlJck+KpNk0QZMSWXo4S0UxPFzJU3pwPUzTgJ0-Z9OOQyqMCexGPMkl2Lom5QoIrhXPcqBVJMDTAN83T8AC8gjiC4yOK48LSRYt4Pi+UcMTc-wwFbM0rxvN9-VcLQkj4UcAGV02VOr6oDU1QCoAlWoEAoIFLJqIk2LDHA4FUIAVJUVTVDUtR1PUDQQWBgE4HhUh+fqIAFIVxvDBwnBmublVVYB1WQ5bdXcfVDQ2ngJpO6a9oo4aXRQtCuDseVFQuxabu1O6mMDMAgA)

### 题意

从 `T` 中，选择一组类型可分配给 `U` 的属性。

例如：

```ts
type OnlyBoolean = PickByType<{
  name: string
  count: number
  isReadonly: boolean
  isEnable: boolean
}, boolean> // { isReadonly: boolean; isEnable: boolean; }
```

### 题解

```ts
type ExcludeKey<T, U, K extends keyof T = keyof T> = K extends K
  ? T[K] extends U
    ? K
    : never
  : never

type PickByType<T, U> = {
  [P in ExcludeKey<T, U>]: T[P];
}
```

- 我这里的是在使用 `in` 遍历 `T` 之前就将其中键进行过滤，主要发挥作用的是`ExcludeKey`

通过具体示例来看看 `ExcludeKey` 的作用：

```ts
// 假如有：
interface Model {
  name: string
  count: number
  isReadonly: boolean
  isEnable: boolean
}
type T = PickByType<Model, boolean>;

// 那么直接代入到 ExcludeKey 中则有：
// 此时 K = name | count | isReadonly | isEnable
type ExcludeKey<T, U, K extends keyof T = keyof T> = K extends K ? T[K] extends U ? K : never : never;
```

`K extends K` 会产生分布，这样对每个键进行处理，那么最后获取的类型就是我们需要的键了。

我的答案略显复杂了点，在解答区看到一个更加简洁的：

```ts
type PickByType<T, U> = {
  [K in keyof T as T[K] extends U ? K : never]: T[K]
}
```

在 `TS4.1` 的版本以上，可以[通过 as 来创建一个新的键类型](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#key-remapping-via-as)，所以就能直接进行过滤了。

## StartsWith

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02688-medium-startswith/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=24&ssc=96&pln=24&pc=1#code/PQKgUABBBMBsAc8IFoIGUAuBDAThgzgOoCWGAFpCstTZQEYCeEAVsVgHYDm+ZHEAFAAFWHbr3YBbAKbYAlBADE0gCbEArhMUYpEgA4AbLNuT7SUnFn2VKC2xACKaqfgzEA9u2tQAknv06pdgwIAANMXAIScgAeABUAGggAVQA+EIgAdzJiAGMyCGwAa2cCjLcIKQAPLBzglxxiLgKGXRKOZQgcGTUcdnxMshlBnFDY9JcI-ozSfJCkkK8IADE3EaqsPylFkJ2MFuccht0MSj3WiCwIAF50bDwiGeiAciw6HKfEl-eUiGBgCsqrVqUg6GHKdCkEAAZpZ8FsoGdIXRrrdJlEyM9Xu9Pq8nj8-gCgdpQeDIRgcE5TvsIDkUeF7ujMW8PhAXm9lHjfv8qkSQQVSdDYfDQjtFj8AGrEKQZCAeCAAcVIAAk1HQAFwQMgYDC6fBqv4EPIAOmY+CNq04wDgiDAIGAYAdoAgAH1XW73W6IABNNw9CAAYTcykhSvMkI9EddEDtDsRqIZj1iAO07GU-XqjU4iSSycCaYgGa4PxuSaqKfzIQAJABvJIAXxrhc4dfSAH4ChTIRqYfo4QBuB1gJ2RiMQWLOYL+rBw-ojj3R+3EPSrYJx6sQACiAEc1JZEhvAVJahA69CcG5NE9BIjkHlLP4uM5gGpXL2nmAl7oVxB1weiUshUSAA5NwMG3Xd9BPM8L1Za99lvXh9AfTgnxfYg31jakcmnEobgAbUoP8jwwaJwMsaJ6UiR42WxVkajxRIezhFIUniQjD1qUid3IyiHhiGiWTZBiOycFi2KgIjOLI-QKLuKj+KxQSsQ5VjBV7KQxLAABdQchxAF0509JYenIcxbikXUDMM50Fz0ygfjQXguggBhfRGfA3H0NCPD1TVtV1fVgENMgTTNC0rQQeBgA4fAMnMeyIElaUC087y+g1LUdT1A18GNU1zRwS1rSijyvNcHyEoAWVWSF-UQ5DnAy-zsqC3KQvyi1bXtMAgA)

### 题意

实现 `StartsWith<T, U>` ,接收两个string类型参数,然后判断 `T` 是否以 `U` 开头,根据结果返回 `true` 或 `false` 。

例如:

```ts
type a = StartsWith<'abc', 'ac'> // expected to be false
type b = StartsWith<'abc', 'ab'> // expected to be true
type c = StartsWith<'abc', 'abcd'> // expected to be false
```

### 题解

```ts
type StartsWith<T extends string, U extends string> = T extends `${U}${string}` ? true : false;
```

- 这个比较简单，通过[字符串模板](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)进行条件类型判断即可。

## EndsWith

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02693-medium-endswith/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMBsCcBmCBaCBRAdgEwM4HUBLAFwAtJUUrqKAjATwgCtCBDTAc11PYgAoAAi3ZcemALYBTYqwCUEAMRTshAK7jFxSeIAOAG1ZaUekpIBOrPRQoLbEAIqrJuYoQD2ma1ACSuvdslMYggAAyw8IjIAHgAVABoIAFUAPhCIAHdSQgBjUggZAGtnfPS3CEkAD1Zs4JczQk58+h1i9mwIM2lVM0xcDNJpAbNQmLTAvAySPJDEkK8IZIgANUJJdIgPCABxEgAJVVoALghSYmIdXEPgYGJcXIA6Jlx7tzMOYDgkMBBgMD-QCAAfWBINBIIgAE03N0IABhNzYSQQXbmJFg9HAiA-P7EZpI8IEKaxcoVLQ4Pp1BocBKJElkiaUziLAC8EBidPGfRCABIAN6MjgAXz5iUFaQA-PkzE4IMcAGaWXCSADcfzAAIx6LZzmCsNYSr6mrBWN+hF0r2CuJaEF5GAAjqpLAl0BUWjUIIKIHKzG4NAByARWyQoXKWfycZzAVSuPS4P1gM06C02jCuyQ1ABiiskCQAcm5iOgHZYPV6ff7A3iQzw9OGOJHo4RY-GwEGINl9cVWQBtCgut3EKJFx16IfkyKkKJ+1i0bJ+hJ+2d+5IJYjSyTJFd9tM1IfF0cEidTmdzhcn5er9ebuLbgd7kdjiJE6dLhfYC9e7PXsAAXTV6pAIEjXBDNujIcwIAAZS0C4gOAwETQAihFkgnhOggehoWGXA3D0RsPEuE4zguK4bjuUhHmeV53k+RBgHYXB0nMZDllWdYcLw1wCOOU5zkua5bgeJ4XjeD4EDojj8N6FiAFlXiRWEazrZweOI-iyKEqi3m+X4wCAA)

### 题意

实现 `EndsWith<T, U>` , 接收两个string类型参数,然后判断 `T` 是否以 `U` 结尾,根据结果返回 `true` 或 `false` 。

例如:

```ts
type a = EndsWith<'abc', 'bc'> // expected to be true
type b = EndsWith<'abc', 'abc'> // expected to be true
type c = EndsWith<'abc', 'd'> // expected to be false
```

### 题解

```ts
type EndsWith<T extends string, U extends string> = T extends `${string}${U}` ? true : false;
```

- 这个跟 `StartsWith` 比，就是反过来了而已，所以还是一样的套路...

## PartialByKeys

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02757-medium-partialbykeys/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMDsCssIFoIAUCGAnALgSwwBsAhATwGkBTUgZ0hWUafoCNSIArAgOwHMaAFhm4QAFAAEuw-kO4BbSjgwBKCAGIFAEzwBXOeoD2LDpQDGOevTXWIARR2Ua+A90tQAknIAOhSgu44EBgQvJTclFh4phAABpi4BCQU1DQAPAAqADQQ5AB8MRAA7gJRAhBKANaO5YUG5aRelEFYvHphgTHpBcKaseQxAHRufQU0jaZ4AGbsOAJNNIoQBpMQXlgGjQnVy7Fd5UKBggY6hL0LgTh1LE0bztxEAxAA6nMiMf0QeDQQ3AaBawYAG54TSUTTZPCHATHU4QOQYKpBQiEVbrTb4bZeO5ETg6JwQQh4RGzJq-LDwlFxbD4IgZfJDKD0ABiBiwEEoAA8MN5fMMYvycA1HKZIlj6HgAhFJhhTE0AKoLNkAb3oUHuCgAXBAnJE+KqgqEtdw9NcsPqMJpNFhHDQtTqJbx6ABfYaCxoQBUReI0wgAOW5TQAvOhqYkyFRaKlPVhsgBydWUWO5CDAYAQJU-AMAfg19r4AG4DZQNca5KbCxarTbczhdbwIC6oPyYsNkwA1PCUQpLEQAcUhAAkdCwtQIcDgvLbUzgaKYBAMODQBqzeMA4IgwCBgGAd6AIAB9Q9H49HiAATWObIAwgZQRABxEmifn4eIFud26mt6w8lI+l2RyOBhJo3xKk62RyhAwZVKQOzpMmwb0AA8nIkKpN+tJoFEFQZBBEAAGQQDBcG5MmhEoWhWQegRRHUCR2ThICES5PmO5sXuL7PhA6SOIEV4YAs3ycSeb7bng3ishcQrphAACiACOOhENkskcuMgROhAkzrPosbiJ+yBzkQvh8I4wA6PghA0LGYDiV4kkyap6lMkQCzZL6fwKUpKKadpBi6fpQqGUIyJhKENDmZZ1k7hKQFYNKsoeoq6b0Amdq1g69AYIaPwmhEWWWtaNC2tqGV6i6tmSvFMryoqGF+gGKVqtm6V1llOWlqaBVVsVrWZRVsVSjVSVeqGRD+goACC3CaJNoRNZmCg5qVbVQNllDLZ1+VrYV1Yrf1H7SaYAnVMGADa9BOWYOCpF5mFjUkEZpNGcYJkmEF1Q9E2UKRmSXWp123Yp90JEQ4YpFGiqvQGsYQAAPhAsY6NwFS-IU3DvSNWD1d9v3-epQPeehD3g5GL2I298OI+tmPRjjAbTbNoR41AV3mITIM+qTz2Krk2T1ZDzF4wAumxYAccJp5MjouBzGyADKQGTgekuvu+oD0Mm8tCNaECwTL2oGIQFl4C4JVjhOU7ADOc4LkuK5rggsDAMINCFNtEDtp23Y0EbJtm6O46Thq06zvOi7Li0juIMAvvG3cdBQMmACyrJNFeIUmeFgeWyH1th3bkeOu+YBAA)

### 题意

实现一个通用的 `PartialByKeys<T, K>` ，它接收两个类型参数 `T` 和 `K`。

`K` 指定应设置为可选的 `T` 的属性集。当没有提供 `K` 时，它就和普通的 `Partial<T>` 一样使所有属性都是可选的。

例如:

```ts
interface User {
  name: string
  age: number
  address: string
}

// { name?:string; age:number; address:string }
type UserPartialName = PartialByKeys<User, 'name'> 
```

### 题解

```ts
type PartialByKeys<T extends {}, U = keyof T> = 
  Omit<Partial<Pick<T, U & keyof T>> & Omit<T, U & keyof T>, never>;
```

参考自[解答区](https://github.com/type-challenges/type-challenges/issues/5395)👍最多的。

先通过一个具体的`case`来进行分析：

```ts
// case 如下：
interface User {
  name: string
  age: number
  address: string
}

interface UserPartialName {
  name?: string
  age: number
  address: string
}

Expect<Equal<PartialByKeys<User, 'name'>, UserPartialName>>
```

1. 代入到类型中：

```ts
// 此时 T 为 User，U 为 'name'
type PartialByKeys = Omit<Partial<Pick<T, U & keyof T>> & Omit<T, U & keyof T>, never>;
```

2. 先计算内部 `&` 右边的 `Omit` ：

```ts
Omit<User, 'name' & keyof User>
// 相当于：（注意（），这是一个整体）
Omit<User, 'name' & ('name' | 'age' | 'address')>;
// & 是交叉类型运算符，返回的是交集，运算后结果为
Omit<User, 'name'>;
// 最终结果：
{
  age: number
  address: string
}
```

上面代码中，`'name' & keyof User` 得到 `'name'` ，因为它们的交集就是 `'name'` ，如果没有交集，则会返回 `never` ，例如：

```ts
// T 的类型为 never
type T = 'username' & keyof User;
// 相当于：
type T = 'username' & keyof ('name' | 'age' | 'address');
```

3. 计算左边 Omit 中的 Pick

```ts
Pick<User, 'name' & keyof User>
// 交叉类型运算符的结果跟第二步一样，返回 'name'
Pick<User, 'name'>
// 最终结果：
{
  name: string
}
```

4. 根据上一步得到的结果，计算 `Partial` 的结果

```ts
Partial<{ name: string }>
// Partial 是内置工具函数，就是将类型 T 的全部属性变为可选的，则最终结果为：
{
  name?: string
}
```

5. 将 `Omit` 内部 `&` 两侧的结果代入到类型中，则有：

```ts
// 因为 { name?: string } & {  age: number, address: string } 是通过 & 运算符产生的类型
// 是两个类型的交集，所以需要将它们融合为一个类型
Omit<{ name?: string } & {
  age: number
  address: string
}>, never>;

// 使用 Omit 再进行一次运算，第二个参数可以传递 never
// 所以就达成将两个类型融为一体的目的了：
{
  name?: string
  age: number
  address: string
}
```

## RequiredByKeys

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02759-medium-requiredbykeys/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMDsCsBOCBaCAlApgRwK4EsAnTAEwCEBPAaUwoGdJUVmXGAjCiAK3wEMA7AOZ0AFgIgAKAAI8BwsfwC2mAC68AlBADEykvlyLtAezZdMAYxWNGW2xACKuTHRX4j-a1ACSigA4AbTGV+FQheCEFMfkxCfHMIAAMsPCJSShp6AB4AFQAaKCoAPgSIAHcROJEINQBrZ2rSo2qKX0wwwkEDKNCE7JKBEkSqBIA6TyGSulbzfAAzThURNrpVCCNZiF9CI1bCV3r1xL7qsVDRI1x-QZXQlSa2NuIU4hIRiAB1Jf4JiHw6CH4RlCWyMADd8CRSPl8GcRBcrhBFLw6mF-P5Nttdvt-k8CC9uLgXBB-PgUYs2oDCEj0UkcHjSDlimMoIwAGJGQgQTAAD14fkC4wSQpULWc5livisUHwIRis145jaAFUVpyAN6MKD8PmYAD8AC4IC5YkJNWFIgaAQYHoQzbwSCRiHQ6JbjTLBIwAL7jEWtCAqmIABV4ez4-gAcjqIABeDB01Lkai0OiZAOEfIAcm1ygzhQgwGAEDVAJ1hrdQgA3Oa9Yb+NaYlX7Y7nC6yyoTYIIN6WVAhQlxnmAGr4TClNbfADiMIAErg2IaRCoVL46PqCyo6OYRCMuHQRhzBMA4EgwCBgGAL6AIAB9W93+93iAATQunIAwkZIRBpzE2g--7eEBnhevptMk9KJhkKZ5BAVAxhAdQUIcMFKly3IqFEJD-IhyHwThGzZHm0aMAA8ooMKZOBCaZIGcQ1Dk+RwQAZP6hR5ixZEUTBzGsfk0SgjEhQVheYBXgB-4QNkzihG+vArP84kPkB574H4HK3KKRYQAAongvD+Pk2nctMoSehAszbIYGZSKBKBbvpgRCM4wC4K4-h0BmYCqb46laUZJmsvpKz5OGQK6bg+lduZlkQNZtn2WiUSRHQLluR5F4yhhhDyoq-qqkWjDZjWRrtu6jC8Batb1raUBNk6rYlR2YDel5srZQqyqqlRLyRsoBVaqWjVlbVlVWooNrlQ69WuqVpotZlcodXlMTdaQvWYAAgvwJAbZE-UlsobZNSNmBVeNMSTc2zozU1LWgRA5hyfUsYANqMP5FgqJk4X6ZR8YvOkyapqqmZFbm+RpqtJDrWxuTvcZn3fXp-h-c8aRJlkaagzqGYQAAPrFuD8DUgKlPw4PLYQUMw4UcNQB9lhIxFKNQ4DmMg7FYP47FFWYBTkP-WtOpbTtkSw-DJlM79rMYymaa03GaMkMDgniwAuiJokgDeimPqyuB7EsnIAMoYSuOu69eyla4webG2IxAQEhBtGkY-iuW4-CrhAi7Lqu66btuu77h0R4IIgwACHQpQXVAQ4jmOdBux77je77K5rsAG5bjue4HmHSDAEn7uuKntsQAAshybRvmIiVOWnS4ZwHOfBwep7nmAQA)

### 题意

实现一个通用的 `RequiredByKeys<T, K>` ，它接收两个类型参数 `T` 和 `K` 。

`K` 指定应设为必选的 `T` 的属性集。当没有提供 `K` 时，它就和普通的 `Required<T>` 一样使所有的属性成为必选的。

例如:

```ts
interface User {
  name?: string
  age?: number
  address?: string
}

// { name: string; age?: number; address?: string }
type UserRequiredName = RequiredByKeys<User, 'name'> 
```

### 题解

```ts
type RequiredByKeys<T, K = keyof T, U extends keyof T = keyof T> =
  Omit<Required<Pick<T, K & U>> & Omit<T, K & U>, never>;
```

道理跟 `PartialByKeys` 是一样的。

- `Required<Pick<T, K & U>>` 用于从 `T` 中获取键**为** `K` 类型的字段组成新对象，并且将该对象的属性都设为必须的
- `Omit<T, K & U>` 用于从 `T` 中获取键**不为** `K` 类型的字段组成新对象
- 最后再使用 `Omit` 将上面两个对象组合成一个完整的新对象，就达到目的了

## Mutable

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02793-medium-mutable/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMDsCcBmCBaCBZArgFwIYCMAbAU0lRQsrPwE8IArAS1wDsBzAZwAtWIAKAAJNWnHiwC2xPAEoIAYkkATRpnHyATsVyKA9i0J05O-PWIBjbCgDWxGhzJk5TiAEVMxDtkZ6HUAJLiAA4kkizYENhcxBBsxCzE6oxmEAAGaVh4RMQAPAAqAHxpKRAA7lxJXBDiuDYcELiEhBCB6jqBCV4eEIwsqWm5RVU4BCT8LDrhmtp6BtIAdL4QAGI66hDEAB64QSSLRdg07RxmiYHYZD3YCQBmuGbRuTq6EADeZFBTuvp0XtgkAFwQTyJdjvCCfGZ0RQeE6MM7eFiA4E9NhgiHfCBmHQ7KTERSA-A6HQkVhkAC+iwO7Qwwyyj2eAF4aZkSHknjp8hBgMBXhFGH9iEjsCC2ABuCDQ46nLx6IUi8VYnFXfEQQnErQscUUqBkIqLTkANUYxBKED0EAA4vyABKYfCArjYbCBDj-bnYY5cOb0DhzVZsYBwJBgEDAMDh0AQAD6MdjcdjEAAmjpMGsAMJPaLWhLReN5mMQUPhqnRDIjHK5dYbK4sRR1ABK5lWimyAAVWu11AcANK2AA09RYNHynKZbygKHRBggAG1W91ejYaDprhBcgBdQG5Ofrqs1utmkzmcIAfmZ5byO85W53orAFPDYEj+bza484TTuA4XRf8cLYcYIJVnCEteQAUQAR0wBoBzAjZ2gsCAyQga5WjUAByAQSxQMweEaOJYg4YAcEYQgOHQsBAMCYDwPg48lgab8BwAOQmSDoKaZDUOxCBMOw3CGhIdgPGIrwyIoyiwhuO4HnZABGV4yF+AEgWFFEyElWF4VlVSRTIRVglxFU1RJFgyEkPBAXHKB6hwLhVjldSoApB9QIAGUYTwICZGc5IHaAB0Qddi0OaIzC-LofLIOCEOwbJ2IabIyyybJG2mb42V0OSRwHekdGy-I+2iuiLHiqDEuS1k0q+Axsg8zwcogersBysBgrAUCEladQ6iiqBuQgLCOBQTZYpG9RurISqcnQ5F2HQwqyAGoaRpKywutWKbaVZAAGRb2ojEBo1-BMllTSIEggABlK4XWOk6o3-J9wCgTkrp4TQIGXVMgWJEi9FdCBHWdV13U9b1fX9QMEEQYBWA4EoEjIQ1jVNDg-plFhAeBl03WAD1cIhv11ADINYfRwh-qx5GMFWaI0zwoTCIdJ1cbBwmfWJ1EizAIA)

### 题意

实现一个通用的类型 `Mutable<T>`，使类型 `T` 的全部属性可变（非只读）。

例如：

```ts
interface Todo {
  readonly title: string
  readonly description: string
  readonly completed: boolean
}

// { title: string; description: string; completed: boolean; }
type MutableTodo = Mutable<Todo> 
```

### 题解

```ts
type Mutable<T extends Record<PropertyKey, any>> = {
  -readonly [P in keyof T]: T[P] extends object ? Mutable<T[P]> : T[P];
}
```

- 使用 `T extends Record<PropertyKey, any>` 可以约束传入的参数是一个对象或数组。
  
  `PropertyKey` 是一个内置工具类型，代表了有效的键的类型：
  
  ```ts
  type PropertyKey = string | number | symbol
  ```
  
  数组的话，可以使用数字索引进行访问，所以这个类型是可以很好地检查出传入的参数是否为`{}`组成的对象或元组类型。

- 通过 `-` 号可以将修饰符 `readonly` 去掉。
  
  关于 `-` 的知识可以查阅[官方文档](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#mapping-modifiers)

- `T[P] extends object ? Mutable<T[P]> : T[P]`
  
  通过条件判断，可以对嵌套的对象继续进行处理，直至将 `T` 中全部的属性`readonly` 修饰符去掉。

## OmitByType

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02852-medium-omitbytype/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMAcCs0IFoIHkC2BLALgIQE8AVAgBwFNIVkbaqAjAiAKywEMA7AcwGcALThAAUAAVadeAjhnI42ASggBiGQBMsAVwzKA9vWbkAxjipUl5iAEUN5Hjiw6OpqADEATju0ADH0R9eAGghSLEMAawg2CB5ZCB0AM2CPCjd7WwgAdz4dGIgcMnJIt0KOHRxInh4sLg42egAbQpwdCH8AVX8AOmcIFx03CABRAA82DFJGnv98ih5DNyxSEygZwsxcPB0dRsEAXnRsfGICgB4AbyooWpkALmicBe5LiEMdDQ4cO44tenI3Z6wPAASuQ2KpHPUCHd6Fsdk4oFBAYNag1yNDYaD4RAAL5BGHbTEAPggwGAEDOEGuaPujy4AG4Xm8Pl8fn8cVR-D1iQA1LDkDJxDgQADiuAAEhp6Hc+DgcKQeDdSTg5nxOsweJ1+lxgHBEGAQMAwEbQBAAPrmi2Wi0QACabwGAGEdKpCmK-oUrZ7zRADUbVgcNscKCciEE2sT9hcoABtAAKECwQrC5AICQgRAq6bjAF0IORhjhyBxVDwIG0IAB+SnkABubLusezdyIObpYGxRuNIDNXqt6ds5QdbBipd7fd9WHG-XK-opgwAjho2PUgiMKMYcRB4h5tAByESrZCGAT1RrcWzADT2eo8XdgSekafkobDdc4FzLmJBAByZQXS-qTdt08CB90PY9lzPLgLyvLAbzve8Pj+eI2EMQoAFlnXIQCo0pMZqTsWkqFed5PkpVl-kRYFQXBDhIXRAlOCoJEUUaBi4XbP0CheYd0n2aMqDXIwcBOf9lxOdYjhIYNMJdFcIHxOFCSCCkqTuQjE3pRlSJZDBfgGbFCWUwTX2E0TF3EyTCGk8gTlk7Cgg07hlOfEjmXIvS-gZQEQTBCEoQUjFOG8nhkTqNjAsYoVDOMqAhOMcyAIkw5rNOez5O+Ty3Bc1T8PUh5NJC3zaPoyK4RCsLUXYzEcSMgIwGzTswBNMdLV6DRUj4NkAGVC3lHtWtNH1DVAKhiW6gRiggVMOuibZYMcBUIBlOUFSVFU1Q1LUdQQaBgE4HgMj+MaIF5fk5vqBaOCWlb5UVYBlWPTbNTcbVdT2nh5vsRaTswqaHRPKDbGlWU7vWp71Rerh9UNMAgA)

### 题意

从 `T` 中，选择一组类型不可分配给 `U` 的属性。

例如：

```ts
type OmitBoolean = OmitByType<{
  name: string
  count: number
  isReadonly: boolean
  isEnable: boolean
}, boolean> // { name: string; count: number }
```

### 题解

```ts
type OmitByType<T, U> = {
  [P in keyof T as T[P] extends U ? never : P]: T[P];
}
```

- 关键点就是通过 [as](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#key-remapping-via-as) 来创建一个新的键类型，由此通过条件判断来排除掉某些键

## ObjectEntries

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02946-medium-objectentries/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMCcAsA2CBaCB5ARgKwKYGMAXAUQDtCAnAS1wGdJUUnmHMBPCbKgQ1IHNaAC14QAFAAEuvAcNIBbXIW4BKCAGIFAEyoBXOeoD2OAoQYM1FiAEUddQlQOkzUAJJyADgBtcC8hEKCuP5s7kEAbrgUtA6kEAYAZhAABilYeEQAdLjk1HQpSc4QAGIGFBC4AB7cHt4QhfmEIXT41O6mUFTkkfHc+EEAsgaauJ4QAN4MUKTVuABcELSUnXwA3JMQ3HxzEKR6mJFrUFCeBvjc9o6084vU-ADaALoQAD47Op6ehxAAvgyNoRA5EMRmQlnQIABeDDGIig3K0AA8g2GngAfBBgMAIHcAOTTBQ4gA0CyW-Cer1xm1wRLecn2FHJ2JxJzOF1ItBpN2Wjxebw+Dy++UK6IAajQAO5xWIAcSohAAEjpMPNBIRCO4rpjCLR8IIMthaBlSnxgHAkGAQMAwNbQBAAPoOx1Ox0QACaBh0ZQAwsCIPLIkFnUGHRBLdb-kEvbItiLuJ5bAiACroqETKB3AAKEE6EAA1rg2AkIImHvNM8TE5mnpVCNlNLQIDpSMN4p1cJoIAB+RvN3Ct0jtiDzSsZh4PMDfOpgCPQ9IkHI0RHJyFQKPSXCx+O4BEAJVwAEcdFQKO2k6jUXd84XEiW1tawLbg0Hi3YIF7uLRwU-naGrVQPKUhDBACYwQMQh5xsSxAVKERA-BA8QUAY+g4uIEYoLqcbePwdDADo9ieByYD-u4gHjGBMEmEUcafsSAByBgkBBoyTohyEQKh6GYR82RbLQeEEURxFdBQPR9BAyIjOMDD4tsXL8AwVLzLsdKRAwLLnDEVwkrcfA8q8uwfBO4ZNBJwKeHCi4rrismcqSfCMpSWw0ip9KOcypyaZcdm6fpfKeOO06mWcn4NlCdwMNBsGEAi4E6HGCJpCYll0Ei5mosSkkWQudDnoSkWUUQsXMYlMLzmCiIZtwFD2AlWV5WZKIpbQeUFdFxXxZ4pVzs1CKgVenbzE2LZth23wZUyV40sNfajQ8rVQFFJgdQlSWwjliL9QWQ29v2g7jcSuJTcSM17Zo80ZWAgU2iA9rfi6RSegEkQQAAyrWGp3fddq-g+4BQOir3CCeECFp6CwGPGbLaaq6qasA2q6vqhrGqaCCIMAvC0OKakAxAYq4JKtCQ-hWkqmqGqzFqOp6gaRoUCaZoY8TUNaQw6KDCDa48ThMMU-DiO0yjDMWlaYBAA)

### 题意

实现类型版本 `Object.entries` 。

例如：

```ts
interface Model {
  name: string;
  age: number;
  locations: string[] | null;
}

// ['name', string] | ['age', number] | ['locations', string[] | null];
type modelEntries = ObjectEntries<Model> 
```

### 题解

```ts
type ChangeValue<T> = {
  [P in keyof T]: [P, T[P] extends undefined ? undefined : T[P]]
} 

type ObjectEntries<T> =  ChangeValue<Required<T>>[keyof T];
```

`ObjectEntries` 最终要返回的类型是一个联合类型，那么针对一个对象，以下操作可行：

```ts
type T = {
  a: 1,
  b: 2,
  c: 3
}

// T1 = 'a' | 'b' | 'c'
type T1 = keyof T; 
// T2 = 1 | 2 | 3
type T2 = T[keyof T];
```

所以，根据上面代码的思路，那么我们只要将类型 `T` 的值变为 `[键, 值]` 的形式既可，最后通过索引类型就可以返回 `T` 中所有值组成的联合类型了。

## Shift

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03062-medium-shift/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMAMBsAmCBaCBlAFgSwGYBdJUUTSiAjATwgCtsBDAOwHMBnTJiACgAE6m2HRgFsApvnoBKCAGIxAE2wBXYbPoAndfUpEiM-RACKS0a3zYA9o11QAksIAOAG1FjG+CPkyjPlBz4A3UXVWS0YIC1wIAANYgEFNbQA6djx8WOibCAAxC3UIUQAPekcXLIz8P1MAY3VsB0IoSv8IACVTJScPAF4MHAIAHgBtaAAaCERxgEYAXQA+CGBgCCHJiFmiDKyFgDVsUQB3CPCAcWx8AAklcgAuCEx8fAdWG6X8VmrMJJpWJLzmYBwJBgEDAMDg0AQAD6MNhcNhEAAmhYlPkAMIWeQ+C7BHzw-EwiCg8HNHxYNIDAAqBUK+FEjHkrAgTEoQ3mEF61KKdIZTKGLPGSSF2EYuGCEAAojMIAB+SUQO6MURBdQAbnBYEhBPxEEppg8aPorFM0O1cKJYOwjjyHlJEAA3pKAI5KehOcYSwr+aoeAC+EFw6gsqgA5DxSShPm6XCxTMAlOYnKwQ2ArQ4bQ7JV7RD7sm7jeMAHIWfASl1uiD+wPBiBhiNRpwx5hxhPYJMpsB26pGk29IZET3e-ADMuupwDcmDEbjNazObjVbTebzgfZn0j8vjyfDoYh+gh8Yh8gH2vVE8h+QhiDzBdH89nw+X5ejMAzDWakCms2E7Korzi9A6WeL9vwtD8iAWLANB8SgUXyVgLCcVsrBee5HmeV5gHeT5vl+f5AQQRBgCYVgDmCCCID2Q4IAQpDzBQu4HieF43g+L4fj+dQASBIjaOQxhWAogBZPIfDRDhG3pZtUKYjDWJwjj-hBMEwCAA)

### 题意

实现类型版本 `Array.shift`。

例如：

```ts
type Result = Shift<[3, 2, 1]> // [2, 1]
```

### 题解

```ts
type Shift<T extends any[]> = T extends [any, ...infer E] ? E : never;
```

- 这个就比较简单啦，通过元组的 [rest](https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types) 元素，就可以获取到我们想要的值啦。

## Tuple to Nested Object

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03188-medium-tuple-to-nested-object/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=24&ssc=88&pln=22&pc=1#code/PQKgUABBDMCMAc8IFoIBUCuAHANgUwgBcB7CAOTwGdC8ATCAeQCMArPAY0MhWV7+6YBPCCwCWAQwB2Ac0oALKRAAUAATFTZCyQFs8hcQEoIAYl21RGbSeKsOXKN2NOIARQxVCo4pO7cA4qIAbniSEOJE2PhEglgEAAYJaAlxRAqEEN44wuze+qKSlBDUAE750tGxADRhkvThhDHxCQCqydVMGKI4daE2bJwQxRwYxZRBeFkAdL5QyQ2xlOylWPYVBOEAvOiReGjEFNR0zP2EADwA2gDk4pcAutUlZQB8EMDAEADe4gBcRYSlMgAvtx5gQmBAtphcLt9h4jrZOBdrpdqpcmHdqpJLEw8MUXm9Pj9PkxfljtDjioDgVBQRB2BDttC9gcaLRjnYLvcIExiMR8FJ8e8eXy8FJJhBRAAzVIEQg7CWFPDaFaCaosDDUQZ6EahQhyAjNNbcZIzCAvABqojwAHcMqEAoQABIYEkQOSEQhYSjfN6ERZySYsSiTYjFaTAOCIMAgYBgOOgCAAfWTKdTKYgAE1iCMIABhYi0AiO3EENNl5MQGNx2ksuinABi1QAotVmi8tvWIHgAB40WqFR4yCAAfk+EHOAAUJaF67dflD8My4WyEWcWxA2xBARBSXhgsVq41GYvYYcVydTmhW+30F3eyFaIVzvlJbiII2IJMvy+303biPyGXBtm2vHcNwAbjjeMQCTcs03QDw83ESgqFguD0yrURlVDdJaQ+CAmwARwwcQcGbbtYgGbdJWKYgrEuFRQWQdgFBwfAZCoYAME8HBKEuQ9YjpZDUK2c5uCbCiOSIkicEvHYlzPdlESuG4uUHaQnmqfCiXUrcnk08TJMRaTSLkplT1ZJSzhUlEIDRDEIDJClNLHIl8NdJy323QF9MqQzKLXYjTIXGFa3PDkbNRdFUXYBzhX5SQXO0353JSulfni0VQm8vSDKgCSAtOEzZJChTLNXTl2l5BKXMygUDNuON8hoYpJXEdgCHzHBQ0lDAcE+bgcm64pfnUyCoGQ2hfkudSUTAYFmtxNqOrzURinYKIPm4YpxHMDVSWxXFxrCSgpr+AFpHmyAwFpLqer63M1o2ggtju1q+ogAAyVb1vwSCcgKdJNk+bdkLzPl7pwR7frwSDxGmaC0PQxN3xGPU3wAZRoL0kfQytY1AbgXgxhQhggQRs2KIo+W4rwCl+d1PW9X1-UDYNQ3DSN4GAKRKGtXEiYgS0bWpnBae8b03Q9L0fWAP0WLZkMwwjBBucoGnPAlwWAFlQ061j2OkKgGel5m5dZoMlcuqswCAA)

### 题意

给定一个 `T` 仅包含 string 类型和 type 的元组类型 `U` ，递归地构建一个对象。

例如：

```ts
type a = TupleToNestedObject<['a'], string> // {a: string}
type b = TupleToNestedObject<['a', 'b'], number> // {a: {b: number}}
type c = TupleToNestedObject<[], boolean> // boolean. if the tuple is empty, just return the U type
```

### 题解

```ts
type Nested<F, E, U> = F extends string ? { [P in F]: TupleToNestedObject<E, U> } : never

type TupleToNestedObject<T, U> = T extends [infer F, ...infer E] ? Nested<F, E, U> : U;
```

- 我这里通过 `Nested` 来组装对象
  - 通过 `F extends string` 进行条件判断只是为了把 `F` 确认为 `string` 类型
  - 因为值是一个嵌套的过程，所以就是要遍历数组元素，递归调用，最后就能完成嵌套的效果

我这里把处理嵌套对象的逻辑拆分为了一个独立的类型，并且通过条件判断来将确认键的类型，显得有点复杂，在[解答区](https://github.com/type-challenges/type-challenges/issues/3282)，我看到了一个更加简洁的答案：

```ts
type TupleToNestedObject<T, U> = 
  T extends [infer F, ...infer R] 
    ? {
        [K in F & string]: TupleToNestedObject<R, U>
      }
    : U
```

上面代码中，使用了 `&` 运算符来解决了要将 `F` 变为可以作为键类型的问题，例如：

```ts
// T = 'a'
type T = 'a' & string;

// 这种情况下，是冲突的，会返回 never
type T1 = string & number;
```

## Reverse

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03192-medium-reverse/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMCMCcAmCBaCAlApgN0wJwGdNJUUzySAjATwgCsBLAQwDsBzAgC1YgAoABRqw7cWAW0wAXJgEoIAYgkATBgFcxCyaoAOAG2JQS84xACKqzAUkMA9ixIkAkmL2YJLSREmdMX6tt9cQlsWCBsAMwgAAxiAQTw8JmoAOjwcfCIYqIcoADEbPAhMAA8mF30ALhzomMl-SwBjPAZtSRI6gIgmCABeDHTCTAAeAG0AciYxgBoIMcoxgF0APghgYAhx+ZmJxfb6iEpe-qCiUZ3trdmGxZW1jbHri+nZyYWSLOqVgDUGTAB3MKhADiDEkAAlVJQKhBOJJJNoCBU1pICA1OMk6ARkgU2MA4EgwCBgGASaAIAB9SlU6lUiAATRsqkKAGEbEpfGD8L4aTzKRAiSSOr4sCdhgAVFZ9MVFYqSTAsJQEDbJFUMFjhfAQXIzNUawoAUQWEAA-Bt9TMVckRRlhrklkboWKANwksBk3k8iBiyyeZlMIhKj00-nEhguAqeIUQADeEH1AEdVExdDN9cUAg1PABfCDhPA2DRjfhClBo5P6diWYCqay6AhjQX7Br+yxHEYkNMZyRDBNJ3RDa2DUbLGYjZZLKYd9OYTM9xPJgcDU7jSZPEf3S47ceTqCdmfd3sLwfL86zTfXdfjR5n55bpYTsBvV3uoO03JM7yagDKcoRFNffICqAJArF+3BpBA1CMoUBA2LoNYhIiMJwgiSLACiaIYliOJ4ggiDAKwBB-PgIEQD8-wQLB8HWHYSGwvCiLIqi6KYtieC4vi+FUQhtGkQAsgUvjMtwugVmwljQvRqFMZhrE4oSxJgEAA)

### 题意

实现类型版本的数组反转  `Array.reverse`。

例如：

```ts
type a = Reverse<['a', 'b']> // ['b', 'a']
type b = Reverse<['a', 'b', 'c']> // ['c', 'b', 'a']
```

### 题解

```ts
type Reverse<T> = T extends [...infer F, infer E] ? [E, ...Reverse<F>] : T;
```

- 首先利用元组的 `rest` 元素，可以很轻易得拿到最后一个元素，并把它放在数组的第一个位置。
- 之后对 `rest` 元素 `F` 进行迭代，反复调用 `Reverse` ，就可以完成啦。

## Flip Arguments

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03196-medium-flip-arguments/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMCMCcA2CBaCAxANgSwA4QEEAnAcwFcBbAUwDsAXAZ0lRVbeYCMBPCAK2wCGNEgwAWQiAAoAAvyEjxNanQEBKCAGJqAE2yVNA0pVqNmzDRYgBFMlQZ1sAexpmoASQq5MVavQh1RKn8uXCCANyoiBicaCEcAMwhMR20BMQByBggAA1yAfQA6eJxcXOyC1wgAFRCgsqw8YnJfRgAeKoA+MogiKgBHMmxerPiyGgBjB2dg0Jzcqu6hbR6qOjIiGiyBCBoqAHcIUYmp2LpaiD3RbHHRCHEsgKCGAWoVtY2ZoITqiA4yOhWESiVGWuEML1WkQYFSgzHQjiIECoAA8Xl4qAAuSplM6hBjjIh4OjMXFBBq4ULLAC8GBKTWM9AYrUkhhIAAYMRB7IThAAaCCs2CcmiUDiRfmsgBMnI4jkc3iE6ipHQgYUc2G0KuYwGAUlZHN+coVNAlpCFO1F4oFpGlXLoPJISpVao1zDKlRVADVsPs4rEAOLYOgACTIHE5ojodFwDAxOsYNwKvGhCJIwDgSDAIGAYFzoAgeULReLRYgAE1HOsIABhFJBYORIIl5uFiDZ3OkiAAJSoQIYVHaKppVSRyLotG0WQA2gVZ9gaPFIhh+fPF4iAKIAXQgAH4IFP1-zZwUe32B+gOtvOVUANwd87k+ktJmdCDD0fjmiTqTH1mxiCrkuAAKToAQuS5druP6zn+nKnlCA5AR0oGQded65mA+Yts21R2AC1ZpHYBbYcWbY5tgngIgCnYAN4QOuAwCJg-LrsioSTBAAC+hxEI4FAQOk0ikigNxMd4wh2MA-zYJgDDpPeszjIRWQ0lOzCsexdCtAxZBMa0j5GM+zKgbK8pUEIHT8pIJlGuZNAdJZ6lsVQkzaYxmD6XShkmEykjxHKnLcvOjpviqIoUGKRCWVI-mOIF9rBaB4WRQ5vJOZpbm6R5BnND5zL6vFDqmiQ5rJVaUoyrZiqhaq6qalZBWGmZQjFaVlpEMVtpBcIoEupqjmbhhmEgMRJGtug6yPIiADK44xqNY1kcNzAqtN4i9BAXCVoiDDytJzj-pG0axvG+KiEmKakOmCCIMAQgMHskQrRA3q+rtmD7ZsEZRjGcbAAm53JgUqbXUgwDvZ9TBQCqACyCJBNW4iYOJJB2N9x1-QDF3A6QWY5mAQA)

### 题意

实现 lodash 的类型版本 `_.flip`。

Type `FlipArguments<T>` 需要函数类型 `T` 并返回一个新的函数类型，该函数类型与 T 的返回类型相同但参数相反。

例如：

```ts
type Flipped = FlipArguments<(arg0: string, arg1: number, arg2: boolean) => void> 
// (arg0: boolean, arg1: number, arg2: string) => void
```

### 题解

```ts
type Reverse<T> = T extends [...infer F, infer E] ? [E, ...Reverse<F>] : T;

type FlipArguments<T> = T extends (...args: infer P) => infer R ? (...args: Reverse<P>) => R : T;
```

- 通过条件类型判断加上 `infer` 就可以去推断出函数的参数与返回值类型
- 目的是将函数参数反转，那么通过扩展运算符，可以收集到所有的函数参数，这时它已经是个数组，只要使用 `Reverse<T>` 进行转换即可

> `TypeScript` 是结构类型，不是名义类型，所以这个函数参数的名称是无关紧要的

## FlattenDepth（数组扁平）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03243-medium-flattendepth/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMBMAs0IFoIDEA2BDALjgpgHYAi+ADjgBaQrJ300BGAnhAFYCWWhA5gM6VuEABQABTt36DCAW3w4sASggBiOQBMOAVxmqsAJ31ZmNGivMQAilvx8cHAPaFTUAEr4Axlv18OAN3wMVgAzbDwiCAMjVi0yCBwHCHVyKniOOT4AOhd0B30IfAAPLBkyDHwALhyAA1qcZjJbD30OChp6xsiIAF50MIISFMoAHgBtAEYAGghYadHoafgAXTnR0YBWJa2VmYA+CGBgCAnp2ZhFuc2lzIhQ3AJ9Qhm0jPaG-AhGHr77olIKEYnGZzBYQZarDbbJb7Q7HKbA85g1ZXa4Qf6pZLBLBaDA4PjxRKMD7jGi1ao5ACSwXilA+yQBEA4+LI+gcfg4yXU0w4OAA5PieFoDNwCPh1ATPh8yA5fPYAozCAQePh9NkoDR9gA1Dj4ADuECcEAA4jyABJaRgVCCUPBkPgVQ54jyUTJsLJ5HjAOCIMAgYBgAOgCAAfVDYfDYYgAE0HN4IABhBzJCCmlUfCMZ0MQP0BjofTC-QYA4YAFQKhQG6nx3GYox28fLlfxhB0RPyvXhAFVG0Qq5FCLWlt86-tujQy0Um8cOIRgir0NNMkuZ3P8q4ljQAPw0KBoHuEPs1us7iDbqDniCd0a88q8Ki8oeT3v4hsnqBni-HNCLpcF8JFqhhlcaZ42mTtdg3T8ICtUYl0yP8BnREZvwTOYAAYf0yTtoUwhC-iGICQLAiCTxglC4LwgCRmA1DLxIqArRLABuANAxAENMwjCAS1sHAEywPhbA4zjIxzdJpX0Pi8wgABvCAAFEAEchQwaZ5MKRoPD4gBfW5WV0XlRDzZBnSwDBb2VPhgC0ewMD4Xlc3eCAPAEoTelGGh1M0nBhiUlThkopCxhw45oV2SZPI0zwfL8syAv6fDiyBM5QWWcK4VOaZUrCiKoC86LfOUuLAoIoFRlgLZ0uSnLIu8wr-JKpL4TOeZFh2NZIW2U4quarKLmOK5dnC2qCtijB4sLILkpBNqIRRELpsReBkW2IbcoUqKtPq4qEqosZ4XKmbjmWgaoR2aAesypbLkq4a8s2mKivGxrALKlrQVGE7Oq6iBxgATlgAA2NCAA5xgAdkuhFUumTY1rADdWKDETw3QbwqHnABlAg7WElHs39UANQgTHBH0D5mFjfI+AcDAbMcQh7WtW17UdPhnVdd19E9b1oGAbg+F1FVie1PUIBpun7CcJmbRwO0HWAJ0XTdTIPS9BA+Yl+npeJgBZPIPnjQRzKISyrVl+W2Y5lWPV9f0wCAA)

### 题意

递归地将数组展平至深度倍。

例如：

```ts
type a = FlattenDepth<[1, 2, [3, 4], [[[5]]]], 2> // [1, 2, 3, 4, [5]]. flattern 2 times
type b = FlattenDepth<[1, 2, [3, 4], [[[5]]]]> // [1, 2, 3, 4, [[5]]]. Depth defaults to be 1
```

 如果提供了深度，则保证其为正整数。

### 题解

```ts
type FlattenDepth<T extends any[], C extends number = 1, U extends any[] = []> =
  T extends [infer F, ...infer R]
    ? F extends any[]
      ? U['length'] extends C 
        ? [F, ...FlattenDepth<R, C, U>]
        : [...FlattenDepth<F, C, [0, ...U]>, ...FlattenDepth<R, C, U>]
      : [F, ...FlattenDepth<R, C, U>]
    : T

// 使用示例：
// [1, 2, 3, 4, [5]] 扁平层级为 2
type a = FlattenDepth<[1, 2, [3, 4], [[[5]]]], 2>
// [1, 2, 3, 4, [[5]]] 扁平层级为 1 
type b = FlattenDepth<[1, 2, [3, 4], [[[5]]]]> 
```

> 答案参考自[解答区](https://github.com/type-challenges/type-challenges/issues/3307)👍最多的

下面通过实际的 case 来进行分析，首先标注说明一下：

```ts
/*
第一个参数 T：需要处理的数组
第二个参数 C：进行扁平处理的层级，默认值为 1
第三个参数 U：辅助参数，是个数组，该数组的长度等于 C 时，就代表当前迭代值扁平处理完成
*/
type FlattenDepth<T extends any[], C extends number = 1, U extends any[] = []> =
  // 条件 1
  T extends [infer F, ...infer R]        
    // 条件 2
    ? F extends any[]        
        // 条件 3
        ? U['length'] extends C     
            ? [F, ...FlattenDepth<R, C, U>]
            : [...FlattenDepth<F, C, [0, ...U]>, ...FlattenDepth<R, C, U>]
        : [F, ...FlattenDepth<R, C, U>]
    : T
```

#### 第一个 case：

```ts
// 没有传入第二个参数，所以默认扁平层级为 1
Expect<Equal<
  FlattenDepth<[1, [2]]>,
  [1, 2]
>>
```

**1.** 代入到工具类型当中：

```ts
type FlattenDepth<[1, [2]], 1, []> =
  // 条件 1 成立
  [1, [2]] extends [infer F, ...infer R]     
    // 条件 2 不成立
    ? 1 extends any[]        
      ? U['length'] extends C
        ? [F, ...FlattenDepth<R, C, U>]
        : [...FlattenDepth<F, C, [0, ...U]>, ...FlattenDepth<R, C, U>]
      // 会走到这里
      : [1, ...FlattenDepth<[[2]], 1, []>]
    : T
```

**2.** 处理数组第二个元素 `[2]` ，根据上一步的结果，代入到工具类型当中：

```ts
type FlattenDepth<[[2]], 1, []> =
  // 条件 1 成立
  [[2]] extends [infer F, ...infer R] 
    // 条件 2 成立    
    ? [2] extends any[]    
      // 条件 3 不成立    
      ? 0 extends 1     
        ? [F, ...FlattenDepth<R, C, U>]
        // 会走到这里，第二个元素可以忽略了，因为返回值是空数组 []
        // 所以结果为 [...FlattenDepth<[2], 1, [0]>]
        : [...FlattenDepth<[2], 1, [0]>, ...FlattenDepth<[], 1, []>]
      : [1, ...FlattenDepth<[[2]], 1, U>]
    : T
```

**2.1** 根据上一步的结果，代入到工具类型当中：

```ts
type FlattenDepth<[2], 1, [0]> =
  // 条件 1 成立
  [2] extends [infer F, ...infer R]     
    // 条件 2 不成立
    ? 2 extends any[]        
      ? U['length'] extends C
        ? [F, ...FlattenDepth<R, C, U>]
        : [...FlattenDepth<[2], 1, [0]>, ...FlattenDepth<[], 1, []>]

      // 会走到这里，第二个元素可以忽略了，因为返回值是空数组 []，所以结果为 [2]
      : [2, ...FlattenDepth<[], 1, [0]>]
    : T
```

第 **2** 步的最终结果为 `[...[2]]` ，代入到第一步的结果当中：

```ts
[1, ...[...[2]]] => [1, 2]
```

#### 现在来看看第二个 case：

```ts
// 这里有传入第二个参数，值为 2
Expect<Equal<
  FlattenDepth<[1, 2, [3, 4], [[[5]]]], 2>,
  [1, 2, 3, 4, [5]]
>>,
```

**1.** 代入到工具类型当中，这里就忽略掉前两个元素了，直接进入到第 3 个元素的处理中：

```ts
// 初始结果为：
[1, 2 ...FlattenDepth<[[3, 4], [[[5]]]], 1, []>]

// 代入后：
type FlattenDepth<[[3, 4], [[[5]]]], 2, []> =
  // 条件 1 成立
  [[3, 4], [[[5]]]] extends [infer F, ...infer R]        
    // 条件 2 成立
    ? [3, 4] extends any[]    
      // 条件 3 不成立    
      ? 0 extends 2     
        ? [F, ...FlattenDepth<R, C, U>]
        // 会走到这里，这里就不继续深入第一个参数的处理了
        // 所以结果为 [3, 4, ...FlattenDepth<[[[[5]]]], 1, []]
        : [...FlattenDepth<[3, 4], 2, [0]>, ...FlattenDepth<[[[[5]]]], 2, []>]
      : [F, ...FlattenDepth<R, C, U>]
    : T
```

**2.** 根据第 1 步的结果，代入类型：

```ts
type FlattenDepth<[[[[5]]]], 2, []> =
  // 条件 1 成立
  [[[[5]]]] extends [infer F, ...infer R]        
    // 条件 2 成立
    ? [[[5]]] extends any[]        
      // 条件 3 不成立
      ? 0 extends 2     
        ? [F, ...FlattenDepth<R, C, U>]
        // 会走到这里，忽略第二个元素，结果为 [...FlattenDepth<[[[5]]], 2, [0]>]
        : [...FlattenDepth<[[[5]]], 2, [0]>, ...FlattenDepth<[], 2, []>]
      : [F, ...FlattenDepth<R, C, U>]
    : T
```

**2.1** 根据上一步的结果，代入类型中：

```ts
type FlattenDepth<[[[5]]], 2, [0]> =
  // 条件 1 成立
  [[[5]]] extends [infer F, ...infer R]    
    // 条件 2 成立    
    ? [[5]] extends any[]        
    // 条件 3 不成立
      ? 1 extends 2     
        ? [F, ...FlattenDepth<R, C, U>]
        // 会走到这里，忽略第二个元素，结果为 [...FlattenDepth<[[5]], 2, [0, 0]>]
        : [...FlattenDepth<[[5]], 2, [0, 0]>, ...FlattenDepth<[], 2, []>]
      : [F, ...FlattenDepth<R, C, U>]
    : T
```

**2.2** 上一步结果为 `[...FlattenDepth<[[5]], 2, [0, 0]>]` ，继续代入：

```ts
type FlattenDepth<[[5]], 2, [0, 0]> =
  // 条件 1 成立
  [[5]] extends [infer F, ...infer R]    
    // 条件 2 成立    
    ? [5] extends any[]    
      // 条件 3 成立    
      ? 2 extends 2     
        // 会走到这里，忽略第二个元素，结果为 [[5]]
        ? [F, ...FlattenDepth<R, C, U>]
        : [...FlattenDepth<[[5]], 2, [0, 0]>, ...FlattenDepth<[], 2, []>]
      : [F, ...FlattenDepth<R, C, U>]
    : T
```

**2.2** 的结果为 `[[5]]` 代入到 **2.1** 就是 `[...[[5]]]` ，再代入到 **2** 就是 `[...[[5]]]`，再代入到 **1** 的结果当中：

```ts
[1, 2, 3, 4, ...[[5]]] => [1, 2, 3, 4 [5]]
```

对这两个 case 的执行步骤进行分析后，想必你也清晰很多了吧。

## BEM style string（CSS的BEM）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03326-medium-bem-style-string/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDM0EwDYIFoICECiBZCBnALgJ4A2ApnvgE4CWAdgOaQrIutMBGhEAygPYMALWhAAUAAVz96QuLACUEAMQBbUgBNqAV2VL8pZQAdiAQz3Ji1PZWPElm2tX67NR0kyaLPEAIqbSBR1p3KAAVAXI0Yl4AYwBrABoIDDJVWnxErF4NADNqUkoIVXwBLN4o+i4RTCwFalwIYwgDXgNNEwLaY2U6egho-gA3UjTAiGzeAuiTXFx-CDoIAGFubgA6CGCIADEJiFIADy7XROLydii4vt5DfmH8CAB3Xja1CHZySlIDT9m09Qb6gADdj4WiAxKkFJ3CDFUwQNRfYZqeouJynN4XWKPZ7EV7vCCfb7+O7-YxAkG0AD6lO+1GipHBhSy1Fy+RhAjh0Q5DDm6IIJHIvGy7LOmOxLzeHy+PxJrzJEGBoJY7Go9EBEF2iqpNJo9JYD2MlAcDEBq02AElDFC0grqgAeNCJDAZAB86oeAjpAgg9GG+VM5AINAYEHso2ylGuItm7M+5AMhq6pCsuHWAHVwp9beq6g0KMHehYrDZEoCMOrjLRXoCsBWs0Geg1KNZCPURNFK5K9oYiHIzVAmC6IAA1PIPDXCADilgAEpp2AAuCACfD4Ay4BfAYD4XBc1YAK1TE3owFgiDAIGAYGvoAg1PvD4fEAAms8CossuQZ-lyI+-9SIEva8iAMcgAFUHH4O0AGk9n2PQq3qBsGESEI4IQ5F8x6ABtABdRJFnQpEkKoHohwAXiYNCDgw+o8KYAB+JhoKYJdYJo4iIFghiIFiUhCCFCAAG8IGwgAFeZhBCbDaG0d5KFwgEFQAEiE6CAF9VMWTShLE9TAVwpcJPUiA2IAbmAwhQPQbAHSIxCsJQpJ7Mw5D6DwjIXJIgs8IoiAIMCO0Aqgx0kkSAByalwpdDIIpYaKLOvG8QDvf9HwgEJ-HuRYyTmNL0qA6gbkoe4QPIESMAAR00Eskn2UDonuEyIyjcKxDK5AuRsMgeVwYBNHwahiFwcLLOsjtZnqcjRKYDB6tIRq7SqmriAdWzwopcLEmw8LaXpcL8NE3CYogDbQWpPbSGimLZvmxblpsNasDtM7aC20Tdt1K7Dp2g0jR6d7wtwTRonpGYDpO16Lq+-VDWNehwogAAfU6KWhulSBYYHQf8EaXRuqA5oa-Aluqx77Ve96PI+3BlG6wHVA0bRAfaX0IYiiksbp4hiERlHXpYRmtGUPnUaVcxDTZ-H4jAXCkrAW98qfLZNBKzMeD0ddUqVylAKvUBBx4DkswE1W8DKAbAg3ZdV3XTdt13AQDyPSgTzPBBgErXAHnyQ3R1IcdJGIS3+Gtlc1w3Lcdz3Q9VmPU94A9oOQ9oXBDcyLNFg5Hnhl9MPbcjh2Y5dxggLAIA)

### 题意

块、元素、修饰符方法 (BEM) 是 CSS 中类的流行命名约定。

例如，块组件将表示为 `btn`，依赖于该块的元素将表示为 `btn__price` ，更改块样式的修饰符将表示为 `btn--big` 或 `btn__price--warning`。

实现 `BEM<B, E, M>` 从这三个参数生成字符串并集。其中 `B` 是字符串文字，`E` 是 `M` 字符串数组（可以为空）。

### 题解

```ts
type Union<K extends string, T extends string[], C extends string> =
  T extends []
    ? K
    : K extends K
      ? keyof { [P in T[number] as `${K}${C}${P}`]: P }
      : K

type BEM<B extends string, E extends string[], M extends string[]> = Union<Union<B, E, '__'>, M, '--'>;

// 使用示例：
// 'btn__price--warning' | 'btn__price--success'
BEM<'btn', ['price'], ['warning', 'success']> 
```

在类型 `Union` 中，首先要判断目标数组 `T` 是否为空，来处理空数组的情况，不为空的情况下才进一步拼接 `BEM`。

为啥会有 `K extends K` 呢？因为需要借助条件类型产生[分布](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types)行为，例如这个下面这个 Case：

```ts
Expect<Equal<
  BEM<'btn', ['price'], ['warning', 'success']>,
  'btn__price--warning' | 'btn__price--success'
>>
```

在执行完 `Union<B, E, '__'>` 时，这时返回的结果为 `btn__price` ，之后拼接需要跟 `M` 中的每个元素进行拼接，所以才有了 `K extends K` 。

我在[解答区](https://github.com/type-challenges/type-challenges/issues/5369)看到一个简洁的答案：

```ts
type BEM<B extends string, E extends string[],M extends string[]> = 
  `${B}${E extends [] ? '' : `__${E[number]}`}${M extends [] ? '' : `--${M[number]}`}`
```

使用 `T[number]` 获取数组的元素时，返回的是联合类型，那么这时搭配字符串拼接，它会与联合类型中的每个类型进行拼接，进行组合，最终返回还是一个联合类型。

当时压根就不记得[字符串模板](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)这个特性了🙈。

## InorderTraversal

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03376-medium-inordertraversal/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDM0OwDYIFoIEkB2B7ATgEwFMcAVHAQwDciBnMgG0hWWZcYCMBPCAKwEsyMAc2oALARAAUAAT4DhYjAFsCAFzIBKCAGJleXgFdF2rG24EAxisaMttiAEV9Bait5YM1qGkUAHOgWUMFQgVEQIQjh9wqhxqNwwILAAzCDZeDDIcLhUcAnD03EIcEPIY2joAOk8IADFcCAIADzJffwAuaoADbpVI53McXh8rKHN3FxK8gEYIAF4IAG9GKAp6NogpgBpliH8klXWMfTo6bagoQcERA8WdlbWIACYz86g9m6XX19W6degXr67Aj7Q7HU53c6Xa6gk4A84AXzhF14VxuR1hO0RjHhEDI1AgYwwLmqvSiEAAgnN0Nh8ERSJQaPQADykgjJSYEKYAPggwGAEAA2lsYJsngBdRjdTrVHkANV4BAA7okEgBxXgqAAS+jY62uKh81DafJU1HMIgq3GoFVwgmAsEQYBAwDArtAEAA+l7vT7vRAAJpYfTFADCWEIEE1RHCvtjXogztd6RURCSZHM4VIeQAcuHwp8ID9QYo2ERGO91lmCLmIwAfCDohjI1GV3LVvMQeuNsDw11gVnUwp00qMuhM4gNRopjB4fFVmvhLtgnmzRgC4hiyfT2eC+d5iVQAD8a4qp8wQ5II9izOIAoA5O872KuaLb3efk-RaeKufaZeGdeY5vlCKhPlyB4QB0UACmKADcYB8hEZK-kU9JlDeW4EDOc5tgunYNmCorZhOTTbjhOYdvMuYYNmYJkGw-jjlyK4IfyUAkVOWE7t2iFHqx5wwfxUAdLxgrfihw4AeUTLEfej7PkRb4fmKX5njSqFXtJsl3iBYFwX2CEgJ6ca+hAxDOMEIZ4s4xkmX6ia8L4uDBAOCwQAAogAjvo9Ciu5jRRJYEA4kkOBYEYd5SKyyDmvQ-hCM4wD6K4dDUHerqEhMOTTFSBZFhsLwVgRGLNtCtzfA8zw7EVBYVb8IoQkVjZIhAIEwuCrxYq8bXFR1wXbDieIEuMVhgJlLlto8uWMPlWzlsCaKEYwPXNT2uL4uNGUjRy0DTfc9VzW8C3rLVhaVXCTVLd1KJlatUBda1N2LRig0bSNW1EhNeQACx7WdB2FcdvUvD1p35VVryXSVkJPe1LxYq9w2fa6A7mNZ+LzAKjD+YFKhMl5PljhJ-7oWOjYvoKz4vtjAUWHjBPMsTaGjiyfTstlnIU0Kor-OKzEvDjdP495jPqZJpOs1E7OTVzUxUwLtOWMLhNMkzmnMqy0t5NAXPPBs8s07jyuixezOAZLbIpBz32y6KjwGxKfbunZPq1MGoREBAADKKaGrZLsJi6oCMDyXtiLkEAcEGxTUFgdDJfERoQPqhrGsAprmpa1q2va8AIMAAjUIqZZQHKCrKrH8euOMeoqAaRommaFpWjaOB2g6+eVwn4whxAACyuDhCGYgnFhgjOLX9dpxnzfZ23ToumAQA)

### 题意

实现二叉树中序遍历的类型版本。

例如：

```ts
const tree1 = {
  val: 1,
  left: null,
  right: {
    val: 2,
    left: {
      val: 3,
      left: null,
      right: null,
    },
    right: null,
  },
} as const

type A = InorderTraversal<typeof tree1> // [1, 3, 2]
```

### 题解

```ts
interface TreeNode {
  val: number
  left: TreeNode | null
  right: TreeNode | null
}

type InorderTraversal<T extends TreeNode | null> =
  [T] extends [TreeNode]
    ? [...InorderTraversal<T['left']>, T['val'], ...InorderTraversal<T['right']>]
    : []
```

> 答案参考自[解答区](https://github.com/type-challenges/type-challenges/issues/10399)

最重要的就是这段代码：`[T] extends [TreeNode]`，这里这么做是为了防止发生[分布](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types)行为。

一开始我是这么做的：

```ts
type InorderTraversal<T extends TreeNode | null> =
  T extends TreeNode
    /*
      这里报错：
      // 可能无穷的。。。
      Type instantiation is excessively deep and possibly infinite.(2589)
      // 这句的大概意思是产生了一个复杂的联合类型
      Expression produces a union type that is too complex to represent.(2590)
    */
    ? [...InorderTraversal<T['left']>, T['val'], ...InorderTraversal<T['right']>]
    : []
```

因为 `T` 可能是个联合类型，这时使用条件类型判断会发生分布行为，然后又存在递归调用`InorderTraversal` ，每一次调用都会产生分布行为，所以就出问题了：类型处理可能是无穷尽的，还会产生复杂的联合类型。

当时也没明白过来，然后就去解答区看了个👍多的答案：

```ts
type InorderTraversal<T extends TreeNode | null, NT extends TreeNode = NonNullable<T>> =
  T extends null
    ? []
    : [...InorderTraversal<NT['left']>, NT['val'], ...InorderTraversal<NT['right']>]
```

由于定义了一个新的类型 `NT` ，并且将其类型确定为 `TreeNode` ，所以后面直接使用 `NT`来获取对应的属性类型是可行的，不会报错。

在解答区多看了几个答案后，结合一开始我所定义类型中报的错，就明白了，所以不管是哪个答案，主要问题就是确定 `T` 的类型，然后才能进行处理使用。

## Flip

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/04179-medium-flip/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAsCMDsBOCBaCAxANgSwA6VRSOIICMBPDAQwCcALKgOwgGkqAXBgdysyogAUAAQDWHbryoBKCAGIAtgFMAJtgCu8uQHtSAK0UBjdgQKyzEAIprFAZ3bYtjE1ACS83JkVLG7CJ0V+5LgBWgBmEAAGump2KKE4uCg6+kYRAHQQAKIAHlTunjYAXM6RERHsQbYGNHjGUFh4ADwA3hBUhRAARNmdADQQpB2d5H0QBkMAXp0QAL4AfADcEMDAEM3ZHQDkVJv95FukuxATWwabMwQNuC1tHbD9gxAATP3jEADMs4vLq82wWzt+k8Dkd3qdzgR4k1Wu0IKFeDZFA8OuwaNYvksVmt4ZhEQCjqjrCCLlAyhESgA5LQQRiKFR+ak2NS4XBaGi+Wl2enJQzsGxtRjKCAAN141n5XDo2AMdDGTEYWl8pBCel5EBEinI-KZMra-NoNCoWpKcwgADVsIouBBHBAAOLYdgACTUjzo7HYuCKKz5MrSuhsaTZAHNgHAkGAQMAwDHQBAAPqJpPJpMQACaWjUNAgAGEtMoAk7FDQAimy4mIFGYxVghgEo0ACoQRTZdiKQX8gBKhjZykadhqjGD-SY5DmpoAvGsCABtAAKEGwzA15DCECbVH5EQAJM0G-OALozCIHjpzsAXWMgBPllPr2y+HOb2w32+pqvYdxs3w1gKtTIAI5qLw-Q5MERj9FS7CAcBmCzHCNBaJomxCL+KAyrwnhDrYwBqPYuKbNWlRyoi-JTjOBBgbyjQwbwNywpsuDYJssz9FcNxMfiXzjr0lHZOB7CNFBtGYDcjyMcxrF1tCECcRA2wsfMPF8QJNFAXRrTvGksDQFsTGbEshKKAcWhaJgilsfWrRyVpOlLKQpmYCiaIBEpcy8VAVFGGpsE3KKmDAvJuCIbgTwGSKvB6SFFnSdc1khVs-nhcFWihYlvBhdx7lgAeMZXq+b7xhgWb+NmADKbZegVb6VtGoAEKaZUMCWECrlmEA2GZeEOIwRQQO6nresAvp0P6gYhmGCCIMATA2FwxYNealrWp1mDdY4fUDV6hQ+jYfoBkGNChuG02retvWLQAsmyAQ5gwmBYcGtgdFtQ0jWNh3BpG0ZgEAA)

### 题意

实现类型 `just-flip-object`. 例子：

```ts
Flip<{ a: "x", b: "y", c: "z" }>; // {x: 'a', y: 'b', z: 'c'}
Flip<{ a: 1, b: 2, c: 3 }>; // {1: 'a', 2: 'b', 3: 'c'}
Flip<{ a: false, b: true }>; // {false: 'a', true: 'b'}
```

无需支持嵌套对象和不能是对象键的值，例如数组。

### 题解

```ts
type Flip<T extends Record<string, any>> = {
  [P in keyof T as `${T[P]}`]: P
}
```

> 参考自[解答区](https://github.com/type-challenges/type-challenges/issues/10420)

使用 `in` 遍历对象的键时，再通过 [as](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#key-remapping-via-as) 映射一个新的键，不就解决了吗？所以一开始我是这么干的：

```ts
type Flip<T> = {
  [P in keyof T as `${T[P]}`]: P
}

  /*
    所以报错了，在 T[P] 下面会有红色波浪线
    报错内容如下：
    Type 'T[P]' is not assignable to type 'string | number | bigint | boolean | null | undefined'.
    Type 'T[keyof T]' is not assignable to type 'string | number | bigint | boolean | null | undefined'.
    Type 'T[string] | T[number] | T[symbol]' is not assignable to type 'string | number | bigint | boolean | null | undefined'.
        Type 'T[string]' is not assignable to type 'string | number | bigint | boolean | null | undefined'.(2322)
 */
```

一般情况下，能作为对象键的类型为 `string`、`number`、`symbol`，所以通过 `T[P]` 去获取值类型是不对的，就报错了。

所以得将 `P` 的类型给确定下来，那么可以通过限制 `T` 的类型来解决：

```ts
// 使用内置工具类型 Record 来限制 T 的类型，这样键的类型就确定了
type Flip<T extends Record<string, any>> = {
  [P in keyof T as `${T[P]}`]: P
}
```

## 斐波那契序列

- [ ] 待仔细思考？？

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/04182-medium-fibonacci-sequence/README.zh-CN.md)

> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAsCMAcAmCBaCgE00Ec2hihMIqah4vUHQlSVFM8kgIwE8IB3ASwDsATAGwYEMmBzCACgACjVig7ceASggBiQLRygSW8IJEjLURAGRmA7txVQAkgFsADmwCmB00wAuEThB6XTAJwYBjCADEGlAPZNOrq4MADoAPAAqwQB8EFacANamAM62TBBMAK4GlM4Q4aksEE6mVhlOTCkMVgDkKa4+TsVJRn4szHwA2l6+-oEM6Vk5TgC6-AAWVlZGSQBcwMCWAHSM8QxGpm2ciw08wCsMwN1+AUEA+pnZzpKLunljphBJpgCOGZauD0lxTlazJLAAGgggIgiCBAGYgQBWIHwIGwSGgkHg6DQmEQeAATnh0FREEWBNuHgaEFMAA9OMYzCQAAZ0gBWSRIVmo6wgACVkhk2FZYBAALyebzHPqhcExeag5msh6cpLcqzIQVHXpBULwCXAJG0um3GKACnUIABxKpjDKUCCAKDlAKfmgGh3QBY-xMprN5r9XGNFoztk5dnAkMAAF5jFAAYQAcmAQMAwDHQBBTgnE0nExBAAbygBC3QDHcoBAD3jyfzpwgUdjIBAEEAnBaAO2NAA6mgB4FQD4roAEI0A4MaALO1AP6pgDK9dOAYUVACN+RejLLZRscTk4VlM4QyJlMoQAMqSyZPWCkLkMgflySuWGvBs4OsMBRBDzF+SRwh1qmZeFYxtUj9vLLuIIuAPx5CAzY1jidTmdmAuQIdASizhECAAMwxRAA3CWZZVoAyvqALJK1aEIAMP+AIXRgDp3oA0HKAABygCeGemtaDmAw4PCGnBsK43J-hES47nulxOKeAoXoxz4pCBBLcNQQLMAAZrkACCAlMMJTgQAAQsMJAfjxiyjkwzh-tOs6hCJnGrgMLEQB+WnfipABuzhRECoHKapk7qYB0naS+665B+9lGaYplOFEwxXjePB3g+JDfpe16WH597DHB5EykKPQnAwDFPjpTlOECWmJY5+6sUegodLA0HHiQIk+aF-mPsuXGfvJyhQFAaXlTpil8eJkkQAAokeJBQB+rXVTVbkeb134qnFETAaBYkQFRNF0ZOmlebBMZgHGBb5hAgDStoAq9GABSugDR8nmK0psWDDGA0NgURAADebWvNRQKtWS6yuDYAC+ECCU4PgGBA1SCBRKDutRvnJMAGRWAwbBJNUMbna4nBPCkOUkPdj1WKErU3WwoTDaK4pAogUTmUjD2mE9aMY1jwqqvFGp47ABMAmAcmLctB0pgRgCm1vtrNkaAJAxIAYEqANVyGaAMeRgAq3o6kzTHMwBuh6Xo7MAfqIMA3BJHQziBsG4Z8xAdri5Lzoy3LnpJN6voICrSQ+GwoMMH4TJQDEgAvZoAWJraIb0uukk7qm+bWuhhGxZgEAA)

### 题意

实现通用 `Fibonacci<T>` 接受数字 `T` 并返回其对应的[Fibonacci number](https://en.wikipedia.org/wiki/Fibonacci_number)。

序列开始：1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, ...

例如：

```ts
type Result1 = Fibonacci<3> // 2
type Result2 = Fibonacci<8> // 21
```

### 题解

```ts
/** 根据数值生成指定长度的元组 */
type GenerateTuple<L extends number, T extends number[] = []> =
  T['length'] extends L
    ? T
    : GenerateTuple<L, [...T, 0]>

/** 根据当前数列，计算下一项的值 */
type Calculate<T extends number[]> =
  T extends [...any, infer A, infer B]
    ? [...GenerateTuple<A extends number ? A : never>, ...GenerateTuple<B extends number ? B : never>]['length']
    : T['length'];

type Fibonacci<T extends number, A extends number[] = [1]> = 
  A['length'] extends T 
    ? 
      A extends [...any, infer E] 
        ? E 
        : never 
    : Fibonacci<T, [...A, Calculate<A>]>;
```

**逻辑剖析**

Case 如下：

```ts
Expect<Equal<Fibonacci<3>, 2>>
```

**1.** 代入到类型当中：

```ts
type Fibonacci<3, [1]> = 
  // 条件不成立 
  1 extends 3        
    ? A extends [...any, infer E] 
      ? E 
      : never 
    // 走到这，结果为 Fibonacci<3, [1, Calculate<A>]>
    : Fibonacci<3, [1, Calculate<[1]>]>;
```

**1.1** 根据上一步的结果，代入到 `Calculate` 中，计算出数列第 2 个的值：

```ts
type Calculate<[1]> =
  // 条件不成立
  [1] extends [...any, infer A, infer B]    
    ? [...GenerateTuple<A extends number ? A : never>, ...GenerateTuple<B extends number ? B : never>]['length']
    // 走到这，结果为 1
    : T['length'];

// 最终结果为：
Fibonacci<3, [1, 1]>
```

**2.** 根据第一步的结果，再次代入到 `Fibonacci` 中：

```ts
type Fibonacci<3, [1, 1]> =
  // 条件不成立 
  2 extends 3        
    ? A extends [...any, infer E] 
      ? E 
      : never 
    // 走到这，结果为 Fibonacci<3, [1, 1, Calculate<[1, 1]>]>
    : Fibonacci<3, [1, 1, Calculate<[1, 1]>]>
```

**2.1** 使用 `Calculate` 计算第数列中第 3 个值：

```ts
type Calculate<[1, 1]> =
  // 条件成立
  [1, 1] extends [...any, infer A, infer B]    
    /*
      走到这里，GenerateTuple 就是用来生成一个指定长度的元组
      这样子把 A 和 B 加起来，就是下一个值
      所以最终结果：[1, 1]['length'] = 2
    */
    ? [
      ...GenerateTuple<1>,
      ...GenerateTuple<1>
    ]['length']
    : T['length'];

// 最终结果为：
Fibonacci<3, [1, 1, 2]>
```

**3.** 根据第 2 步的最终结果继续：

```ts
type Fibonacci<3, [1, 1, 2]> =
  // 条件成立 
  3 extends 3        
    // 条件成立 
    ? [1, 1, 2] extends [...any, infer E]     
      // 走到这，最终结果为 2
      ? 2
      : never 
    : Fibonacci<T, [...A, Calculate<A>]>
```

最终结果为 2，符合 case ，测试通过。

我想出来的比较复杂，虽然做出来了，但不妨碍去看看更好更简洁的[解答](https://github.com/type-challenges/type-challenges/issues/4204)：

```ts
type Fibonacci
<
  T extends number,
  // 记录数列的索引，就是计算到第几个值了
  CurrentIndex extends any[] = [1],
  // 数列倒数第二个值
  Prev extends any[] = [],
  // 数列最后面一个值
  Current extends any[] = [1]
> = 
  // 当数列计算的索引与 T 一致时，就可以返回数列最后一个值的长度了
  CurrentIndex['length'] extends T
    ? Current['length']
    : Fibonacci<T, [...CurrentIndex, 1], Current, [...Prev, ...Current]>
```

## AllCombinations

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/04260-medium-nomiwase/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAsBMBsAGCBaCBBANpgwgewFsAjASwDsBDAFxLzIGdJUUXWmiBPCQW7DAFbUGkdQDPKgW0UIACgAC9AK4BzPACc8Aa2koOASggBiAgFMAJiWkEITJtssQAitL30adc1ACSBAA6Y9+slQhUOdz0IAAMwrFxCUkpHBgAeAGUAPjCQ-wALaggFPSppBTIICmwIAGMo8mpaBgg8ADMIBwVyWXoIAHd0klL0iGl6YJ6KBQpSqj0FNrqlU1SE1KK-AjwHWrJSvQA6ZwgAMUUIPQAPCg8vAC4d1KpGKACgjGx8YkrY+gB9dAAhHAgAXkekReMWq9DiAHJvjhwUkANxMYDARrpPDSTAGCBEYLg8EQAA+EEhuIJ4K+xMJ0PxhO+5MhlJJX3QtJ+tJwTKp4JwZI5UNp6C5zP5zLZrJpHK5TKYqR2SQgADUSHp2msIABxEhUAAS0iI5wg6SoVHc9HOiJuPU2ACt6JtFLJgHAkGAQMAwG7QBB3l7vT7vRAAJqohQQfAGYKaibBX3Rr0QF3upGAMB1AOragDJvQBMcoBaOUAIW6AeetAFRygB4FQD+8oAKVypgBG-QAQKoAEI0AKgFVwDePoBo9TA92CCSozTIslgAFUyNVEocjuMyAY2k0WrK-kwEiOxxPQgASADe5DqExDAF81xutwAlbchJgAfhDVM73d7A6HB6STD1ZD0ADcJvC24FghFntEqnQwTnBc9HHScuxaAAaJg+2A0DGnAnt-ggK8Wn7Qc6ESJIoKgGcIAAbT7ABdWClzw583wUQiz0JcFHxoqlV3wgBpCByAgIi9RCNcmN3VcfwqEEALiciJggiAAFEjlKTBpDDOI+zEpikiSY8IG3AjCI-MAPRjaMIAAFXsPwcAoAY2l03041dEgPEUPx2wgRjxIAR2kYoxMkoIxjUiBpkIQkJHbFAhmwEDZHsYBpBoTB6Foz8HlKUz7CQvCmE8vQxjiFy3MwOJ+OBf94hxLCaOU7CJKOLyqCy1zijyp4BMKsEiRKnEeRhLC0sqjLquyur8r-N4IRpVq+WZPluQZJkyq6qqapy+qgUG0FhpZUb2o5SaKQmvl6UJRlhVFVktshFkeQFTahU2kVxTFEkJQ68r0syvrcoG14VtOnAABEYTEtqSXZBlWVpX6eROq7AbBqbDs26GKSBikTp+0HEfBb6Tu+vavom+G6QhlGeQxvkscFbHGTxn40Z+Snvup0nbuR9A8Ye8UvhZunWWJjk6cxyHCQxtGMexrGhYuwGadx7H+XZ3buahs6ofF-b+Up5nybZSmfvp-nSVF0VZdu+WkeZ47OfFXmuYOnneRt5X0cZEWqdBtlMYemaqLdbSQE9Cy-V2fIqHSLdOz0Y1fb994rO9phZQSTIcggDgg0aPAZLePUDSNE0zXoC1rVtBR7UdRBgAoBh2gmWP5UVZV6DTqLQUzw1jVNYBzXSK0bTtB0EFL+v09BauAFlFGCHBMlCnt7Gb7O247rvC9kZ1XTAIA)

### 题意

实现返回最多 `AllCombinations<S>`使用一次字符的所有字符串组合的类型。

例如：

```ts
type AllCombinations_ABC = AllCombinations<'ABC'>;
// should be '' | 'A' | 'B' | 'C' | 'AB' | 'AC' | 'BA' | 'BC' | 'CA' | 'CB' | 'ABC' | 'ACB' | 'BAC' | 'BCA' | 'CAB' | 'CBA'
```

### 题解

```ts
// 将字符串中的每个值使用 | 组合成联合类型
type String2Union<S extends string> =
  S extends `${infer C}${infer R}`
    ? C | String2Union<R>
    : never

type AllCombinations<
  S extends string,
  U extends string = String2Union<S>,
> = [U] extends [never]
  ? ''
  : '' | {[K in U]: `${K}${AllCombinations<never, Exclude<U, K>>}`}[U]

/*
  使用示例：
  '' | 'A' | 'B' | 'C' | 'AB' | 'AC' | 'BA' | 'BC' | 'CA' 
  | 'CB' | 'ABC' | 'ACB' | 'BAC' | 'BCA' | 'CAB' | 'CBA'
*/
type AllCombinations_ABC = AllCombinations<'ABC'>;
```

答案参考自[解答区](https://github.com/type-challenges/type-challenges/issues/5339)👍最多的。

通过 case 来分析类型的执行过程，进行理解：

```ts
Expect<
  Equal<
    AllCombinations<'ABC'>, 
    '' | 'A' | 'B' | 'C' | 'AB' | 'AC' | 'BA' | 'BC' | 'CA' | 'CB' |
    'ABC' | 'ACB' | 'BAC' | 'BCA' | 'CAB' | 'CBA'
  >
>
```

**1.** 代入到类型中 ：

```ts
type AllCombinations<'ABC', 'A' | 'B' | 'C'> = 
  // 条件不成立
  ['A' | 'B' | 'C'] extends [never]        
    ? ''
    // 走到这
    : '' |
      { 
        [K in 'A' | 'B' | 'C']: `${K}${AllCombinations<never, Exclude<U, K>>}`
      }['A' | 'B' | 'C']
```

**2.** 以 `'A'` 为例，看看该键对应的值：

```ts
// 代入到类型当中：
{
  A: `A${AllCombinations<never, 'B' | 'C'>}`
}
```

**2.1** 分析 `AllCombinations<never, 'B' | 'C'>` ：

```ts
type AllCombinations<never, 'B' | 'C'> = 
  // 条件不成立
  ['B' | 'C'] extends [never]        
    ? ''
    // 走到这
    : '' |
      { 
        [K in 'B' | 'C']: `${K}${AllCombinations<never, Exclude<U, K>>}`
      }['B' | 'C'];
```

**3.** 这次以 `'B'` 为例，则其对应的值为：

```ts
// 代入到类型当中：
{
  B: `B${AllCombinations<never, 'C'>}`
}
```

**3.1** 代入类型 `AllCombinations<never, 'C'>` ：

```ts
type AllCombinations<never, 'C'> = 
  // 条件不成立
  ['C'] extends [never]        
    ? ''
    // 走到这，就到底了
    : '' |
      { 
        [K in 'C']: `${K}${AllCombinations<never, nerver>}`
      }['C']

// 则结果为：
'' | {
  // 由于 AllCombinations 一开始对 nerver 类型进行了处理
  // 所以返回结果为 ''
  C: 'C'
}['C']

// 最终结果：
'' | 'C'
```

根据 3.1 的最终结果，回到 2.1则有：

```ts
// 在字符串模板中，A 与联合类型进行拼接
// 则 A 会与联合类型中的每个类型进行拼接
{
  B: `B${'' | 'C'}`
}
// B 的最终结果：
{
  B: 'B' | 'BC'
}


// C 的处理逻辑跟 B 是一样的
'' | {
  B: 'B' | 'BC',
  C: 'C' | 'CB'
}['B' | 'C']
// 所以最终结果为：
'' | 'B' | 'BC' | 'C' | 'CB'
```

根据上面的结果，再回到第 2 步：

```ts
// A 对应的值处理完毕：
{ 
  A: `A${'' | 'B' | 'BC' | 'C' | 'CB'}`
}
// 根据字符串模板的特性，A 的最终结果为：
{ 
  A: 'A' | 'AB' | 'ABC' | 'AC' | 'ACB'
}


// 最终结果：
'' |
{ 
  A: 'A' | 'AB' | 'ABC' | 'AC' | 'ACB',
  // 下面这些跟 A 的处理逻辑一样
  B: 'B' | 'BA' | 'BAC' | 'BC' | 'BCA',
  C: 'C' | 'CA' | 'CAB' | 'CB' | 'CBA',
}['A' | 'B' | 'C']

// 最终结果：
'' | 
'A' | 'AB' | 'ABC' | 'AC' | 'ACB'
'B' | 'BA' | 'BAC' | 'BC' | 'BCA'
'C' | 'CA' | 'CAB' | 'CB' | 'CBA'
```

**总结**

- 首先将字符串 `S` 变为一个联合类型，通过 `in` 遍历，对每个字符进行处理
- 主要是利用[模板字符串](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)的特性，来产生每个可能性的字符串拼接组合

## Greater Than（大于）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/04425-medium-greater-than/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAs0EwFYIFoIHEBOBTAhgFywwgBUALHAO0hWVruoCMBPCAY1IGZWBrCgVwA2AiAAoAAuy69BAgJQQAxAFssAEwCWfJYpwYMOJtWoKTEAIp8sAZzzqA9lSjUAkhRKl1ViAGFyQrBQA5lgANBAAmnZ8EFakUQKqEOpKAA4CWCoUeBA4EHhMKVgQAAaYuAQYZJQAPMRhAKoAfMUQAurcRcXEEI0Q9cVGUAByWIH46gBuRfxKDIReqnYQFHbZFFhqeUtzbA5W6qqEagB0gxAAYnZEWAAeOKnpZ8XPeFbUZfiEVRTVcGEAjL1gMBYvFEjs8BhLO9sJ9KuQfv8AUCQXFBOCigAzHACKxYGHlL4I6r-AAMANJpJRoPREB22Nx+KgHwq3xJ-yREA51LRCTpRUh0KgzwGTmZdjsiQAMnweABCM69ABq6iwAHcIA50Oo8AAJPgMABcEFIeDwKSshuBr3YxwAVlZjldAsBYIgwCBgGBvaAIAB9AOBoOBiJRIjeSVFXVHf3BuN+iCen3ACCAbCVAF96gAA5QBUcoB-VMAZXqALjkSBBACFugGFFQAjfmB8oUIEMtHMMHBiHw0lhamEAEoQW4ECiqLwzJsAbQAuhAALwQce9afULsjgDk6SCeFIS4nfYCg5L1AA-BAe9RjQ3ZoQW230p2Z8c712wqSx40ANze2tFFlEmrdbcDoeNoQDS9jc-a7sOQEQAAgnoU71oBzatu2tSNHOEDUDB1ygTuXgjhBGBhHexzqBQmKEBAACiE4HuhUBQBRy6roE66biBYFePUtF0YegpFNQUDGl+8I-g0YQUY0J4QAyeJvt6YC+vGcYkNY2TeDgeJeIpwaJl6yQpFc2QfhAADelEAI58DiYk3IUrDZAAvlJGB2NoS5iB+yDsDiTHWMAfC2LiS7vgURSsOp1hwSO1AUTZWB2dUFEWTi1RCWynJUmEvGoSE0WxfFiWWQIKWwqyxIIGE0CNJlUJYNluW2XgCVJUVqXEtAYQIFVUk4nidX0XljUFclrU1OSEAZd1jJ9ZRA1NYVxWEsJPxwGNK1ddJtVVfVcWDc1C1wmlY1khNG3TTFDVzcNJXfoiHIAoC1WWHVY5yfJICxlpAYXHwGDruRADKBAWh9n06W91C9P95DYBATBhjEdgCP59gUJaJpmhaVrADapD2o6zquvACDAJQVhqoQEMQCq6oI0jth7Maprmpa1pWLaDpOhgLpusTViI8jeyUwAslcRS+N5ATBGjTOY6z7P41zHpemAQA)

### 题意

`GreaterThan<T, U>` 在此挑战中，您应该实现类似的类型 `T > U`

不需要考虑负数。

例如：

```ts
GreaterThan<2, 1> //should be true
GreaterThan<1, 1> //should be false
GreaterThan<10, 100> //should be false
GreaterThan<111, 11> //should be true
```

### 题解

```ts
// 创建一个长度为 T 的元组
type Number2Tuple<T, R extends number[] = []> = 
  R['length'] extends T 
    ? R 
    : Number2Tuple<T, [...R, 0]>;

type GreaterThan<T extends number, U extends number, Arr = Number2Tuple<T>> = 
  Arr extends [number, ...infer E]
    ? E['length'] extends U 
      ? true 
      : GreaterThan<T, U, E>
    : false;


// 使用示例
GreaterThan<2, 1> // should be true
GreaterThan<10, 100> //should be false
```

`Number2Tuple<T>` 用于创建一个长度为 `T` 的元组：

```ts
// 相当于 type A = [0, 0, 0]
type A = Number2Tuple<3>
```

执行流程如下：

![](../../\imgs\ts-challenges\ts-challenges-3.png)

## Zip

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/04471-medium-zip/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAs0OwEYIFoIC0CWAHSLn4NwCMBPCQWoZArhkFaGQQ4YIBHDAQwDsAPDCACgAEm2nAAzQEQgJQQAxAFsApgBMMAVxnSALsqwAbOblxTDEAIrK5AZ3UYA9q31QAkqwgAVABYZzEAMJvm23VYAczkAGggATWtlCHM3aO0FCAwZHTl5VnUIZgh1Eiw5CAADTCwAHhdwgFUAPiLwl2zWJKqIGWVLCCJCopctXSLcIuH1c1w8gog5diwIAF50bDKAbQRwgCYAXXDl9QAnM3CAM39zOU2aiGBgKZm5AGN1RVzrLsLl1fD9s22IZfXjqdzpshsN7BBLgA1DByADuEFsEAA4hh1AAJZREABcEDc6nUWHMWOuo3ubgAdAArczk6x7ILAWCIMAgYBgdmgCAAfR5vL5vMi0T2PmsCkKaLke0K-JlPIgrPZE0KpQqtyezS8bBIy1+rWm6oUmtY2ou81wjX1cg1fwwrCOkogADFwuTXbb7cKAKIgqAAflwevYBq8y3dDsdawgrvJYa9CE2EF9f2Wzqd8ZdrpVnvCnoQNQTOJ1uELIPZHJA3Nl-NcFiy3mYZy8VerCpSWDpWSVEAA3hBPQxlP4c3dHhAAL4QI57axqADkvCVyDJ-kCIXMwGUVm05lnivyhXuDYs8z+uE9I-UZX7g+0ZRVOp2F0fNRqoTPF6vA-8d6WnwgWx2b4wknIEn2TSMgN+f5AW3YEXzfKBzwKR5PxvH9yj-AEIAAZig2cEFncJZ3WWcwI+SN8NInYsOI0iLlfd9kMva9v3vKDIyw3DXz+eiEL7D8WNve9MM2KCuJ2ciNl+XDeLAUty0rZs5UdZQ9nUNwHQAZSeQlFKU+U2VAXBLk0vwpQgEghViaxtE3GxWCJXF8UJYlgFJClqVpelGXgBBgDYcxYUlYyIGhOFrNsqxbEcvECSJElzDJKkaTpBkmT88wbLs6KQoAWTpQpfBXK01xxWKXISpLPNSlk2TAIA)

### 题意

在这个挑战中，你应该实现一个类型 `Zip<T, U>`，T 和 U 必须是 `Tuple` 。

```ts
// expected to be [[1, true], [2, false]]
type exp = Zip<[1, 2], [true, false]> 
```

### 题解

```ts
type Zip<T extends any[], U extends any[]> =
    T extends [infer F, ...infer E]
    ? U extends [infer F1, ...infer E1] 
      ? [[F, F1], ...Zip<E, E1>] 
      : []
    : []

// 使用示例：期望值为 [[1, true], [2, false]]
type exp = Zip<[1, 2], [true, false]>
```

我的解题思路如图：

![](../../\imgs\ts-challenges\ts-challenges-4.png)

在[解答区](https://github.com/type-challenges/type-challenges/issues/5619)看到一个更简洁的答案：

```ts
type Zip<A extends any[], B extends any[], L extends any[] = []> = 
  L['length'] extends A['length'] | B['length']
    ? L
    : Zip<A, B, [...L, [A[L['length']], B[L['length']]]]>
```

- 主要就是使用 `L` 来存储每次处理好的值
- 而每次处理的时候， `L` 的长度刚好就是可以用来当 `A` 和 `B` 的索引使用

## IsTuple（判断元组类型）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/04484-medium-istuple/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAs0BzQgWggSQM4BUCuAHANgKaTJJnkkBGAnhAFYCWAhgHYDm6AFqxABQACjVh24sAtoQAuTAJQQAxBIAmDbGIWS8REiXl6IARWyF0khgHsWOqKjEFCElpIhMIk6rkIQABr4w57X28AGggAd04GAGNONyYAaxMXFggGFlxsZ3dPH19MIOSlCAAnKWxilnRwzika4tzvfN9Uqs17Nw9CADprCAAxc3rCAA8mOyIALl6g7JMo4oZcSRJZiCimdEIARggAXjQsLUIAHgBtFjVKQmKAXQA+CGBgN2LjFc61jcIAJj2DgKIx1KTCUlnwtHOl2u90ez0kr2IUFW602AGY-v4jscLmIrsVTjCnhAAGZMfCbEhBXoPABqDEIYQglggAHEGJIABLYSgTCCcSSSXDoCZPSToGJdOjoLqDNjAWAIMAgYBgVWgCAAfS12p12ogAE1zOUIABhcxKLwc65eXW2rUQZVq56AEiVALWmEBYhAAbtcIIBvH0A0epgVYYABy3uux0wD32p0wNwgI0khBYSiq5wjtwgAH4XsYILzSeTCABuJ0QN2AB1NACN+geDHwwAEFisUmNQo6EAKp-HF4mMQEiYRPDZOpqrN1vtzsPEhQXPdpMptMQTCnADkRHYkk4a4Tufh+cLZM2A6gR+LZfrOUx9ij-ZIYczd+Ho+XB68JFzRZPJF5TZbbbPouY55h+UBziSx5geBvJDsBy7AqCLDghApxdOhrDUHuoEFpBF6qmqICanauoriYzgml8VQkaRjoMHYgxZB8ADeEAAKIAI7YGSoRscMnhRM4AC+JLFOY6hrvwsxIDEZKbmwJjAJkDDkmuqrIlRfynCQfECZIxycdx+DHDegIEncoTvncFk6fxhCCQZXFkiZhy3pCuLQhZoHWcEtl6Y5RkuQCJyIWCEJbPclkIj5fn2fphnOaZJysfJ268jsQled+hAxVAulxQFiWuYCvbXOZoTZbl7F2Q5CXGUl2KZllUExTcBFgOqNE6v05Tbr6ADKyZCsRXUag6KqgCQDz9dwpQQNQRr1Og5j4MpljCnyApCiKwBihKUoysUcoKtAwCsOgYTXFNEB0gyEDLatZjrby-KCsKoripwkrSrK8rwKdD1rZU10ALKDF4JrcPg8kmC9W3vbtn3fYdbBKiqYBAA)

### 题意

实现一个 type `IsTuple`，它接受一个输入类型 `T` 并返回是否 `T` 是元组类型。

例如：

```ts
type case1 = IsTuple<[number]> // true
type case2 = IsTuple<readonly [number]> // true
type case3 = IsTuple<number[]> // false
```

### 题解

```ts
// 判断 never 类型
type IsNever<T> = [T] extends [never] ? true : false;

// 判断数组类型
type IsArray<T, U = number> = 
  T extends Array<U> 
    ? U extends T['length'] ? true : false 
    : false;

type IsTuple<T> = 
  IsNever<T> extends true 
    ? false 
    : IsArray<T> extends true 
      ? false 
      : T extends readonly [...any] ? true : false;

// 使用示例：
type A = IsTuple<[]> // true
type A = IsTuple<[number]> // true
type A = IsTuple<{ length: 1 }> // false
type A = IsTuple<number[]> // false
```

我的解题思路就是针对每个类型进行判断，层层过滤，然后得到答案。

1. 使用 `IsNever` 判断 `never` 类型

```ts
// 为了能正确获取到 never 类型
// 所以将 T 变为了元组类型
type IsNever<T> = [T] extends [never] ? true : false;
```

2. 使用 `IsArray` 判断数组类型，顺便也排除了对象 `{ length: 1 }`

```ts
type IsArray<T, U = number> = 
  T extends Array<U> 
    ? U extends T['length'] ? true : false 
    : false;
```

- `U` 设置默认值是为了正确判断空数组。如果没有默认值，则 `U` 会是 `never` ，最后结果也会返回 `never`

元组通过 `length` 属性得到的结果是数值，而数组类型得到的会是数组元素的类型：

```ts
// 结果为 3
type A = [1, 2, 3]['length'];

// 结果为 number
type B = number[]['length'];
```

3. 到了最后判断一下是不是元组类型就好了

```ts
// 通过 rest 元素就可以判断啦
T extends readonly [...any] ? true : false;
```

我在[解答区](https://github.com/type-challenges/type-challenges/issues/4491)看到了一个非常简洁的答案：

```ts
type IsTuple<T> = 
  T extends readonly any[]
    ? number extends T['length'] ? false : true
    : false
```

## Chunk（lodash.chunk）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/04499-medium-chunk/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAs0JxwgWggYQBYFcB2BrSySRxBARgJ4SC1DIFcMgrQyCHDBAI4CWAhtgB6sQAUAATaceABmgBGUQEoIAYgC2AUwAmrTAvkAXTAAcANkoIE5piAEVMSgM5bWAe2zGoAEXsRy9zBFzZ7AdwgAA317FXZrdCCAfmCMHFwgiFZrCHYIADclACdKTGslADNMfQhi7ABjO0dk7GStABoIP0DDLQByVNYFAyVlbC16gDoCIPi8AB4AFSaAOQA+JPYKiqVdLVStf3dspWZMVl2VCC1yXSUIXXZs9mUtHOsmrXQLoKmkhXzB0gv0oJ1ekEmpxjs9XrMPl8ID80nUgqwBkoAOY5CDzAC8EiCzmCQX+1gIp3OECUXF0Egg6LQWEmAG0JE0AExNADMAF0mfMIMBgCSyUoqqoTu4YbT6UyORBaey2YSzhdSbpGZTqQkJuKIMyIOymtAuTy+edBaCRRcxQzNay2bKoESFWSWSrxrh1RatTqIBJ9bzFQL7iboWb6ZLaYyQzLRnicVyAGqsJSBGoAcVYWgAEphSAAuCDoLRaXTWLM8jYVdBDABW1iG9mySOAsAQYBAwDAbdAEAA+t2e72exAAJpebJoMIXNM5C596fdiAtttgO2qyZTPn3bAqVKcci0yUAVTXSg3qWwGh+2SaAEFD8fYTu2Srd1yqQRV6T15upQjCqiAGJNIZAO-VEAFEHwIWJL1pdpDGwJFnnaB93yPT8DwIKAoFiWlLwAwDnQmECmj3JpaV-Nl5nAjCMJzfDCIgYipUAoYcIgMiuQIHNr2Q29dwgLCHxzbDZXbEAuxnPsICmGxBlQCIbDE8T+3nbpdFrQYlwAbwgED9nYfQmhA-kqggABfMpsnsTR2gEO0kDLPTYJRaxgEwOx9Gsdo2yXCo5NSKlaQIQyjS0AjdP0CZ8N3JovRI8j5gaQKjJCnTMD0iKaRdDV3UlGKpWDEiwxImV5nixLgtC1Lwsit0rU5EisvDa0SoSqAgr9Cq0uqplWV1SVGXivK3XDXq4pa7Sko6qqMtdbrtV6poAFYBvNWaWRG5qyvalLOumjUtGyKxZsKPSCj65a9oOpQQy1Y73Ku0awGEkSFMUztWMwbIwRHABle5CxexS51bUACC5b70GuC5PA+iBrHsfRXIcbAi1zfNC2LYBS3LKsazrBt4DgYBOGsfwclBiA4wTWH4cRxwUbzAsixLawy0rata3rRtCbhhHqmR8mAFlawuDAHKPJycwZ9HmdZnGOebVswCAA)

### 题意

你知道吗`lodash`？。

`Chunk` 里面有一个非常有用的功能，现在我们来实现一下。 `Chunk<T, N>`接受两个必需的类型参数，`T`必须是个 `tuple`，并且 `N` 必须是 `integer >=1` 。

```ts
type exp1 = Chunk<[1, 2, 3], 2> // expected to be [[1, 2], [3]]
type exp2 = Chunk<[1, 2, 3], 4> // expected to be [[1, 2, 3]]
type exp3 = Chunk<[1, 2, 3], 1> // expected to be [[1], [2], [3]]
```

### 题解

```ts
type Chunk<T extends any[], U extends number, A extends any[] = []> = 
  T extends [infer F, ...infer E] 
    ? A['length'] extends U 
        ? [A, ...Chunk<E, U, [F]>] 
        : Chunk<E, U, [...A, F]>
    : A extends [] ? [] : [A]


// 使用示例
// 结果为 []
type exp1 = Chunk<[], 1> 
// 结果为 [[1], [2], [3]]
type exp3 = Chunk<[1, 2, 3], 1> 
```

直接通过一个 case 来了解整个过程：

```ts
Expect<
  Equal<
    Chunk<[1, 2, 3], 1>, 
    [[1], [2], [3]]
  >
>
```

**1.** 代入到类型中：

```ts
type Chunk<[1, 2, 3], 1, []> = 
  // 条件成立
  [1, 2, 3] extends [infer F, ...infer E] 
    // 条件不成立
    ? 0 extends 1     
        ? [A, ...Chunk<E, U, [F]>] 
        // 会走到这，结果如下：
        : Chunk<[2, 3], 1, [1]>
    : A extends [] ? [] : [A]
```

**2.** 代入结果 `Chunk<[2, 3], 1, [1]>` ：

```ts
type Chunk<[2, 3], 1, [1]> = 
  // 条件成立
  [2, 3] extends [infer F, ...infer E] 
    // 条件成立
    ? 1 extends 1     
        // 会走到这，结果如下：
        ? [[1], ...Chunk<[3], 1, [2]>] 
        : Chunk<E, U, [...A, F]>
    : A extends [] ? [] : [A]
```

**3.** 根据第 2 步的结果代入类型 `Chunk<[3], 1, [2]>` ：

```ts
type Chunk<[3], 1, [2]> =
  // 条件成立
  [3] extends [infer F, ...infer E] 
    // 条件成立
    ? 1 extends 1     
        // 会走到这，结果如下：
        ? [[2], ...Chunk<[], 1, [3]>]
        : Chunk<E, U, [...A, F]>
    : A extends [] ? [] : [A]
```

**4.** 根据第 3 步的结果代入类型 `Chunk<[], 1, [3]>]` ：

```ts
type Chunk<[], 1, [3]> = 
  // 条件不成立
  [] extends [infer F, ...infer E] 
    ? A['length'] extends U 
        ? [A, ...Chunk<E, U, [F]>] 
        : Chunk<E, U, [...A, F]>
    // 会走到这，结果为   
    : [3] extends [] ? [] : [[3]]
```

将所有结果进行代入：

```ts
// 第 3 步结果
[[2], ...[[3]]]
// 等于
[[2], [3]]

// 第 2 步结果
[[1], ...[[2], [3]]]
// 最终结果等于
[[1], [2], [3]]
```

我的解题思路如图：

![](../../\imgs\ts-challenges\ts-challenges-5.png)

## Fill（Array.fill()）

- [ ] 待仔细研究？？

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/04518-medium-fill/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAsCsCMAOCBaCAxAlgG25VKhR+ARgJ4SC1DIFcMgrQyCHDBAI6YCGAdgB6YQAUAAiw7cADNHgiAlBADEAWwCmAE0wBXObIAuqgA7YF+fDOMQAiqoUBnTZgD27Q1AAGWXE4A0EVhADGtuXL2EABSrABurADKPgBOmDqaEABmquw+Nvae7LYA7hD6iaqWEJhyegqK7ImYiTk1ABYQmmQ6VgB0+C442AA8ACqeAHKekZqsMZoA-J4AouxKkwB8Hl7FZLaqvhwQlgoK7l1uXj4+CgnFSRsxTS1WELZJEDrjrIqaCjGWng8QOfWYPkaTj6Ti88wgTkGoPGCggMQUTFUmHhSieLzeHy+YNRTlG4000PBTjmSmh8PuCTs7FY2DRMVeCnenw6UD69Vh8MRyIqCiqF1s1007N2dIZTOKMIAXBCQRA5EVEiRYd4nNpyitIaCfNslWCKM1WvdHhFsBZPLixhNCTiSaD5dYILrMFUFABzTEQV3w1hMpr1bYCiAI1Q0pq2CAiFkQTpOVWWfAG2EKTg6CAAXgw3R6AG14J4AEyeADMAF1PCJFhBgMAgymFOllGHHbDsyJy+WSzGnPgAJLse4xJQfJuWUqqbA+2FCjkKUMpNIZdieadNKyJOSsCh+KqsZ07fywkgbebjLf2FSLrE9iD1WyG9abbX93kAK1sFBqEElkkcEErADVMAUPIggAcRqAAJVQSGlepNE0HRLElatNEsQE2hfSw2gFV1gDgJAwBAYAwBI0AIAAfUoqjqKoiAAE0rggABhWwhwgCCPlhGjuMoiAiNImtAGwlQAvvUAf1TADK9QAuOQgQYIEAELdAAdTQARvzARMIAAISRbAlAAQRiekyB6WTk3eeZinYdQlRiTw+lrUylAldgyGzEt0wgFzKwzPpswAcn0dhXSFHzXJM3kHJkiBJggWzpU0nBdP0zcjM8bM2jSgYIxLRYAG4BIgQBNv0AYUUlOiiBAG-bQACpUAADlACo5QrAAJfQAYf8AWMVABX4wA9tQk1TbggHSlCUAB5dgFH6OywvMyyPk89y0raOLtL0gz+kWDtfP8wL6mCki1NcXp8Fs0KzIgVIAGtshydgXPcfBhnwPEJjGo6LLkKy3LbfASUe8Lntery1t5DbguuqAAFUvuKU7zsu1yMyu-BPP28H3OdJJhywT5NE8WaUeHAAlNdOygKL8Cge7EkO8LPpJyKZrSkGsfSwmoC-CAQf+gKgpCzh7OKMnqaJzNcB6fHrCGTw+sG4aejJysADIIB+j5ZnmFLZvpmSsv5lnduFtcxYgMnlaUVW6c8dHrE1qBpRBkiwDInjuOitdmNYXZigdmi+OI0odAFRI1IAbwgGZERpWY63SCAAF9khifwIB8-hExQQEaXWqxgFUGxsEsHztp67U3bc7MPojzQehDkNeh1q6IxW9yspW0vWnSCvQ+rrNa7bCNi3rjym6gGYy7bquehrvMIELCBS3bHu65Siep9LRYB+D4fK5pMfO8X4syznqf8z7nfp8b4G15b8uN47oXcwLXfyz77un9P5v60v9ut5v4+Z6aGILD7zQf99i-zNCAhQL9B7rw-uPO+J9lxANnvAABCDJ67xXmfIeF8R6bxgaguBYDPATyLEfeBoDAEWAgefN+2Dr69Fvngn+5DgESAfgvWBy9V6YOoVfT+dDv57yYYgisKVBEELAS-Tstt7Ye1ougVQEx2TXFGGcd2MjeL8VAPDA2-pyQPmuJYWwppLywXgohZCwBULoUwthGIuF8KIGABwSwOQPhaMAsBfcRiqRIRvKYpCKE0L1AwlhHCeEEAOIMV4+w8YoCVgALIClhExf0uAAZWBMQhfxFjAnBJsa6QixEwBAA)

### 题意

`Fill` ，一个常见的 JavaScript 函数，现在让我们用类型来实现它。 `Fill<T, N, Start?, End?>` 可以看到，`Fill` 接受四种类型的参数，其中 `T` 和 `N` 是必填参数， 和 `Start` 是`End` 可选参数。这些参数的要求是：`T` 必须是一个 `tuple` ，`N` 可以是任何类型的值，`Start` 并且 `End` 必须是大于或等于 0 的整数。

```ts
type exp = Fill<[1, 2, 3], 0> // expected to be [0, 0, 0]
```

### 题解

```ts
// 创建长度为 N 的数组
type BuildArray<N extends number, T extends any[] = []> =
  T['length'] extends N
    ? T
    : BuildArray<N, [...T, 0]>;

// 给元组 T 添加一个元素，再返回长度
type AddOne<T extends number> = [...BuildArray<T>, 0]['length']

type Fill<
  T extends unknown[],
  N,
  Start extends number = 0,
  End extends number = T['length'],
  U extends unknown[] = [],
> =
  T extends [infer First, ...infer Rest]
    ? Start extends End 
      ? [...U, ...T]
      : U['length'] extends Start
        ? Fill<Rest, N, AddOne<Start> & number, End, [...U, N]>
        : Fill<Rest, N, Start, End, [...U, First]>
    : U


// 使用示例
Fill<[], 0> // []
Fill<[1, 2, 3], 0>    // [0, 0, 0]
Fill<[1, 2, 3], true, 1, 3>    // [1, true, true]
```

> 答案参考于[解答区](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Ftype-challenges%2Ftype-challenges%2Fissues%2F8247 "https://github.com/type-challenges/type-challenges/issues/8247")

- 其中占位类型 U 是用来存储迭代中处理的元素，`BuildArray` 与 `AddOne` 只是工具类型。
- 当当前索引位于 `Start` 与 `End` 之间时，则使用 `N` 进行填充，并且 `Start` 要加 1，再继续进行迭代。
- 当 `U` 的长度始终不等于 `Start` 时，说明 `Start` 大于 `T` 的长度。

解题思路汇总成一张图：

![](../../\imgs\ts-challenges\ts-challenges-6.png)

## Trim Right（删除结尾空白符）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/04803-medium-trim-right/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAsAcAMBmCBaCAVATgSwLYQCVsBzACwBdJUUbaqAjATwgE0BXYgQwDtiIBhTgHsIACgAC6TgBtpAK04BjANYBKCAGJAtHKBJbwhUqGoxEAZGYDu3A1EB52oAbnCAAMseImXIAedAD57EQDD-gYO1AUuNANlNAOw9ALO1AELdAdW1AMm9AJjlAbx9AaPVANz1AFfjAPbVAADlAKjlABtMYhN9AN0UtfKyi+MACJUATNMAwuUB85Tj4wGW-QD4dSMAvL0BfNxbAIAZLCEBo+UAgzSH7SfIAZypyRgAHAFMMHFwlgBMIAF5VvAAZJYAzdwByKAAJJdkRAHUhTGktiFPPCGBgCEAUvUAK40Af7UAX4ovS7XaR3B5PU5USb2IZvQAU6hAAOLYcikNj0CCAKDlAKfmgGh3QBY-xRyAtpgAuD4zRSkAB0cmmNIexGAcCQwAAXqQUPwAHJgEDAMBC0AQAD64olkolEEABvKRQDHcoBADzFUtVoogAqF82WEAAygslCtdudzgAfF4AHW4Zst5FOAG4tYsVk5cC4KG5dRAlgAPchLbgbaYQabkHC8N67L2+-2B4P2AAkAG9sNwjktMIQAL7J-WGrM+AD8ezdJA9BDeZL1jqFYBFatVEEA0raAVejABSuwxVDelmrwCwe5Ag2pWSYgAFEAI5sGQAGnHPuWikHWYgR0wQnwp3Ew5Q1Jk0gDxCW02AbHI2Gk0yhYGHEEUnGmx52EAA2lQxwulku3JPp9IPGs7pnKGmCvHOpwga8ngzu+n7fr+MgAc4ZbAWGLzQS8kGeNBsGLu4CH-q6QFuBBaFQFAYGYWGUEwVAH54T+U6IURKEkeRUAgex6HgVxWE4XRcH4UxhGAax5xQEcQgiPQnCZhAVoWoOlHiauUkQDJoHYbR84MQRSGlq4JHKTRuFfkJf76cRpwKUpGGnCZAC6tZ1iAXbduqgDQctkgCm1m53YaoKoBUG8gBgSoA1XJyoAx5GACreRLkCS5KUtM1J0gyTIsggiDADw0wAO4ZhyXK8sFED4jFcUJRSwBUrS9KMpgzKsll0xCNIZ7YEI3CzFAbyAC9mgBYmuYFWklVNWpfVzKctyfKamAQA)

### 题意

实现 `TrimRight<T>` ，它接收确定的字符串类型并返回一个新的字符串，其中新返回的字符串删除了原字符串结尾的空白字符串。

例如：

```ts
// 应推导出 '  Hello World'
type Trimed = TrimRight<'  Hello World  '>
```

### 题解

```ts
type Space = ' ' | '\n' | '\t';

type TrimRight<S extends string> = S extends `${infer R}${Space}` ? TrimRight<R> : S;


// 使用示例
// 应推导出 '  Hello World'
type Trimed = TrimLeft<'  Hello World  '> 
```

> 跟 [Trim Left](https://www.yuque.com/liaojie3/lxxhgu/yzugax#gyztQ) 的逻辑是一样的

使用[模板字符串](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)来匹配 `S` ，从而将尾部符合 `Space` 类型的字符串一个个地干掉。

## 去除数组指定元素（lodash.without()）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/05117-medium-without/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCsCMsHYIFoKG7lQJmmAdTQI36HBjQLO1BhRUAJfSFZSq8gIwE8IAFASwDsBTAQwAduAbdhAAUAARYce-dgAYAnLFkBKCAGJAtHKBJb1WAVAMAQKoG8fQNHqq3OXIqLEQBkZgO7czUQHnagBudAAHKAqOUDzChAAyAewAmnADOABYAdADuzAAuIb4ArtEQgL+KWC6A7BaAIW6A2zbGAOoxcYkAPAAqADQQAKoAfIAw-4DB2oClxoBsprhGmRClEIAxKliA6tqAaEbthp1VEIBccoBDylh1gFjygCvxgHtq7pjVEGqkmbhdgEAM9hCA0fKAQZqAWP8HAAZX0UHk0XTcggBK7EEQALwQBbEJ0cUA2rBKgAmAC6lVgNQA3BBgMAIOwAB6PADG0XY-gg0V8EBogn+YLuD2er1gHy+hV+AKBEGBlQALBDKtBwRBASDQdDYfCkaj0Zjsbj8YyYKCiY8IC8gsDyd8in8CZUAMwg5WqiAq2nK1mKjWcmFwhHI9hojFYnF4tliqBXC4HGoQQAU6hAAOKFeI0CCAKDlAKfmgGh3U4haLRbhBABccJuKPCACsgmFfAAnADmwDgiGAAC8QsgAMIAOTAIGAYFLoAgAH0q9Wa9WIIADeUygGO5QCAHpXax2KxBi6X7hKAGqcPjxdgAeQAZmUHZ9ukj0ax-G9-mEV5xWHRQRAAPxdf6seIAWzxic3Ya6UN7xIpPxKFXWc-YC7e+6P7ET-03AB8IC-j9OIOQs6IvOi5smw45vhAABilQrmE4GQQAotaUDbtBRogW8g7DmOk61OQqFocuK5ylSiGVLUKGEWe-wwRAcGkSU5HVDUKFnqUF5liA7adjWECANK2gCr0YAFK6HDxvFVt2JbMAe3BJkkfaCAA3hAiEAI7xEOlSIcaaIQAAvhA46Jr4B4QAA5CIinINGQ4CKwyavMAiTMHwQTmZeEoosErzkv85A6XyxTqZpfDFIxCo0mCEI1JUBKcrFAW6X8IVDuFlIlOyWoQCKNIsnFUWcnFIosjUiVQIFJopRpaURQCdIauqmoNc12pxS1RVWmV5RgGKpZgOWEl8YA0HIuIAptbiRJUkDeAUAOoAYEqANVyjaAMeRgAq3oGwahhGwBRrG8ZJqm6YIMAa5BBEb5ZjmBbkA6-rrZtIbhpGQTRmEcYJimabwCdQS+MO0TML4rC3HNECAC9mgBYmrYj3bS9b0fYdV15oWPZgEAA)

### 题意

实现一个像 Lodash.without 函数一样的泛型 Without<T, U>，它接收数组类型的 T 和数字或数组类型的 U 为参数，会返回一个去除 U 中元素的数组 T。

例如：

```ts
// expected to be [2]
type Res = Without<[1, 2], 1>;
// expected to be [4, 5] 
type Res1 = Without<[1, 2, 4, 1, 5], [1, 2]>;
// expected to be [] 
type Res2 = Without<[2, 3, 2, 3, 2, 3, 2, 3], [2, 3]>; 
```

### 题解

```ts
type ValueOf<T> = T extends [...any] ? T[number] : T;

type Without<T, U extends number[] | number> = 
    T extends [infer F, ...infer E]
        ? F extends ValueOf<U>
            ? [...Without<E, U>]
            : [F, ...Without<E, U>]
        : T


// 使用示例
// expected to be [2]
type Res = Without<[1, 2], 1>; 
// expected to be [4, 5]
type Res1 = Without<[1, 2, 4, 1, 5], [1, 2]>; 
```

- 迭代数组 `T` ，对元素 `F` 进行处理，符合条件就保留，否则丢弃

- 然后调用 `Without` 继续处理下一个元素，知道迭代完毕

解答思路如图：

![](../../\imgs\ts-challenges\ts-challenges-7.png)

## Trunc （Math.trunc()）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/05140-medium-trunc/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=36&ssc=16&pln=36&pc=30#code/PQKgUABBCsCMAsAGCBaCAVATgVwHYGNJUUTSiAjATwgCsBLAQ1wHMBnACyYgAoABepm064AtgFMALgwCUEAMTiAJnWwj5EsSIAOAGwYaUOuhswMdRInKsQAitjGsJdAPa4LUAJLadmsbgkQEuxigZRaIQBuYpisLrgQzgBmEAAGaQCy+uwAdBI4BGkpADQQAO7sdPjsgQwA1g4Qjph0LAmYELiq5NEQTIoQmJLYmLisgcEQLRrMPVoMmAFJvR1dPVQDms4RLcy9uNSJpvhOrmYQyszGrNnuEABizu1iAB4M3mIAXLeFEmEO+M0tBIiL9whAAIIQAC8GHy+AAPLAAEzZADM8AAfBBgMAIMiiIVbliAGp0MSlBLxADixgAEthyB8IOwJBItKwPjiJKwqtkaNdHsxgHAkGAQMAwJLQBAAPpy+UK+UQACazmGEAAws5FCFadEQorDXKIOLJaCQlg8AiAMoQF4aXCKMZNHYQAA+KxE3UwWJhBIAJABva0AXxSdueDqdqSDLUSPTuIeyQZdLDDRAA-PciEyUkHQykANySsDSo2GjAOAIahisBrlxUmiV0bSPALmiCBiAAUQAjtgzCVu89wscICGIIdnGoAOS8c0oKpmHwsBzAbBOHSsGdmv4QfC1howgDaRGHo4k8L7A508MtBHhiGysAxJRniBnGNfZ5HYmOV-7Mw7zhRFsiRdFXwgGdYE-b8oHPP9L2vID7wRZE0XgaBIOgpFYKKH8LwAm9gKteEUGgZ9sPIvCCMQoiUJA6CwPRT83xgr98Pg39-2Q29UPhGcUFgJ9wPgVioKEj8ONonjAL4kDhOw4SaIAXRLUsQFlBslTuYYgh6a0NHZLTtJlJsNKILFrU4QYIEoNV2lYZwdA3OIOWZVl2U5YBuV5flskFYUEEQYAmFYUpoksiBSXJRpnNc1x3JZNkOS5Hkcn8wKRRCpyXJOUYovSR4Qg1TgdBXGYks81KfPSvkBUwZgxQlMAgA)

### 题意

实现Math.trunc的类型版本，它采用字符串或数字，并通过删除任何小数位数来返回数字的整数部分。

例如：

```ts
type A = Trunc<12.34> // 12
```

### 题解

```ts
type Trunc<S extends string | number> = 
    `${S}` extends `${infer F}.${string}`
        ? F
        : `${S}`


// 使用示例
type A = Trunc<12.34> // '12'
type B = Trunc<'1.234'> // '1'
```

- 先将 `S` 转为字符串类型

- 利用 [模板字符串](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html) 的特性完成小数匹配

- 根据判断条件返回对应的字符串即可

## IndexOf （Array.indexOf()）

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/05153-medium-indexof/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCsCM0GYIFoIEkB2ATApgDwHkAzSFZci0gIwE8IAFASwxwEMAHdgGxwgAoAAkxYduOAAwBOWJICUEAMQBbHFkYBXJYtYAnHaxqlSCkxACK6nAGcALowD2GI1DRKxKjDYg2AFrxs07LwAbjg6Vg4YEPZEEACCegYAdMy4hEQANBCp+MQAPAAqWQCqAHzerADW1hCsUQn6dEW1GHTFLVgQOjg26joYVt5+2dj40bG+vESM4V7tzPGJTUnOEAAGGzZWpAFBEABKNQC86KPpeQDasFkATFkIALq3pQDcEMDAEPhBAMY2qt57BAqLxYDtArxDlZYBATpg0vkLncAGz3DIADgyABYMtcAOxoyRPCAIV7vT7fHB-AE2IEgiA3cF7KE3WGnBFES7iLLciDiYk3MkfL54X7-Tq04G8ZBgqAbNarcoANUYOAA7tEogBxRg2AAS6ioAC4ID4bDZ2FYjR8tj8fEkAFZWJL2HQAc2AcEQYBAwDA-tAEAA+iHQ2HQxAAJr2PoQADC9lwED1YV44fTIYgvv9u148NynOaxSy+xF-2wgzqNAuDzZNfKJ1IBTLOArEAuzCIYQgADEskkB53uwBRB6kKAAfggw4AjupWFw8n2IGUW22bDpLOOoBAp-sLgByHgYN2+A9jnc7k3587DkpZC4DpL7HkPUqkE0yl7+sCBjPpiACmsLw41YKwan-cMsz9Rg3FdLxcwgABvac5wXLJh1FKkvAAXwgIgdHsLQDwEXNkDtBdjzdaxgHUOwuCsA8cwhCAfjA4521ITCxTyWd50XG9EWuBl7gFUoslgUpxK4rC-l4tCBLORE7ggVESSyTEIBxCBhIJdSICJe5xIZKSMhkni+IXPJBM5C5eXssSshlUzzOw+T+OspTbNsHRmDdCSsgwTQQR0LID1Yc9AuCsJjMFaSoG4tzLMUjlLh8vyAogIKlBCsKIqyKtiSrYysRcscfz-SCIx7PpJh0CAAGV-ktYMqszbNQFIcoGp8XReBoGN6qsewuDoyIrVNc1LWtYBbXtJ0XXdT14AQYA6isNUwi6iAVXVCBhtGuxHAms0LStG0rDtR1nVdD0vVWg6xuO7aAFlXV4ONeq4KjrBNU7pouq6Ftun0-TAIA)

### 题意

实现 `Array.indexOf` 的类型版本，`indexOf<T, U>` 获取数组 `T`，任意 `U`，并返回数组 `T` 中第一个 `U` 的索引。

```ts
// expected to be 1
type Res = IndexOf<[1, 2, 3], 2>; 
// expected to be 2
type Res1 = IndexOf<[2, 6, 3, 8, 4, 1, 7, 3, 9], 3>;
// expected to be -1 
type Res2 = IndexOf<[0, 0, 0], 2>; 
```

### 题解

```ts
type IndexOf<T, U, R extends any[] = []> = 
  T extends [infer F, ...infer E]
    ? Equal<F, U> extends true
      ? R['length']
      : IndexOf<E, U, [...R, 0]>
    : -1



// 使用示例
type Res = IndexOf<[1, 2, 3], 2>; // 1
type Res2 = IndexOf<[0, 0, 0], 2>; // -1
type Res3 = IndexOf<[string, 1, number, 'a', any], any> // 4
```

- 我使用了 `R` 来存储当前数组迭代的索引
- `Equal` 就是使用挑战中 `@type-challenges/utils` 导入的，用它来进行类型的严格比较，从而判断是否为同一类型
- `T` 没有值或者迭代完毕，则直接返回 `-1`
