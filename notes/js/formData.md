# FormData

**`FormData`** 接口提供了一种表示表单数据的键值对 `key/value` 的构造方式，并且可以轻松的将数据通过[`XMLHttpRequest.send()`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/send) 方法发送出去，本接口和此方法都相当简单直接。如果送出时的编码类型被设为 `"multipart/form-data"`，它会使用和表单一样的格式。

## [构造函数](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData#%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0)

[`FormData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/FormData "FormData()")

创建一个新的 `FormData` 对象。

## [方法](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData#%E6%96%B9%E6%B3%95)

[`FormData.append()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/append)

- `FormData` 中添加新的属性值，`FormData` 对应的属性值存在也不会覆盖原值，而是新增一个值，如果属性不存在则新增一项属性值。

[`FormData.delete()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/delete)

- FormData 对象里面删除一个键值对。

[`FormData.entries()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/entries)

- 一个包含所有键值对的[`iterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)对象。

[`FormData.get()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/get)

- 回在 FormData` 对象中与给定键关联的第一个值。

[`FormData.getAll()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/getAll)

- 一个包含 `FormData` 对象中与给定键关联的所有值的数组。

[`FormData.has()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/has)

- 回一个布尔值表明 FormData` 对象是否包含某些键。

[`FormData.keys()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/keys)

- 一个包含所有键的[`iterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)对象。

[`FormData.set()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/set)

- `FormData` 设置属性值，如果`FormData` 对应的属性值存在则覆盖原值，否则新增一项属性值。

[`FormData.values()`](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/values)

- 一个包含所有值的[`iterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)对象。

## 使用

```js
const formData = new FormData()

formData.append('a', 1) 

console.log(formData.get('a')) // 1
```

## 资料

[FormData - Web API 接口参考 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData)
