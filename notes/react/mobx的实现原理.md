# MobX 的实现原理

mobx 是流行的状态管理库，热度仅次于 redux。它和 redux 有的地方一样，也有的地方不一样：

一样的地方是 mobx 和 redux 都是单向数据流，通过 action 触发全局 state 更新，然后通知视图。

redux 的数据流：

![](../../\imgs\mobx-principle-1.png)

mobx 的数据流：

![](../../\imgs\mobx-principle-2.png)

但是它们修改状态的方式不一样：

redux 是每次返回一个全新的状态，一般搭配实现对象 immutable 的库来用。

mobx 每次都是修改的同一个状态对象，基于响应式代理，也就是 Object.defineProperty 代理 get、set 的处理，get 时把依赖收集起来，set 修改时通知所有的依赖做更新。和 vue2 的响应式代理很类似。

其中，redux 那种方式是函数式的思路，所以状态的修改都在一个个 reducer 函数里，而 mobx 那种方式则是面向对象的代理的思路，所以很容易把 state 组织成一个个 class。

这也就导致了两种状态管理方式的代码组织是有区别的：

redux 是在 reducer 函数里组织状态（函数式的特点）：

```js
const reducer = (state = 0, action) => {
  switch (action.type) {
    case 'INCREMENT': return state + 1;
    case 'DECREMENT': return state - 1;
    default: return state;
  }
};
```

而 mobx 则是在 class 里组织状态（面向对象的特点）：

```js
import {observable, action} from 'mobx';
class Store {
  @observable number = 0;
  @action add = () => {
    this.number++;
  }
}
```

此外，redux 那种方式每次都要返回一个新的对象，虽然可以用 immutable 的库来减少创建新对象的开销，但是比起 mobx 直接修改原对象来说，开销还是大一点。

而且 redux 通知依赖更新的时候是全部通知的，而 mobx 因为收集了每个属性的依赖，可以精准的通知。

所以 mobx 的性能会比 redux 高一些。

综上，**mobx 和 redux 都是单向数据流，但是管理状态的思路上，一个是函数式的思想，通过 reducer 函数每次返回新的 state，一个是面向对象的思想，通过响应式对象来管理状态，这导致了状态组织方式上的不同（function/class），而且 redux 创建新的 state 的开销还有通知所有依赖的开销都比 mobx 大，性能比 mobx 差一些。**

对比下来，我们会发现 mobx 似乎比 redux 优秀一些。那我们具体看下 mobx 怎么用吧：

## mobx 的使用

官方提供的 demo 是这样的：

```js
import React from "react"
import ReactDOM from 'react-dom';
import { makeAutoObservable } from "mobx"
import { observer } from "mobx-react"

class Timer {
    secondsPassed = 0

    constructor() {
        makeAutoObservable(this)
    }

    increase() {
        this.secondsPassed += 1
    }

    reset() {
        this.secondsPassed = 0
    }
}

const myTimer = new Timer()

const TimerView = observer(({ timer }) => (
    <button onClick={() => timer.reset()}>Seconds passed: {timer.secondsPassed}</button>
))

setInterval(() => {
    myTimer.increase()
}, 1000);

ReactDOM.render(<TimerView timer={myTimer} />, document.getElementById('root'));
```

就像前面说的，mobx 基于响应式对象来管理状态，所以组织状态是用 class 的形式。

我们声明了 Timer 的 class，有一个属性是 secondsPassed 代表过去了几秒，有两个方法来修改它。

在构造器里调用 makeAutoObservable 来创建响应式的代理。

然后 new 一个 Timer 的对象，传到组件里，组件使用 observer 的高阶组件包裹，它负责把被包裹的组件添加到 timer 的响应式依赖中去。

然后把这个组件渲染到 dom。

这样就完成了 mobx 和 react 的结合使用，看下效果：

![](../../\imgs\mobx-principle-3.gif)



我们是把时间（secondsPassed）放在 mobx 的全局 state 中管理的，在组件里使用，然后定时更新它。发现每次更新组件都得到了通知并做了渲染，这就是全局状态管理的功能。

demo 里我们用的 makeAutoObservable 函数，它会自动给属性添加响应式代理，方法会添加一层触发 action 的代理。

也可以手动标识：

```js
import { observable, action } from "mobx"

class Timer {
    @observable secondsPassed = 0

    constructor() {
    }

    @action increase() {
        this.secondsPassed += 1
    }

    @action reset() {
        this.secondsPassed = 0
    }
}
```

我们大概知道了 mobx 怎么用，那它是怎么实现的呢？

接下来我们从源码来理一下它的实现原理：

## mobx 的实现原理

