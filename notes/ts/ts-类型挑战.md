# ts 类型体操挑战

## 简单

### 实现 Pick

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00004-easy-pick/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAsELQUHnagG5wgBQJYGMDWl5yFH4BGAnhAIIB2ALgBYD21FAYgK4QAUAAgIZ0AZuwCUEAMSAA70CqyhPbUMzCSXYYANrTgZq+fOP0RAGRmA7t11QAfBEBE1oDn49ACUIgTlNA2-GBv-0CL0YApYwPOJgIAYzJFQAFQBlCEBQxUA7f0AQtwgAA0xcAB5ggBoIAGlzeMAYf8AxeUBYOUB75UBTuUB-eUAKV0Bg7QCofBAQQDm5QG8fQGj1BOD4iEBaOUBIBMBLo0AvxUA9HUByAwTMvMAQ80ACBMAEI0AAOUAqOUAG0xj2xrqIfEBo+UAgzUAsf8D409oAZ3xtWgBTACdBPiwbiGDGABNGCABvfChaDC0NQ3ABcEHOtDu2gA5n8IO8bucsFCAA4A5hgiFQ6iwqBQLCMAC2KOBt3eYJIjEYwIE+AAvoFaGQUS83p80HcbgA3DA3ADuEAAvBAALJkJI4VIfRgZADkAKBN1lEAAPhBZQTiaSbu9ZeZAgTqBCILRpWC2YwOdzeQLhb88XiFcCwbKAMI06gQO5UwmytJw-FEkk3Mlgx5qc43f1QBlQU7xQKWQAU6hAAOKA+jsEgQQBQcoBT80A0O6Hei0Wgo84g4DAC5YegAOgAVuc64w7tDgNBgAAvehwV0AOTAIGAYFHoAgAH0p9OZ9OIIADeRigGO5QCAHpPZxuJxBh6OmSzReLsJL0lkIDcAB63ajvc4QHA3MiMQSvSx2-AAbTQEG0WQAumDXk-X8AG4wDpUCxxAddNxnCBAGlbQBV6MqXZoJgqdtxHDBiVbWgfggABRABHdg+DUDJ8PPFksFwukIEEb1CXVHg9xuOBa1I4EcURYB2ABCNZV3ZkXiwPhI1vYV33wCiqNoZIiJItQ5Mom5qJ1ABGDIxQlKVPjlJ0lXMQzowI5TqLk4jSKUmSdQAJk0w8UgtPTAWBZU1Q1INtV1QzzGMqsIGY844AvGTgrub07nwLSjx0mV1X0tz1U1YMyUS2VtC5UiMG8-1f1Ha57keZ5XmlH58H0zFIRhfAESRVF0WoSrsVxCBkq8ikqQ9MD8roQqnheaSVLJNSyv+FzQXBKqcW6sACoefqTOs94bNGk1xqa6rAy1EMdQ66kblpBlRzAcc0NgwBoOUWQBTa1QtCMJO8ALAgQAwJUAarlF0AY8jABVvYtS3LStqyResmxbNsO2AARzj5e5u17Ad8EsQtvt+ssKyrGtgebVt207c5qV4xQjQRiBABezQAsTRMFH-vRoHGyxsGez7QcdzAIA)

#### 题意

不使用 `Pick<T, K>` ，实现 TS 内置的 `Pick<T, K>` 的功能。

**从类型 `T` 中选出符合 `K` 的属性，构造一个新的类型**。

例如：

```ts
interface Todo {
  title: string
  description: string
  completed: boolean
}

type TodoPreview = MyPick<Todo, 'title' | 'completed'>

const todo: TodoPreview = {
    title: 'Clean room',
    completed: false,
}
```

#### 题解

```ts
type MyPick<T, K extends keyof T> = {
  [P in K]: T[P];
};
```

- 首先使用`extends`来限制`K`的类型，这样可以确保使用的时候只能传入`T`的键类型
- 因为`K`可能是联合类型，使用`in`遍历`K`，获取到每个键类型，再通过`T[P]`就可以获取对应的值类型啦

### 实现 Readonly

> [挑战要求](https://github.com/type-challenges/type-challenges/tree/main/questions/00007-easy-readonly/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDsELQUHnagG5wgJQKYEMAmB7AOwBsBPSeOSq8gIxIgEECAXAC0PoDEBXCACgACWFgDNuASggBiQAHegVWVpNbgEsizOMoLSATtnzF6UvDQBWGAMbqA1hhIBncuSnOIgDIzAd26OoAPgiAia0A5+IgABTQIQE5TQG34wG--QEXowApYwHnEwCAGLwhAWDlAQMjAf3lACldAUMVAO39AELcAA0xcQlIAHgAVb1LAGH-AK8DAR90UQAA5QCo5VKhyQFPoiHK9KpJSiEAseUBS40A2U26IAH1AbZtAaPVAIeVAB1MFxsA3PUAV+MA9tW7AGO1AC0UOwHYLYsBvHxXGwCvlQHozQAEjQEhzQD0dQHIDQF+EicBqiKGukqBnGz0Ad-KAB0zehByIB9OUAjDqPdKAe+VAKdygFjFQCf2v1MYBDGOKX0A0rGAHgVoeRANHygCDNQBY-2lSvTmA4oJpmBhtCIsOYMBBanh8BAAN7kKDMZTMIgYABcEDszG0mgA5sKIDgMHZzPKAA6iwjS2XyghKqAAXzS5kIsogzD5eGlAFkSBV9DVefhfABeQXK0XiqUQABEAAlbP6ADTK1XqrU6gjS-0iPDGLDaf3kU19EU2gB0PolEE9QYwRCIeH9EGAwAgAFFtNo8NppeZhAQ8MwIEC7HZlAqtFh2yMDBBNXXNWzmGRM-gs5GNcptcpCPmAzRk5xE2WK9Xa-XG83W-2sJ3u72D876MO8KPtOPyPTSmlfIAKdQgAHExaxuDQIIAoOUAp+aAaHdqVYZhmE1OxJQrRlzFYLMTDsLN6wVYBoGAAAvVg4AAYQAOTAEBgDAQjQEWBZSLI8jSIgQADeWKQBjuUAQA8SIo5iIHwwjx1HCAHSdUY6g9L0oCBM8IAAbWCCBNAgGwSDwEQeQAXWlWoxPkgBuMBTSIkAmOY8iIEAaVtAFXo3JyR03TKLY5QAFtNXrNsBWrABHbgsCIUNqwAD1HSwIGNCARDrKyIAAcgEDiMDgaDXIlQ01WAbhRSIOxgvYkhOKbOw1SXETyCrLyLGYaoq2c1zqm4gcXRtABGbx3J4gw6mq7xmvDeTCJZNkOS5HlqoEq0xQlPU5UVcgZ2jBdYxlYbDXIc0bIlVkcGlGhEwlYRyCsjBmCwaUhSgKAsAS9gGymg0jV8jTCK0szzIgQBoOQ6QBTaxu3TWII0ByF8QAwJUAarkaMAY8jABVvICQLAiDgCgmC4IQ7QkJQ4Q7AAdzZNCMJwz6IAAoGQdA8DIPVKH4MQ5DgDsPAiASiamQgXxABezQAsTQ8HGwfx6DYKJ2HUaw3C2LAIA)

#### 题意

不要使用内置的`Readonly<T>`，自己实现一个。

泛型 `Readonly<T>` 会接收一个 *泛型参数*，并返回一个完全一样的类型，只是所有属性都会是只读 (readonly) 的。

也就是不可以再对该对象的属性赋值。

例如：

```ts
interface Todo {
  title: string
  description: string
}

const todo: MyReadonly<Todo> = {
  title: "Hey",
  description: "foobar"
}

todo.title = "Hello" // Error: cannot reassign a readonly property
todo.description = "barFoo" // Error: cannot reassign a readonly property
```

#### 题解

```ts
type MyReadonly<T> = {
  readonly [P in keyof T]: T[P];
}
```

- 通过`in`遍历`T`对象，然后组成新的类型对象，主要是在键前面添加个`readonly`属性就好了

实现`Readonly`的时候，没有要求进行递归处理，挑战中定义的类型，还特意在其中定义一个字面量对象类型，想必是想让挑战者自己去发现补充吧：

![](D:\project\docs\imgs\ts-challenges-1.png)

为了确保`Todo1`类型下嵌套的对象类型的属性也是`readonly`的，那么可以这么进行改进：

```ts
type MyReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? MyReadonly<T[P]> : T[P];
}
```

- 通过`extends`进行条件判断，从而进行递归处理，这样确保嵌套的对象和数组都能变为`readonly`了

### 元组转换为对象

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00011-easy-tuple-to-object/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCM0QtBQwoqBG-QNvGCNjQXHKE-tQhjGQLzEmEBGAnhAM4CWAdgPaP0QAUAAnUywJQQBiQAHegVWUIhQgKkRAGRmA7twlQAfBEBE1oDn4iAAUAShECcpoG34wN-+gRejAFLGB5xMBADIoiACeUCmioAA5QFRyqQN4+gaPVAMP+AwHUBN+PcUb0xcPG8fIPxvQBC3QDpU4EAeBUBfhMB6M0A5uVRAWjlAWQTAMOVAL8VAU3NbKEJAaPlAIM1ALH+7AANGgBdqQgBjFmomiCaAVwAHABsAUwgAXggAbQByJuHqQYBDaYAaCGmAW0YAE2HBiABmVfWt3f2ADWPNnb2IAE1pgF0IReoIDvouuyaKftGAJ3mvUG3QmABUBiMwYwAPJkABWwzaTQAPD8-owAGY9SHDFTAYAQYYADz+yOG2wgAG8evMlgAudZzBbLNbXM6HaaM9m3I5s063S7cgUXK4i+5ck43fYPAC+hEa9TsKkAFOoQADitCaAAtemQIIAoOUAp+aAaHcatqmk1+tR6QSWm1tQA6eHUR2Mf4Ac2AsGAAC9tfAAMIAOTAIGAYEjoAgAH04-GE-GIIADeVigGO5QCAHrHEzmYxBw5H0aMIUNhtC4YjkSiwUTiXN6Ns3oDFtsWIMqF1-gwPZNHioJlTCJMtBAGBAwZN6L0NmRhv9Ho9GVowPKoyBs7mExBANK2gFXowAUrhUN5u4-mI7QNv13d0aQBRACOvUWgzWt9JSO6sogmP+jA26w4It4AdZ8RnoD15mAXomloQZqGmSMPi6HFS3GKZZjpVkpQ5PlsMFMVpQlZ5XneTomkLX5RjaV55jQyZCDfMlUQfJ9BmrXFywRD80UorEUJGJQ1hpZkGSZTDpgAbjw-YjmFQijiknlRTkjlLkU8UHhU245SUQSwEeKNCUA6h4BJJjTP+X9-jAIsiUs900JLKFYS4qtJkmaA1gAJkeITZT7SM12PE8IEAaDlnEAU2tgs3M8wFAQgVEAMCVAGq5VNAGPIwAVb3NS1rVtYB7SdF03U9b1oGARZPgAdznP0AxDBKIFNTLsqtG07WoB1nVdd0vR9ahGEGaDaE6BrABezQAsTXkFrcvazqip62qg1DAsgA)

#### 题意

将一个元组类型转换为对象类型，这个对象类型的键/值和元组中的元素对应。

例如：

```ts
const tuple = ['tesla', 'model 3', 'model X', 'model Y'] as const

type result = TupleToObject<typeof tuple> // expected { tesla: 'tesla', 'model 3': 'model 3', 'model X': 'model X', 'model Y': 'model Y'}
```

#### 题解

```ts
type TupleToObject<T extends readonly string[]> = {
  [P in T[number]]: P
}
```

- 挑战中要求以下代码会报错：
  
  ```ts
  // @ts-expect-error
  type error = TupleToObject<[[1, 2], {}]>
  ```
  
  所以我们要限定`T`的类型为`string[]`。

- 使用索引访问类型可以获取到元组中的类型。也就是通过 `T[number]` 可以获取到联合类型：`"tesla" | "model 3" | "model X" | "model Y"`
  
  索引访问类型使用文档：[Indexed Access Types](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)

- 后面就是常规操作啦，通过`in`来迭代联合类型，然后进行组装了

### 第一个元素

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00014-easy-first/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAsEFoKBpvQAHKCo5QwoqAJfSiEGF4BGAnhAIIB2ALgBYD2V5AYgK4QAUAAgIa0AzNgEoIAYkAB3oFVlcbwBOc3qTx4xaiIAyMwHduKqAD4IgImtAc-EQACgCUIgTlNA2-GBv-0CL0YApYwPOJgIAZdEQHnagBucMgFgJgBSuAAYsAJZyAM40ADwAKnohgDD-gMHagKXGgOvKGIAOpoAjfiHxIYBueoAr8YB7aqmAIW5oWNhVgN4+gNHqnlB4gNHygEGagFj-XiH9NFF4NKQADgCmEPJy0BAAvBAA2gDkvMsANBDLxBtbAMbLALrDY5PTAEzzSwDMm+eb0MdtUCMTEHTjvAAmswsR0XFptADMBgBBxgAPCZ7GjjL4QGgMCDESarZYnN4fb6XP6RGKxC4gsGQ6Gw+GI5GTa54fohLwGQAU6hAAOLhehsYgQQBQcoBT80A0O7dOg0GijKIALlBgz2dAAdAArKIyhhyADmwDgwAAXnQEABhAByYBAwDAptAEAA+lbrTbrRBAAbyVUAx3KAQA9LbaPRaIMbTa9Jv98fFwRDYVQvlEpsxFocDAt4osqGwALYouSHYOh8MQKjjABu4zkEAA-Nm8wWIGKIPGAAzHM0gd2em0QQDStoBV6KC7UbTat3pN4STo2VNAgAG8IABRACObF4ABtNhOoeMYRAAL4QARyBhJrbcP0IaXzufjKgq8ZRYBsGjhOdRdFgP0QPa8KIXq6LPBL0mxaezuexAGcSLLcED3DAMabNceh6OsX7LjCv4zvOgF4sBnCiHMBjQOcoHjrwlYxHI4RnuukFcJh2G4TBcFQN+K5xH+KFAbE0awaW+ZyDR8E-kxAEsYsbBhuMAgkXC5FCV8IliV8MFgHWT4Ftu0QfngoIQPuUQICSDHaQoyp4CxyxUAwNAUAoSjLLBalgpp2kITQenKYZaGxOO1aVqsFmkAAMuEADW4zLOuejyaa9bdj2ECANByqCAKbWkVNn2YCgHgBiAGBKgDVco6gDHkYAKt6CsKooSsAUqygqSqquqsDAPwUQAO4FlqOoGmlED8vlhUiuKkpRNK8qKsqaoalEDBzte4RMEM+gQIAL2aAFia2hdcVvX9RVQ3NXqho+mAQA)

