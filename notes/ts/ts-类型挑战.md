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

### Flatten

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00459-medium-flatten/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBAsCsCcEC0EBiAbAhgF2wUwDtJklSziAjATwgC8ALAewFcqBLAWzYIHMIAKAAIBjehQBOjAJQQAxIFo5QJLeczOPGYqxYrJ0RAGRmA7ty1RAFOqBN+MBUcoERjQBhG8wDD-gA3lAAOmBAyMCZioAA5C4FLjQOvKgA6mgCN+gCFugN4+gNHq9oBueoAocoAr8YB7aoCCRoDOeoBoyiHBkYBADMYQgNHygEGaAFz5AAaV2ADOxNhUAA54EABmWLiEEAC8aO34BAA8ANoAjAA0EABME0MAzBPQALozQ0OwixuLAHwQwMAQoxPTEPMwE+vEleX5OyYQAOJs2PTMFBCAUHKAp+aA0O6AWP-0uAa1RKexqogAdAArargxjiHjAODwYAMJAAYQAcmAQMAwHjQBAAPrEkmkkkQRwhQDHcoBADyJZIZhIgOLx9SavRw-QGABUIHgAB79AAm1QgmAIVCG226EGIvIFwtFQ24LTw4jQE3BWpVaogAFFFrKoAB+A5a8H8VB8wWEEViiVSiCmjCcwgDVA7YglA6oRZSTVal0dQZ6raG71SvFgAmMhkQQDStoBV6MAFK4Femx8kszgNOHYCAAb31AEdmJh0BM9fymsI8wBfVqSDgQADkgjZeCQojL6EIPDw1WAzGwbHQ1WbrMazWEmGq-ZlQ2Iler2AGepLZfdfTdUq2M22u8XVbwNdX6-Qm9dg0OUwmpyWu4O4xvJwW+7Gh+Xp9L56DXOvQ0mDYH2vQCtgPKAl2PFc12-C9g2GJ9jjmV8VjWTZthmRDbwWc43w-KCvw3X9t3zFpGEYb1mwoVRmwAbimb0RgABlrCZmzIxhqPEZsMIOUjyMori6IYiBmNYlsOKEvDFijaMQHTDMmUAaDlPEAU2sFIzZlcVAYgdkAMCVAGq5SlAGPIwAVb3+QFgVBaoIWhWF4URBBgHFaoAHc1RReh0SxKAdh+MyLOwIEQWAMF6ChGE4QRJFgGqRh0CHNhGAIWpfIgQAXs0ALE1DEC4LrNsyKHNRTFsVxMAgA)

#### 题意

在这个挑战中，你需要写一个接受数组的类型，并且返回扁平化的数组类型。

例如:

```ts
type flatten = Flatten<[1, 2, [3, 4], [[[5]]]]> // [1, 2, 3, 4, 5]
```

#### 题解

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

### Append to object

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00527-medium-append-to-object/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCsBMB2KBaKBBADpgpgOwBMoAXAeylICMArHAY2MhhRdZSakoE8NCAnHDwDSfAIYBnUgDdxAax4AKAAKUBAZgAMssQDYAnHXEBKKAGJAtHKBJbzNVaDFLMHiOHU26iAMjMB3bi+hRAedqADc6AAHKAVHKAXHKApcaAx8qA37aABUphgA2mgOragK3WgCFugN4+gNHqgEAMgKfRuZGAbKaAkHKhgEPKgA6mgDD-gCvxgHtqgGR6gJDmqZkxuXm+MIDR8oBBmgBcvVAABhPEzn7EXLhQACo44sRQALxQAN5QAJYEQ1AA5ACMh1AAvhyz8wBKywCuADarG1i4hAukAPI09MQAPEsVgAaI5SUSPe44Q6ggAsAD4oMBgFAcAAPXAMHBEMicHBbXb7I6nUHgyE4A6wi4cCZjUaIwAU6lAAOI7YgAC3ulCggCg5QCn5oBod0AWP-s4jETDiIbIqZ0dkAOmo4jlpD4AHNgAhEMAAF7slAAYQAcpAQMBIOaIKAoAB9W12+12qCAA3kMoBjuUAgB42h3e61QU0W674t74AifH52AELUFCVFo4gh8RQFZ8HZ4VWggBqiI2mw4AG0AAq7PBQRxcUgAM0WUAAPlAhABdA5F9HxwiJsuV6sAfkWhabUAzAG5IJcIBbIFafd6oIBpW0Aq9GAClc+l7p47-RAdgBbTAq1bbACiAEd7hDQQeMX8LlAK3xSFujkpAyhZRDHvhVctgPdiDtHuJDgDOZ8XjFZjnWLYODLA5DjoURiEAvwyShGDVQEfBELHSBAxIZZiAvTFiHAnMoMEGC4IQjhkIpI40JwDCOHZe8aMoUhSHfUQ8FHID5lA4h4Ag3M-Ggo4CFIVUzjre5CBwCtU2xKiIRQo4AHd2TZaEOHEaSDmIPgoW48cIBwviCL+ASSOEsjRPEySoGkghZPkghFPJGC1I0xCYG0vBdP0nBGOYg5jkM7DgNwlY1EE0iuHI0gVK8qBqJgrgcEeR54sSnyDgrCFxACiAsOM8LTMvBgossmARNgzLXOUw5UvS2q-Gym88oKmAdnEABZUgORwPgbnucRxB2UQcva2t7JkuS8AUwqePxOD8sTDY8w4MyGH+Y9T0ef5gw+b5fi2vjjlBQ4mK3aFQVY9icE4+FQVKwjjnhR6NrKgEdohfbsBDMNjoBPj4HOy7rqgV6nrwzb+Le4EPsI7aTx+g7QyOiN-j4tRzq63r+sG4bRtEGE2v-fEpJm5zHoi-DPrUOHIAbCcjKnNdHUAaDlgkAU2tVzZv0zUtcA-ERQAwJUAarkXUAY8jABVvEUxQlKVgBleVFWVNUNSQYBOPEFSBp1PUjQ4RFBRluXxUlaVxFlBUlRVdVNWASRIV-Ug8GmGBEUAF7NACxNbwzYVy3rdVu39YNY0IA3SAgA)

#### 题意

实现一个为接口添加一个新字段的类型。该类型接收三个参数，返回带有新字段的接口类型。

例如:

```ts
type Test = { id: '1' } 

// expected to be { id: '1', value: 4 }
type Result = AppendToObject<Test, 'value', 4>
```

#### 题解

```ts
type AppendToObject<T, K extends string, V> = {
    [P in keyof T | K]: P extends keyof T ? T[P]: V;
}
```

- 遍历对象类型 `T` ，然后将 `K` 和 `T` 的 `key` 联合起来，也就有了 `keyof T | K`
- 而设置值类型时，只要对 `key` 进行判断就好了

### Absolute

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00529-medium-absolute/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCsBMCcEC0ECCAjAzgewDYFcAXAU0mSQsrPQE80A7AEwCdi6BpZgQxwDdMA1nQAUAAXSsAzAAYB3AGzwAxpgCUEAMSBaOUCS3poC2XQgAtNJfQAdcR4klwBLEt1xkyG9xEAZGYDu3V1EB52oANzoAAcoBUcoClxoBsppiEzPb0AOYANPT4+ujEzIBoRuj2CQCS9ISA3j6A0eqAQ8qADqaAIW4ABhg4BCS1ZUmAK-GAe2phgMbWlYDq2oBk3oBMcoBADH4QgNHygEGa47VzhJhkhDQWxBAAKsQxEAC8yACM0tIA3EsrawBKW-i4hLtoWHhExAA8mzEAfMcQwMAQxAAPVZKEiMCCEbAQDIQABEh2kMLIc1q4w+EEAFOoQADijmM+HQEEAUHKAU-NANDugCx-4yEQgWTAALl+CyUxgAdAArTAs7DMBLAODwYAAL2MSAAwgA5MAgYBgWWgCAAfSVypVyoggAN5aqAY7lAIAeitVBoVEGlsuWqwejWeb3+AJITEwEFS6UyEAAPhAYnFEm6oXl4oQkhALvdagASADe6wAvrU0Xtg4C7YwHbUkBH4gAzF0AURjEAA-BBsxA6UHTrKwPLDQaIIBpW0Aq9GAClcJvrq2qTfZLNy7uGiwBHfBcXCB7NA4ggiBRiAZ5jYfQQADkojNtmZQ9wxESW2ARHsuEwC9N5wgSh4W3uAG0yKPgYQXtmB0OXg0niQXtIPoGF9IFx9P9exxBe9H1wZ9HiaV4kA-L8fz-JIANvYDB1Al8IJeQ5P0XQ5f3-KAb3HO8H2QsDLTfJBoEwhdoBw+C8MAwiQJI19Xm-X8YJohCCKQp9UKtBcoLYxdYNwot6O4lDwL47DKOkkT8KAoieMkt9+Ooyi1LksTFIk0jIP2BUjmkAyjnoGTDMMji6MQ7SmLQ+AFXgRzTK-RzHI4gBdCtKxAVs2yNQBoOWCQBTa18ttjRlUAyDRQAwJUAarlNUAY8jABVvSlqVpBlgCZVkOS5Hk+QQYAuHoTAAHdMiFEUJSiiAyWS1KaXpRlMGZdlOW5Xl+WAUj7GwYrqsAF7NACxNHx6vSpqWpy9qKrFSUTTAIA)

#### 题意

实现一个接收 `string,number 或 bigInt` 类型参数的 `Absolute` 类型,返回一个正数字符串。

例如：

```ts
type Test = -100;
// expected to be "100"
type Result = Absolute<Test>; 
```

#### 题解

```ts
type Absolute<T extends number | string | bigint, R = `${T}`> = R extends `-${infer E}` ? E : R;
```

- 已经确定要返回的结果为字符串类型，所以可以通过定义一个泛型`R`通过字符串模板进行转换。这样使用泛型还可以简化代码，相当于占个位嘛
- 之后就是通过条件类型将`-`去掉就好啦

