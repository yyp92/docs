# 揭秘 Redux 设计思想与工作原理

## Redux 背后的架构思想——认识 Flux 架构

> Redux 的设计在很大程度上受益于 Flux 架构，我们可以认为 Redux 是 Flux 的一种实现形式（虽然它并不严格遵循 Flux 的设定），理解 Flux 将帮助你更好地从抽象层面把握 Redux。

Flux 并不是一个具体的框架，它是一套由 Facebook 技术团队提出的应用架构，这套架构约束的是应用处理数据的模式。在 Flux 架构中，一个应用将被拆分为以下 4 个部分。

- View（视图层）：用户界面。该用户界面可以是以任何形式实现出来的，React 组件是一种形式，Vue、Angular 也完全 OK。Flux 架构与 React 之间并不存在耦合关系。
- Action（动作）：也可以理解为视图层发出的“消息”，它会触发应用状态的改变。
- Dispatcher（派发器）：它负责对 action 进行分发。
- Store（数据层）：它是存储应用状态的“仓库”，此外还会定义修改状态的逻辑。store 的变化最终会映射到 view 层上去。

这 4 个部分之间的协作将通过下图所示的工作流规则来完成配合：

![](../../\imgs\interview-principle-react-redux-1.png)

> 一个典型的 Flux 工作流是这样的：`用户与 View 之间产生交互，通过 View 发起一个 Action；Dispatcher 会把这个 Action 派发给 Store，通知 Store 进行相应的状态更新`。Store 状态更新完成后，会进一步通知 View 去更新界面。

值得注意的是，图中所有的箭头都是单向的，这也正是 Flux 架构最核心的一个特点——单向数据流。

## Flux 架构到底解决了什么问题

> Flux 的核心特征是单向数据流，要想完全了解单向数据流的好处，我们需要先了解双向数据流带来了什么问题

### MVC 模式在前端场景下的局限性

双向数据流最为典型的代表就是前端场景下的 MVC 架构，该架构的示意图如下图所示

![](../../\imgs\interview-principle-react-redux-2.png)

除了允许用户通过 View 层交互来触发流程以外，MVC 架构还有另外一种形式，即允许用户通过直接触发 Controller 逻辑来触发流程，这种模式下的架构关系如下图所示：

![](../../\imgs\interview-principle-react-redux-3.png)

**在 MVC 应用中，会涉及这 3 个部分：**

- `Model（模型）`，程序需要操作的数据或信息；
- `View（视图）`，用户界面；
- `Controller（控制器）`，用于连接 `View` 和 `Model`，管理 `Model` 与 `View` 之间的逻辑

原则上来说，三者的关系应该像上图一样，用户操作 `View` 后，由 `Controller` 来处理逻辑（或者直接触发 `Controller` 的逻辑），经过 `Controller` 将改变应用到 `Model` 中，最终再反馈到 `View` 上。在这个过程中，数据流应该是单向的。

> 事实上，在许多服务端的 MVC 应用中，数据流确实能够保持单向。但是在前端场景下，实际的 MVC 应用要复杂不少，前端应用/框架往往出于交互的需要，`允许 View 和 Model 直接通信`。此时的架构关系就会变成下图这样：

![](../../\imgs\interview-principle-react-redux-4.png)

这就允许了双向数据流的存在。当业务复杂度较高时，数据流会变得非常混乱，出现类似下图这种情况：

![](../../\imgs\interview-principle-react-redux-5.png)

图中我们的示例只有一个 Controller，但考虑到一个应用中还可能存在多个 Controller，实际的情况应该比上图还要复杂得多

在如此复杂的依赖关系下，再小的项目变更也将伴随着不容小觑的风险——或许一个小小的改动，就会对整个项目造成“蝴蝶效应”般的巨大影响。如此混乱的修改来源，将会使得我们连 Bug 排查都无从下手，因为你很难区分出一个数据的变化到底是由哪个 Controller 或者哪个 View 引发的。

此时再回头看下 Flux 的架构模式，你应该多少能感受到其中的妙处。这里我们再来回顾一下 Flux 中的数据流模式，请看下图：

![](../../\imgs\interview-principle-react-redux-6.png)