#### 题意

实现一个`First<T>`泛型，它接受一个数组`T`并返回它的第一个元素的类型。

例如：

```ts
type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type head1 = First<arr1> // 应推导出 'a'
type head2 = First<arr2> // 应推导出 3
```

#### 题解

```ts
type First<T extends any[]> = T[number] extends never ? never : T[0]
```

- 如果是空数组类型，那么`T[number]`就会返回`never`类型的
- 而通过`T[0]`（索引类型访问）就可以获取到第一个元素的类型

在`Issues`看到一个👍比较多的答案，是通过`infer`来推断类型的：

```ts
type First<T extends any[]> = T extends [infer P, ...any[]] ? P : never
```

### 获取元组长度

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00018-easy-tuple-length/README.zh-CN.md) 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAcEFoKHbgwa8qGFFQI36H9UwZXqUQSOIICMBPCAZwEsA7AewbogAoABWx5gSggGJAAd6BVZQEAXAK4AHADYBTAgX7KIgDIzAd26KoAPgiAia0Bz8RAAKAJQiBOU0Db8YG--QIvRgCljA84mAgBi0RA2EqAvvUAAcoCo5QCwEwApXQBC3AAMAGTk6AHMxAAswwBh-wFLjQHXlfzCAJzkAQwATZhlyMJDAB1NMJMAV+MA9tUBN+L8KkLw3KAJAaPlAIM1ALH-3MP6xKgIxcik5CDE5KhlciABeCABtAHJJ6dzlgBoIZYBbBny5GQgAZi2d-cPjgA1zvYOjiABNZYBdYdHxqilcgGM5a7zJbLABiAEEIgBhADyADkIABOO7gqFwiAACQAomCAGovbbLAAipjBAHE4XcAMoAFTBpkp6IAksY7uiAKoAWTB8MpxjBkMxIIijNJ6Opb3cIzGEymMyisQSQPlcXiAB41jNdMBgBA5AAPMa-Sb5CAAFg+0u+fwBysVC1taqt-2uWp1+sNxogAFYCP0wu5dIAKdQgpJoCQkpAggCg5QCn5oBod268TEYikVAAXNrBr94gA6ABWVBzDCyMWAcGAAC94ghIbCwCBgGAm6AIAB9dsdzsdiCAA3kQoBjuUAgB5truj1sQBtNqXjB2q6m6vWTOj5KgQHIFIqUXJ0ciLV66Bbz-VLlcQADeEHkCviaYg9AAZnIshBzABfCAAfhfEFvdDkADcnwAbibZsQBHMdOwgQBpW0AVeignaCDIPbCdGxoXYpGLMRzwgTEAEcJFyGRtkxA05CNCB33vLIGF2HZ2GnBBsyIq8YimYAJDEGgZCoZYm1+ZgqGwjVZgWFYRLuS5HjOAkpJuSSHmOF5XggXJVwEughLADShOoH5nSBFYURheEkQJYy0SxXF8R2YkyQpAkaTpBlmVZTluQgXl+UFYVRXFFS1IgHSxCnT4grUqZDIIUiPVVfDCJkVVZ2nBh7xldZtG2U1tEy6KyKNOKCKIpLohVdVPlSvTrRdbYvRyzYCG1CAGKoBB3XIsQ2qyGisgIWc6oaqAmpatr8s6p8er60qElVZZ4iOGQGAgAB3YsZHyZZcveUCW2QqDAGg5HxAFNrJDkNQsBQAIXRADAlQBquT7QBjyMAFW9E2TVMM2ALNcwLIsSzLWBgG3KhlqfStq1rK6IHjZ7XpTdNMyobN80LYtS3LKgGBkTiaEEyHABezQAsTQ0WH3oRpGftRsGazrScgA)

#### 题意

创建一个`Length`泛型，这个泛型接受一个只读的元组，返回这个元组的长度。

例如：

```ts
type tesla = ['tesla', 'model 3', 'model X', 'model Y']
type spaceX = ['FALCON 9', 'FALCON HEAVY', 'DRAGON', 'STARSHIP', 'HUMAN SPACEFLIGHT']

type teslaLength = Length<tesla> // expected 4
type spaceXLength = Length<spaceX> // expected 5
```

#### 题解

```ts
type Length<T extends readonly any[]> = T extends { length: infer R } ? R : never;
```

- 通过条件约束加上`infer`来推断长度的值类型，就可以准确返回数组的长度了
- 当然，数组得是不可变的，所以`T`也要加上约束

在官方文档中，有这么一句话，说`TS`是允许推断出最长的返回类型，所以说肯定是知道数组的长度的：

> [文档地址](https://www.typescriptlang.org/docs/handbook/2/functions.html#constraints)
> 
> There are a few interesting things to note in this example. We allowed TypeScript to infer the return type of longest. Return type inference also works on generic functions.

### Exclude

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00043-easy-exclude/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=19&ssc=48&pln=19&pc=1#code/PQKgUABBAsDMEFoIFEAeBjANgVwCYFNJEETSiAjATwgC0ALfRgOwHMIAKAAQC8HmWAlBADEgAO9Aqsojy2AJaYALghlMiRYeoiAMjMB3bqqgA+CICJrQHPxEAAoAlCIE5TQNvxgb-9Ai9GAKWMDziYCAGPREB52oAbnQFDFQDt-QBC3NCw8fAgAHgAVQBh-gFV9QG8fQGj1eMAxeUBYOUBfgMAXt0BS40B-eUAKV0Bg7UAac0BquK8DQDm5QBUAwAgVdNjAWjlAJONAEzTEkPTABCNADRV4wFNzQBDzQAIEwAA5QCo5QAbTfrS6iCJDQAp1CABxGXk6bHIIQCg5QFPzQGh3QCx-unl5AAcAZwAuYGB5e-Q6ADoAK3vPgHsAE4sYBwYC8BAAYQAcmAQMAwIjQBAAPpo9EY9EQQAG8iFAMdygEAPVGYkkoiDwxHySi3KIAWUo4RwBDiABoIMkIABeCCxCD4VDyfBMXD3dkQAD8ECY+AAbvhARBHjyANyIsDI0kkiCAaVtAKvRpUA0fLEzVYikyAC2tyB8ggAG8UABHbAAQ0wbLQNPQNoAvhAAGaA-7miAAck4VJpCA+rswQpY+HuwGw8jk9xDlOpUXQzvuCa5EAA2kQoB78F7osgna7ovTGZFogAiZ0NiAAHwgDfILfbDfQDbZTYb+ndGCZ+EbzbbHa7U97-Y7zf0S5ZxZQqE98grVcwNYZo-rg9nM57fYHk57XeHa4izMPF+7HdPC4fnaHy9XpfLlZdO9r++Z9zyICyhsO2TDYOa5DylO7DsEInKGDK-wyLgAhsgAYtgTBejI-xMFedYAUBIFTuBkHQe2sHwYhyGoRhWE4XhS7DmAAC6arqiAxommSgDQctMgCm1txJrkgioBrBAgBgSoA1XK4oAx5GACreVw3A8zyvO8Xy-ACwKgrAwDOkw9wAO7yuCdBQrCBgQOcinKXcTwvG8Hw-H8QIgmC9z-DgKZ4fcEmAC9mgBYmjodmqY5GkudpIIQjCcIImAQA)

#### 题意

实现内置的 `Exclude<T, U>` 类型，但不能直接使用它本身。

> 从联合类型 `T` 中排除 `U` 中的类型，来构造一个新的类型。

例如：

```ts
type Result = MyExclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
```

#### 题解

```ts
type MyExclude<T, U> = T extends U ? never : T;
```

当联合类型作用于泛型的时候，会将每一个类型都分布到该条件判断中。

例如：

```ts
type T = MyExclude<"a" | "b" | "c", "a">;

// 执行时大概是这样：
"a" extends "a" ? never : "a" |
"b" extends "a" ? never : "b" |
"c" extends "a" ? never : "c" 

// 所以最后 T 的类型就是
type T = "b" | "c"
```

查阅官方文档请参考[分配条件类型](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types)。

### Awaited

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00189-easy-awaited/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAcCcEC0ECCB3AhgSwC4FMATSZJM8kgIwE8IBZTAY23wCsIBlbAawHsAnTBAAUAAQC2TFqwDOPAZgCUEAMSAA70CqyqoAO-XuOwz8qygFdsAG1xJsAOxIkVTiIAyMwHduDqIHEFQEGagRCNAG7lASHNAADlAKjkIAAU9AyMIQE-tQEMYwBh-wE34iOj9Q2MkwCx5QBX4wD21cMBvH0Bo9UAgBkAKdQgAFQ4IQFo5ZMDAClcomOyAHlqAPibAELc6iEBTc0B540AH+PSOrLiigfKKwHfowAN5QDztQAbnUrLkwHvlQFO5QHbgwDXldMXPCEAV6x9ALH+AA0zY-C6AUQAPTHFtC3xa6m18L07slVkUIO9Pt9fv9jOcoCR+ulAIjGgAwjEaAK8DAHFyEAA2hIpGw5HxBABdYQAC1wuG0MgAXMBgABzPDk0yUAB0jH0wEkzEJ8kEygGgHDTQAHXjccQJsMzbJgLBBMPxcNhGD8yZTqXSGYR8AA3dm4Xg8phEhTATCEXWYWyMIhIXAwmSMfjYbTWfBvfD8ZhGGRIACOpnwMmVvFsSGgSAALABWbhRxQXfo1ADiLLZEEAUHKAU-NANDuNw1NPpwFwTvJ7Nk7IEjOAcHgwAAXuSkABhAByYBAwDAPdAEAA+oOh8OhxAVgNAMdygEAPAcjuf9iBdnsOgH0agYHAEQg9foAXmGHoItkIMhmTy6dgAZl6IABVfoAfjXG7wRC694gtLqAG4e72QLO87DhAgDStoAq9GtIA0fKAUBg6Lt22BfAIuAQAA3uCgZygANOCbwAowKEAL4QJeMQQAA5KIK74EgjDknKPy2IywbAKYyoWDI5HLjCEAABoQPujzdCGLpMb0YDURAACaAlnt06GXiwFiEF+timOIlA3oR4mSQAWrJQlGF0hnPCJdiMhAAA+EBqRpXq9OJEk8YwmC+rJOIkO8+G4K8mEWF0dDrlgr7brxvQ4WZYnhZ5eH4ARvmmHKAVBZub5SeFaEkUpKk2epmn8BA2nRVAXlxT5Lx+clL5bl0ukZZFFnWbZ+UOVhYAkr2wAQFRfoet5SBeno-BOaug0CLJgXVW+zX2X+YB9rBwGANByISAKbWMGwfB83gFA-SAGBKgDVcuOgDHkYAKt4FlSRYMqWdEVjIVb8DWdbmrYMjoF6jbNu2CIQHmZ0XZqxY3eWlbVrWCDADIvAWGx2BhjIP2AC9mgBYmu4ANXSWZZ3Q9NZNq2HZLmAQA)

#### 题意

假如我们有一个 Promise 对象，这个 Promise 对象会返回一个类型。在 TS 中，我们用 Promise 中的 T 来描述这个 Promise 返回的类型。请你实现一个类型，可以获取这个类型。

例如：`Promise<ExampleType>`，请你返回 ExampleType 类型。

```ts
type ExampleType = Promise<string>

type Result = MyAwaited<ExampleType> // string
```

#### 题解

```ts
type MyAwaited<T> = T extends Promise<infer U> ? MyAwaited<U> : T;
```

- 这个没啥好说的，就是通过条件判断加`infer`去进行推断
- 需要注意下递归处理