### String to Union

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00531-medium-string-to-union/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCsDMCMEC0EDKAXATgSwHYHMJ0B7CAVV22N0mSXodoCMBPCAQVwBNMBTNgNKYAhgGdiAN1EBrNgAoAAkz6wADNJEA2AJwBjUQEoIAYkC0coElvEwFdK1E6Kx58tWsbcRAGRmA7txdRAedqADc6AAHKAVHKAYDqApcaAbKaADEqAIW4YOASAQ8qADqaANvGARsaAXHKhgOragLPWFFS4cYDePoDR6oBADL4QgNHygEGatQAGreiitOgsAA68EAAqvA4QALwQAOTwAEyw4wDcnT19AEpDVgA26KNojgT9xMXUADyDDgB8cxDAwBC8AB69uui8XESkTH0ARPCfEAA+EE+U1+AM+sE+tFazVqZwggAp1CAAcWw6AAFlYmBBAFBygFPzQDQ7oAsf9R6HQ3VEAC5ru1dKiAHQAK1EtOImHwwDg8GAAC9UUgAMIAOTAIGAYDFoAgAH1pTLZTKIIADeTigGO5QCAHlK5ZrJRAReKbl1egMDrZcCdbndntxRBBhLgWABtAC6sLG0n4xAAZhAAN5ga5QCD2gAKEDwA3tuCsAFsPphHY7yRAgwt-QBfP36pY7JL4faHU39c2WrjWhw5gA0EGW2ydLoGRd4VogzQAJN68B7eJgIAAxVNtjtdiAAUVTzQgAH4jfmjvae5XaYv7YknHmTUdh2d47DE7heBIuwswAa+iu9saSmb7sXS7t8JXq2Nvam64Xr42S82B7hO92+9-fxHMdJ2zVcL2OYdK29YNQ1wXsE17VMIAAMirHcIDdFhPSrI8xT9EANS1WUIEAaVtAFXowAKVzqQiiOlHVRWwKNuhZLZvRHABHKxhHWSthweXgnggJCPUwYgowmBQTyQGluPWRt8CGYArHQbB1lEcYxRPCBdDEIYa1oPjHnQDdOO4o4z1zcDTU+T4zkrPcD0wM47IM-inhMrj1nMu810vT50Fsyt-NslyoEMgTjOHUyvIs3zjk+VFeHWdZiECwFURBQFeEyz51hyvL-kBVLnPLVyjI8szYqso5Pl0FlqGECRsEwKxRDS2qctSwrPkwTqctwHLhByiQcuwHLeu6qwcrakqwEdPD8Jo2iIEAaDkgkAU2slqI+j8NoWFADAlQBquSVQBjyMAFW8iRJMlKWAak6UZZlWXZBBgFtUQAHcu25XlBT2iB8XOy7SQpKlRBpBkmRZNkOWAcR1mUkoOigWFABezQAsTW8IHrtB8GHqh77+SFXUwCAA)

#### 题意

实现一个将接收到的String参数转换为一个字母Union的类型。

例如：

```ts
type Test = '123';
// expected to be "1" | "2" | "3"
type Result = StringToUnion<Test>; 
```

#### 题解

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

### Merge

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00599-medium-merge/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCsCcsQLQQLIFMBOBzNkmIMLwCMBPCALQE0BlAL1IGcIAKAAWvqYEoIBiQLRygSW9+Ae2IArNAGMALnjx8lEQBkZgO7cFUQGA6gEjlAVHKBvH0DR6oAgVQG56gBCNAAHKGjgGH-ANN6AYuVuAQt0B0qYCx5QGGRgNbcHNsYegEAMmhCA0fKAQZphAAZxsox4sqQADmgQAGaiohAAvBAA3nhQAHYAhgC2aABcEIyyGACWJVgA3MUQZTi19U0t7VAAvmHJaRDS2XmFHV01ECUArhXEmANQdWgAHj0NzVh4w1BJqekASmiMCwA2slPo2GgAPFmiADQTogB8rRDAwBBbNJyNAAEwgshyK0K5SqOz6WFenW68yWKwwiMYWzhe0OEDiMTCnwggAp1CAAcUasgAFgtiBBAFBygFPzQDQ7oAsf6pslkKUY1T+CWkVIAdBJGILRNhgHBYMA6FTEABhAByYBAwDA6tAEAA+jrdXrdRBAAbyrkAx3KAQA9tfqrVqIKr1aN0vccI8AGKImhE-JFKAAbQAChBmhAANZoUiiDKsF0QABkEBo3AAurUA1tZGgSiDmKHw5GaBAAPzx-3JqOx+PcEtgYbqsCa61WiCAaVtAKvRgApXcKWhsGu2NCopcW3AoQACiAEcFmUroiR5sgbdBpkMKIKhAAORsB2IAVTq4ZnCMYALWSNK6MNf2k4QF2TL14Mq1RbLVYkbH9avtB0QABCZQwU29CBiEfVEXygaRamIbI9zKEp2kGdowC-aQykxZh8h9PBZ3nR5x0nK5HidJ4bzeH8-0+REigASAfFFnwwdoqOAui0UYiCgOgtBYLWQZPk+MBE1rOsQC7bsbUAaDkrEAU2tRO7W01VAPAiUAMCVAGq5Y1AGPIwAVb3ZTluV5YB+SFEUxQlKVgFgxgAHdMBlOUlSUiAWW03SuR5PlGAFYVRXFLBJXgYBGFEK5j0aUQSkSKAiUAF7NACxNdRXP0jyvJM3y7IVZU7TAIA)

#### 题意

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

#### 题解

```ts
type Merge<F, S> = {
  [P in keyof (F & S)]: P extends keyof S ? S[P]: (F & S)[P]
}
```

- 首先通过交叉类型操作符 `&` 把 `F` 和 `S` 变为交叉类型，然后通过 `keyof` 就可以拿到键的联合类型了
- 由于 `S` 需要覆盖 `F` 的 `key` ，所以在设置对象的值类型时，需要进行判断，优先使用 `S` 的值 

### CamelCase

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00114-hard-camelcase/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=21&ssc=7&pln=16&pc=1#code/PQKgUABBBsCMAMEC0EDCBDAtgUwDYYGdtJkkzySAjATwgCkB7ACwDsCGW0mBXCACgACAK2ZsOAYwYATbEwDWASggBiHFICW3TCoAu2TAAdc6PUlzq9AJ3S4SJZQ4gBFbtgI71HO1AAGAMwZLJEp0IJCALx9kAD4If0CAIVCkyO8IWIA1dWwAdwgOCABxCwAJbkoALggmHR0DAgrgYB0CcSYAOiECdsCAc2A4eDAQYDAx0AgAfWmZ2ZmIAE0Gbks0aWwIEuxLDbm96YgRsZ1qAw2MHHx0IgAeAGUIbAAPPRYpAgh3S3UWXtiAXhID2er3ecQAJABvH5+bYQABiAF8kFCYXCAKKInwkAD8EMhSKh6MeL2wbw+GAMFhs6nC2Bu6OiJCgeJ8KMhFzwhHpjKxzIgVU5V1ulOp5jpDOi0T5UCqdwA3GMwBN9nsIAAVNw6NDXNxTVWzQ6jdSGQLayEQdEAR24NgANJanmdxNrERA-JYGNoAOQCE5nJBtGy4Mm9NzAbgeXAEb3HU4bcS6j7-CAAbRI6Kd2BdDJtNhuQu5N29AQYwVC5fC3uiDpLDAYSUsKWrNYzWZz1ttuALWC5uuLpaQjaH6CrNYgdbLw+bUrtbedOlzXZ7lyLk5HYVH1drg8bM9bUEzC6X+cL-fXIUs24npcbLbnh-bi87p97wvpk8ml+vn+-s-n2bPnm3Znrc67lkE5ARD+u4VmQ+4Po6x4viBb5rugwSBj+6AJKg94AR2wErn2YEYZQgZINhuGUf+j7IURoEfgAgrhP4sXhtFIYBJ6oau540bWNEHlxhHLoxxY-vhdHcShxHvsWgC8G4AcHs-sp+EALpKsqID6gaBzwisOhMHCdx6PUul6Ua2kkLEdxMKEGzUMsqzsLgkaeGwVQ1HUDRNC0bSdN0fQDAgwDoGwOTbDZEBZLknwMG5HgcA01S1PUjTNK0HRdD0lj9IMwCue5yXRQAsoE5z2bgIa-G4Xlpb5mUBTlfTDKMYBAA)

#### 题意

实现 `CamelCase<T>` ，将 `snake_case` 类型的表示的字符串转换为 `camelCase` 的表示方式。

例如：

```ts
// 预期为 'helloWorldWithTypes'
type camelCase1 = CamelCase<"hello_world_with_types">

// 期望与前一个相同
type camelCase2 = CamelCase<"HELLO_WORLD_WITH_TYPES"> 
```

#### 题解

```ts
type CamelCase<S extends string> = S extends Lowercase<S>
  ? S extends `${infer F}_${infer RF}${infer R}`
    ? `${F}${Uppercase<RF>}${CamelCase<R>}`
    : S
  : CamelCase<Lowercase<S>>
```

- `CamelCase` 是内置的工具类型，用来将字符串的首字母变为大写

### KebabCase

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00612-medium-kebabcase/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBsCMBMEC0EDSBTARgQ0wYWwGd1JklyLTMBPCAKQHsALAO0IZYjyYFcIAKAAIArZmw4BjBgBN0TANYBKCAGIAtumkBLHmtUAXdGoAOAG2yGkprYYBO2U6VIqXEAIo90hfVo5OoAAYAYgwMAELYthEAXgHIAHwQAQBmoUg4tunYsf4QiQBqWugA7hAcEADiNgASPJgAXBBM+vrGhPXAwPqEEkwAdMKEfQy2AObAcPBgIMBgc6AQAPrLK6srEACaDDy2XDLoENXotgdrZ8sQM3P61MYHGDj4ROgAPADKEOgAHoYs0oQQby2LQsUaJAC8EFIH2+v3+SQAJABvEHJY4QIIAX2RqPRAFFMQFSAB+CB4z4-dB-AEAVRYEmwxhsDi00VeePipCgpICyLpDKZ+hZbJeQXi2KRD1wBGILw5hK5EEavKR-MZzOsIrFmKQyKlT1l8qJUEabwA3HNLQtzmcIAAVLz6LjPAE2taXWZaEwjJ1IskARx4DgANGSvncJE7MRBkrYGHoAOSCG53JC9BymKmjLzAHg+UyEBPXW4HBnEAGQgDapDx4fQkblgYcL31MteCZC4UiMQT8VDCdSDCymRw0V7fZrdYbeKbphbWGlzxeA9CESi2V7-cHw6yY-iE6gtYj+kbQbnraXK6HGU3ECvw-Hwcnx9PzYvsqvixvfbvg6-kUfZ96xPGcz3nR422XTskDXW97wffcn0PKcQNncDFw-ABBMI8Dg7B0jTQDkJfUC3wXA12yQOCqMQoDpzQ992zgoiwxIhjyMghNAF4NwA4Pbg3jAIAXUtMBrTddYgh2fQmHRN5DDaJZxIuK5QFIRI3iYSIDmobZdnYUw818NhGmaVp2k6bpegGIYRnGSZgGwNhimONSIEKEpAQYAyfA4domhaNoOi6Hp+kGYYxgmBBgH0wzfNcgBZEYDm4DMsy8EyAvM4KrLC2zplmMAgA)

#### 题意

将camelCase或PascalCase字符串替换为烤肉串大小写。

例如：

```ts
type FooBarBaz = KebabCase<"FooBarBaz">
const foobarbaz: FooBarBaz = "foo-bar-baz"

type DoNothing = KebabCase<"do-nothing">
const doNothing: DoNothing = "do-nothing"
```

#### 题解

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