`Flux 最核心的地方在于严格的单向数据流`，在单向数据流下，状态的变化是可预测的。`如果 store 中的数据发生了变化，那么有且仅有一个原因，那就是由 Dispatcher 派发 Action 来触发的`。这样一来，就从根本上避免了混乱的数据关系，使整个流程变得清晰简单

不过这并不意味着 Flux 是完美的。事实上，Flux 对数据流的约束背后是不可忽视的成本：除了开发者的学习成本会提升外，Flux 架构还意味着项目中代码量的增加。

Flux 架构往往在复杂的项目中才会体现出它的优势和必要性。如果项目中的数据关系并不复杂，其实完全轮不到 Flux 登场，这一点对于 Redux 来说也是一样的。

现在你不妨结合 Flux 架构的特性，再去品味一遍 Redux 官方给出的这个定义：

> Redux 是 JavaScript 状态容器，它提供可预测的状态管理。

此时的你，想必更加能够体会“可预测”这三个字背后的深意。

## Redux 关键要素与工作流回顾

> Redux 主要由 3 部分组成：`Store、Reducer 和 Action`。

- Store：它是一个单一的数据源，而且是只读的。
- Action 人如其名，是“动作”的意思，它是对变化的描述。
- Reducer 是一个函数，它负责对变化进行分发和处理，最终将新的数据返回给 Store。

Store、Action 和 Reducer 三者紧密配合，便形成了 Redux 独树一帜的工作流，如下图所示：

![](../../\imgs\interview-principle-react-redux-7.png)

在 Redux 的整个工作过程中，数据流是严格单向的。如果你想对数据进行修改，只有一种途径：`派发 Action`。`Action 会被 Reducer 读取，Reducer 将根据 Action 内容的不同执行不同的计算逻辑，最终生成新的 state（状态）`，这个新的 state 会更新到 Store 对象里，进而驱动视图层面作出对应的改变。

对于组件来说，任何组件都可以以约定的方式从 Store 读取到全局的状态，任何组件也都可以通过合理地派发 Action 来修改全局的状态。`Redux 通过提供一个统一的状态容器`，使得数据能够自由而有序地在任意组件之间穿梭。

## Redux 是如何工作的

我们先来看一下 Redux 的源码文件夹结构，如下图所示：

![](../../\imgs\interview-principle-react-redux-8.png)

其中，utils 是工具方法库；index.js 作为入口文件，用于对功能模块进行收敛和导出。真正“干活”的是功能模块本身，也就是下面这几个文件：

- `applyMiddleware.js` 是中间件模块，它的独立性较强
- `bindActionCreators.js` 用于将传入的 `actionCreator` 与 `dispatch` 方法相结合，揉成一个新的方法
- `combineReducers.js` 用于将多个  `reducer` 合并起来
- `compose.js` 用于把接收到的函数从右向左进行组合
- `createStore.js` `createStore` 方法是我们在使用 `Redux` 时最先调用的方法，它是整个流程的入口，也是 Redux 中最核心的 API。理解 `Redux` 实现原理，真正需要我们关注的模块其实只有一个——`createStore`

## 故事的开始：createStore

使用 Redux 的第一步，我们就需要调用 `createStore` 方法。单纯从使用感上来说，这个方法做的事情似乎就是创建一个 store 对象出来，像这样：

```js
// 引入 redux
import { createStore } from 'redux'
// 创建 store
const store = createStore(
    reducer,
    initial_state,
    applyMiddleware(middleware1, middleware2, ...)
);
```

> `createStore` 方法可以接收以下 3 个入参：

- `reducer`
- 初始状态内容
- 指定中间件

从拿到入参到返回出 store 的过程中，到底都发生了什么呢？这里我为你提取了 `createStore`中主体逻辑的源码（解析在注释里）