### If

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00268-easy-if/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMBsAcEC0ECSAzSyk91gRgJ4QAKAhgG4CmANhAOI0CuAzgBYDWA9hRABQABAA5l2TAC4cAlBADEgAO9AqspyJASxossWWToiAMjMB3blqiA87UANzoAA5QFRyEAAaoAYrYiBvH0DR6oBh-wMHagUuNAbKbWgIbmgG9y7nYAws4e1oAkSoC1poBccoD47oBvpoAhboAr8YB7amG2ACpRgKdygFPKsYmA4grp2blOgEAMEc6AV8qAvwGA9GZ24gBOTFTOgGhGgKABduhkGr0edgUQgDEqdk4QgPfKha2A33KA8IbutcYQgHxygNHyAFzbtifimlDihEJUEACCEAC8aOgAPF09ADQQAORk31-ffDfAB8UGAwAgVAAHtcAMbiKgAEwg4i4EHwN1+3ywl2uEAAQo9ni8RmMAX8AUDQeDITCqPCkSi0RifkCsCdbNtQYAKdQYqnEbCY+AggCg5QCn5oBod0AWP9scTiIQsA7gs6wtgAOgAViw1VxOgBzYBweDAABebCQ4QAcmAQMAwPbQBAAPou11u10QQAG8mlAMdygEAPZ3uoNOiC2+24m4YF7hWkIgB2iJY6K4XBoVDIca+eS+DlBTxj0PjiZR3RuAH4IHkIAcIA4ANz2sCO4NBiCAaVtAKvRgApXPaBlsesOqAC2Ql14ggAG8IABRACOTFGX2ndPhEAAvhB0J0uEOfgII0hVaM03G9VQWMA1BpsWAIxBYaJz0SANpYZdw8QvOcLmgvKPvKhyX+VkQSA4FgQ+N8V0-b9Rj-V5SRYQCfgpGAILQiCwAAXQdCF9xYJBoQ-QjOm3TpbyuG4qFI3UiSjOMmBoGggMpEFGybEA+37ENAGg5CxAFNrLj+1DO1QCwUFADAlQBquW9QBjyMAFW8ZTlBUlWAFV1S1HV9UNBBgAzFgAHdqNNc0rXEiBJQUpT5UVZUWFVTVtV1A0jWAFhUzULg43OCBQUAF7NACxNQxrJUuyHM05yTIta0wzAIA)

#### 题意

实现一个 `IF` 类型，它接收一个条件类型 `C` ，一个判断为真时的返回类型 `T` ，以及一个判断为假时的返回类型 `F`。 `C` 只能是 `true` 或者 `false`， `T` 和 `F` 可以是任意类型。

例如：

```ts
type A = If<true, 'a', 'b'>  // expected to be 'a'
type B = If<false, 'a', 'b'> // expected to be 'b'
```

#### 题解

```ts
type If<C extends boolean, T, F> = C extends true ? T : F;
```

### Concat

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00533-easy-concat/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=22&ssc=62&pln=22&pc=1#code/PQKgUABBCsDMsQLQQMIHsB2BjAhgF0iUWJMICMBPCAQQwBMAnAUyoGkGcBnNAN04GsqACgACZZrAAM-DgDYAnFk4BKCAGJAAd6BVZXU4GHCoUJqTEQBkZgO7cjUQBTqgbx9A0eqBvn0D7foBnEwHnagBucIAKRw8OADKWAwAlgAOeBCAoYqAdv6AIW4QAAbU+jgUAHRYmLh4yRCAnaaAqzaAMP+Am-GAVHKOgKXGgOvKgCRylYBDyoAOpqWAK-GAe2oJgA2mrYAjfo6AKXqAp9GAkMaA5o6AyfGApoptgHNygGe6gAxKgM-KCYBeGYDxeoAQKoBueoBccoAAcpW9CYOAQAzWEIB8coDR8qU3yS94nIR4FBFMEABKTJwAK4AG2iAF5ULl8AAeADaAEYALoAGggsIATIiAHwQYDACBMAAe3yweCYdAgeDQEDIPwRqMxhBeyRuOJsEAA4mE8AALQFkCCAKDlAKfmgGh3QBY-zy8HgIpwAFx4t5YHmZABWnEyaAYAHNgHBYMAAF48xAoAByYBAwDANtAEAA+o6nc6nRBAAbyCUAx3KAQA8HS7-faIFabZ9vpDsDCACoEwlk+icCA4DAUWEoiAAVRjcboCaTKexEAhsMyJcjqJLmXTiIA3DawHaA-6IIBpW0Aq9GACld7n7G67g2EALYRLXRADeEAAogBHQE4YGo8fEpikiAAXwgADMGGh+xAAOQiUNMRDK2fApgYbUA4CAvBhYGcXchr4-XCcAGFtGEBckvDQqcz4FoXQCNf1TVFUyxcDsUgr9F1JP9p1nICoVAtMEWxcCkSxGCoG-Jdf3-JDgLyOF4QZNDYFRAAWDC0TIiB0VRSiIBo7DkVgn8EIA5CQLhXd4V3BlUV3WBdzQ9dZzfVEyDQNAzyTYSqLEyC0X4wSGOE0TUQk+8mGk2T5IwRTlJgxEnzDHAP2ImEwLo7FbRAbse0DQBoOWOQBTaycnsg2tUBCBxQAwJUAarkPUAY8jABVvSVpVlBVgCVFV1U1HU9XgYAk04AB3JgGCNE1zX8iAxQiqKZXlRVOGVNUNS1XV9WAbhgRvMJMHeKAcUAF7NACxNSwSpi8rKsSmrctNC1gzAIA)

#### 题意

在类型系统里实现 JavaScript 内置的 `Array.concat` 方法，这个类型接受两个参数，返回的新数组类型应该按照输入参数从左到右的顺序合并为一个新的数组。

例如：

```ts
type Result = Concat<[1], [2]> // expected to be [1, 2]
```

#### 题解

```ts
type Concat<T extends any[], U extends any[]> = [...T, ...U];
```

- 因为数组可以拥有`rest`元素，详情请看[官方文档](https://www.typescriptlang.org/play?ssl=22&ssc=62&pln=22&pc=1#code/PQKgUABBCsDMsQLQQMIHsB2BjAhgF0iUWJMICMBPCAQQwBMAnAUyoGkGcBnNAN04GsqACgACZZrAAM-DgDYAnFk4BKCAGJAAd6BVZXU4GHCoUJqTEQBkZgO7cjUQBTqgbx9A0eqBvn0D7foBnEwHnagBucIAKRw8OADKWAwAlgAOeBCAoYqAdv6AIW4QAAbU+jgUAHRYmLh4yRCAnaaAqzaAMP+Am-GAVHKOgKXGgOvKgCRylYBDyoAOpqWAK-GAe2oJgA2mrYAjfo6AKXqAp9GAkMaA5o6AyfGApoptgHNygGe6gAxKgM-KCYBeGYDxeoAQKoBueoBccoAAcpW9CYOAQAzWEIB8coDR8qU3yS94nIR4FBFMEABKTJwAK4AG2iAF5ULl8AAeADaAEYALoAGggsIATIiAHwQYDACBMAAe3yweCYdAgeDQEDIPwRqMxhBeyRuOJsEAA4mE8AALQFkCCAKDlAKfmgGh3QBY-zy8HgIpwAFx4t5YHmZABWnEyaAYAHNgHBYMAAF48xAoAByYBAwDANtAEAA+o6nc6nRBAAbyCUAx3KAQA8HS7-faIFabZ9vpDsDCACoEwlk+icCA4DAUWEoiAAVRjcboCaTKexEAhsMyJcjqJLmXTiIA3DawHaA-6IIBpW0Aq9GACld7n7G67g2EALYRLXRADeEAAogBHQE4YGo8fEpikiAAXwgADMGGh+xAAOQiUNMRDK2fApgYbUA4CAvBhYGcXchr4-XCcAGFtGEBckvDQqcz4FoXQCNf1TVFUyxcDsUgr9F1JP9p1nICoVAtMEWxcCkSxGCoG-Jdf3-JDgLyOF4QZNDYFRAAWDC0TIiB0VRSiIBo7DkVgn8EIA5CQLhXd4V3BlUV3WBdzQ9dZzfVEyDQNAzyTYSqLEyC0X4wSGOE0TUQk+8mGk2T5IwRTlJgxEnzDHAP2ImEwLo7FbRAbse0DQBoOWOQBTaycnsg2tUBCBxQAwJUAarkPUAY8jABVvSVpVlBVgCVFV1U1HU9XgYAk04AB3JgGCNE1zX8iAxQiqKZXlRVOGVNUNS1XV9WAbhgRvMJMHeKAcUAF7NACxNSwSpi8rKsSmrctNC1gzAIA)

### Includes

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00898-easy-includes/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAcCc0QLQQJIDsDGAbArgEwFMBnSJRci0gIwE8I0cssIAKAAQGsa0CAzHDgEoIAYkAB3oFVlUQEMATrOk1SpEaoiAMjMB3bsqiAKdUDePoGj1QN8+gfb9AM4mA87UANzhABS0gG7SAyhlkBLAA4AXCIBC3CAADAEF5RQA6D0xcQiIgiEBO00BVm0AYf8BN+MAqOSNAUuNAdeVAEjlMwCHlQAdTVMAV+MA9tX8jQEDIwAk5QHozYJ9ZHAIExpag3mksIk7AIAYdCEA+OUBo+UBTc0AX6NTRoMWfEigfGi8CCA8iAAUPJjkAWQI0CABeVBj8YgAeAG0AcgBpOSIHgBoIB4BRIg88bYeD5fADq0gAtjgcMCHi5pGgfPDpA8ALqfB4AEQ8AHsHgA+CDAYAQAgADw2GB8BDwEB82IgVE2vX6gyCpEWbKgpAJuggAHEPD4ABY4KgQQBQcoBT80A0O6ALH+hT4fF4iAAuInLDBCiIAKyIEWxsgA5sA4NBgAAvIWIADCADkwCBgGBnaAIAB9D2er2eiCAA3l-IBjuUAgB7u71ht0QR3OtYbCDfACOOBZNwAGp8AJoEi6kFg3AAqeJYwjOBLzJNJVLQeCIEBTEAA-BAAIwQFUQABMwjJlerrHzheLpfLPZr6YbzdbHcEpEbbQ6pDbfQGBAA3NH1pt0NhrkR88OTr3ZARpHhsWgsHR4TQ7miIABVLMQUhl7sHmt3aK8AiyCAAMU+ESAZ+34QAASiiT5QI2CZJgMNz-veBKvlWNZzgQpBQbS7SbG2W6xLcoGfA+C4QEugxrs6YCuuGYYQIA0raAKvRgAUrhMoY0T6UYeOCXgGn4ADecaJv0nzfOSBCUhAAC+pGyNi4JfGwMYEIgmr9FgJyGsQwA4D4+xvOusYYNIgw1hcdykKJFI+DcMH9DceE7vczyvDCvz-ICMJgpC0LonCCJIqi6IvLIbx4p8aF4mFFliZSNlCVg9lXHETnBW86JuQCfyeRCUIwn5iJoMit6Yji+KfGRBCRe80VWXFsGJduyV3E2nztp8ADMnwAKyfAAbJ8ADst4DWFWEdFVNXidZtkJQ5TUtR2HXdX1g23gALKNFUTVAllTXVdlzbczWtR1t7tqNEVRTtMXTfFDX4bux2LRA7W3k2F3YdtcY3fts1JUdfGSbeAnSG2DwhA8UmbSylVXd9tUzfdjl3FQ2LYup8InS9y0QP1EBDeVMPQ8uX27bFiOHY9aFY51EA9bjq2fKj6PHmgxODKTP0U-9j0VTTON4wTpFE+Fn1w2Tt31ZT9wg2DENScDEBHieZ4XhAoNfPLkns7D1XXQjd3S3cAnK6e56XnLkNA58sua1bOucwbUs8-cTZvRAAA+HYO+LXOGy7zWex2b0+3r8N7dzjVHQwTC3jgVZ8NE1Kh5N5P+1Hj3x4QvBJ3gt4x1gKcopRVEgGx7ERoA0HKAABygCm1uX7GRk6oDchAgBgSoA1XL+oAx5GACre8qKsqarABqWq6vqRomvAwDwkQADu34WladqtzKfcD0qqrqkQmo6nqBrGqawBEOjOk4mgKwQASgAvZoAWJpaBvQ-b7v48H0vNr2lGYBAA)

#### 题意

在类型系统里实现 JavaScript 的 `Array.includes` 方法，这个类型接受两个参数，返回的类型要么是 `true` 要么是 `false`。

例如：

```ts
type isPillarMen = Includes<['Kars', 'Esidisi', 'Wamuu', 'Santana'], 'Dio'> // expected to be `false`
```

#### 题解

```ts
// 判断两个类型是否相等
type Equals<X, Y> = 
  (<T>() => T extends X ? 1 : 2) extends (<T>() => T extends Y ? 1 : 2)
  ? true
  : false;

type Includes<T extends readonly any[], U> = 
  T extends [infer F, ...infer R] 
  ? Equals<F, U> extends true
    ? true : Includes<R, U>
  : false;
```

> `<T>() => T extends X ? 1 : 2` 
> 
> - `<T>` 表示这是一个泛型函数类型，并且 `T` 是一个类型参数。括号中的 `() => T` 表示该函数接受零个参数并返回一个类型为 `T` 的值。
> 
> - 在函数体中，`T extends X ? 1 : 2` 是一个条件类型（Conditional Type），它根据类型关系来选择两者中的一个类型。具体来说，如果类型 `T` 能够赋值给类型 `X`，则条件类型的结果是数字字面量类型 `1`；否则，条件类型的结果是数字字面量类型 `2`。
> 
> - 因此，该泛型函数类型可以表达为：“接受一个类型参数 `T`，如果 `T` 能够赋值给类型 `X`，则返回数字字面量类型 `1`，否则返回数字字面量类型 `2`”。

首先来看下`Equals`类型，可以这么来理解，我通过了一些步骤来演进代码：

- ```ts
  type Equals<X, Y> = X extends Y ? true : false;
  
  type T = Equals<true, boolean>;
  ```
  
  上面代码中，`T` 的类型会为 `true` ，因为 `boolean` 类型兼容 `true` 类型。

- ```ts
  type Equals<X, Y> = (() => X) extends (() => Y) ? true : false;
  
  type T = Equals<true, boolean>;
  ```
  
  上面代码中，`T` 的类型还是 `true`，函数的参数和返回值也是存在兼容的，道理和第一个一样的。