### Diff

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/00645-medium-diff/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBsAsCsEC0EAiBLAZpyyn4NwCMBPCALQE0BlALxIGcIAKAASrsYEoIBiQWjlAkt58A9kQBWAUwDGAF1y5eSiIAyMwHduCqIHbgwGvKgEjlAVHKBS40DHyoG8fQNHq-QCFugO91APAqA9HUDkBoCAGTRAAG32Q1yySAAdJCAAxEREIAF4IAG9cKABDAC4IBlkAJ3QAOwBzAG4EiCJU7IBXAFsiSQzCqABffyCQgCFEjOi4opS0zJyCoulUogiAG0lE7NxGqCbgiAAlSQYy0dkARk6MbAAecJEAGjaMgD4IYGA44tLK6oyDiCHisYnsiBmIAPmllbWAJi2WEwO2OB32ZwuVxKEHKVRqDyeIxE40m7w83k8HjOgAp1CAAcXQsgAFmUiBBAFBygFPzQDQ7oAsf6JslkgQYyQuvmkRIAdOIGJyRBlcsA4PBgLQiUgAMIAOTAIGAYAVoAgAH1VWr1WqIIADeWsgGO5QCAHiqNcblRA5QqviFtsCAPIPG3rM4xG0VQk7G0QABkEAdDwA1pISCJMCwPQAfH3rLgnQoKsBKk3GiCAaVtAKvRgApXQDR8kbE5rzegKoF+bIrgBRACOZUSowepYAHsE5O8IJgMiIKhAAOSsS1IDnV8Z5ZbAMqydCjBidi3NMIRTrxKDZRIVSSpdJZPK4RK5Ve9De5MCNS0QY7z3BLldrvqbpI7q-73A77IAExqNzhGUPYGPErnMQXMLLru679I+kgvm+MK3DUX7fjO0iJAwyydAA2rg9aNrIOwVlWow7NaewRA8xwnA8sQQE+r4ZO+dzvCcpHoQ2MhYTh1b4UCILtA84JkRR4FUTRNR0QxUAYcx2GVmxBH7A8v4iKRVzbsB165A8lGQbCtH1PRByMZhEm4exuxydxEQKeRSn3v0an8Rp0EdNpDEALpxvGIA5rmpqANBygAAcoAptYebmZryqAuBnIAYEqANVyOqAMeRgAq3vSjLMqywDslyPJ8gKQoIMAkwMAA7jUoritKYUQDS8WJUyLJsgwHLcry-KCsKwAMMio7oCI2R+FAZyAC9mgBYmuoVXJbV9UZU1xWSjK5pgEAA)

#### 题意

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

#### 题解

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

### AnyOf

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/00949-medium-anyof/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCcAs0QLQQIIDsCeB5AZpJihR+ARhhGgK4A21EAFAAIDWGaApjpcwJQQDEAW3YATAJaVBAgIYAnWdIz58-VRACKldgGcALmID2aZVACSggA7V2wtLogAFDLoAWRiNTHNREAAbTMXwguNABjfXcxNAhXdhiMCzjtDD0bADpUeMSY6W9tGJc4lHlFCACRCFl2XUpZNHzfXVktILEcMswIdmtbewN22NQS8jF8pq0M0wHCoYUR-JsLXQwAGkrq2ujfHGlqbXZfNJMIADEDWS6AD2lLawAuY98n3W18ZeyAZRurdgBGCAAvKhMLgADwAbV+awARNC1js9uw1uCALprADeAF8UQA+ADcEGAwCuiXCPl0BggJDi43YRyg7ziX1u7AATIDgdgcBCAAwwuHBXb7ZFoiBY3EEokk9hkioUqlxBH7el+J7HHEQABqYnYAHcIO4AOJiXQACUoJDuEBcul0Fm0dyJL1CLjSACttGlzgBzYBwaBgEDAMAh0AQAD6kaj0ajEAAmgZahAAMIGERxU3sKoRmO58MQIMhxmcsEAFSuunYaBE+Sq0hERmo5ACGFRGqBpfBVEE1NkKIrVZrDAA5MOIAAfCA8ieCxEz1Ez9EQcHeDBWvSyKLelFWjgANyzEExfAA-LP9hArbS8SGwGG87mIKWdPZk9J9vkHzGC8GxJZzvYS4AKIAI6ULsaxAZcpL2JiwSyAYUjDowjKIC6uzWGg3o6MAlD6Hsw5FgkcShO+OgcuC+BQTBoKgeB1CgugXIQlCEDDpWejDmstLIr8opLmgNzsFa7EvmOmIYhAvwiRxujibi3HNOwOI4isVHQTKui0WBuyMSC3KQmso7wkKSLLvx2KqTESkqWpUDUZp2n0XpzHgnybGyVx55maiGKWYpWi2epNF0bpTFgm5RleTx5l+Qp1mBapwWOaFDHhQZ7nGd5vEWfFtJBfZGnhE5YX6byUUmYiIqSYJwgyWJR55TZSWFSFOlpWVkVsV5So+fxUn1ZxjVWflLUQA5xWpS5EWZT1pnVWKFBCYNclHpJ0keQ1-kJcpY0TVpU3peV3WVcKsVittvUFeNRUHe100GfFV1JSit53iAOZfpGpy1LEFwfJW9qfV9P7vfgGofC4chxBgiYXNoBjUHhhj1FaNp2g6TraC67qej6frwMAATaLqWbg1qOr6gjSMRKj1q2vajrAM6roel6si+v6wDU8jRivFAGoALLnHEyZQ7QVbYQ69MY0zLO4+z3qBsGYBAA)

#### 题意

在类型系统中实现类似于 Python 中 `any` 函数。类型接收一个数组，如果数组中任一个元素为真，则返回 `true`，否则返回 `false`。如果数组为空，返回 `false`。

例如：

```ts
type Sample1 = AnyOf<[1, '', false, [], {}]> // expected to be true.
type Sample2 = AnyOf<[0, '', false, [], {}]> // expected to be false.
```

#### 题解

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

### IsNever

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/01042-medium-isnever/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAMAsAmCBaCBJAzgOQKYDdcAnSVFci0gIwE8IALASyIHsaBDCRxgLwFcA1pwAUAASasO3fkICUEAMQBbXABNGfJYr4A7Rix3aALowA2mUqQXWIART65MJg5ajolAB1O4VOoxE4jGg9cDBwCYgAaCAB3JgBjeggjdgFHLh0PPn8gkIgAAwAVfIA6UnQAM2T6UNzQliqiRxZTQkxklgKdCKJ86KajPiJDfKMiBz6IFiMaohjGTFD8ivZzXFLXCAAxFiIIXAAPdk9vAC5N-MujCyg6iABBCABeMLxCIgAebveAPihgYD7A4heJGNQdCBUWrjXCkO4AIWerx6H10qlwFUY3VUfwBQJBYNUEKhEBWazhwVCAGEkVg3sQvnxTKZcYDDgTwUZOiSyYsKXkACK08LvD4AbQAuqz8bhQZzuaFebDbpSIABRYX0z46TRQojS9mywnExWrPlQS75TZ-ABqjFwMSmhgA4owjAAJPhUU4MIxGDyYU4A66JEoAK0wJV2AHNgHAkGAQMAwCnQBAAPqZrPZrMQACaLCGECpLHREHdxFCOermYgSZTdzpKMKfxeYsKEqBYJ0qnaYu+xE7AH5kjCID6lQBuFNgNM16sQQqOfxU9iLdrznN15OMTy7fwAb3VAEc+KtomrgUaIABfUmsLQAclEdRQiVW3h00ccwGyZkwj4Nqq8RrukbakJeBIfGqp6rB8TaigO+rRGMDg-D8kQQVeoLQbBpjwSKDJIRAAA+EBOEQWLRhhpJmrg6GYVAkFGrhZ74QhDIAEScTRSoMVhUEwWxBFaqiPYYliai8XR-FMdhRisXBHHakyLLRHxGECSxQlKYRnyStJayyeq8mKexekfAeN6GYs-ESjOs4gBmm65lsQwzMQEAAMpggGzkuem26OaQfxefQ7BNBANCFnsmAtH+BiBr6-qBsGmChhGUZELG8aIMA7A6JgMTECFEB2g65Hxc4hU+vQfoBkGwAhvQ4aRjGcYIHlcWmAlhWlQAsrs1LhcyuBfo4tX1alTXpS1mUxomyZgEAA)

#### 题意

实现一个类型 `IsNever`，它接受输入类型 `T` 。如果的类型解析为 `never` ，则返回 `true` ，否则为 `false` 。

例如：

```ts
type A = IsNever<never> // expected to be true
type B = IsNever<undefined> // expected to be false
type C = IsNever<null> // expected to be false
type D = IsNever<[]> // expected to be false
type E = IsNever<number> // expected to be false
```

#### 题解

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

### IsUnion

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/master/questions/01097-medium-isunion/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBCMAMCcB2CBaCBJAzgVQHYEsB7XSVFci0gIwE8JcBXAGyYgAoABKgU1wGNCAJwCUEAMQBbbgBN8DCRFKkxKiAEUG3TABciJKKXQSADk25Tc2iAEMI2mse4QABljx7nAGggB3ABb4fH521gDWWja4EPi4xgxW9o4uACrOkdIQgtzaDIK4mL5+2UWCKWlZmIRMAG4R2oQ2EAwExHYO3AB0SlAAYkIQ3AAe1iZmAFykBlBQzrPamJNtSXzWmNzQEAC8GDgtuAA8OoIxAOYAfFDAwBAAZtZMa4uJTitrAExbO+7Eh9rHuCcAD6MCQ8QQXCBXOyCTRPdoQV7cADMnzce32AG0jqdgfIwQBdCFQu4PbiLWbOboQC4ANXw3B8EFaAHF8NoABIMKhjCB+bTaYyYMZXeZBDoAK0wHSEJ2AcCQYBAwDAKtAEAA+pqtdqtRAAJqEXIQADChGkTnZ3CyGp1tvVECVKueX3RyW8ACFPskLttkgNBtpeNICp6APwQDHu-H+wO4YMR5LR8MktYQHl-TRp+jcWqCADcKsLarttogyS0VmNqwiJZ1DuV+BMQisAG8IABRACODHu3nbg0cfCsAF9boJCAoAOQcZ4oIL3MwArTAeL4B6Tp3wxEFbYY0j9wfafZdntMfZovS-f7nbwp7hnM6efcD7hD4-d+7n3aX7EAiCA+g8StR9oU0B8nygA9XyPE9PwvH5J2sSd-wgScqGQgDJz4DDUOkScQIze9H2fQ931PL9vgOZpzRuGIZBQxgWBQ6pCHwDJMPw7xCPAkjoLIuDvx+NtrB5X8TggUcAOEnkQTBCSCJhIiII7F831gs94IOaSIDEhigNKYcQLvHjINUmCPw0wSDixP5Tj00ErUJW97jWEzIWuKs1gKfwrScP0KiqWoCnqRpcGIFBmj0JZOl4tSLIo9FdIA3Ac2A5zSTcqC4vIzSrzsgDmlCMKfFwIyXKU2LzJyqy8r-ADrFwGgyoy4jTNI9SEp-Wy6tQpDmtc4j8ULMBi1rXUelybQSggABlQNBRtMb7UdUBSAuGa-Gsa0aENUpKiYVdiCFXl+UFYVgFFPwJSlGU5QQRBgAazAfCtNaIDpBkdKqQ78h5PkBSFEVMDFSVpUEWV5Qe-afoWKALgAWSEJxjU2lheBOLQ-tOwGLuBq7QZlRVlTAIA)