```js
function createStore(reducer, preloadedState, enhancer) {
    // 这里处理的是没有设定初始状态的情况，也就是第一个参数和第二个参数都传 function 的情况
    if (typeof preloadedState === 'function' && typeof enhancer === 'undefined') {
        // 此时第二个参数会被认为是 enhancer（中间件）
        enhancer = preloadedState;
        preloadedState = undefined;
    }
    // 当 enhancer 不为空时，便会将原来的 createStore 作为参数传入到 enhancer 中
    if (typeof enhancer !== 'undefined') {
        return enhancer(createStore)(reducer, preloadedState);
    }
    // 记录当前的 reducer，因为 replaceReducer 会修改 reducer 的内容
    let currentReducer = reducer;
    // 记录当前的 state
    let currentState = preloadedState;
    // 声明 listeners 数组，这个数组用于记录在 subscribe 中订阅的事件
    let currentListeners = [];
    // nextListeners 是 currentListeners 的快照
    let nextListeners = currentListeners;
    // 该变量用于记录当前是否正在进行 dispatch
    let isDispatching = false

    // 该方法用于确认快照是 currentListeners 的副本，而不是 currentListeners 本身
    function ensureCanMutateNextListeners() {
        if (nextListeners === currentListeners) {
            nextListeners = currentListeners.slice();
        }
    }

    // 我们通过调用 getState 来获取当前的状态
    function getState() {
        return currentState;
    }

    // subscribe 订阅方法，它将会定义 dispatch 最后执行的 listeners 数组的内容
    function subscribe(listener) {
        // 校验 listener 的类型
        if (typeof listener !== 'function') {
          throw new Error('Expected the listener to be a function.')
        }
        // 禁止在 reducer 中调用 subscribe
        if (isDispatching) {
          throw new Error(
            'You may not call store.subscribe() while the reducer is executing. ' +
              'If you would like to be notified after the store has been updated, subscribe from a ' +
              'component and invoke store.getState() in the callback to access the latest state. ' +
              'See https://redux.js.org/api-reference/store#subscribe(listener) for more details.'
          )
        }
        // 该变量用于防止调用多次 unsubscribe 函数
        let isSubscribed = true;
        // 确保 nextListeners 与 currentListeners 不指向同一个引用
        ensureCanMutateNextListeners(); 
        // 注册监听函数
        nextListeners.push(listener); 

        // 返回取消订阅当前 listener 的方法
        return function unsubscribe() {
            if (!isSubscribed) {
                return;
            }
            isSubscribed = false;
            ensureCanMutateNextListeners();
            const index = nextListeners.indexOf(listener);
            // 将当前的 listener 从 nextListeners 数组中删除 
            nextListeners.splice(index, 1);
        };
    }

    // 定义 dispatch 方法，用于派发 action 
    function dispatch(action) {
        // 校验 action 的数据格式是否合法
        if (!isPlainObject(action)) {
          throw new Error(
            'Actions must be plain objects. ' +
              'Use custom middleware for async actions.'
          )
        }

        // 约束 action 中必须有 type 属性作为 action 的唯一标识 
        if (typeof action.type === 'undefined') {
          throw new Error(
            'Actions may not have an undefined "type" property. ' +
              'Have you misspelled a constant?'
          )
        }

        // 若当前已经位于 dispatch 的流程中，则不允许再度发起 dispatch（禁止套娃）
        if (isDispatching) {
          throw new Error('Reducers may not dispatch actions.')
        }
        try {
          // 执行 reducer 前，先"上锁"，标记当前已经存在 dispatch 执行流程
          isDispatching = true
          // 调用 reducer，计算新的 state 
          currentState = currentReducer(currentState, action)
        } finally {
          // 执行结束后，把"锁"打开，允许再次进行 dispatch 
          isDispatching = false
        }

        // 触发订阅
        const listeners = (currentListeners = nextListeners);
        for (let i = 0; i < listeners.length; i++) {
            const listener = listeners[i];
            listener();
        }
        return action;
    }

    // replaceReducer 可以更改当前的 reducer
    function replaceReducer(nextReducer) {
        currentReducer = nextReducer;
        dispatch({ type: ActionTypes.REPLACE });
        return store;
    }

    // 初始化 state，当派发一个 type 为 ActionTypes.INIT 的 action，每个 reducer 都会返回
    // 它的初始值
    dispatch({ type: ActionTypes.INIT });

    // observable 方法可以忽略，它在 redux 内部使用，开发者一般不会直接接触
    function observable() {
      // observable 方法的实现
    }

    // 将定义的方法包裹在 store 对象里返回
    return {
      dispatch,
      subscribe,
      getState,
      replaceReducer,
      [$$observable]: observable
    }
}
```

通过阅读源码会发现，`createStore` 从外面看只是一个简单的创建动作，但在内部却别有洞天，涵盖了所有 Redux 主流程中核心方法的定义