首先，mobx 会对对象做响应式代理，那代理以后的对象是什么样的呢？

我们打印下：

![](../../\imgs\mobx-principle-4.png)

原始对象的 secondsPassed 属性是 0，increase 和 reset 方法体修改 secondsPassed 的值。

而代理以后的对象属性分为了 get 和 set，并且实现变成了 this[$mobx].getObservablePropValue 和 setObservablePropValue，这明显是做了响应式的处理了。

代理以后的方法都变成了 excuteAction，执行方法会 dispatch 一个 acition。

那这个响应式的代理是怎么实现的呢？

跟踪 makeAutoObservable 的源码会发现 mobx 创建了一个 ObservableObjectAdministration 的对象放到了 $mobx 属性上。

![](../../\imgs\mobx-principle-5.png)

在 timer 对象确实是有这个属性的：

![](../../\imgs\mobx-principle-6.png)

用 Symbol 声明了一个私有属性 mobx administration 来放 ObservableObjectAdministration 对象。

然后还用 Symbol 声明了一个私有属性。mobx-keys 来放所有做了代理的属性和方法名。

那这个 ObservableObjectAdministration 对象是干啥的呢？

看下它的定义：

![](../../\imgs\mobx-principle-7.png)

可以看到它有 values 属性记录每个 key 的依赖。

还有 getObservableValue 和 setObservableValue 来获取和设置某个 key 的值。这两个方法就是被代理后的属性的 get set 最终调用的方法：

![](../../\imgs\mobx-principle-8.png)

这不就串起来了么：

创建对象的时候 mobx 会对属性和方法做代理，会添加一个 Symbol(mobx administrator) 属性到对象上来保存 ObservableObjectAdministration 对象，它是用来记录属性的所有依赖的，对属性的 get 和 set 都会被代理到这个 ObservableObjectAdministration 的 getXxx 和 setXxx 方法上。

我们打印下这个对象看看：

![](../../\imgs\mobx-principle-9.png)

确实，values 里保存了唯一一个属性和它的所有依赖。

至此，对对象做响应式代理的流程我们已经理清了：

![](../../\imgs\mobx-principle-10.png)

那这个依赖是什么时候收集的呢？

我们继续往下看 get 收集依赖和 set 触发依赖更新的部分：

我们用 observable 包裹了组件，它是一个高阶组件，对组件做一层代理，返回新的组件：

![](../../\imgs\mobx-principle-11.png)

在这层代理里面，创建了 Reaction 对象，也就是收到更新的通知之后怎么做出反应，在回调函数里用 setState([]) 的方式实现了强制更新。

![](../../\imgs\mobx-principle-12.png)

并且，这层高阶组件的代理里会把当前组件设置到全局，这样后面做 get 的依赖收集的时候就能拿到对应的组件了。

![](../../\imgs\mobx-principle-13.png)

所以在组件里用到 state 的 get，做依赖收集时，就知道当前是哪个组件了：

![](../../\imgs\mobx-principle-14.png)

![](../../\imgs\mobx-principle-15.png)

当然，这里收集的不是具体哪个组件，而是 onInvalidate 的回调函数，也就是收到更新的通知之后如何做出反应。

这样就完成了依赖的收集，在后面修改响应式对象的状态属性的时候，就会触发依赖，然后实现组件的更新：

![](../../\imgs\mobx-principle-16.png)

这样，我们就串联起了 mobx 的响应式原理：

![](../../\imgs\mobx-principle-17.png)

## 总结

mobx 是热度仅次于 redux 的状态管理库，它和 redux 有相同的地方也有不同的地方：

相同的地方是都是单向数据流。

不同的地方是 redux 是函数式思想的实现，通过 reducer 函数管理状态，一般会用 immutable 的库来提高创建新对象的性能。而 mobx 是面向对象的思想，通过响应式代理来管理状态，可以通过 class 组织 state。

性能方面 mobx 的响应式能精准的通知依赖做更新，而 redux 只能全局通知，而且 mobx 只是修改同一个对象，不是每次创建新对象，性能会比 redux 更高。

然后我们又通过一个 demo 来入门了下 react 中使用 mobx：通过 class 组织状态，然后创建响应式代理，组件用 observer 高阶组件做一层包装，传入 mobx 的对象，这样 mobx 和组件就结合到了一起，状态更新就能通知到组件。

之后我们从源码层面理清了 mobx 的响应式机制的实现原理：mobx 会在对象上添加一个 Symbol($mobx) 的隐藏属性，用来放 ObservableObjectAdministration 对象，它是用于管理属性和它的依赖的，在 get 的 时候收集依赖，然后 set 的时候就可以通知所有收集到的依赖（Reaction）做更新。
