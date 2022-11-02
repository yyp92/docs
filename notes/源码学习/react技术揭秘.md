# react技术揭秘

## render阶段

### beginWork 流程图

![](../../\imgs\react-beginWork.png)

### completeWork 流程图

![](../../\imgs\react-completeWork.png)

## commit阶段

### before mutation 阶段

在`before mutation阶段`，会遍历`effectList`，依次执行：

- 处理`DOM节点`渲染/删除后的 `autoFocus`、`blur`逻辑

- 调用`getSnapshotBeforeUpdate`生命周期钩子
  
  - `getSnapshotBeforeUpdate`是在`commit阶段`内的`before mutation阶段`调用的，由于`commit阶段`是同步的，所以不会遇到多次调用的问题

- 调度`useEffect`
  
  - `before mutation阶段`在`scheduleCallback`中调度`flushPassiveEffects`
  
  - `layout阶段`之后将`effectList`赋值给`rootWithPendingPassiveEffects`
  
  - `scheduleCallback`触发`flushPassiveEffects`，`flushPassiveEffects`内部遍历`rootWithPendingPassiveEffects`

### mutation 阶段

`mutation阶段`会遍历`effectList`，依次执行`commitMutationEffects`。该方法的主要工作为“根据`effectTag`调用不同的处理函数处理`Fiber`。

### layout阶段

`layout阶段`会遍历`effectList`，依次执行`commitLayoutEffects`。该方法的主要工作为“根据`effectTag`调用不同的处理函数处理`Fiber`并更新`ref`。

## useLayoutEffect和useEffect执行时机

### useLayoutEffect

- `mutation阶段`会执行`useLayoutEffect hook`的`销毁函数`

- `layout阶段`会执行`useLayoutEffect hook`的`回调函数`

- `useLayoutEffect hook`从上一次更新的`销毁函数`调用到本次更新的`回调函数`调用是同步执行的

### useEffect

- 在`Layout阶段`完成调度`useEffect`的`销毁`与`回调`函数

- `useEffect`则需要先调度，在`Layout阶段`完成后再异步执行。