- 所以最后就成这样了：
  
  ```ts
  type Equals<X, Y> = 
    (<T>() => T extends X ? 1 : 2) extends (<T>() => T extends Y ? 1 : 2)
    ? true
    : false;
  ```

要把括号括起来的看做是一个整体，一个定义好的类型，例如 `(<T>() => T extends X ? 1 : 2)` ，所以这时使用 `extends` 进行判断的时候，检查会要求类型定义一致，所以就能判断出类型是否相等了。

挑战里测试用例用到的 `Equal` 也是这么来判断的。

![](../../\imgs\ts-challenges-2.png)

这时来看看 `Includes` 类型，应该好理解很多了。

就是获取数组的第一个元素类型，然后对比是否相等，然后通过递归的方式，把数组中的每一个元素类型都对比了一遍。

### Push

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/03057-easy-push/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=25&ssc=82&pln=25&pc=1#code/PQKgUABBDMAMCsB2CBaCAFArgZwBaVRSOIICMBPCAKwEsBDAOwHM9GIAKAAVsZd0YC2AUwAudAJQQAxIADvQKrK0ugCcldcgQJStEQBkZgO7cNUQBTqgbx9A0eqBvn0D7foBnEwHnagBudAWAmAKV0AhbhAAG3gIIq1AHQADji43p4QgEAMhhCAfHKA0fKAQZqA0HKAMP8x4SLkQULYAMZKNEEiBFk5EABKuZgANiIQALwYoQA8ANoAjAA0EABMALo9AOTQQwB8EMDAEJ09vcOj-QThMRNGEADiNCK4mKQQgFBygKfmgNDugFj-uCIiQdgAXFMi+bgBVNgBAPZKTMBwSMAAXrgUABhAByYBAwDA0NAEAA+gjEUjERBAAbybkAx3KAQA94ci8XCIJCwGUhBAALJ0IJBGjMFoAFQgQgAHiIhAwACbYCCMchtfoTJoAbwIbXQEBpEDpbQYmAEpCESn6-VuGDAAF9oSTmnh6YyWWzORAlEI6Oz3gwapQeXyegBVAXkynU2l0sZtW39PWsjlc20QAD8kogKraATDdLt-QA3NCwLD8XiIIBpW0Aq9EueK4hMook0ARBT71QUQACiAEdMHQaj1i0ycnkRNXa0J6wAxSvYIQ9UHvERlis1CBqiAAMyU7wEECGnBJKDy-BqNTZTFywEwIhoNWwQ012VJeToHa5TTaBBrdZELT7lZaWB1NogHTGPU6-Kfp6b9cv5evt9w7W6fSDJOoxPjMAHzMBQyvl077nl+-Y3q0bRDB0QxzAsUE9KQ7zvIujCgchqHoZBWE4XhDDQbBzYXleNSIXeKFoX0GFASM4zPoxxFsZRSyxvGWYoskgAAcoAptaZgJhJQqABATIAYEqANVy6KAMeRgAq3hcVw3PcwCPHOLxvJ83y-IgwCMNgADuCoAkCYIyRApyqep1x3A8Tx6R8Xw-AgxnYLha40Oa2C2YAL2aAFia+iOZpLm6a87nfICILgkSYBAA)

#### 题意

在类型系统里实现通用的 `Array.push` 。

例如：

```ts
type Result = Push<[1, 2], '3'> // [1, 2, '3']
```

#### 题解

```ts
type Mapping<T extends any[]> = {
  [P in T[number]]: P
}

type Push<T extends readonly any[], U> = Mapping<T>[U] extends U ? T : [...T, U];
```

- 为数组创建一个类型映射对象 `Mapping` ，`key` 和 `value` 都是数组元素本身
- 这样就可以先判断元组中是否已经有该元素类型了没，然后再确定是否组装一个新的元组对象

### Unshift

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/03060-easy-unshift/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMAMBssIFoIFUB2BnAFgSwDMAXSFZci0gIwE8IArPAQwwHNcWIAKAAUZfY4WAWwCmRJgEoIAYkAB3oFVlWUwBOKpjVKkZOiIAyMwHduWqIDztQA3Ogbx9A0eqAJJ0A05oBC3CAANXAQTUaAdAFds+YldnQCAGYwhAPjlAaPlAGH+woKIaAAdRLABjFTxEkigE5IgAJRSfABsiCABedH9CIgAeAG0ARgAaCAAmAF1W2AA+CGBgCHrYVpb25o7SILC+wAp1CABxPCIcHyoIQCg5QFPzQGh3QCx-nCIiRKwALgGiNJwveiwvAHsVVmA4RGAALxxkAGEAOTAQMAwMDQBAAPoQyFQyEQQAG8g5AMdygEAPcHQtFgiCA4G5URVXA1WoAFQgogAHkRRBgACZYAqiJhU+4YYo0DzqGi1Fg0HqtNB9Sr1NCtLwiwkdADcwLAoPRaIggGlbQCr0YAKV0iqNlMKxeGEiUeZQA3hAAKIARx8TGKrSNpOSqSIVptojtADELVhRK1fvciKbzcUIABfCAEFT3YQQADkPBxyFSQmKxUprBSwB8RDwxSwEexSVxqSY7tpAtI1ttdV9FtqmHxxAaXQgjR5Q0aHR6PJLjrttQrxSr1VrTVanW6TeGoyHEzbzQ7Ze7Zsr1YCdXqEcaEYnkegEa6VHu90TLFHu-39IwrVX6-Gm+3U7AkylMo1MMA0HKAADlAKbW6qfmKBoFIfUAMCVAGq5eFAGPIwAVbwOI4TnOYBLjjG47keZ5XlgYAWCwAB3UQVA+L4-n-CBdggqDjjOC4rkQh4nheBA0Kwfc0zwJksEIwAXs0ALE1DFImCKIQ25qOeT4fn+LEwCAA)

#### 题意

实现类型版本的 `Array.unshift`。

例如：

```ts
type Result = Unshift<[1, 2], 0> // [0, 1, 2,]
```

#### 题解

```ts
type Unshift<T extends ReadonlyArray<any>, U> = [U, ...T];
```

- 这个就跟`Push`是一样的道理，就不多说啦

### Parameters

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/03312-easy-parameters/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDM0IwCYIFoIAUCGAnDBbApgC75YDOkKyV1FARgJ4S4CWAJgPZbMBe+LEACgACLDl14sAlBADEgAO9AqsqzmAOwBmJWYQCuABwA2+WbW3N9hZKooUZtiIAyMwHdu1qIDztQA3OgUMVAdv6AQt3TYeEQkpAA8ACoAfBCA3j6A0eqAMP+AMAGAsHKA9GaAL26ApcaA-vKAFK6AwdoJgPfKgEPKgMABANrh9Lr4AMoAxly6hIAZ2oCdpoDhpoDGFgC6AgAWhIS6pABcwMAA7rMAdIS1+KTNzK36GCoA5nOcW8AcjaTAg5ustOzsANbA2oRmzIvIi3Wkc8O4+jK6gQTEZC98JJAEAMLgg0UAFOoQADij0G2loEEAUHKAU-NANDugCx-4ajCZTQgrQZzABWbz2wFgiGA3EGyAAwgA5MAgYBgVmgCAAfS53J53IggAN5XyAY7lAIAenN5Eo5EGZrMBEAAsvRMDg-iEIhB8AAPYgqVikQRzQ3YLYTCCbeiVPrSAC80XN0WtEHCGu1+F1+oEhrmxtNqg0WHQNrtKkYAH50BBxhAVPgAG4kADcrLZIHFkp5EEA0raAVei8oBo+TT6a50pZzFwuk4hAgAG8IABRACO2gw+gANPXNXVGoR23XO-huwAxFukfDt+nsQiN5v6CAAXwgaiw7FwEAA5EJAchGqd9IZtstbvd9KQ16zGuwVKQq2pLhBHQJjXAo9euNt28aEFGVNpcLQSJIUaxuwbD3tE1ZzmAF5XlWtDYPeghPlGFzsIYmwflgWxfjWGBRmuACCa5zoBEDAaBto1pB0HXhAcHcAhAgkWRrBgZRspLBAjQYKO+qOpUFB9l2hChNOLahIqypBP8YSAuwaiLpckTtpUr6qFs7Y-n+JB9JESkCf23YiU2YkSb8wRkKEsnyXBWBKRAlQoWhKjttWuHroRc46XpUCCQOwmifo4lKmZ0mWUscm0Rg3B2ZaumtmAfTJmA7JFhmgDQcoAAHKAKbWhZFiWyXgFA0SAGBKgDVcoKgDHkYAKt5YiMYyTMA+I7sSpKYeS8AIMAmykNMJBUjSDIUNE6LVbVOINU1hIkrsbUUp1pCoXczCXuQRUQIAL2aAFiaThjfVeIEi1M37NSdKMjKYBAA)

#### 题意

实现内置的 Parameters 类型，而不是直接使用它，可参考[TypeScript官方文档](https://www.typescriptlang.org/docs/handbook/utility-types.html#parameterstype)。

例如：

```ts
const foo = (arg1: string, arg2: number): void => {}

type FunctionParamsType = MyParameters<typeof foo> // [arg1: string, arg2: number]
```

#### 题解

```ts
type MyParameters<T extends (...args: any[]) => any> = T extends (...args: infer P) => any ? P : never;
```

- 主要就是利用 `extends` 和 `infer` 搭配使用，推断实际定义的类型

## 中等

### 获取函数返回类型

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00002-medium-return-type/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMELQUO3Bg15UL+KgHU0Cvxg9tUN4+ho9UnjhNKICMBPCAQQDsAXACwHs7qAxAVwgAoABAIaMAZlwCUEAMSBaOUCS3tICWdEQFMATtPJdFAGwZxlRIlNMRAGRmA7t2NRAsHKB-eUAUrhAAGAJVUMu6ugBVKAA6qLhCAedqADc4Q-kEAygDG6ooBDBCAIW6uHl4+0aoAPL4AfCGA2zb4gEAMNhCA0fKAQZqAWP+VLk0MAM5EcWwtKSJ0EAC8fABuAFwQ5CwsuqrCkn0FEADeRFCKIkPiy1AQ6p7evQCMm6q6LaqbUDtZvdBEAL6VDIGqEIL9EACylJl7ObmPQSw1j15sBgBBACl6gArjQA-2oAvxQgACJ9hAAD4wBFEJouSrzQAU6hAAOKKZhccgQQBQcoBT80A0O51JgMBgBFrDUGtOJMAB0ACsWhyWOoAObAaDAABeTDgAGEAHJgEDAMCK0AQAD6avVGvVEEABvKpQDHcoBAD1VmpNKog8sV-2en2+2Se+QgqgAHgxVHQACYtPgcn2CQWjYSUADaAF1ZvNA-MBr5HS63Z7vb7-S92KHwxBlGpNG4IAB+CA50Z0VSDDQAbkVYGVppNEEA0raAVejHFVjTWtRbFABbAL8lILCAAUQAjlxBLoADSDp1BOIpW4QETqFidiAAcn4Vrg7LHUzoAtULWAXAYehaq8tTwgcUEpy9AyDRAH09Us9yw9HulyXUSe8nNt2dpBLkvDpt+ygCgUkHjo+z6vu+Y65Ps0AAMx-l8AF+PaIH9PMSHIZBBTQVAT4zgwb4jghkrLgEUxOgA8uQXIvgwaG2phQHYXMEBUd2tEMUxs4EURU6keRH65AACkunaKKcuTjJM0x0IRHzoVcvycfMUnLrJeQKVMwgEYRMGifBn6aWuIgTKurEYRp6YWauVksKuQkmcxYkIciaLQLZ6n2lagILspUHuXBFGft5MB+T8AVPEFPT7G5IYXkE3HUXxjHMW8SxQIIoxBkhk5OdZKVQOQfqjKuTDHLoLlEAEOyDCBRZcJ25AaGA9xgB0dBdMFby8CMYwTAZdDpoMeYQMiow3L1-WJYNw36Upk4AO4BuwE1TTNMCVlWICtm2ZqANBygAAcoAptZHW25oKqARDzIAYEqANVyuqAMeRgAq3nSDJMiywBspyPJ8oKwrAMILRrRoYoSjKD0QDSn3fYyzKsi07Lcry-JCiKLSTMeiidHDgAvZoAWJpWEjv2o+jQNY9DUqyhaYBAA)

#### 题意

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

#### 题解

```ts
type MyReturnType<T extends (...arg: any[]) => any> = T extends (...arg: any[]) => infer R ? R : never;
```

- 跟`Parameters`一样，反正只要是想获取函数类型使用时的一个实际类型，就得用`extends`+`infer`进行推导