#### 题意

实现一个类型 `IsUnion` ，它接受一个输入类型 `T` 并返回 `T` 是否解析为联合类型。

例如：

```ts
type case1 = IsUnion<string> // false
type case2 = IsUnion<string | number> // true
type case3 = IsUnion<[string | number]> // false
```

#### 题解

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

### ReplaceKeys

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/01130-medium-replacekeys/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCMsGYAMUC0UBKBTADgGwEMBjLAaSwE8BnSGVeh1WqAIwqkEZYjqACgAE8AVzyE2AK0EA7LAEooAYgC2WACYBLQYqjNm8vVACKgrFQAuagPaSd0KAElF+LMsmmoBKKYo4smXIRJyagAaTwALAjcAJ39iXwBrSiooNUkoKUs0rx8qULUAMygqC2VPb18I5MkLN1MwtWTEilCJMyL4tRwoGPxiVIBzYOYAQTKfTwJE5LqY3wIo-s0sVyoAOm1bGxgAMQsoqCwADwJHPCwALi2oAANb0xpbbN8AOQsVLFGAXigAb2YYJ7nKAAcmGwP+UEkJwuRVMUQGEPyhH6QMkmhYWCizAAvlcnlBXu8AEJQb5-WwA8pA4FE8EUlIqVHozGI5FMxQYrG2XGbR7lAlvLAAYVJvwhgJBQrpMBgUOUQLM8Mk-VZBBRkOZXJgPJgeP5hJMooNowAPgLiVAzQahXrxtheiQVAbkt97QEyEkADzO0LAuVYYGWkFItXA0I-f3szmhEPqxUDbEAPigwGAvwloLDkOhUcxMbZsKV-WxQZ+GdpeUZGo5eagsYVcITpYzUtCker0brBfjypLqagAHcLMIVN1YiRs6V8lESoWBp4LB3az13V21XWZ1o0TX9qZFz3+qtbb43XEnTUAKKHBqmIIuvwOj3Ub2C3Ig-1Zn4EAi5qJJlNpmWVIgmCbY5pCWAAG61vWS5-kGBokmaQE+NSraTjC0jQVE+Zqr+fZpkOI5jo+GELhB2HMLc1xXMmABqahYAOUBWFAADiaimAAEoILBAmEpimDgVDnKm9xEGEqxiGsez9MA8DIJAIDAJAqkQKAUAAPraTpuk6VAACaw77EKgpQFxmK+Hp1naVAylqfip6BF6ACqoQACoHIcpjLCoyQHqEBnJt8kAuV5PmSH5UBhZAAD8Yr0gA2gACikaRNBYhQuQAukCqVHBFUWefFPD5d5vmNJQmWGVA8UGSl2VQKiUGYnIQIuQ1OIbO1ADcamQBpNnWVA7kmG4QoEFQhpDXpdkqRAaiOHstT8j8UAXgAjoIBB4KEV4+EQbgltOs7AnwTyoBJO1nMqJjAII5h4FQdKQPixqiuSlKoSB0oYQ2RbMLB26cpAPKvfqgokmSzDlr9ahVsDLK2EDmqgw5EPvCK0N8t9wJSsw7YHoDBaI1yYMQI546qO92NfTCmYE+BpPE3hc7KmjEDg3aVPnhatNjPTtLMPDv4s3GjbsxA5OU4+vPCh9MPAXjv3tszyPdhLKpS+j4yvM80I0wldPUmCYui7YqstWTOsvBY+vKNaCs4-T+Pq6zauykzVsc1ztvvPe71WpDCGCjaFP8k51OvqKkdy6aD7unLSEJ2e1q++aJh69CRp2wbgrx1nDuhyHxI21ARCTYa3yJcw+1YIdnqbdteCepHd4vv7vofkGwKxp+f1wT1a7i0WUBJqEsfOomiZDLYdcN03O2t1T7c+u+0L99+v5jzPGdUIXWDT7P2X9Zz6kgFpM36dsghRHUmJQAAyj5wmX1fmlzQN4C2Mmj8RDEUAKDGSKBYIQ5grAiSgAJISIkxJUAklJGSCx5KICQMAAgkgqADiRjAeijFmLFDAZkSB0DhKiWAOJSS0lViyRQcgYAhCHrEOYMmAAsnsXwQoIgiGWP0Ew-FBJkLgQg6hsklLzUgEAA)

#### 题意

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

#### 题解

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

### Remove Index Signature

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/01367-medium-remove-index-signature/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCMDMA2A7FAtFASgUwLYHsA3bKASQDsATbADygGUBLAc3IEMAXAVwCdtIYaIcLQCoAIwCeUABaMe+SWyiNGALy4BrZQAoAAnIVLVG7QEooAYlzZKjLrihixl11ACKXbAGcOjfOTO0GS4AA4ANnjY5BxQAAY4BMQU1DRMrJy82AA8ACoAfHFQADRQtADG4VzUUBwyJIxUtFDeLOzcfFAAZgqO+OIAVtjlsRySoT4AdEEwAGL4PGU0bGGRAFwz8XFxm2MTUPP4UAC8UADeYjAA2prYkmstHDyNzAC6D2zkkgDcl934+B0ZgehHwjEov2CAF9duMSABBE5YPBEbApWjpdpZbKHfIwYDAJYTEa2c7-QHAqCg8FQGHBMTbHb04J4gBqjGwAHcoAEoABxRgcAASXHEDxkHA4oW8awJHG85RkkwG3kmC2YwAQKEgIGAkH1EFAUAA+qazeazVAAJr4XhQADC+BqQuwnQt7tNUF1Br2JESqPRaTamT4eTxpwuwSuAAUVOQoLdJPgulBclA2N4oDpY7QONFKJm4gASM6NLquqAAaShRQA-FWoA9yNhiDwzO9UzHXpA6QbIEaPe7Uz5YvaMz4TYPzV69RBGGEFqM4WSAKIARy4bHCpRXNGJsSh3V6UAA5HpfWhFVvIuRmD5gFw-OFvCefcvDkjI9dEw9fM9bx2nySGIXQAkCIJgpQPZvvsABCbCLBGYg3HcTYOOIrqAV8YjiAh4FUpB0EQJAvpQPBaifjheGUtSUHBLhai-k8LxESRy7lOOmanFcYi7vu2Trpu4TZP6yRNEGGQdDkuKlGc5L4bRtL5PkxS8XuwwcAJG5biJKJiakmIhjk8E8CpZK4TwCmQUpKlqfxgk6aJaLiYZUnZORZlyRZVngt8EhsIxjz-swNmqRA3bEZF-YgJOU6erMvB1BW9B5tKsVxTO0ViHi9AyAhJBJna3j4FUfgBDKsiStKsrAPKirKqq6qakgyDAJ83icq62VQOyXItCVj7+OQFUSlKMpygqSoqmqPAalqrXFaVQ3eN1ACyCwkPaeXhDed4jVV421ZNDUzcwOqzpAQA)

#### 题意

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

#### 题解

```ts
type RemoveIndexSignature<T> = {
  [P in keyof T as (P extends `${infer K}` ? K : never)]: T[P];
}
```

- 键是可通过 `as` 进行[重新映射](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#key-remapping-via-as)的，所以这时可以使用模板字符串进行扩展或条件判断
- 而通过 `${infer K}` 可以推断出键的字面量类型，所以能够排除掉索引类型，获取到普通的键

答案参考自[解答区](https://github.com/type-challenges/type-challenges/issues/3542)，大佬也有详细的解释，并对 `case` 进行了扩展。

### Percentage Parser

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/01978-medium-percentage-parser/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgsAUABFCMCcB2AHFAtFACgUwE4GNsA7AFwEMBzbLM3AZz0hjRdbSagCMBPKAZQEALAK5kAlgnjwoACgACAviPGT4ASigBiALbYAJmOHaoHDpvNQAisOx0SYgPZFT0KAEltABwA22XaSw8QlJKbExaBlwAHgAVKGwADxJiPTooO1wxIgoAPgA6DgBBfHwHXANsqBIHKsFqAANgAD0ZAB0AagAfVrQ1AH42vRB+toBSfoASYHqoXGwKYW9aMRJeaqhtMhJ8QSg4siI9KCoSWrnqTe26ugLXDhi69JJcYXwSYTn0wQdFo85sABcUAA2vUfMI0mUNlkIfUADRQepEIz-XDwxHCIgreoAXQ4bgAZlAVsS0kQHKd8GRPO85noESRHnpsASyItTmI0gd4l5Vk9MtlbjAOAAxKGJMheXwAjj1OUkOgcVaeaiYPjPLIUWBQAC8UAA5PqldwVVh1QKKAAmXUG9rIACsoyNrmVqvNmoAzDb9WgHU7jaa1RrsgAWb1+50wV1m4MUe3h+2Rkwuk3UABK2r1OAIxHIVHC9DwUSDFtgORgwGA8QSKre+hBhoRjYN+rxKdNaetWaCudCBcixfd2Ut5aglerteSR2BACJ2jOETOHQuoDPRjO21HU1A017uzmQvmIkWS57R+PEpP67O0Cul-a7+vN1Vt2mw-vgnmwsfoqfQ+eq0vbA62nGc72XRcnwDdN4w-Xsj0LX8hzjACJ2AqcQTAxcINXDdZTlFwYHLAA1MRsAAdygJwoAAcRWAAJYROCBQQSBITw6ABSsFR2PIACsbjKChgAQFBIBAYBICkiBQCgAB9BTFKUxSoAATR+XAoAAYQcZkoHovBqGU4yFKgCTpOjLS6nwABrTA5gJMQEliatkkONIMk1cs9TiRI3NSW19SgToDTQIK+j2KAgSIbAADc8AAbkgSzrJsvhhAJRznN8pIUg82NvMivy8sRCYAG8sgJPAdwAX1GGYIuBNMmydHEopBGIm1bJKIGjbNPz7H8okKVySs87JCpG4r3NKsqrOAuyHKcqJKuqkUchq8rVs0gBRGqGpBEUETyE75ts9LMuWnacjaoFgWbE68jOtKMqy4abp6yBZJM4y9lsU4tLIBg0h+5SzMkiAxC8MpTmjMqoB2gBHURvARHaa3QqAaqgAlcAcYx9TkV00B2MhvF8bJbGAYR7G8OhnWS7dAYYTMG31LqurbSygewLsG3admW05xnTWZ7A935wX9VgKXWxF6gxffSWm1gAAGVWpda+XtJ52C2ZVjWWrl3qmZ5gA2G17rCpspE143tapYHLY4dHJyiJGUeLHtD2-RCokNHIETFstA5djG3nd5Gya9g8v37It9QFwOdYYEdQ9cV30Mjz3+vg32B0TmXk7Fj0cnTmBM4jj3o9zn34+iQv1adYueZDMu4TDt3q+8GOBoQgu1eboPdfbzus+73u8-r-20CkIeU+wM3R4gNtpK+kB5NBlSRQ+RlqvVbAOM3re5PB9eOHLJRaGobgNPSBxvBpxwiE4qBWPYzjuLoXiBLyISRKQMgYABw6DkUYK4EiZFKJ0Afk-Jwr934cS4sAHigh+KCVwMJUSQCYGP3sPAi+UAACyZQFaCDJhTKgCC2JIK-j-DBFBxIQ0gEAA)

#### 题意

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

#### 题解

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

### Drop Char

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02070-medium-drop-char/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMAMDssIFoIBEBOB7ADhAwgBYCGGkKylV5ARgJ4HE4AuxAlgHYDyAZgAqEAQhAAUAAXxNWnXgMEBKCAGIAtgFMAJmwCuK5czUqcAG2IHkxtgYzFjyzjzVko5JW4gBFbWoDOzNlgc5OSYuBDEED44agDGbDxsmhAxJBgQPNh6EX4YnADmAHTBUABiWGlqAB7ERsZqAFzFEAAGrcw+5Mx00RCC2szWPMYMALzo2DhEpAA8AOQQNBDaEMwrEGoQaTwQdgwAhBCzADSHhwB8EMDAhzT9g8N7s+StzU0XAGqJAO4QgRAA4lYABLaGj1CCEAY4Hz1K7tFIFABWPgK5TywDgiDAIGAYDxoAgAH1iSTSSSIABNLDaNL4LAaDZApwbMms4kQHF4ro9UKTVLTADKJ3wFzG5AF60qBg4Gh8LQAJABvBxOCAlAC+SpVaQAourmhByAB+BWKkqS6WyggQE2zebgtWaxW8qYYaY64VnfWGqDggUAbjxYAJbNZEAAKr5VpIfL4iaHSRzcWwjOVVtyNoqIDqAI7aWwnHWVaIxVbq9KZQ5iDPIFK2OocPK+YD9NjGHxPMAZ5LEWNysYAbXIVwg1Z8yCqJeYE4w2Gc2eLsWY7rztmmLv5s1uA1VQzojxOdrOh+3933szOx-IRanK-zxnXE1dc1Pu4ex1OF5Pdyce8el6Oa9F1LO81w3GYtx-LZ30Pf9vx3aC6AvK8oBvJdQIfcC3XmKAFig9IHlwojDkPc54LPf8UIXW9c3vR9cGfeZFmWdN1k2dIdggfYSM-Y8bigv9kMA1DgOXWiwKfTcFiWNZVg2LZOO4j8ty-U4ZNY+SON2CADiEoCaNXTDJIg6SWLWTTtm03TD2YVSmJkqALMUnTzivABdINgxAeME3ZEoaWYQhVQFAxoR83yky88gLgFVINjoak0h8LBjFbQIYQhKEYThHwEWRVEMHRTFYGAYgOB8L4nGiiBPjUH5ktS-x0vBSFmGhWFgHhQgkRRNEMQQEqGrS8rqoAWXKDYpmMBsmwy1r2pyvLesK7FcTAIA)