接下来我将 createStore 内部逻辑总结进一张大图中，这张图涵盖了每个核心方法的工作内容，它将帮助你快速把握 createStore 的逻辑框架。

![](../../\imgs\interview-principle-react-redux-9.png)

在 `createStore` 导出的方法中，与 `Redux` 主流程强相关的，同时也是我们平时使用中最常打交道的几个方法，分别是

- `getState`
- `subscribe`
- `dispatch`

## Redux 工作流的核心：dispatch 动作

dispatch 应该是大家在使用 Redux 的过程中最为熟悉的 API 了。结合前面对设计思想的解读，我们已经知道，在 Redux 中有这样 3 个关键要素：

- `action`
- `reducer`
- `store`

之所以说 `dispatch` 是 `Redux` 工作流的核心，是因为`dispatch` 这个动作刚好能把 `action、reducer`和 `store` 这三位“主角”给串联起来。dispatch 的内部逻辑，足以反映了这三者之间“打配合”的过程。

```js
function dispatch(action) {
  // 校验 action 的数据格式是否合法
  if (!isPlainObject(action)) {
    throw new Error(
      'Actions must be plain objects. ' +
      'Use custom middleware for async actions.'
    )
  }
  // 约束 action 中必须有 type 属性作为 action 的唯一标识 
  if (typeof action.type === 'undefined') {
    throw new Error(
      'Actions may not have an undefined "type" property. ' +
      'Have you misspelled a constant?'
    )
  }
  // 若当前已经位于 dispatch 的流程中，则不允许再度发起 dispatch（禁止套娃）
  if (isDispatching) {
    throw new Error('Reducers may not dispatch actions.')
  }
  try {
    // 执行 reducer 前，先"上锁"，标记当前已经存在 dispatch 执行流程
    isDispatching = true
    // 调用 reducer，计算新的 state
    currentState = currentReducer(currentState, action)
  } finally {
    // 执行结束后，把"锁"打开，允许再次进行 dispatch
    isDispatching = false
  }
  // 触发订阅
  const listeners = (currentListeners = nextListeners);
  for (let i = 0; i < listeners.length; i++) {
    const listener = listeners[i];
    listener();
  }
  return action;
}
```

这里我结合源码，帮大家将 dispatch 的工作流程提取如下：

![](../../\imgs\interview-principle-react-redux-10.png)

**1. 通过“上锁”避免“套娃式”的 dispatch**

`dispatch` 工作流中最关键的就是执行 `reducer` 这一步，它对应的是下面这段代码：

```js
try {
  // 执行 reducer 前，先“上锁”，标记当前已经存在 dispatch 执行流程
  isDispatching = true
  // 调用 reducer，计算新的 state 
  currentState = currentReducer(currentState, action)
} finally {
  // 执行结束后，把"锁"打开，允许再次进行 dispatch 
  isDispatching = false
}
```

![](../../\imgs\interview-principle-react-redux-11.png)

> 在调用 reducer 之前，Redux 首先会将 isDispatching 变量置为 true，待 reducer 执行完毕后，再将 isDispatching 变量置为 false

这里之所以要用 `isDispatching 将 dispatch 的过程锁起来`，目的是规避“套娃式”的 dispatch。更准确地说，是`为了避免开发者在 reducer 中手动调用 dispatch`。

若真的在 reducer 中调用 dispatch，那么 dispatch 又会反过来调用 reducer，reducer 又会再次调用 dispatch......这样反复相互调用下去，就会进入死循环，属于非常严重的误操作。

因此，在 dispatch 的前置校验逻辑中，一旦识别出 isDispatching 为 true，就会直接 throw Error（见下面代码），把死循环扼杀在摇篮里：

```js
if (isDispatching) {
  throw new Error('Reducers may not dispatch actions.')
}
```

**2. 触发订阅的过程**

在 reducer 执行完毕后，会进入触发订阅的过程，它对应的是下面这段代码：

```js
// 触发订阅
const listeners = (currentListeners = nextListeners);
for (let i = 0; i < listeners.length; i++) {
  const listener = listeners[i];
  listener();
}
```

## Redux 中的“发布-订阅”模式：认识 subscribe