### 实现 Omit

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00003-medium-omit/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMELQUHnagG5wgeQLYEsAul5yFH4BGAnhAIIB2OAFgPbUUBiArhABQACAhrQDM2ASggBiQLRygSW9xbalibiSbLABsccLNXz4xeiIAyMwHduOqIFg5QP7ygClcIAA0y5bSVABUyABwCmAZQDGAJyx3HAhAELc7BxwAHhcAGggAaQA+J0Btm0Bo9UAgBlMI7BwnQCx5QGwlQC+9QAA5QCo5QEB3QFNXOwSnCUB1bUBW63DbFydAT+1AQxjsqHxAaPlAIM1ALH+c20mcAGd8LRxPfwFeX08IFwYAEwYIAG98KBxcVU8ALghpnEDqAHMDiE3PaYCgo6Zzy+u7qChfBgx3CcFptziQGAwTvx8ABfHI4DxrDbbAAK-k8ADcsJ4AO4QAC8EAAsmRIjEtgx4gByR7PQLBBTUSkQAA+EEpRxwJ0pSRyf2olwgOHJ5yRDFRGKxuIJ+x+ED+AKBnhBEGWqmmnliMPwk1sOSSEEAFOoQADiuDobBIEEAUHKAU-NANDuozoOBw7mmp2AwBmvjoADoAFbTH0Mfw3YDQYAALzocAAwgA5MAgYBgFOgCAAfUzWezWYggAN5UKAY7lAIAeGZz5fTECTqeAEEAScaAEzT1hBAN4+6Qk4QSrfSYHhXiJZAAogAPXyqNiPGLxZL45ueYcLaibaaJCAAfgg1AxiwgIoA3Cm+2tiaS4qv54vlxcyBgwaoWRcrlobg-qGxb4t9dL8ABtZEQLQBxHMcJ08KIAGtPDIBgBHWackgAXRFP8EIPWFUxAMsK2zCBAGlbQBV6KsQYsOwzMq2TLAAWDEJdggQcAEc2F4VR4hHLxfBCaEVX8f42W4I84G9ZiTluJ5gDYI41UpQ8ETlXh1RXAkf3wNjPA4qIGKY1QNOHdjgQARniE88jJbYqRpF56SYbkkiSTUoFU9TNOYnS9KVAAmIySRM0VzKeSy3kZB9KXlQFPGBGyeQQmsIH46Y4HnPSEv8Hj-F7WTFlS2djNwUyKTZCy6UCplWUpLR0WYrBNm5FN5kWZZVnWck9nwDkTg+J9bnwQrXgZDqvnwULFWVMEIU8KF0LqpYVjWRyDJaw5jjOR8Bt+f4wuBUFwUhbRJtoeqZro3S1OBdyFsFJb+ufMB0IwkjSIgQBoOTKQBTa3u7DyLAUB8H1QAwJUAarkC0AY8jABVvR1nVdd1PWeX0AyDEMw2AfhpmxRZI2jeMfoge1QfBl03Q9L1YcDYNQ3DaYIQkhlZigfVABezQAsTWMPHIcJmH-RJhGo1jBNqzAIA)

#### 题意

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

#### 题解

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

### Readonly 2

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00008-medium-readonly-2/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAcELQQEoFMCGATA9gOwDYE8IAmSeOci0gI0IEFsAXACx0IDEBXCACgAFVGAMw4BKCAGJAtHKBJbwkAnNFjyFxmKgCtkAYwZwA1snwBnUqXHmIgDIzAd26moAPgiBGV0DsMYBlrQFeBgCqVA3-6BF6MAYf8Aia0A5+IgABUQIQE5TQG34-0AKWMB5xMAgBjsIQDztQAbnQAA5QCo5QCwEwApXAAMAWXwUDBwCIgAeABUAGggAaXsSgMBg7UAyPUBIc0ASOUBZz0BvH0Bo9UAQt0Ah5UAHUxKGksAYlRLWkrSoUhWSwHBjQCztQBS9QD7owDt-QC45KqUCCfmSicA9HUByA0AxtJTAIM1AHPNAKnNABeNAbPktgKATCV9oB-eUAAkZ9R6AX4TABvKp0AV8qAb+iAoBGHUA8wqAOzMCtcLjV8I0OjlAOwW6wgpEA0fKvdIlGkMExQACWjGQckEqC0yAgDUwWAgAG9SFAGAyGLhkAAuCBGBhyJkAc0FEHQyCMWllAAdhThJdLZdgFVAoFpMABbdVihjIdCSqiYTBigSkAC+6WN2GlEAYPMwkoquOU9W5WBaAHJhaLkCGIAAfCAh5WqjVa7AhxwAXn5ivDYslACIABKGXNNRUJtUMzUM7UQXOCO1UVByYuK41mi1WyVs3BGZAlqAujZC70AOmznIzBeQuFwmFzEGAwAgAFE5HJMHJJVoBNhMAwIApUEYjAy5dgIKh94o8RB1Wv1SyGPhSF6sMOy0mq2eJw25Gw7XOF2XVd103bdd0vQ9j1Pc8IMuQhb0we85EfZ8R1bc1kEtdAIAzGUOE5QCAHlWk2Gl0kcQAKdQgABxEUmA4KgIEAKDlAFPzQBod0ALH+mAYBh1SMcUFzpLQmGHdQjGHdc5WAaBgAALyYOAAGEADkwBAYAwE00AIAAfT0-SDP0iBAAN5CZAGO5QBAD10wybJ0iB1M0x97wgP0rwDRoWlaCBkAAD0tbB0CMCADHwTBBC5HDgsMMKuXTJA3IIOpwgZLQ9A8tp7EcAAyCBCJNEV0vaTSwG02ybIgQBpW0AVeiinJayyqMhyGTNdc9z5CBaFwBkDBaJcfPvHQICdCBBDXE0414JzkDgYTUGnZB9RVYAOGFbsQ0c-BnK3HsgozABtUg+oGhg6k67rkDqVzqncoNMAARnsFp-US26Hsyvtl367QTrOgxLsqBL8EDb07tDMco1jeMVXLSscFTXqvp0K13sOxGfq6v6rrg4GsCIMGRTFCG43fCtk3hz7juRx6wAAXU0plLVZdlOVezMhQJiUpRleVSBJ2HsAAfh1bn9VIdD22tCBbXtNBsDAF0wAZlk2Q5LlvSINnYOvMdhb1A0lWhj8cCFrm9bF00MKwm07QdOWFaVpnVaO76rU1g84M9DndZ5qB3evPnkxN3UfYgcXMI7KWbdl+XNLdD0Xx9FyAeu2pGhB-GIyJqHE1Jz9U0igV2YjPNC3wZsoADz88zrNRG3L0OLYlzs5p7EsFYT0cOciydp1nedFxXNcN1DsC9wPI8TzPC8-eUG87wfJ8O8rnBu5-P8+8AweQJH7AdzHtAJ+g6fAbnxCF7ADuw6wyK8IIxdiK0kB6oauzAGg5HJAFNrZ+GvsjTQFIRwgAwJUANVyplADHkYAFW8uI8T4gJYAQkRJiQknIKSMkBBGAAO4sjkgpFSACIDsUgdA3i-FBKqkQeJSS0lgBGHtCtT89IICOEAC9mgAsTRsMQ2BZDhKiUoSgnBSlVIOTAEAA)

#### 题意

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

#### 题解

```ts
type MyReadonly2<T, K extends keyof T = keyof T> = Readonly<Pick<T, K>> & Omit<T, K>
```

- 首先使用`Pick`将`K`类型的键挑选出来组成新的类型，然后通过`Readonly`变为只读的对象，这样就达到了将部分属性变为`readonly`的目的了
- 那么另外一部分属性就使用`Omit`来创建新的类型
- 最后通过`&`操作符组成交叉类型

### 深度 Readonly

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00009-medium-deep-readonly/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCcELQUI+2gyvQgJQKYEMAmB7AOwBsBPSeOSq8gIxIgEECAXAC0PoDEBXCACgACWFgDNuASggBiQLRygSW9pAJ2z5i9KXhoArDAGNmcANYYSAZ2k4MGAA7lyUhxEAZGYDu3O1AB8EQIyugdhjAMtaAV4GAFUqA3-6Ai9GAMP+ARNaAc-EQAApoEICcpoDb8RGAFLGA84mAQAzuEIB52oANzoAAcoBUcoBYCYAUroAhbgAGACJW1pi4hKQAPAAqHvWRgMHagGA6gJ-agIYxtYDz1uWAQ8qADqaAU8qAboqACtrjgEgJgEr6gAzqgH3RgFxygFfKgN-R+VDkgBUGgPfKgKdygOIKu4AU6oAm1oCIxoAYRjKAiEaAN3KAo3KAEE1AGAu41yc0AI36RQC-inNIoBvHzkgF+EwAHpoAAdMAwAGARTDcoAxeUA9GaRC6AWblAGyOt0qgHH4wBhkYA1t0AvDpXQC-AYAsTVqgFg5QAwKoAJC0A0fKAU3MPoFAI+6Zwg5B5gCDNAr1GXMUzkZgkawYCAADQgAF4IABvMVQKAADwAXDq9fqoFgTQBGcjmmgmgDkrAAlg7bRAAL7ukiO1gmN1QL3nKCK5UQACiBuV+gwOE1pvdynaaggxoT5qgSdUpAglogNozECzHXo9ogTtd7qD+uLKZ95b9JAdZurCqVKu6eHw8eaNja2ZInVVXmAwAgpnY3CIcZoKtMWAAtiqsOZ6pHo8xY-VyDLt8GIF5HhAAOLOtjcGgQQBQcoBT80A0O6ALH-WMxmNZTEbR3LdKwAHRaUw-ngigAObANAwAAF6sHAADCAByYAgMAYAoaAEAAPqYVh2FYRAgAG8rUgDHcoAgB4YTh5HoRASEoaGKq9q0Kglj0Xhatq5C1jmADaCQQM6BAQMYJB4CIEDdAAuia3TcWJEAYAam4EDg5iYLoQE4J0pjMIofHAQANBA3AEIYBB4AA7gQXgAPwQPR-ZMVJCRiV4knSQA3GAXooahIBkRR2EQIA0raAKvR1Q8r5fmYVRyHOgu1hAcwprhgAjtwWBEPp656AlHoQCIih4Au5YCLRcDfmlRAYAQwEYKYwDcMwzpEKYbpgLREC6CuNXxpx5CZfonTJalRCdLZjFqEOHgZVGWWxh4k1gGJNHtmq8ZsRaJp8JIGpeAATDttAmpp2lVeQugmmt+o4CaNB4HgFXCO6GDne6UDAc9hZQKw70fVAzomlp3AYC9GZaI6R06QGH3VoWhi+hgRBEHgkPmtDnrkF6nltX1m5xqx7FjTmeabZqu37ZmBOlodWk6fjyY5mdOqJhTEBXRAN13dgBBM3T9BPYzhYcfQb38x9gsQF9Is-WLf0QADQM-eTPMQKD5bg1VyMZqj5pi7DDbw4jGuBlW6MeV5YBoRF-mANBypSAKbW4URVF5vgJ4ECAGBKgDVcgRgDHkYAKt5Pi+b4fsAX6-v+gEgWBwDCKYpkYIokHQfB5BePefsB6+76fqY35-gBQGgeBph3fVzqEPKruAC9mzKuBnQfZ7n4cF4nsEIdRYBAA)

#### 题意

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

#### 题解

```ts
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends Record<string, unknown> ? DeepReadonly<T[P]> : T[P];
}
```

- 主要在于判断`T[P]`的值类型是不是对象，是的话要进行递归处理

### 元组转合集

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00010-medium-tuple-to-union/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAMEFoKGFFQI36Bt4wECqDG0yiEGF4BGAnhAIIB2ALgBYD2V5AYgK4QAUAAgIa0AzNgEoIAYkC0coElvcQEsqAgKYAncTTYAHADaLxbKrKZ48Y0xEAZGYDu3Y1EB52oAbnQNs2gaPUABgBVNO9wwCqBpgAedwA+V0AYf8Bg7UAV+MA9tTRAASNASHNAHgVAELccQCAGGwhAaPlAIM0c12KaAGc8GlINXQplVQBeCABtAHJoFoAaCBaAJk7ugGYWgF0cyuqId0VSmghGz21FH39DKkDa5RCIYGAIRQAPaoBjGkUAEwgaBghiXTaWiAAfbr6nwZa8YtccrcAKdQgAOKyehsYgQQBQcoBT80A0O6ALH+6DQaBpSgAuHZlI50AB0ACtSliGMoAObAODAABedAQAGEAHJgEDAMDM0AQAD6HM5XM5EEABvJpQDHcoBAD3Z3LFbIgjOZ410AFleBoNPIicE9vtTlQzqUIMpFLwzkwtOR+KQmsMto0AN54JoABQg8kmTSobAAtrdlMNhiiILaANxgAC+YBlky8Sz8ATW7jVGq1Or1BqoRogJrNFogAGtFKQGAIIPLFcrgiEA2AdpcqroFt5I6sS3NJrHFJrtRteKRAvIlKoAJKnV1bAD8EH7ildEB9VEUADcVAGK6GaxGVkEYwc49rdfrDcbmOnG+5nW6PcMy+WQKLxVyIIBpW0Aq9GACldclfrxzJUzZK6NITZpaIAAogAjmwvBaF0AGHIoJwQIGEACMoDATi03AyggmJgToVBEtMwBsDQshaKUHwhlWEBHLwpTTI2TR4JBxw0IEwGgVowThssUaBE00A9AMXQtAALAArAAbP0NDKGwijml0PEDG8gmiQ8zwSVJIQhB0dFQScTEgWBbGLBx9bcbxMkwLx6maaMzIXq+b4QIA0HKAABygCm1nZ14fheeBbIAYEqANVy-KAMeRgAq3vCiLImiwAYtieIEsSpKwMA-ClAA7ioFJUnS3kQDCIVhUiqLoqUmK4vihIkmSpQMFo+GrOUUBbIAL2aAFiaVj5RFRUlbF5UZTS9JSmAQA)

#### 题意

实现泛型`TupleToUnion<T>`，它返回元组所有值的合集。

例如:

```ts
type Arr = ['1', '2', '3']

type Test = TupleToUnion<Arr> // expected to be '1' | '2' | '3'
```

#### 题解

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