#### 题意

从字符串中剔除指定字符。

例如：

```ts
type Butterfly = DropChar<' b u t t e r f l y ! ', ' '> // 'butterfly!'
```

#### 题解

```ts
type DropChar<S, C> = S extends `${infer F}${infer E}` 
  ? `${F extends C ? '' : F}${DropChar<E, C>}` 
  : S
```

- 遍历字符串 `S`，然后对第一个字符 `F` 进行处理，字符串 `E` 则使用 `DropChar` 进行处理

### MinusOne

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02257-medium-minusone/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBM0KwHYIFoIFkCWA7ArgZwHksBTSFZCysgIwE918AXAQwDMB7CAMWYwC8IACgACrZrT6SAtgEoIAYinEAJhhxSFU5owAWZMvMMQAijmJ5GGdln1QA4hgBuxLBGYRcU6sQBOQ5gA2AO7ieBAADux4GJbOcsxh7oy04cQAdBAAmuw4fsmpEHg6OQHKED7EjLmuusQe6t5+ysQAxhUJKhB0ENbpttzsfsQAHsxS4QHEAFz9AAbzjHhk+XUAWr6cALzo2PhExAA8AIwAfBDAwBAADMspdVwYrMlcOX7bmLiEJAdwcGcXEDgABYyPNZv0zgA1DDEII9VwORgACRw1CmEB0jEY4TwUwuixaOjSACs8GlBgBzYCwRBgEDAMCM0AQAD6bPZHPZWVeEAAwuxmhAkb46pyxWyIPSmZdAA6mgB4FQDkmoB4HUAcHKAYUVACN+gH9UwBleoAPt0Akt6AELdAARmgBAdQCf2oBDGLAKwgABEMBSYgAVdgAQR8PnEEG2AG8yAAiK6B9EAbQAugAaINHUMQMM4LAAayw7CCWGjQeg8cTKbTGajECTqfTmZjUEDAGZcyWC1gi3Wy4382Ws5WgbXW4Xi92G73Sz2mxn2xBA3Au4P+8Pp32W1P5-XR4GAGyT+uL5sDjfbrczzcjitjhDrvdz3dD8-7i+zqfLgAcp8vC5vB9vO+vn77y4AnE-32eL5fkBV6gSBd5gAAvgA3NKEAAHIQIA9GaAABygBUcoANvGAEbGgBccoA6tqAGTegBMcka8qADD-gCcFoAdsaAMHagDYSoAX3robqOEIRARqaradx8u0jDEJ63q0AAQrQAAyLgUroByISMfFYMoYQWD42AUkWABKECyS4Ck3pGvoJhGZzbDJwxyTpswACR+tgrC+NwGA+BYkFWTZdlqeYjCQeCAD89mOYwmmmdpYTJsQtDsKwDpOq6HpeuIZC+byvH8XFwliRJUnuRYRZhmkeVqUWeVpAVEBFSVZWFfllXFdV5VVaV9UVQ1aSOs6jBugJ4hhg8-kRoZZDoiQzg+GA6JqbBcGANxygDePoA0epQBAXEFB8ezfC6gVmWEniNEZPHENoKWCSJ4lYJJOgHJZfoul5ZxafJYRhq5fg9dlzVPRAWWMBGYC+Z9YaBpMp26IG32DcQw0TWAzLimKEAuh5fIdGEMOcpKDIYOMgwBXafoQAAogAjjggRFnjwypC0AWQRArA+OwGgAOTCCsyCEoEgMUuYwA4JYAR4AzjJ2i0SP6WGZBkxTjAHITxMBAcK1fIcpxFlcJwnEeEutFLMuBPLuyKz8fxFsCasa+TWvS0TusK-sBxVurMCm+L5uU5bst658ttHFcqtFj+P5O1Amuuzrcs298Rze8rECRz7TvfYyUMgKyKNclwuS1H4ADKfE4inqcsmjSdkGcWc6MwFQQOFuSFOwAQ81YWC4hiWI4niwAEkSpLkj4VI0ggwDME3QS+CXEDQrCtf15Y1jN5i2K4vieCEiSZKUtS8AD3gdcN7PY9oIMdS8uXAQc+Y6Lz23S8r93lJ0gyYBAA)

#### 题意

给定一个正整数作为类型的参数，要求返回的类型是该数字减 1。

例如:

```ts
type Zero = MinusOne<1> // 0
type FiftyFour = MinusOne<55> // 54
```

#### 题解

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

### PickByType

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02595-medium-pickbytype/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMCsCcsIFoIAUCWBjA1gIQE8AVAgBwFNIVkbaqAjAiAKwwEMA7AcwGcALThAAUAAVadeAjgFtyAFzYBKCAGJZAEwwBXaaoD29ZuSxyqVFRYgBFLeR5yMejmagAxAE57dAAyLeANBCk2DgQbBA88hB6AGZBnhTuDnYQAO58epEQcmTkYe55bDw8GFwcbPQANnlyehDeAKreAHQuEK567hAAogAebNKk1W3eozkUPFjuGKSmUON5APIclQR4enrVggC86CGEJBQAPADeVFDlsgBcEXLT3OcQWHpaHHI3HDr05O6PGDwAJXIbHUTlWN3oGy2zigUH+3XKVXIEKhwJhEAAvoFIZs0QA+CDAYAQE4Qf5AkFgggo3GcADcZJ4CIq1Rp0IZGKoo28bQJADUMORUtEOBAAOIYOQACS09BufDkclIPCuRLkkz4zWYPGanS4wDgiDAIGAYDNoAgAH1rTbbTaIABNF5dADCenUeSlPzydt91ogJrNCx6vSwlS0HoA0uQCEciIEGoFIxByL05OQOOoeBAcDHYhAiBBdrmCPmiATdsnU+nM9nkwB+AsAbUjAF0U2mM1mIA0II3kx9yAA3H4QQcj9x0oO5Pa4A65OMJiskqhNtBk0V9MMR8jR2Pxnt41s3Ihr1tTznmkBWv12gt2OQQF1FFK3u+BjCDTqP4Ok7oARy0NhKkCPoKBMTEIBiTxdAAchEBZkCwARKmqbg7GALQHEqHhYLNDA3h+GI2CwPIAFl3XISoVwuAZkVue4uCoZ5XneCBPmkb5fjhQFgVBFZqQgHFoSoeFEVZITUU4MBL2DLAX2zXYmyoMDjDkI4AKAyojkwOdiAXCiPRAyTaQ4PFAlJck+KpNk0QZMSWXo4S0UxPFzJU3pwPUzTgJ0-Z9OOQyqMCexGPMkl2Lom5QoIrhXPcqBVJMDTAN83T8AC8gjiC4yOK48LSRYt4Pi+UcMTc-wwFbM0rxvN9-VcLQkj4UcAGV02VOr6oDU1QCoAlWoEAoIFLJqIk2LDHA4FUIAVJUVTVDUtR1PUDQQWBgE4HhUh+fqIAFIVxvDBwnBmublVVYB1WQ5bdXcfVDQ2ngJpO6a9oo4aXRQtCuDseVFQuxabu1O6mMDMAgA)

#### 题意

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

#### 题解

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

### StartsWith

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02688-medium-startswith/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=24&ssc=96&pln=24&pc=1#code/PQKgUABBBMBsAc8IFoIGUAuBDAThgzgOoCWGAFpCstTZQEYCeEAVsVgHYDm+ZHEAFAAFWHbr3YBbAKbYAlBADE0gCbEArhMUYpEgA4AbLNuT7SUnFn2VKC2xACKaqfgzEA9u2tQAknv06pdgwIAANMXAIScgAeABUAGggAVQA+EIgAdzJiAGMyCGwAa2cCjLcIKQAPLBzglxxiLgKGXRKOZQgcGTUcdnxMshlBnFDY9JcI-ozSfJCkkK8IADE3EaqsPylFkJ2MFuccht0MSj3WiCwIAF50bDwiGeiAciw6HKfEl-eUiGBgCsqrVqUg6GHKdCkEAAZpZ8FsoGdIXRrrdJlEyM9Xu9Pq8nj8-gCgdpQeDIRgcE5TvsIDkUeF7ujMW8PhAXm9lHjfv8qkSQQVSdDYfDQjtFj8AGrEKQZCAeCAAcVIAAk1HQAFwQMgYDC6fBqv4EPIAOmY+CNq04wDgiDAIGAYAdoAgAH1XW73W6IABNNw9CAAYTcykhSvMkI9EddEDtDsRqIZj1iAO07GU-XqjU4iSSycCaYgGa4PxuSaqKfzIQAJABvJIAXxrhc4dfSAH4ChTIRqYfo4QBuB1gJ2RiMQWLOYL+rBw-ojj3R+3EPSrYJx6sQACiAEc1JZEhvAVJahA69CcG5NE9BIjkHlLP4uM5gGpXL2nmAl7oVxB1weiUshUSAA5NwMG3Xd9BPM8L1Za99lvXh9AfTgnxfYg31jakcmnEobgAbUoP8jwwaJwMsaJ6UiR42WxVkajxRIezhFIUniQjD1qUid3IyiHhiGiWTZBiOycFi2KgIjOLI-QKLuKj+KxQSsQ5VjBV7KQxLAABdQchxAF0509JYenIcxbikXUDMM50Fz0ygfjQXguggBhfRGfA3H0NCPD1TVtV1fVgENMgTTNC0rQQeBgA4fAMnMeyIElaUC087y+g1LUdT1A18GNU1zRwS1rSijyvNcHyEoAWVWSF-UQ5DnAy-zsqC3KQvyi1bXtMAgA)