> dispatch 中执行的 listeners 数组从订阅中来，而执行订阅需要调用 `subscribe`。在实际的开发中，`subscribe` 并不是一个严格必要的方法，只有在需要监听状态的变化时，我们才会调用 `subscribe`

subscribe 接收一个 Function 类型的 listener 作为入参，它的返回内容恰恰就是这个 listener 对应的解绑函数。你可以通过下面这段示例代码简单把握一下 subscribe 的使用姿势：

```js
function handleChange() {
  // 函数逻辑
}
const unsubscribe = store.subscribe(handleChange)
unsubscribe()
```

subscribe 在订阅时只需要传入监听函数，而不需要传入事件类型。这是因为 Redux 中已经默认了订阅的对象就是“状态的变化（准确地说是 dispatch 函数的调用）”这个事件。

> subscribe 是如何与 Redux 主流程结合的呢？首先，我们可以在 store 对象创建成功后，`通过调用 store.subscribe 来注册监听函数，也可以通过调用 subscribe 的返回函数来解绑监听函数`，监听函数是用 listeners 数组来维护的；`当dispatch action 发生时，Redux 会在 reducer 执行完毕后，将 listeners 数组中的监听函数逐个执行`。这就是 subscribe 与 Redux 主流程之间的关系。

接下来我们结合源码来分析一下 subscribe 的内部逻辑，subscribe 源码提取如下：

```js
function subscribe(listener) {
  // 校验 listener 的类型
  if (typeof listener !== 'function') {
    throw new Error('Expected the listener to be a function.')
  }
  // 禁止在 reducer 中调用 subscribe
  if (isDispatching) {
    throw new Error(
      'You may not call store.subscribe() while the reducer is executing. ' +
      'If you would like to be notified after the store has been updated, subscribe from a ' +
      'component and invoke store.getState() in the callback to access the latest state. ' +
      'See https://redux.js.org/api-reference/store#subscribe(listener) for more details.'
    )
  }
  // 该变量用于防止调用多次 unsubscribe 函数
  let isSubscribed = true;
  // 确保 nextListeners 与 currentListeners 不指向同一个引用
  ensureCanMutateNextListeners(); 
  // 注册监听函数
  nextListeners.push(listener); 
  // 返回取消订阅当前 listener 的方法
  return function unsubscribe() {
    if (!isSubscribed) {
      return;
    }
    isSubscribed = false;
    ensureCanMutateNextListeners();
    const index = nextListeners.indexOf(listener);
    // 将当前的 listener 从 nextListeners 数组中删除 
    nextListeners.splice(index, 1);
  };
}
```

结合这段源码，我们可以将 subscribe 的工作流程提取如下：

![](../../\imgs\interview-principle-react-redux-12.png)

### 1. 订阅过程中的 listeners 数组

两个 `listeners` 之间的第一次“交锋”发生在 `createStore` 的变量初始化阶段，`nextListeners` 会被赋值为 `currentListeners`（见下面代码），这之后两者确实指向同一个引用。

```js
 let nextListeners = currentListeners
```

但在 subscribe 第一次被调用时，`ensureCanMutateNextListeners` 就会发现这一点，然后将 `nextListeners` 纠正为一个内容与 currentListeners 一致、但引用不同的新对象。对应的逻辑如下面代码所示：

```js
function ensureCanMutateNextListeners() {
  // 若两个数组指向同一个引用
  if (nextListeners === currentListeners) {
    // 则将 nextListeners 纠正为一个内容与 currentListeners 一致、但引用不同的新对象
    nextListeners = currentListeners.slice()
  }
}
```

在 subscribe 的逻辑中，ensureCanMutateNextListeners 每次都会在 listener 注册前被无条件调用，用以确保两个数组引用不同。紧跟在 ensureCanMutateNextListeners 之后执行的是 listener 的注册逻辑，我们可以对应源码中看到 listener 最终会被注册到 nextListeners 数组中去：

```js
nextListeners.push(listener);
```

### 2. 发布过程中的 listeners 数组

触发订阅这个动作是由 dispatch 来做的，相关的源码如下：

```js
// 触发订阅
const listeners = (currentListeners = nextListeners);
for (let i = 0; i < listeners.length; i++) {
  const listener = listeners[i];
  listener();
}
```