### 可串联构造器

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00012-medium-chainable-options/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMBMEFoKHvlQTHKBUAwIeaAIEwFmqUQSOIICMBPCAQQDsAXACwHsbKAxAVwgAoABAQ3oAzDgEoIAYkC0coElvSfwAOCgDYBLAMb86qlgQISDEQBkZgO7c9UQBTqEAFL8AbvwDK6gE6qFdCFMCIRoBu5QBH6gBx6gFjygP7ygBSuaOiAEP8Awgz8qjT8pMoApsAACu5pajRpgJD-gCFugL+KgA6mOIAAcoBUcoCf2oCGMYAw-4Bi8lYAKuQKac5uHl6NgAbygL8BgBAqgGAuRYCbfoBrcoAiaYBQcoDePoDR6oDoKoD4-+YQFoCb8dWAiMaAGEZSg8iAp3IRgN9ygPCGA4A5qoBE1kWAnaaA8PqAedqADc57K4gQAJL0NIuIT8dRpAA0EA6XQggDQjCCxZT8ADOKIggF+EwAyEYAgBgGgAB0wCBkYAF40A2fKAEjlquUIAADJgeHQ0bgAazS5ChDmUHDSohpEEAMSq0gDmaTo3F5OKsdIZLD5UgJhIiZKKEFZlEFnO5EEAlkaAVR1AMr6gFklIoNFaNQBYCYBx+OFor5gHbgwBryoAAc0AE36AZb9ADnmOK2gGj5QBBmlsaUG6CiCAATNLqZEuNIQdQsFGeeM0ISqIUALkRCSSKXSW2TiYgMZRHGUngAvHGWKmhQQoAA6enaFjcADkQiYTFbULgAGZRHWII2ZUzW8kALZpbsQVt0TppBDqBLKdI0EUo1sDqANpuMtukfguacAbwgmrSmdbAAlcsomBAAOpMFzKMOtiAAXy32-rIrF34IYBgAgQB8c0AbHMizSEsywgIoVkAejNACx-ggkjoYFQXBCAACUoNLTxj0HDsmEzGgOHHUhgUHCcLwgRM3DXQcDxcTMCO3bdz0zOiklrNiPwIPioCDGktgGQAr5SJCwVkAIR1ACN0r49kAfKUhj+QBYOSJL5LkAVXloUcYBrEcWD3kAUTTAG4EwBE+O9KACAGc5AHEFQA+6NpNU+VEwBS40AdeVAHVtQAyb1QQAYANpc8+Q8rSzTEolABazQAmdMAYO1AAJ5QAkBLg5Y-LUwlAG34rEtMAEE0xhxQAYFUAdgsVRpZyIFE4JAGqIiJKkAQmtLIgAgAD5tggABxVRGA4UgIHmQBT80AaHdEIYOg6AUFF0yAkMl3rAArFFGxcIVgDgYAAC8GAQWIADkwBAYAwEO0AIAAfTO86LvOiABiKQBjuUAQA9Tsu56TogfbDrnWF4kSZJUjSAAeNoIErY8PxakGCF3Fh-oAaQgNIAA80JoMN0S4tcoQANSalk2UzABRBGow4CNYahNUmCEaEmo5fguRozHREzb6cz+wGIAAMmwyNnzDMmIGxmmCD-cVMzaABuMA+KOkAnpei6IEAaVtAFXo8IfTl+Wzreg7VHHBRn3w6g1FZKFCa6dRPA-CAhBcJhxxnXhPoXJc6dXddgA4bRlA3Q6IyjQ9YwLTx+GZ7NfrzMAg8g6C6GgYGIH4Ahh2bUciOnPtv2TvdWyYk8zzp7lLxvFd7yfF830-TOodHajp1nedF2XN2oM3JORYHSOE08Ys8PgStE53Ec21rqFW0EJhGGBCBa+-ICIEdlEEER826CXlxbZcJPq+H-hJzr5FCxntvRXFD75zjVEoPjgBtAgzcjOh-qoY2Aadyno7w6BTYRle0jDaAmpCygPfC2T8X7-TflTHuZZYDf1-mGWAgCIRgAALpn1hCAtC-946sWtp2EiZEKKbygExFig4OK0ToPRHin4CDUU4lQ7iUt0GxkwX-PuEBcH0ModQ5hh0wDHU1grQA0HKVEAKbWGtNbawEeAKALVABgSoAarkbqAGPIwAKt7DVGuNSawBpoMDmgtZ8y1VqCBRAAd2BOtTaO1moQEGuozRY0JpTRRDNeai1jGwGACiJgXIU6hjkRAQAL2aACxNUwjjtEuLcYYpaVitq7XemAIAA)

#### 题意

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

#### 题解

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

### 最后一个元素

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00015-medium-last/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=30&ssc=79&pln=30&pc=1#code/PQKgUABBCMCsEFoKABzQcCqAA5QVHKGFFQBL6UQSOIICMBPCAQQDsAXACwHsbKAxAVwgAoABAQ3oAzDgEoIAYkC0coElvSfwBOC-uQIEJGiIAyMwHduaqAD4IgRldA7DGAZa0BXgYAqlQN-+gRejAMP+Aia0Bz8RAAKAJQiBOU0Db8Q6AFLGA84mAQAz6EAaAFOqAJtaAiMaAGEZSgF96gHXRgP7ygBSuACrkAA4ApgDKAMYKAJb5dBAALAB0AAyRgHnagA3OWIBYCVkABgAy-ADOdAA8OQY9joDB2oClxoDryliADqaAI349OT2AbnqAK-GAe2qAbopoWHiAIW6A3j6A0eoRUASA0fKAQZqRPU90gwR0BYUQigrQEAC8EAA2gByfjAgA0EGBpAhUNKwIAum8Pl8lAAmf5AgDMkLRkOgSOuUHeRQgdH4FQANr8AQNhiNvtAjMBgBBCgAPIqlOiFAAmZKYEFIn2B8ORpPJVIxtKGo2+aOZrI5XJ5-LoguFMAITx6kSM0QgAHEKowOKQIIAoOUAp+aAaHdAFj-DDodHygwAXCyXqUGPUAFaDepMBQAc2AcGAAC8GAgAMIAOTAIGAYGToAgAH0M5ms5mIIADeWOgGO5QCAHuns2W0xBE8mSZ86aMcmz2TyaLzBhAFIV+LyWJTKIJyICEUYAQ2Oc3W0D6lOKjQhIUFBAvJCZ3OF30ERAAPwQPoQV0QHIAbjALLJKLrY0b47bHa7Pb7rEHw6B-chU-qOQRgJyIMphRoQaMIiCLHimICluWWYQIA0raAKvRWQ3BBkEZpWSYVAAtvkgY1AA3hAACiACOHD8JSkL4ZyhTchAAC+EBCAoTDoVCvA1ggXqkX+AGFIMwAcHQVKDMC1YoqUQw8ZigIEBRKojERJGUiMF6AjiEB4jAQ74gYBjgtJlHcnJxGkUpsojIC3DiH8RjQGiql4fw+7DJUAG0ZpED2Y5dDOUGtHabpSLJie4HIVBgDQcuggCm1khyGoUFBBGIAYEqANVy+aAMeRgAq3g6Touu6wCet6foBsGoawMAgiDAA7vOEZRnG8UQLaGVZc6boeoMXq+v6gYhmGgxMJS-EVCwryGBAgAvZoAWJq6M1OVtR1hXdTVMbxlWYBAA)

#### 题意

实现一个`Last<T>`泛型，它接受一个数组`T`并返回其最后一个元素的类型。

例如:

```ts
type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type tail1 = Last<arr1> // 应推导出 'c'
type tail2 = Last<arr2> // 应推导出 1
```

#### 题解

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

### 出堆

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00016-medium-pop/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMBsEFoKC-FQYBqUQr2MCMCeEAggHYAuAFgPYmEBiArhABQACAhuQGYMCUEAYkC0coElvQewBOE9vgwYBCiIAyMwHducqAD4IgRldA7DGAZa0BXgYAqlQN-+gRejAMP+Aia0Bz8RAAKAJQiBOU0Db8RcAUsYHnEwEAM6hAagBTqgCbWgIjGgBhGQoBfeoB10YD+8oAUrgAq+AAOAKYAygDGEgCW6WQQACwAdAAMgYB52oANzoAAcoBUcoBYCUkABg5U6QA8KRrtloDB2oClxoDrys2ADqaAI37tKe2AbnqAK-GAe2rNgIU2gJDmgADmgHAqzYDCioAEvoAhbjMBUBiA0fKAQZqB7Q9kAM4YZBmZEJIS0BAAvBAAbQA5OwgQAaCBA3DgyG5GFAgAmQIAuq93p8pAAmP6AgDMEMxEOgqMuUDeWQgEkyP3+XV6X2gWmAwAgmQAHllcmRMgiIGQqBBcB9gaCIVD4XCSXz0VTsbTuj0vpimSz2Zzubz+YLhfiIJipQ92oEQCBALkZgDRNE2ALH-ADAqgHYLSyACoNAPpygHvlQCncvV2tkKAUuGRBp0GE8KO1ADEq7QAqiQQ36A4B0FUA+P+BLTBCAAcQKlAYuAggCg5QCn5oBodytFDIZHSTwAXMznrkKOUAFZPcpUCQAc2AcGAAC8KAgAMIAOTAIGAYAnoAgAH1Z3P53OIIADeROgGO5QCAHjOF9vpxAxxPyR86X1WWzuSQEU9PrQAcitP8Uqfz5fAeU3wUSFxMhIIE4IZx8GRCAAH5fwgKsIBSABuScWQtE4wEPCAfTjE92WfK8ANve9IKfTILyvAEAIhN9yg-L8fycIDQJcCDoMQ9EHGDCg0LPfCXyw5EIQAaRwgFSJSHjkRgpDo1jf1WIw698FvHi+O4ki3xSYSJzAKcd23CBAGlbQBV6KSK4tw0xd9wKABbdI2xKABvCAAFEAEcGHYAAbCFbI5TIuQgABfCAuAkKhTMhVhDwQesXOc-D20yJ5gAYMgCmcp4gQPdFcnYJ4YpxAEMHc9UegcpznJ6Y8AV1QkYDvCEyoJO8NDBXKPK5ArHJckqFRFeFoTFOExSRCEqsBEEuolFENHqxr8sKtqUIkzqxW62Exuq8UerGiaoDyzyyBaoqSuYnoar1IkuIgSp6rxAkiQhSo6oazamp26birE305uGhbRtOxEgQu4F+shUVIUWoFJXGhrUVU9SjMXQBoOQaQBTa0MmG93HUAMC0QAwJUAarkV0AY8jABVvUty0rGtgDrBtm1bDsu1gYBOCeAB3b9e37YcMYgYtCeJitq1rJ56ybFs207bsnioZz4oKGgXk0CBABezQAsTVUHnSf5wWqZF1nBxHfcwCAA)

#### 题意

实现一个泛型`Pop<T>`，它接受一个数组`T`，并返回一个由数组`T`的前 N-1 项（N 为数组`T`的长度）以相同的顺序组成的数组。

例如:

```ts
type arr1 = ['a', 'b', 'c', 'd']
type arr2 = [3, 2, 1]

type re1 = Pop<arr1> // expected to be ['a', 'b', 'c']
type re2 = Pop<arr2> // expected to be [3, 2]
```

#### 题解

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

### Promise.all

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00020-medium-promise-all/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMAMEFoIAUBOB7AtgSwM4FMA6AQwBtTJEFqbKAjATwgEEA7AFwAt1WmAxAK4QAFAAFiHAGYCAlBADEgWjlAkt4LiqVMSby6A7KXYJsrSpXnmIgDIzAd26moAPgiBGV0DsMYBlrQFeBgCqVA3-6BF6MAYf8Aia0A5+JQAJQhATlNAbfj-QApYwHnEwCAGOwhAOlTAU0VAX8VAB1MAAzQsPHxmcnyAwGDtQFLjQHXlIpwCABlsAGt8QE-tQEMY3MARvwDAFfjAPbVAHgVAFL1ALjlCjEb8AB4AFXsKwDdFRXyF-MB6M0BjyMAE80AQt0Blv0Ac8z7UqEp86-ZcSgBjHlx2CAAHGZKARggAXhQPgiEVD4XDoUgAN3wwgAzDIANwPJ4vd7FAjQX4QAAs0ARUEerGebwB+GhGNY+AA7v9UfNnqhjABzezCYTA0EQ-AAGggwIAVvh7uw5D9HABvShQAjsBbYTD4dACdiskFgyHcz6wWDcgDkknQ6G18MoAF8jZcoMBgBB8AAPV4C9j4AAmEHY6AgdHwEGmNLmAG1WAJMJ7UNzsdy6YyALrLREE5EYholEjkYR+lGzT7cjMlaDZ4nQqMQYi4CD454yK7XNKOQAU6hAAOLYLgCOgQQBQcoBT80A0O6ALH-OOx2K9cAAuS23e6cQi83CEdCoBnAODAABenAQAGEAHJgEDAMAH0AQAD6p7P57PEEABvIHQDHcoBADxPF+fx4ge4P7AY9ogCwEr1I+ALF+8wLNaNqOqwTqlhIDB+jGGLilAfrIBAxgQO0DDoJIP5RiOP7IUWtoQVB1KzHMxiSPgqAQOEjgAPw0RAeELARYDGhAB5OgKpDqF60isIK2A8KRJRlKQixgcR0G8HBzLgmQAggnhwLEE6PCkEwfqENpCxRjIeFJgQix-gBQH2os9j2AeYBHi+z4QIA0raAKvRgAUroA0fJPnZl7vrKrzzi8ooQAAogAjgIZDckFdoOhA7GSDMEDaqIn72ggk5kABrAMiCwCKvouDage5bIsSYkLCC7DfH8hmlKmfpZjA3KFsWpbFZWxVEjSZUVei1WlXVDV5iJgJsqqUKwkWJZlki7VIp1szdc8pJ9V1A3ckNNVAiqHIwjIekfsBZYliCGJ+pQUX2oKcyheF4kpfK2E5gQi2VdyNX+oNTUxpZnLndFV03WQcz3Vh82ieQ5XPBtxIfet3KBsGVHffYv1QBdDrXWFQMg49-WkJD7DQm9MMBkGIbw2TVEU4jqDI2AUbWTZICeV5r6ANBygAAcoAptYs15b77qAlCOIAYEqANVyN47IAKt79oOw5jsAE5TjOc4LkusDABIuAUlRq7rtuQsQD2Usy0Oo7jrgk7TrO86Lsu7J5U8BuAC9mgBYmjYJty+blvKzbuubju75gEAA)