#### 题意

实现 `StartsWith<T, U>` ,接收两个string类型参数,然后判断 `T` 是否以 `U` 开头,根据结果返回 `true` 或 `false` 。

例如:

```ts
type a = StartsWith<'abc', 'ac'> // expected to be false
type b = StartsWith<'abc', 'ab'> // expected to be true
type c = StartsWith<'abc', 'abcd'> // expected to be false
```

#### 题解

```ts
type StartsWith<T extends string, U extends string> = T extends `${U}${string}` ? true : false;
```

- 这个比较简单，通过[字符串模板](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)进行条件类型判断即可。

### EndsWith

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02693-medium-endswith/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMBsCcBmCBaCBRAdgEwM4HUBLAFwAtJUUrqKAjATwgCtCBDTAc11PYgAoAAi3ZcemALYBTYqwCUEAMRTshAK7jFxSeIAOAG1ZaUekpIBOrPRQoLbEAIqrJuYoQD2ma1ACSuvdslMYggAAyw8IjIAHgAVABoIAFUAPhCIAHdSQgBjUggZAGtnfPS3CEkAD1Zs4JczQk58+h1i9mwIM2lVM0xcDNJpAbNQmLTAvAySPJDEkK8IZIgANUJJdIgPCABxEgAJVVoALghSYmIdXEPgYGJcXIA6Jlx7tzMOYDgkMBBgMD-QCAAfWBINBIIgAE03N0IABhNzYSQQXbmJFg9HAiA-P7EZpI8IEKaxcoVLQ4Pp1BocBKJElkiaUziLAC8EBidPGfRCABIAN6MjgAXz5iUFaQA-PkzE4IMcAGaWXCSADcfzAAIx6LZzmCsNYSr6mrBWN+hF0r2CuJaEF5GAAjqpLAl0BUWjUIIKIHKzG4NAByARWyQoXKWfycZzAVSuPS4P1gM06C02jCuyQ1ABiiskCQAcm5iOgHZYPV6ff7A3iQzw9OGOJHo4RY-GwEGINl9cVWQBtCgut3EKJFx16IfkyKkKJ+1i0bJ+hJ+2d+5IJYjSyTJFd9tM1IfF0cEidTmdzhcn5er9ebuLbgd7kdjiJE6dLhfYC9e7PXsAAXTV6pAIEjXBDNujIcwIAAZS0C4gOAwETQAihFkgnhOggehoWGXA3D0RsPEuE4zguK4bjuUhHmeV53k+RBgHYXB0nMZDllWdYcLw1wCOOU5zkua5bgeJ4XjeD4EDojj8N6FiAFlXiRWEazrZweOI-iyKEqi3m+X4wCAA)

#### 题意

实现 `EndsWith<T, U>` , 接收两个string类型参数,然后判断 `T` 是否以 `U` 结尾,根据结果返回 `true` 或 `false` 。

例如:

```ts
type a = EndsWith<'abc', 'bc'> // expected to be true
type b = EndsWith<'abc', 'abc'> // expected to be true
type c = EndsWith<'abc', 'd'> // expected to be false
```

#### 题解

```ts
type EndsWith<T extends string, U extends string> = T extends `${string}${U}` ? true : false;
```

- 这个跟 `StartsWith` 比，就是反过来了而已，所以还是一样的套路...

### PartialByKeys

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02757-medium-partialbykeys/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMDsCssIFoIAUCGAnALgSwwBsAhATwGkBTUgZ0hWUafoCNSIArAgOwHMaAFhm4QAFAAEuw-kO4BbSjgwBKCAGIFAEzwBXOeoD2LDpQDGOevTXWIARR2Ua+A90tQAknIAOhSgu44EBgQvJTclFh4phAABpi4BCQU1DQAPAAqADQQ5AB8MRAA7gJRAhBKANaO5YUG5aRelEFYvHphgTHpBcKaseQxAHRufQU0jaZ4AGbsOAJNNIoQBpMQXlgGjQnVy7Fd5UKBggY6hL0LgTh1LE0bztxEAxAA6nMiMf0QeDQQ3AaBawYAG54TSUTTZPCHATHU4QOQYKpBQiEVbrTb4bZeO5ETg6JwQQh4RGzJq-LDwlFxbD4IgZfJDKD0ABiBiwEEoAA8MN5fMMYvycA1HKZIlj6HgAhFJhhTE0AKoLNkAb3oUHuCgAXBAnJE+KqgqEtdw9NcsPqMJpNFhHDQtTqJbx6ABfYaCxoQBUReI0wgAOW5TQAvOhqYkyFRaKlPVhsgBydWUWO5CDAYAQJU-AMAfg19r4AG4DZQNca5KbCxarTbczhdbwIC6oPyYsNkwA1PCUQpLEQAcUhAAkdCwtQIcDgvLbUzgaKYBAMODQBqzeMA4IgwCBgGAd6AIAB9Q9H49HiAATWObIAwgZQRABxEmifn4eIFud26mt6w8lI+l2RyOBhJo3xKk62RyhAwZVKQOzpMmwb0AA8nIkKpN+tJoFEFQZBBEAAGQQDBcG5MmhEoWhWQegRRHUCR2ThICES5PmO5sXuL7PhA6SOIEV4YAs3ycSeb7bng3ishcQrphAACiACOOhENkskcuMgROhAkzrPosbiJ+yBzkQvh8I4wA6PghA0LGYDiV4kkyap6lMkQCzZL6fwKUpKKadpBi6fpQqGUIyJhKENDmZZ1k7hKQFYNKsoeoq6b0Amdq1g69AYIaPwmhEWWWtaNC2tqGV6i6tmSvFMryoqGF+gGKVqtm6V1llOWlqaBVVsVrWZRVsVSjVSVeqGRD+goACC3CaJNoRNZmCg5qVbVQNllDLZ1+VrYV1Yrf1H7SaYAnVMGADa9BOWYOCpF5mFjUkEZpNGcYJkmEF1Q9E2UKRmSXWp123Yp90JEQ4YpFGiqvQGsYQAAPhAsY6NwFS-IU3DvSNWD1d9v3-epQPeehD3g5GL2I298OI+tmPRjjAbTbNoR41AV3mITIM+qTz2Krk2T1ZDzF4wAumxYAccJp5MjouBzGyADKQGTgekuvu+oD0Mm8tCNaECwTL2oGIQFl4C4JVjhOU7ADOc4LkuK5rggsDAMINCFNtEDtp23Y0EbJtm6O46Thq06zvOi7Li0juIMAvvG3cdBQMmACyrJNFeIUmeFgeWyH1th3bkeOu+YBAA)

#### 题意

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

#### 题解

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

### RequiredByKeys

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02759-medium-requiredbykeys/README.zh-CN.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMDsCsBOCBaCAlApgRwK4EsAnTAEwCEBPAaUwoGdJUVmXGAjCiAK3wEMA7AOZ0AFgIgAKAAI8BwsfwC2mAC68AlBADEykvlyLtAezZdMAYxWNGW2xACKuTHRX4j-a1ACSigA4AbTGV+FQheCEFMfkxCfHMIAAMsPCJSShp6AB4AFQAaKCoAPgSIAHcROJEINQBrZ2rSo2qKX0wwwkEDKNCE7JKBEkSqBIA6TyGSulbzfAAzThURNrpVCCNZiF9CI1bCV3r1xL7qsVDRI1x-QZXQlSa2NuIU4hIRiAB1Jf4JiHw6CH4RlCWyMADd8CRSPl8GcRBcrhBFLw6mF-P5Nttdvt-k8CC9uLgXBB-PgUYs2oDCEj0UkcHjSDlimMoIwAGJGQgQTAAD14fkC4wSQpULWc5livisUHwIRis145jaAFUVpyAN6MKD8PmYAD8AC4IC5YkJNWFIgaAQYHoQzbwSCRiHQ6JbjTLBIwAL7jEWtCAqmIABV4ez4-gAcjqIABeDB01Lkai0OiZAOEfIAcm1ygzhQgwGAEDVAJ1hrdQgA3Oa9Yb+NaYlX7Y7nC6yyoTYIIN6WVAhQlxnmAGr4TClNbfADiMIAErg2IaRCoVL46PqCyo6OYRCMuHQRhzBMA4EgwCBgGAL6AIAB9W93+93iAATQunIAwkZIRBpzE2g--7eEBnhevptMk9KJhkKZ5BAVAxhAdQUIcMFKly3IqFEJD-IhyHwThGzZHm0aMAA8ooMKZOBCaZIGcQ1Dk+RwQAZP6hR5ixZEUTBzGsfk0SgjEhQVheYBXgB-4QNkzihG+vArP84kPkB574H4HK3KKRYQAAongvD+Pk2nctMoSehAszbIYGZSKBKBbvpgRCM4wC4K4-h0BmYCqb46laUZJmsvpKz5OGQK6bg+lduZlkQNZtn2WiUSRHQLluR5F4yhhhDyoq-qqkWjDZjWRrtu6jC8Batb1raUBNk6rYlR2YDel5srZQqyqqlRLyRsoBVaqWjVlbVlVWooNrlQ69WuqVpotZlcodXlMTdaQvWYAAgvwJAbZE-UlsobZNSNmBVeNMSTc2zozU1LWgRA5hyfUsYANqMP5FgqJk4X6ZR8YvOkyapqqmZFbm+RpqtJDrWxuTvcZn3fXp-h-c8aRJlkaagzqGYQAAPrFuD8DUgKlPw4PLYQUMw4UcNQB9lhIxFKNQ4DmMg7FYP47FFWYBTkP-WtOpbTtkSw-DJlM79rMYymaa03GaMkMDgniwAuiJokgDeimPqyuB7EsnIAMoYSuOu69eyla4webG2IxAQEhBtGkY-iuW4-CrhAi7Lqu66btuu77h0R4IIgwACHQpQXVAQ4jmOdBux77je77K5rsAG5bjue4HmHSDAEn7uuKntsQAAshybRvmIiVOWnS4ZwHOfBwep7nmAQA)

