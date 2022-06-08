# 动画：react-transition-group

[使用-掘金](https://juejin.cn/post/6844903869894524942 )

# 深入React合成事件机制原理

https://segmentfault.com/a/1190000039108951

由于fiber机制的特点，生成一个fiber节点时，它对应的dom节点有可能还未挂载，onClick这样的事件处理函数作为fiber节点的prop，也就不能直接被绑定到真实的DOM节点上。为此，React提供了一种 ***顶层注册，事件收集，统一触发*** 的事件机制。