这段源码告诉我们，在触发订阅的过程中，currentListeners 会被赋值为 nextListeners，而实际被执行的 listeners 数组又会被赋值为 currentListeners。因此，最终被执行的 listeners 数组，实际上和当前的 nextListeners 指向同一个引用。

这就有点奇妙了：注册监听也是操作 nextListeners，触发订阅也是读取 nextListeners（实际上，细心的同学会注意到，取消监听操作的也是 nextListeners 数组）。既然如此，要 currentListeners 有何用？

### 3. currentListeners 数组用于确保监听函数执行过程的稳定性

正因为任何变更都是在 nextListeners 上发生的，我们才需要一个不会被变更的、内容稳定的 currentListeners ，来确保监听函数在执行过程中不会出幺蛾子。

举个例子，下面这种操作在 Redux 中完全是合法的：

```js
// 定义监听函数 A
function listenerA() {
}
// 订阅 A，并获取 A 的解绑函数
const unSubscribeA = store.subscribe(listenerA)
// 定义监听函数 B
function listenerB() {
  // 在 B 中解绑 A
  unSubscribeA()
}
// 定义监听函数 C
function listenerC() {
}
// 订阅 B
store.subscribe(listenerB)
// 订阅 C
store.subscribe(listenerC)
```

在这个 Demo 执行完毕后，nextListeners 数组的内容是 A、B、C 3 个 listener：

```js
[listenerA,  listenerB, listenerC]
```

接下来若调用 dispatch，则会执行下面这段触发订阅的逻辑：

```js
// 触发订阅
const listeners = (currentListeners = nextListeners);
for (let i = 0; i < listeners.length; i++) {
  const listener = listeners[i];
  listener();
}
```

当 for 循环执行到索引 i = 1 处，也就是对应的 listener 为 listenerB 时，问题就会出现：listenerB 中执行了 unSubscribeA 这个动作。而结合我们前面的分析，监听函数注册、解绑、触发这些动作实际影响的都是 nextListeners。为了强化对这一点的认知，我们来复习一下 unsubscribe 的源码：

```js
return function unsubscribe() {
  // 避免多次解绑
  if (!isSubscribed) {
    return;
  }
  isSubscribed = false;
  // 熟悉的操作，调用 ensureCanMutateNextListeners 方法
  ensureCanMutateNextListeners();
  // 获取 listener 在 nextListeners 中的索引
  const index = nextListeners.indexOf(listener);
  // 将当前的 listener 从 nextListeners 数组中删除 
  nextListeners.splice(index, 1);
};
```

假如说不存在 currentListeners，那么也就意味着不需要 ensureCanMutateNextListeners 这个动作。若没有 ensureCanMutateNextListeners，unsubscribeA() 执行完之后，listenerA 会同时从 listeners 数组和 nextListeners 数组中消失（因为两者指向的是同一个引用），那么 listeners 数组此时只剩下两个元素 listenerB 和 listenerC，变成这样：

```js
[listenerB, listenerC]
```

listeners 数组的长度改变了，但 for 循环却不会感知这一点，它将无情地继续循环下去。之前执行到 i = 1 处，listener = listeners[1] ，也就是说 listener === listenerB；下一步理应执行到 i = 2 处，但此时 listeners[2] 已经是 undefined 了，原本应该出现在这个索引位上的 listenerC，此时因为数组长度的变化，被前置到了 i = 1 处！这样一来，undefined 就会代替 listenerC 被执行，进而引发函数异常。

这可怎么办呢？答案当然是将 nextListeners 与当前正在执行中的 listeners 剥离开来，将两者指向不同的引用。这也正是 ensureCanMutateNextListeners 所做的事情。

在示例的这种场景下，ensureCanMutateNextListeners 执行前，listeners、currentListeners 和 nextListeners 之间的关系是这样的：

```js
listeners === currentListeners === nextListeners
```

而 ensureCanMutateNextListeners 执行后，nextListeners 就会被剥离出去：

```js
nextListeners = currentListeners.slice()
listeners === currentListeners !== nextListeners
```

这样一来，nextListeners 上的任何改变，都无法再影响正在执行中的 listeners 了。currentListeners 在此处的作用，就是为了记录下当前正在工作中的 listeners 数组的引用，将它与可能发生改变的 nextListeners 区分开来，以确保监听函数在执行过程中的稳定性。