#### 题意

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

#### 题解

```ts
type RequiredByKeys<T, K = keyof T, U extends keyof T = keyof T> =
  Omit<Required<Pick<T, K & U>> & Omit<T, K & U>, never>;
```

道理跟 `PartialByKeys` 是一样的。

- `Required<Pick<T, K & U>>` 用于从 `T` 中获取键**为** `K` 类型的字段组成新对象，并且将该对象的属性都设为必须的
- `Omit<T, K & U>` 用于从 `T` 中获取键**不为** `K` 类型的字段组成新对象
- 最后再使用 `Omit` 将上面两个对象组合成一个完整的新对象，就达到目的了

### Mutable

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02793-medium-mutable/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMDsCcBmCBaCBZArgFwIYCMAbAU0lRQsrPwE8IArAS1wDsBzAZwAtWIAKAAJNWnHiwC2xPAEoIAYkkATRpnHyATsVyKA9i0J05O-PWIBjbCgDWxGhzJk5TiAEVMxDtkZ6HUAJLiAA4kkizYENhcxBBsxCzE6oxmEAAGaVh4RMQAPAAqAHxpKRAA7lxJXBDiuDYcELiEhBCB6jqBCV4eEIwsqWm5RVU4BCT8LDrhmtp6BtIAdL4QAGI66hDEAB64QSSLRdg07RxmiYHYZD3YCQBmuGbRuTq6EADeZFBTuvp0XtgkAFwQTyJdjvCCfGZ0RQeE6MM7eFiA4E9NhgiHfCBmHQ7KTERSA-A6HQkVhkAC+iwO7Qwwyyj2eAF4aZkSHknjp8hBgMBXhFGH9iEjsCC2ABuCDQ46nLx6IUi8VYnFXfEQQnErQscUUqBkIqLTkANUYxBKED0EAA4vyABKYfCArjYbCBDj-bnYY5cOb0DhzVZsYBwJBgEDAMDh0AQAD6MdjcdjEAAmjpMGsAMJPaLWhLReN5mMQUPhqnRDIjHK5dYbK4sRR1ABK5lWimyAAVWu11AcANK2AA09RYNHynKZbygKHRBggAG1W91ejYaDprhBcgBdQG5Ofrqs1utmkzmcIAfmZ5byO85W53orAFPDYEj+bza484TTuA4XRf8cLYcYIJVnCEteQAUQAR0wBoBzAjZ2gsCAyQga5WjUAByAQSxQMweEaOJYg4YAcEYQgOHQsBAMCYDwPg48lgab8BwAOQmSDoKaZDUOxCBMOw3CGhIdgPGIrwyIoyiwhuO4HnZABGV4yF+AEgWFFEyElWF4VlVSRTIRVglxFU1RJFgyEkPBAXHKB6hwLhVjldSoApB9QIAGUYTwICZGc5IHaAB0Qddi0OaIzC-LofLIOCEOwbJ2IabIyyybJG2mb42V0OSRwHekdGy-I+2iuiLHiqDEuS1k0q+Axsg8zwcogersBysBgrAUCEladQ6iiqBuQgLCOBQTZYpG9RurISqcnQ5F2HQwqyAGoaRpKywutWKbaVZAAGRb2ojEBo1-BMllTSIEggABlK4XWOk6o3-J9wCgTkrp4TQIGXVMgWJEi9FdCBHWdV13U9b1fX9QMEEQYBWA4EoEjIQ1jVNDg-plFhAeBl03WAD1cIhv11ADINYfRwh-qx5GMFWaI0zwoTCIdJ1cbBwmfWJ1EizAIA)

#### 题意

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

#### 题解

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

### OmitByType

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02852-medium-omitbytype/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMAcCs0IFoIHkC2BLALgIQE8AVAgBwFNIVkbaqAjAiAKywEMA7AcwGcALThAAUAAVadeAjhnI42ASggBiGQBMsAVwzKA9vWbkAxjipUl5iAEUN5Hjiw6OpqADEATju0ADH0R9eAGghSLEMAawg2CB5ZCB0AM2CPCjd7WwgAdz4dGIgcMnJIt0KOHRxInh4sLg42egAbQpwdCH8AVX8AOmcIFx03CABRAA82DFJGnv98ih5DNyxSEygZwsxcPB0dRsEAXnRsfGICgB4AbyooWpkALmicBe5LiEMdDQ4cO44tenI3Z6wPAASuQ2KpHPUCHd6Fsdk4oFBAYNag1yNDYaD4RAAL5BGHbTEAPggwGAEDOEGuaPujy4AG4Xm8Pl8fn8cVR-D1iQA1LDkDJxDgQADiuAAEhp6Hc+DgcKQeDdSTg5nxOsweJ1+lxgHBEGAQMAwEbQBAAPrmi2Wi0QACabwGAGEdKpCmK-oUrZ7zRADUbVgcNscKCciEE2sT9hcoABtAAKECwQrC5AICQgRAq6bjAF0IORhjhyBxVDwIG0IAB+SnkABubLusezdyIObpYGxRuNIDNXqt6ds5QdbBipd7fd9WHG-XK-opgwAjho2PUgiMKMYcRB4h5tAByESrZCGAT1RrcWzADT2eo8XdgSekafkobDdc4FzLmJBAByZQXS-qTdt08CB90PY9lzPLgLyvLAbzve8Pj+eI2EMQoAFlnXIQCo0pMZqTsWkqFed5PkpVl-kRYFQXBDhIXRAlOCoJEUUaBi4XbP0CheYd0n2aMqDXIwcBOf9lxOdYjhIYNMJdFcIHxOFCSCCkqTuQjE3pRlSJZDBfgGbFCWUwTX2E0TF3EyTCGk8gTlk7Cgg07hlOfEjmXIvS-gZQEQTBCEoQUjFOG8nhkTqNjAsYoVDOMqAhOMcyAIkw5rNOez5O+Ty3Bc1T8PUh5NJC3zaPoyK4RCsLUXYzEcSMgIwGzTswBNMdLV6DRUj4NkAGVC3lHtWtNH1DVAKhiW6gRiggVMOuibZYMcBUIBlOUFSVFU1Q1LUdQQaBgE4HgMj+MaIF5fk5vqBaOCWlb5UVYBlWPTbNTcbVdT2nh5vsRaTswqaHRPKDbGlWU7vWp71Rerh9UNMAgA)

#### 题意

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

#### 题解

```ts
type OmitByType<T, U> = {
  [P in keyof T as T[P] extends U ? never : P]: T[P];
}
```

- 关键点就是通过 [as](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#key-remapping-via-as) 来创建一个新的键类型，由此通过条件判断来排除掉某些键

### ObjectEntries

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/02946-medium-objectentries/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBBMCcAsA2CBaCB5ARgKwKYGMAXAUQDtCAnAS1wGdJUUnmHMBPCbKgQ1IHNaAC14QAFAAEuvAcNIBbXIW4BKCAGIFAEyoBXOeoD2OAoQYM1FiAEUddQlQOkzUAJJyADgBtcC8hEKCuP5s7kEAbrgUtA6kEAYAZhAABilYeEQAdLjk1HQpSc4QAGIGFBC4AB7cHt4QhfmEIXT41O6mUFTkkfHc+EEAsgaauJ4QAN4MUKTVuABcELSUnXwA3JMQ3HxzEKR6mJFrUFCeBvjc9o6084vU-ADaALoQAD47Op6ehxAAvgyNoRA5EMRmQlnQIABeDDGIig3K0AA8g2GngAfBBgMAIHcAOTTBQ4gA0CyW-Cer1xm1wRLecn2FHJ2JxJzOF1ItBpN2Wjxebw+Dy++UK6IAajQAO5xWIAcSohAAEjpMPNBIRCO4rpjCLR8IIMthaBlSnxgHAkGAQMAwNbQBAAPoOx1Ox0QACaBh0ZQAwsCIPLIkFnUGHRBLdb-kEvbItiLuJ5bAiACroqETKB3AAKEE6EAA1rg2AkIImHvNM8TE5mnpVCNlNLQIDpSMN4p1cJoIAB+RvN3Ct0jtiDzSsZh4PMDfOpgCPQ9IkHI0RHJyFQKPSXCx+O4BEAJVwAEcdFQKO2k6jUXd84XEiW1tawLbg0Hi3YIF7uLRwU-naGrVQPKUhDBACYwQMQh5xsSxAVKERA-BA8QUAY+g4uIEYoLqcbePwdDADo9ieByYD-u4gHjGBMEmEUcafsSAByBgkBBoyTohyEQKh6GYR82RbLQeEEURxFdBQPR9BAyIjOMDD4tsXL8AwVLzLsdKRAwLLnDEVwkrcfA8q8uwfBO4ZNBJwKeHCi4rrismcqSfCMpSWw0ip9KOcypyaZcdm6fpfKeOO06mWcn4NlCdwMNBsGEAi4E6HGCJpCYll0Ei5mosSkkWQudDnoSkWUUQsXMYlMLzmCiIZtwFD2AlWV5WZKIpbQeUFdFxXxZ4pVzs1CKgVenbzE2LZth23wZUyV40sNfajQ8rVQFFJgdQlSWwjliL9QWQ29v2g7jcSuJTcSM17Zo80ZWAgU2iA9rfi6RSegEkQQAAyrWGp3fddq-g+4BQOir3CCeECFp6CwGPGbLaaq6qasA2q6vqhrGqaCCIMAvC0OKakAxAYq4JKtCQ-hWkqmqGqzFqOp6gaRoUCaZoY8TUNaQw6KDCDa48ThMMU-DiO0yjDMWlaYBAA)

#### 题意

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

#### 题解

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

### Shift

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03062-medium-shift/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMAMBsAmCBaCBlAFgSwGYBdJUUTSiAjATwgCtsBDAOwHMBnTJiACgAE6m2HRgFsApvnoBKCAGIxAE2wBXYbPoAndfUpEiM-RACKS0a3zYA9o11QAksIAOAG1FjG+CPkyjPlBz4A3UXVWS0YIC1wIAANYgEFNbQA6djx8WOibCAAxC3UIUQAPekcXLIz8P1MAY3VsB0IoSv8IACVTJScPAF4MHAIAHgBtaAAaCERxgEYAXQA+CGBgCCHJiFmiDKyFgDVsUQB3CPCAcWx8AAklcgAuCEx8fAdWG6X8VmrMJJpWJLzmYBwJBgEDAMDg0AQAD6MNhcNhEAAmhYlPkAMIWeQ+C7BHzw-EwiCg8HNHxYNIDAAqBUK+FEjHkrAgTEoQ3mEF61KKdIZTKGLPGSSF2EYuGCEAAojMIAB+SUQO6MURBdQAbnBYEhBPxEEppg8aPorFM0O1cKJYOwjjyHlJEAA3pKAI5KehOcYSwr+aoeAC+EFw6gsqgA5DxSShPm6XCxTMAlOYnKwQ2ArQ4bQ7JV7RD7sm7jeMAHIWfASl1uiD+wPBiBhiNRpwx5hxhPYJMpsB26pGk29IZET3e-ADMuupwDcmDEbjNazObjVbTebzgfZn0j8vjyfDoYh+gh8Yh8gH2vVE8h+QhiDzBdH89nw+X5ejMAzDWakCms2E7Korzi9A6WeL9vwtD8iAWLANB8SgUXyVgLCcVsrBee5HmeV5gHeT5vl+f5AQQRBgCYVgDmCCCID2Q4IAQpDzBQu4HieF43g+L4fj+dQASBIjaOQxhWAogBZPIfDRDhG3pZtUKYjDWJwjj-hBMEwCAA)