#### 题意

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

#### 题解

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

### Type Lookup

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00062-medium-type-lookup/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFBsBMUC0UAqBPADgUygGQHsCBrAV00hiWpqUqgCN0oBBAOwBcALAt5gMVJQAFAAEAhpwBmpAJRQAxIFo5QJLei0mwCWvRQFtxFaFHoLTUQBkZgO7d69AHxRAjK6B2GMAy1oCvAwBVKgb-9Ai9GAYf8Aia0A5+KgABQAlKEBOU0Bt+N9ACljAecTAIAYbI0BIc0A30z9ACoNAe+VAX4DAGD1AbHNATgtAO2NAN0VAPR1AcgNACnVANz1AMbSlQFPzQD8jQG8fQGj1VKN6BsATa0BEY0AMIyU-QEQjQBu5QGeDQCwEwHH4hsAVAMAIFQADAGFxDigAHygAEQIAcy2lQBwTQCJfQBtFQEdFLY4sbC3AdW1AVutAU3NAduDAGvKgA+3QApeoAQtz6yUARsaAU+VALvRgBfovwNQCncoBoOUAXJ6AaPkpnNAPjmJS2hBIAFVMAAeM7nI5QPYcAA0UAA5AATC5M2xbP6AdP0tlStn4iURiGTKRcaXTGUyAMb7Dlc3l0rYDGD0LbqjgAZ3omk42AATlJxNLcHSoABvegwV44ABczNlHCZVsY+uw2BZmvtTJYTE1mt1mkkTJpTIAyjx9dxxJp9SHjkydqR9QAbePMgBC2DY53EafoAF80jBdRwDUaTadxZajNa3t62ednbXXe7Pd6ABIEDQs9NMjP6zQcDiSdB9jOkFMpxvjggADwNzZgMGlBBTBH13oY+oIAHc2H3d1wh9g+wwU8biEuoEXBkYbbgALLoKlQAC8+GForNxypUpn9jAMAUDYHOODSmWLJQBwBCMLgfIXFsarqsWUD2A0UAAOJDlwpAMFAgBQcu0gDQ7oAWP9cMOmBekBWrSlwAB0ABWmr0Ru5zAAgwAAF5cEgOwAHKQCAwCQKJECgFAAD60kybJMlQIABvJgoAx3KAIAeUlyZpklQMJYkPp+pIUiSjKoCBc5lmwnpQJqHCDjm9gfiSZkWVZ5rQfWaA3lAAD8UBOfabDYAAbgaADcYkReJIAaVpslQIA0raAKvRgAUrliMWxdJOkiRAmi6JgG4HG5ACiACOpB5oyRVgdgEFeVIO66MyogPkgdF5im2bnNgmrAKQHCaCmmrNpApblsapr7Ba9APt6jrXtubZesyvroP6gbBqGEYFVwMZxqGSapuOnV5s2t4jXqhrjVW1I1nWdrMjO9ALR6S1Ml2PbjoOw6juOk7TuyoYZvOi70Ku66bsy257geoZHieZ4XtKV6QGdED6ewuV5u+tKTb+Fx6W8UCypq3XYwA2vQVXgRw5KleVKbksSIoUhj+gpv+7K2IyVK2FzlPVRBtNlXmjNfizWhs1Kc1czjHC8-SkAALqRZAEkZXFaKAABygCm1ulGVZar4BGPYgBgSoA1XJKYAx5GACre5GUdRwC0QxzGsfq7GcZImq7ga3G8QJdhQCRNt2xwVG2jRmp0UxLFsRx8DAJqa59dobDasbUCAC9mgBYmlYIdhxHUcu7HPF8YJEC6RAkBAA)

#### 题意

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

#### 题解

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

### Trim Left

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00106-medium-trimleft/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAMBsEC0EAqAnAlgWwgGQFMAzAF0mSUqvICMBPCAQQDsSALAe2YYDEBXCAAoAAgENWRPgEoIAYkC0coElvOSQLYADgBtRqpJsyr0ozeXKzzEQBkZgO7dTUQHnagBucIAAww5CpADyoAfC4hAGH-AYO1AUuNANlNAOw9ALO1AELdAdW1AMm9AJjlAbx9AaPVANz1AFfjAPbVAADlAKjlABtMElMDAN0V5Yryy5MACJUATNMAwuUB85STkwAB9QBZNWMAvL0BfNw7AIAY7CEBo+UAgzTGXWZIAZ3ISOnUCCBIsbAIAEwgAXjRNzxIvAHIoAAkCTU0OCAB1DnRNXYhT3whgYAhAFL1ACuNAD-agC-FN5XG53R7PV6ncizFxjD6ACnUIABxAxsPg0CCAKDlAKfmgGh3QBY-2wSCR1PMAFxfBYAYzYADoAFbzelPADmwDg8GAAC82EgAMIAOTAIGAYAloAgAH1ZXL5XKIIADeVigGO5QCAHjKFdrpRAxRLlqsIABldSiGlrA7nc4AHzeAB1mLaHSRTgBuMCGtbubDHLzGiAEAAeqmY23mEHmG0wzDZHwOAeDofDrgAJABvU3mggAXwzMaIBHQEAASjmAgB+Q4eYgnEsfCkmj0SsBSnXaiCAaVtAKvRgApXcZa9uK-U4dRPEgQdMQACiAEc+MYADQzoOrGkTnMQIjoDi4U7CL1IOnGTQEWMEebAPgkTCaeYwz0rNY00TzC-7CAAbXI09XBHXXhzgumg+EctZnFG6DvMupyQe8viLj+f4AUBxigTW3jnHBCFvNhCFIWuJyoSBPp+ucUBQNhMF4YhUC-oRgHzmhpHgeRFGQRRUDQbhGycW8vj4XRyFEUxJFgZhFGOvaE5EBwdw0KIxbcacsnyYp-GCSuDHEehvqscp8G0Vp-4icBulkRAUmujhpyGWAAC6LatiAg5DrqgDQcvkgCm1q5Q56uKoDkB8gBgSoA1XIqoAx5GACrexKkuSVLALSDLMqy6AclywDiPMADuRa8vywpBRABLRbFZKUtS8x0kyLLspyCDAPMHCaNemBcIsUAfIAL2aAFiaNhlfFlXVSldV8oKIr6mAQA)

#### 题意

实现 `TrimLeft<T>` ，它接收确定的字符串类型并返回一个新的字符串，其中新返回的字符串删除了原字符串开头的空白字符串。

例如:

```ts
type trimed = TrimLeft<'  Hello World  '> // 应推导出 'Hello World  '
```

#### 题解

```ts
type Space = ' ' | '\n' | '\t';

type TrimLeft<S extends string> = S extends `${Space}${infer R}` ? TrimLeft<R> : S;
```

- 主要就是利用模板字符串来进行拼接，然后作为判断条件，从而推断类型
- 通过递归的方式，将空格`\n\t`等字符排除掉，就能获取到最终结果了

关于模板字符串的文档请看[Template Literal Types](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)。

### Capitalize

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00110-medium-capitalize/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCMsAYoFooGECGAHAlgFwwBscAvAU0hmWpuUqgCMBPKAQQDs8ALAe3ZYBiAVygAKAAIZOAMyEBKKAGIAtmQAmOIcqV4yyrIQy7kxXQCci9eoptQAikLIBnPDj5XoUAJL7CespxQAAaYuATE5AA8ACoAfEFQAO5cOADGXFCpfABuZGZ4TlDcZFDSOGYuUH545lA80lAYUC5mOOwA5kU8UEJYWHmpGE4lUmpVZBi5RVwlZs54jU7IOE4AdB4wAjxmUGQAHhi+FJ70QWcF9HhM-ZnY+ESk6lAAvOh34Y+RAOQzhITdiW2hDUX1iUGAwF2e36qV0Yzw3QYJS+AAkyH8AUCQaczhsoGCAGo4MiJOrsKAAcXwKKEDAAXFAuDUsE46RCCulVgArNbbdrAeAISAgYCQMUQUBQAD6MtlctlUAAmjwhDs0Dw1CU0XNpfK9VKoCLxVcbgBZbC4DovKAAb3oXwwXwZX1YXwANPaGE6oF8AELu+2pb1fNABzxfEHOgAiYZgXzIwYAorGfdJgwIU192sGKZmuMGUZmcMGvJmucGAFKZgDWwYA0pnCMGADKZ5TB02Z9jBgBymZ4wYA8pmsMGAAqZgCOwbsmbMwYASpmnMGAMqZvDB6KZoTBgCqmeywYJmcSwYA6pm9sGABqZpjBxWZkjBgBaYYAvsbriVTUxQvcERkJEq5Qro7BqIULRtO0YKvFAoH7OBkHBAAJDabTSHkUACB+6GYdhC4fgkAD8aE2gIYEBCh1ZkEw9RQOafQwVAZFMZa7QANoCAAulADK4ehREJAyq4ANzipAkr6nqUDRPMbzDIUMnyoaooQDg+jbAsNpQImk5CEQbp6dCZCwlAH6lGYPDaF84gmmQyDpEQfgdM4wBCK4hArt+NyDEp1qcfQiambCkT6YZhCRH+AEfFEXzSDwPAMBg86xMZXxbMlqWgulwWhXg4UGUQ0X-u8DzxQIg6Dr6rBLulPpVTVdW5R6nghTChURSVMXlUB3yJYiOUNZlSWMMNeXtQVRWRaVsUVcBXyghlrX5Z1M09WVYQLd8joja6sSTTAHVmV1xVRb1239V8Xojf6h1tcd03dRdW2AZ8XxBiNoYPWtp0ba983XSCI0xr9U3rS9c19R9CYjcm4NPZD53Q1dH1piNGaIyZyOzZd73xdmI25tjJ1hVD+NxYt+YjYWpPPSjlM7V8xYjaW9O45tQMfeWI1Vhz-0U29VPfLWI0NgL5OM8LzNNiNraS2deMy9d7YjZ2isA6jBOLd2I19prQvc-FA4jcOhvS8bi2jiNE4W8rVvfNOI2zvbXMw-FaUZfVR044Llse4tK4jeubuA4H3ybiN25h9rItfLuI0HrHTPXUeI0ninKsfWeI2XlnjtfNeI13gXEdfA+I1PmXaPxS+I3vuDPGSRAUkgLqKkyjhqrFDsq66CyHed2pbf0GCq5cKlJT0aqzQ8IQnluOwrKMsyrLsk4nI8qsfICogwBSE4iR5GPUBEiSc8L64fAr0yeAsmywAclw3K8mY-KCsATjz4vN+n6a2wShoEnn8AI7RnAMjvg-DeW837tGFOpSAQA)

#### 题意

实现 `Capitalize<T>` 它将字符串的第一个字母转换为大写，其余字母保持原样。

例如：

```ts
type capitalized = Capitalize<'hello world'> // expected to be 'Hello world'
```

#### 题解

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

### Replace

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00116-medium-replace/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCM0GwQLQQEoFMAOAbAhgYzUiURNKICMBPCAQQDsAXACwHs7qAxAVwgAoABHIwBmXAJQQAxIFo5QJLeUhmgC22HIsRYAlooBOOLESKTjEQBkZgO7dDUQHnagBucIAA3SqCAHgDKAGggcdLJd4AKiwAfA4QgGA6gOragGTegExyju7h0oAhboA03oAAcoBUcoAK2rEJDr7+4YD-ZoBGxoBcco7B4YBADFYQgNHygEGaDQ7tDADORAyUGGgQOpi4BAAmEAC8qMP4aK4A5L39nRA4QxCidACE897zm7sQ8zgA7mid-mjzIRDAwBCA+OaA2OaA9GZHS+er66fnlztE7QcDRugAp1CAAcW0TC45AggCg5QCn5oBod0AWP9MBgMDCdABcdy6eCYADoAFadQksHQAc2AsDgwAAXkxEABhAByYBAwDA3NAEAA+gLBULBRBAAbyKUAx3KAQA9+cK5XyIJzuR9pi45u4IGgAB6KOijFadBg6TR0SneIpKTU6tB6g1Gk1miDBK26-UQQ3G003KZgC0um1u+bzCBgAD8EA1YGxEf9tscABIAN4m4RoHQ+AC+SYtWeTdFT6YAohnwkRww5s7ngrni0CoNH3ABublgXnyuUQQDStoBV6MAFK6NWXtkVKzQqCkMCCJiCFgCOXH03kLWv6eAnGY2fkt834H0QBP0WBtlPOwC4DE0WE682VfQGeBwnU+UwA2kQlyuGK5Z-OsK5nCM5n2FgWHINZDnmUCdHA4RgOuPYYJYBDrhCTw32XNBVy-Od9D-GY3CAkC1kg8DiPg2CUKOBCEOIkIULQj8sJ-XC1QWaiiLAvZoPIsjCJ0Gi6Kgd8MM-b8cP-WZWOAyDSKOcg9C4lg4MoqT2Kg2jUME9DMNE39xPw8CDIooN1LAABdFtWxAQchwVQBoOQyQBTa2sodFS5UAiBuQAwJUAarlxUAY8jABVvNEMSxXFgHxIlSXJKkaXgYAhE6M4dAZJk2Q8iBkUC4LMRxPFOgJEkyQpalaWAC4sDPTQ2G6KAbkAF7NACxNCxstCvKCqi4qUpZdklTAIA)

