# react hooks 使用

## 闭包陷阱

https://juejin.cn/post/6972893133243695141

### 解决闭包陷阱的方案

- 使用 useRef 解决闭包陷阱的问题

- 更新 State 时的回调函数
  
  ```ts
  // 回调函数的最新值
  setValue(value => value + 1)
  ```

### 闭包陷阱和 Hooks 依赖

**useEffect**、**useLayoutEffect**、**useCallback**、**useMemo** 的第二个参数为依赖数组，依·赖数组中任意一个依赖变化（浅比较）会有如下效果：

1. **useEffect**、**useLayoutEffect** 内部的副作用函数会执行，并且副作用函数可以获取到当前所有依赖的最新值。
2. **useCallback**、**useMemo** 会返回新的函数或对象，并缺内部的函数也能获取到当前所有依赖的最新值。

### 总结

- **React Hooks** 存在“闭包渲染”的问题，每次 render 都会闭包缓存当前render对应的 state

- 可以通过 **useRef**、**state 更新时的回调函数**来解决这个问题

- 使用 **EffectHook** 依赖时要注意取消副作用

## useMemo和useCallBack的区别

![](../../imgs/useCallBack和usememo的区别seMemo.jpeg)

## useDeferredValue 和 useTransition的区别

延迟更新界面。

| 名称                   | 区别            |
| -------------------- | ------------- |
| `useDeferredValue()` | 延迟的是状态值       |
| `useTransition()`    | 延迟的是状态组装界面的过程 |

- **相同：** useDeferredValue 本质上和内部实现与 useTransition 一样都是标记成了延迟更新任务。
- **不同：** useTransition 是把更新任务变成了延迟更新任务，而 useDeferredValue 是产生一个新的值，这个值作为延时状态。