#### 题意

实现类型版本 `Array.shift`。

例如：

```ts
type Result = Shift<[3, 2, 1]> // [2, 1]
```

#### 题解

```ts
type Shift<T extends any[]> = T extends [any, ...infer E] ? E : never;
```

- 这个就比较简单啦，通过元组的 [rest](https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types) 元素，就可以获取到我们想要的值啦。

### Tuple to Nested Object

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03188-medium-tuple-to-nested-object/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?ssl=24&ssc=88&pln=22&pc=1#code/PQKgUABBDMCMAc8IFoIBUCuAHANgUwgBcB7CAOTwGdC8ATCAeQCMArPAY0MhWV7+6YBPCCwCWAQwB2Ac0oALKRAAUAATFTZCyQFs8hcQEoIAYl21RGbSeKsOXKN2NOIARQxVCo4pO7cA4qIAbniSEOJE2PhEglgEAAYJaAlxRAqEEN44wuze+qKSlBDUAE750tGxADRhkvThhDHxCQCqydVMGKI4daE2bJwQxRwYxZRBeFkAdL5QyQ2xlOylWPYVBOEAvOiReGjEFNR0zP2EADwA2gDk4pcAutUlZQB8EMDAEADe4gBcRYSlMgAvtx5gQmBAtphcLt9h4jrZOBdrpdqpcmHdqpJLEw8MUXm9Pj9PkxfljtDjioDgVBQRB2BDttC9gcaLRjnYLvcIExiMR8FJ8e8eXy8FJJhBRAAzVIEQg7CWFPDaFaCaosDDUQZ6EahQhyAjNNbcZIzCAvABqojwAHcMqEAoQABIYEkQOSEQhYSjfN6ERZySYsSiTYjFaTAOCIMAgYBgOOgCAAfWTKdTKYgAE1iCMIABhYi0AiO3EENNl5MQGNx2ksuinABi1QAotVmi8tvWIHgAB40WqFR4yCAAfk+EHOAAUJaF67dflD8My4WyEWcWxA2xBARBSXhgsVq41GYvYYcVydTmhW+30F3eyFaIVzvlJbiII2IJMvy+303biPyGXBtm2vHcNwAbjjeMQCTcs03QDw83ESgqFguD0yrURlVDdJaQ+CAmwARwwcQcGbbtYgGbdJWKYgrEuFRQWQdgFBwfAZCoYAME8HBKEuQ9YjpZDUK2c5uCbCiOSIkicEvHYlzPdlESuG4uUHaQnmqfCiXUrcnk08TJMRaTSLkplT1ZJSzhUlEIDRDEIDJClNLHIl8NdJy323QF9MqQzKLXYjTIXGFa3PDkbNRdFUXYBzhX5SQXO0353JSulfni0VQm8vSDKgCSAtOEzZJChTLNXTl2l5BKXMygUDNuON8hoYpJXEdgCHzHBQ0lDAcE+bgcm64pfnUyCoGQ2hfkudSUTAYFmtxNqOrzURinYKIPm4YpxHMDVSWxXFxrCSgpr+AFpHmyAwFpLqer63M1o2ggtju1q+ogAAyVb1vwSCcgKdJNk+bdkLzPl7pwR7frwSDxGmaC0PQxN3xGPU3wAZRoL0kfQytY1AbgXgxhQhggQRs2KIo+W4rwCl+d1PW9X1-UDYNQ3DSN4GAKRKGtXEiYgS0bWpnBae8b03Q9L0fWAP0WLZkMwwjBBucoGnPAlwWAFlQ061j2OkKgGel5m5dZoMlcuqswCAA)

#### 题意

给定一个 `T` 仅包含 string 类型和 type 的元组类型 `U` ，递归地构建一个对象。

例如：

```ts
type a = TupleToNestedObject<['a'], string> // {a: string}
type b = TupleToNestedObject<['a', 'b'], number> // {a: {b: number}}
type c = TupleToNestedObject<[], boolean> // boolean. if the tuple is empty, just return the U type
```

#### 题解

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

### Reverse

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03192-medium-reverse/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMCMCcAmCBaCAlApgN0wJwGdNJUUzySAjATwgCsBLAQwDsBzAgC1YgAoABRqw7cWAW0wAXJgEoIAYgkATBgFcxCyaoAOAG2JQS84xACKqzAUkMA9ixIkAkmL2YJLSREmdMX6tt9cQlsWCBsAMwgAAxiAQTw8JmoAOjwcfCIYqIcoADEbPAhMAA8mF30ALhzomMl-SwBjPAZtSRI6gIgmCABeDHTCTAAeAG0AciYxgBoIMcoxgF0APghgYAhx+ZmJxfb6iEpe-qCiUZ3trdmGxZW1jbHri+nZyYWSLOqVgDUGTAB3MKhADiDEkAAlVJQKhBOJJJNoCBU1pICA1OMk6ARkgU2MA4EgwCBgGASaAIAB9SlU6lUiAATRsqkKAGEbEpfGD8L4aTzKRAiSSOr4sCdhgAVFZ9MVFYqSTAsJQEDbJFUMFjhfAQXIzNUawoAUQWEAA-Bt9TMVckRRlhrklkboWKANwksBk3k8iBiyyeZlMIhKj00-nEhguAqeIUQADeEH1AEdVExdDN9cUAg1PABfCDhPA2DRjfhClBo5P6diWYCqay6AhjQX7Br+yxHEYkNMZyRDBNJ3RDa2DUbLGYjZZLKYd9OYTM9xPJgcDU7jSZPEf3S47ceTqCdmfd3sLwfL86zTfXdfjR5n55bpYTsBvV3uoO03JM7yagDKcoRFNffICqAJArF+3BpBA1CMoUBA2LoNYhIiMJwgiSLACiaIYliOJ4ggiDAKwBB-PgIEQD8-wQLB8HWHYSGwvCiLIqi6KYtieC4vi+FUQhtGkQAsgUvjMtwugVmwljQvRqFMZhrE4oSxJgEAA)

#### 题意

实现类型版本的数组反转  `Array.reverse`。

例如：

```ts
type a = Reverse<['a', 'b']> // ['b', 'a']
type b = Reverse<['a', 'b', 'c']> // ['c', 'b', 'a']
```

#### 题解

```ts
type Reverse<T> = T extends [...infer F, infer E] ? [E, ...Reverse<F>] : T;
```

- 首先利用元组的 `rest` 元素，可以很轻易得拿到最后一个元素，并把它放在数组的第一个位置。
- 之后对 `rest` 元素 `F` 进行迭代，反复调用 `Reverse` ，就可以完成啦。

### Flip Arguments

> [挑战要求](https://github.com/type-challenges/type-challenges/blob/main/questions/03196-medium-flip-arguments/README.md)
> 
> [在线示例](https://www.typescriptlang.org/play?#code/PQKgUABBDMCMCcA2CBaCAxANgSwA4QEEAnAcwFcBbAUwDsAXAZ0lRVbeYCMBPCAK2wCGNEgwAWQiAAoAAvyEjxNanQEBKCAGJqAE2yVNA0pVqNmzDRYgBFMlQZ1sAexpmoASQq5MVavQh1RKn8uXCCANyoiBicaCEcAMwhMR20BMQByBggAA1yAfQA6eJxcXOyC1wgAFRCgsqw8YnJfRgAeKoA+MogiKgBHMmxerPiyGgBjB2dg0Jzcqu6hbR6qOjIiGiyBCBoqAHcIUYmp2LpaiD3RbHHRCHEsgKCGAWoVtY2ZoITqiA4yOhWESiVGWuEML1WkQYFSgzHQjiIECoAA8Xl4qAAuSplM6hBjjIh4OjMXFBBq4ULLAC8GBKTWM9AYrUkhhIAAYMRB7IThAAaCCs2CcmiUDiRfmsgBMnI4jkc3iE6ipHQgYUc2G0KuYwGAUlZHN+coVNAlpCFO1F4oFpGlXLoPJISpVao1zDKlRVADVsPs4rEAOLYOgACTIHE5ojodFwDAxOsYNwKvGhCJIwDgSDAIGAYFzoAgeULReLRYgAE1HOsIABhFJBYORIIl5uFiDZ3OkiAAJSoQIYVHaKppVSRyLotG0WQA2gVZ9gaPFIhh+fPF4iAKIAXQgAH4IFP1-zZwUe32B+gOtvOVUANwd87k+ktJmdCDD0fjmiTqTH1mxiCrkuAAKToAQuS5druP6zn+nKnlCA5AR0oGQded65mA+Yts21R2AC1ZpHYBbYcWbY5tgngIgCnYAN4QOuAwCJg-LrsioSTBAAC+hxEI4FAQOk0ikigNxMd4wh2MA-zYJgDDpPeszjIRWQ0lOzCsexdCtAxZBMa0j5GM+zKgbK8pUEIHT8pIJlGuZNAdJZ6lsVQkzaYxmD6XShkmEykjxHKnLcvOjpviqIoUGKRCWVI-mOIF9rBaB4WRQ5vJOZpbm6R5BnND5zL6vFDqmiQ5rJVaUoyrZiqhaq6qalZBWGmZQjFaVlpEMVtpBcIoEupqjmbhhmEgMRJGtug6yPIiADK44xqNY1kcNzAqtN4i9BAXCVoiDDytJzj-pG0axvG+KiEmKakOmCCIMAQgMHskQrRA3q+rtmD7ZsEZRjGcbAAm53JgUqbXUgwDvZ9TBQCqACyCJBNW4iYOJJB2N9x1-QDF3A6QWY5mAQA)

#### 题意

实现 lodash 的类型版本 `_.flip`。

Type `FlipArguments<T>` 需要函数类型 `T` 并返回一个新的函数类型，该函数类型与 T 的返回类型相同但参数相反。

例如：

```ts
type Flipped = FlipArguments<(arg0: string, arg1: number, arg2: boolean) => void> 
// (arg0: boolean, arg1: number, arg2: string) => void
```

#### 题解

```ts
type Reverse<T> = T extends [...infer F, infer E] ? [E, ...Reverse<F>] : T;

type FlipArguments<T> = T extends (...args: infer P) => infer R ? (...args: Reverse<P>) => R : T;
```

- 通过条件类型判断加上 `infer` 就可以去推断出函数的参数与返回值类型
- 目的是将函数参数反转，那么通过扩展运算符，可以收集到所有的函数参数，这时它已经是个数组，只要使用 `Reverse<T>` 进行转换即可

> `TypeScript` 是结构类型，不是名义类型，所以这个函数参数的名称是无关紧要的