#### 题意

实现 `Replace<S, From, To>` 将字符串 `S` 中的第一个子字符串 `From` 替换为 `To` 。

例如：

```ts
type replaced = Replace<'types are fun!', 'fun', 'awesome'> // 期望是 'types are awesome!'
```

#### 题解

```ts
type Replace<S extends string, From extends string, To extends string> = 
    From extends '' 
        ? S 
        : S extends `${infer F}${From}${infer E}` 
            ? `${F}${To}${E}`
            : S;
```

- 主要还是基于模板字符串，然后搭配条件类型来进行处理，跟`Capitalize` 是一样的道理

### ReplaceAll

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00119-medium-replaceall/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCM0JwQLQQEoFMAOAbAhgYzQEEstIlELKyAjATwkIDsAXACwHtH6AxAVwgAUAARwsAZrwCUEAMSBaOUCS3rOZoAtthwrEWAJYqATjlJQyMsxEAZGYDu3MmUB52oAbnCAAN0GgsSwAeAMoAaCG59dlUAgBV2AD5nCEAwHUAAOUAqOUB1bUAyb0AmORcfGLlAELdAASNASHNABW00zOcgkJjAf7NAI2NALjkXCOdAIAZbKEBo+UAgzXaXZ2dmAGcyZloMNAh9TFwCABMIAF5UafwiEi8AcmYIegwICcGNgI2II9ONyIhgYAhAfHNAbHNAejNT0fHDsn7nXsvACnUIAHE9KxeNQIIAoOUAp+aAaHdAFj-rGYzAwgwAXNchnhWAA6ABWg0x7H0AHNgLA4MAAF6sRAAYQAcmAQMAwMzQBAAPoczlczkQQAG8rlAMdygEAPdncsVsiCM5mvCZuGZrbw+fYADxUjFmgwgg2Y+h0jEJAUqqhVao1Wp1eoNEAiJrQ6s12t1+suCzARtt9vOEDAAH4IEqwMj-R6zc4ACQAbz1YjQ+kCAF9I0bE1HGDG4wBReMxMh+8MR7gpiIpuWrTxeDOG4Kha1RbNkIM+ADczLArPFYoggGlbQCr0YAKVw6oo7PKlOnUBO2EYgGYAjrwjAEM8rxnhtvGIGJq6chDLEBijFg7YS0INgLxmDosO8wDKIHgcIMT4sIABtMhLlfMCtzoxeUsedYNjEdh2GoHB9DODYwIg45gPYC5YJAuCLkiPx32XNBV2-edvH-BVNjg6DILAwlIOQ1DTkI8CULQqAP0wr9Zxwv8VgA7wgJA6CiOObjKJAhC+PYODyNQ9DP2w388PLLYdggPYDkg05jgErYxhPGixIYiTcNY-CONA8DeI2Mj+Io-SuOoyJRLojCsKYyTdOk6DkJ46jEPgszhNM6zp1sxifx09w9Ko-QQsg0DiJU4DnIMiCrNo3zxPswL5Wk6KQNC0D2Dc05qHgnioqyzKiPizS7ICligukyCarMjSAF1WzbEAh2HCVAGg5eJAFNrVrh0lJlQDIS5ADAlQBquX5QBjyMAFW84QRJFUWAdEsVxfEiRJeBgFEQYAHdYwpKk6SGiBoWm2bERRNFBgxHE8QJYlSWAQZ2Cwc8dE4YYoEuQAXs0ALE1rDO+bLuula7v2ml6SlMAgA)

#### 题意

实现 `ReplaceAll<S, From, To>` 将一个字符串 `S` 中的所有子字符串 `From` 替换为 `To`。

例如：

```ts
type replaced = ReplaceAll<'t y p e s', ' ', ''> // 期望是 'types'
```

#### 题解

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

### 追加参数

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00191-medium-append-argument/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMCc0QLQUL-xgCpUEPKgHU0kx+DcAjATwgFkBDAYwEsBTAKwgGVaBrAewCdKIAKAAIBbGg0YBnDj0oBKCAGJAtHKBJb0WVuAcwCuw+gDsALhNy4F5iIAyMwHdupqAD4IgRlcIgykYBm2iIG--QIvRdhCAedqADc6AAHKAVHKA2zaA0eoQAAYAggAOyQYAJolaugaGADwAYvoANBCJ9vGAMP+An9qAcXKAm36AWdqAIW6Av4pYgN4+cfFFVYCncoBTypGA33KA8IZdCYlVgCvxgHtqkYANpm1YCQDi8YBADPHrEICnRoCQ5gm9EM2AAkb72IBueoAU6oDU5oB8OuhdgFxyk-En2BuB8T+GJGkSajcWjJQy4P5pCBFCAAXgElAAXBB9LoiPRuKUiEiJIZgfpNPIYY4UcI0dxAhD6BAAEr0CTaAA2hlhZVSGSyOj0RkKJQgRE4nAZ9HcjlwwGAEEA+OaAbHNAPRm8KRJLJmOxuNo+NKAA8kfzBcL9ITiaj0bgfvFAo5ADTmgERjQAYRoBTc0AV4G1CAAbREYiYUi4vAAuvwABaGQzJCQI8WaWiGAPaIgAOmonGEwFEdC90l48muEAAIvQAG5x2icCCAKDlmq7AOGmgAOvf1BkNh8XpAtxwycFM0b0yYCUdL59zUejpRCUwHA0GIeia9F0CR0xAAR20dMMxf0iAALJvYABWaiyC0QbOrKMxohlwCn5oBod0AWP-10Ph4DGagBuOSOM8TTAODQYAALwDRAAGEADkwBAYAwCg0AIAAfXghDEIQiBAAN5ZpAGO5QBADzgpDcNgiAIKgylWTSfRMmyLl8iKUpyhZaEp0MDIJAEONWI0TQwwgdUPHRCAAAVDS4-QeO4GkIAAfhY1jtTdVi4z4mjfUE6kICRIoAG4oK0mC8NwiBAGlbQBV6MACldAGj5HDdOQwjaGEZIeGZABvCAAFEl0oBlSmczU0moZkAF8IA8bgkwgAByQRKUQF93KFfE6WAbRVwZCRQqI-4qSAyg5wQOEUlI8jOVyPJ+ERZFjQxPlVTxAlYSNUl0UxAUhRFMBiNpekmRyhUyvqiqsQgHFqq1HUmv1QSlRNVr0ogTK5wAJhZPL2Qoor+EE-NOFodJSm0Mj6A8dUh3sKbIXaxlDAWuF+Bk3bmwO-Qh3Wzb0jSyFqCyukWVdXAvJ8-JXO0dy8lm+hoFKM7Ovsexih+7z6F8vIAaBkG5vBulzrmqGYd9LSwB0yzkMAaDkwkAU2sLIJgjINAXBHEAMCVAGq5NDAGPIwAVbzvYMH3FZ9X3fT9v3gHt9AkAB3dF-0A0CaYga9WfZhtH25t8JA-LR+d-CRBUStcTAcCBABezQAsTRsOXOafQEeeVvmAOAsDCLAIA)

#### 题意

实现一个泛型 `AppendArgument<Fn, A>`，对于给定的函数类型 `Fn`，以及一个任意类型 `A`，返回一个新的函数 `G`。`G` 拥有 `Fn` 的所有参数并在末尾追加类型为 `A` 的参数。

```ts
type Fn = (a: number, b: string) => number

type Result = AppendArgument<Fn, boolean> 
// 期望是 (a: number, b: string, x: boolean) => number
```

#### 题解

```ts
type AppendArgument<Fn, A> = Fn extends (...args: infer P) => infer R ? (...x: [...P, A]) => R : Fn;
```

- 先利用条件判断推断出 `Fn` 的参数与返回值类型
- 然后通过 `rest` 参数来将新函数的参数组合起来就行了

### Permutation

- [ ] 待仔细思考？

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00296-medium-permutation/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMCcBsEC0EAKBTATgWwK4BcBDfASwHsA7SZJWu6gIwE8IA5Qs-MiASQGtcFbgAoAAgAdCFACZSyJTAEoIAYmzppJXNlWDyVKNRXGIARVzoAzqUrVqPbOIA26dRXwRxWPERsUI+ExeAQAWxAGYUpYAZmQ4lhB6lAFBVhAk7tz4IegQhJiRLNnhGQDGTrjSaV44BMT6CWTRiRQNAHR2UAAGPYFelqWYJOL41H25NToAvGjedX4APADkAIJLEAA+EEsAQutbSwDCSwB8ANwQwMAQANqrSwA023tPR0sAupu396-Hr3ufLZ3F7bNa-D5fYGPbZ-UEQoFvV5g57w76w3bQ+7vag9LqdCAnCAANRI6AA7hBkgBxEj4AASuAYAC4ICF8PhxJYmVd8AMQm0AFaWNpxADmwDg8DAIGAYDloAgAH1lSrVSqIABNMi4TAQQ5kKoQOlYXJqs3KiAyuXjWa1Xz6BYAFSeAGkIDNHYSZtQbo7PugAB74dAyBI3CjoABuWGxUAA-LdYxAWW7A8HQxA3dR47cXU82gWMHb6pQFgBRAPlSroJ2uk4nJNQFkR6OYM5y+UgJXmtUQR1WDyHQiWNI93tWkiOOIeADeEDLAEdcIQnE8K15Sh4AL4QaKYMg6JaicZIUphJwuCiiqzAAgkJyWJbW1IQUrDtIzG7UdfoTflpcrgsRY+CWFDLGsJxPHcazvPWDzfgGG74P+y5OEBcz2qW9xfBiOHHJB3zIhi4KAoRmLogCkLEXCJFUSCiJwqRdzokRlEIhRmIwXBCFIShgHAfMDq4Qcax4acUE-CitEIkRHFMdRklvPJ9EsaizGcZibFoppGmwZBPG-shi6oehxaLC2WAETcenwdiHYKmOqoQAAYjq2RYBAADKwact2jmKpasqgNQhKeWEmC5Ew2q6pYZAVH4XKsuynLcsAvJnoKwpihKCDAFEZJYCFxKkhSsXxQ0LJshyXI8nymUipg4qSsAZV3pQlhFQAsnEuSHOel7XolVUpbVGVCg1orSrKYBAA)

#### 题意

实现联合类型的全排列，将联合类型转换成所有可能的全排列数组的联合类型。

```ts
// ['A', 'B', 'C'] | ['A', 'C', 'B'] | ['B', 'A', 'C'] | ['B', 'C', 'A'] | ['C', 'A', 'B'] | ['C', 'B', 'A']
type perm = Permutation<'A' | 'B' | 'C'>; 
```

#### 题解

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

### Length of String

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00298-medium-length-of-string/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMCcAcEC0EAyBTAdgcwC4AsIB7AMwgGVcAnASx0mSSeYYCMBPCABRuwgDEAhjggAKAALYkABwA2w9AEoIAYgC26ACY0ArmtW50auYMNJZNQ1UGyGDFQ4gBFHegDOuGkUx2oAYSJjHUMIAnQIWSw8QlIIQQgPWhELKxsAGggAd3waAGNCVnR8QQA3dwiaAGtwgANKJOwVSJwCGoA6XwgAPggANRp0TOJMCABxSwAJHVYALgh8XFxpNxngYFw3fLaAKzc2oipsYDh4MBBgMEvQCAB9O-uH+4gATSIdKggAzXCJ9Cpwx6Au4Qc6XXDsaThDAtfAAeRI9To2AAPOQIOgAB6GTCaNwJahIjIAaXRWKwuLimHYAG0ALoQAC8EDpPSZDDRmOxFJqABIAN50Eh-AQAX35guFAFERTUGAB+NBRAjwxE4ZGSjLUtraokZfi0roMOZE6kAcma0VNtIA3JcwNcgYCIAAVdy4T6CNzlR2PEEXGjGA7uvkQSUARx06VDGMhuXdIogJCogQgpvE4MhSHyNgt7mAwRosjcprBEPCuU95SZ1IYkpj6Dj6ojNmR0OiKoJatNpq6GQADF1e7X643w5HZK2lXCEZ2UabKnoqkQexkAGyDtLD2O4JvjycwjsNZGm-50QQriAAVg3W4bO7HLbbypnR9N5DeOIAhKGdNJ8N5dDUC8AEZ1yHWk7XtEBbh9J5+HeMIPkodBlhg2Cbj9KCGB6chin+CB2DeD43CIWQC28FZ5kWZZVnWTZ8B2PYDiOE5gGENxMj+bC+gGIYSLIzwKLmBYlhWNYNi2XZ9kOY4EGAfjyMwNxuIAWQOcI-GKWRc0okSaPE+jGOk7AzguMAgA)

#### 题意

计算字符串的长度，类似于 `String#length` 。

#### 题解

```ts
type LengthOfString<S extends string, K extends any[] = []> = 
    S extends `${infer F}${infer E}`
        ? LengthOfString<E, [...K, F]>
        : K['length'];
```

首先需要明确一点，只有[元组类型](https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types)才可以推断出具体的长度，因为元组中的元素个数是已知的。

使用模板字符串进行推断是可以获得字符串的第一个字符的，所以依赖这个特性，将字符串遍历到底，并将每个字符放到一个数组中，这样当循环结束，就可以通过元组类型获取长度了。
