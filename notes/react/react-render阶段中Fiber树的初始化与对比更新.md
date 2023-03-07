# Render阶段中Fiber树的初始化与对比更新

## 前言

下面这个点击`button`，网页应该变成什么样？ 注意他们的`key`是相同的

```js
import React, { useState } from "react";

function Demo2() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((i) => i + 1)}>点击Count+1</button>
      <h3 key={count}>大{count}</h3>
      <h2 key={count}>舌{count}</h2>
      <h1 key={count}>头{count}</h1>
    </div>
  );
}

export default Demo2;
```

![](../../\images\react-init-update-1-1.gif)

## 前置概念

react框架可以用来表示，输入状态 —> 吐出ui。

```js
const ui = fn(state)
```

### react架构是什么？

可以分为如下三层：

- scheduler（调度器）：用来分发优先级更高的任务。

- **render阶段（协调器）**：找出哪些节点发生了变化，并且给相应的fiber打上标签。

- commit阶段（渲染器）：将打好标签的节点渲染到视图上。遍历effectList执行对应的dom操作或部分生命周期

![](../../\images\react-init-update-1.jpg)

- 输入: 将每一次更新(如: 新增, 删除, 修改节点之后)视为一次更新需求(目的是要更新DOM节点).

- 注册调度任务: react-reconciler收到更新需求之后, 并不会立即构造fiber树, 而是去调度中心scheduler注册一个新任务task, 即把更新需求转换成一个task.

- 执行调度任务(输出): 调度中心scheduler通过任务调度循环来执行task
  
  - fiber构造循环是task的实现环节之一, 循环完成之后会构造出最新的 fiber 树.
  
  - commitRoot是task的实现环节之二, 把最新的 fiber 树最终渲染到页面上, task完成.

主干逻辑就是输入到输出这一条链路, 为了更好的性能(如批量更新, 可中断渲染等功能), react在输入到输出的链路上做了很多优化策略, 任务调度循环和fiber构造循环相互配合就可以实现可中断渲染。

![](../../\images\react-init-update-2.jpg)

### ReactElement, Fiber, DOM 三者的关系

上面我们大概提及了一下react的架构和更新的粗略流程，考虑到本文的重点是Render阶段发生了啥，接下来上重量级嘉宾JSX,ReactElement, Fiber, DOM。以下面这个jsx代码为例，讲解三者的关系。

```js
function Test() {
  const [showName, setShowName] = useState(true);

  return (
    <div>
      <div>今天肯德基疯狂星期八，和我一起玩彩虹六？</div>
      <ul>
        <li>抱枕一号</li>
        {showName && <li>抱枕二号</li>}
      </ul>
      <div
        onClick={() => {
          setShowName(false);
        }}
      >
        点击让高启强少一个小弟
      </div>
    </div>
  );
}
```

`createElement源码`

所有采用`JSX`语法书写的节点, 都会被编译器转换, 最终会以`React.createElement(...)`的方式, 创建出来一个与之对应的`ReactElement`对象.

这也是为什么在每个使用`JSX`的JS文件中，你必须显式的声明 `import React from 'react';`(17版本后不需要）否则在运行时该模块内就会报未定义变量 React的错误。

#### ReactElement数据结构和内存结构（结合上面jsx示例代码）

##### 数据结构

```ts
export type ReactElement = {
  // 用于辨别ReactElement对象形式
  $$typeof: any,

  // 内部属性
  type: any, // 表明其种类
  key: any,
  ref: any,
  props: any,

  // ReactFiber 记录创建本对象的Fiber节点, 还未与Fiber树关联之前, 该属性为null
  _owner: any,

  // __DEV__ dev环境下的一些额外信息, 如文件路径, 文件名, 行列信息等
  _store: {validated: boolean, ...},
  _self: React$Element<any>,
  _shadowChildren: any,
  _source: Source,
};
```

##### 内存结构

![](../../\images\react-init-update-3.jpg)

#### Fiber 对象数据结构

##### 数据结构

```ts
export type Fiber = {|
  tag: WorkTag,
  key: null | string, // 和ReactElement组件的 key 一致.
  elementType: any,//一般来讲和ReactElement组件的 type 一致 比如div ul
  type: any, // 一般来讲和fiber.elementType一致. 一些特殊情形下, 比如在开发环境下为了兼容热更新
  stateNode: any, // 真实DOM是谁
  return: Fiber | null, // 爹是谁
  child: Fiber | null, // 孩子是谁
  sibling: Fiber | null, // 兄弟是谁
  index: number, 
  ref:
    | null
    | (((handle: mixed) => void) & { _stringRef: ?string, ... })
    | RefObject, // 指向在ReactElement组件上设置的 ref
  pendingProps: any, // 从`ReactElement`对象传入的 props. 用于和`fiber.memoizedProps`比较可以得出属性是否变动
  memoizedProps: any, // 上一次生成子节点时用到的属性, 生成子节点之后保持在内存中
  updateQueue: mixed, // 存储state更新的队列, 当前节点的state改动之后, 都会创建一个update对象添加到这个队列中.
  memoizedState: any, // 用于输出的state, 最终渲染所使用的state
  dependencies: Dependencies | null, // 该fiber节点所依赖的(contexts, events)等
  mode: TypeOfMode, // 二进制位Bitfield,继承至父节点,影响本fiber节点及其子树中所有节点. 与react应用的运行模式有关(有ConcurrentMode, BlockingMode, NoMode等选项).

  // 优先级相关
  lanes: Lanes, // 本fiber节点的优先级
  childLanes: Lanes, // 子节点的优先级
  alternate: Fiber | null, // 双fiber缓存 指向内存中的另一个fiber, 每个被更新过fiber节点在内存中都是成对出现(current和workInProgress)
|};
```

##### 内存结构

![](../../\images\react-init-update-4.jpg)

#### ReactElement, Fiber, DOM 三者的关系

![](../../\images\react-init-update-5.jpg)

### React的启动过程发生了啥

接下来介绍的都是当前稳定版`legacy` 模式

```js
ReactDOM.render(<App />, document.getElementById('root'), dom => {});
```

在没有进入`render`阶段（`react-reconciler`包）之前,`reactElement(<App/>)`和 DOM 对象`div#root`之间没有关联。

![](../../\images\react-init-update-6.jpg)

在react初始化的时候，会创建三个全局对象，在三个对象创建完毕的时候，react初始化完毕。

- `ReactDOMRoot对象`
  
  -  属于`react-dom`包，该对象暴露有render,unmount方法, 通过调用该实例的`ReactDOM.render`方法, 可以引导 react 应用的启动.

- `fiberRoot对象`
  
  -  属于`react-reconciler`包,在运行过程中的全局上下文, 保存 fiber 构建过程中所依赖的全局状态，
  
  -  其大部分实例变量用来存储`fiber构造循环`过程的各种状态，react 应用内部, 可以根据这些实例变量的值, 控制执行逻辑。

- `HostRootFiber对象`
  
  - 属于`react-reconciler`包，这是 react 应用中的第一个 Fiber 对象, 是 Fiber 树的根节点, 节点的类型是`HostRoot`.

这 3 个对象是 react 体系得以运行的基本保障, 除非卸载整个应用，否则不会再销毁。

![](../../\images\react-init-update-7.jpg)

此刻内存中各个对象的引用情况表示出来，此时`reactElement(<App/>)`还是独立在外的, 还没有和目前创建的 3 个全局对象关联起来

![](../../\images\react-init-update-8.jpg)

到此为止, `react`内部经过一系列运转, 完成了初始化。

## render阶段发生了啥

**以下所有示例按照下面的代码 请注意**

```js
class App extends React.Component {
    state = {
        list: ['A', 'B', 'C'],
    };

    onChange = () => {
        this.setState({ list: ['C', 'A', 'X'] });
    };

    componentDidMount() {
        console.log(`App Mount`);
    }

    render() {
        return (
            <>
                <Header key='d' />

                <button key='e'>change</button>
                <div className="content" key='f'>
                    {
                      this.state.list.map(item => (
                        <p key={item}>{item}</p>
                      ))
                    }
                </div>
            </>
        );
    }
}

class Header extends React.PureComponent {
    render() {
        return (
            <>
                <h1>title</h1>
                <h2>title2</h2>
            </>
        );
    }
}
```

#### 双缓冲fiber技术

在上文我们梳理了`ReactElement, Fiber, DOM三者的关系`, `fiber树`的构造过程, 就是把`ReactElement`转换成`fiber树`的过程. 但是在这个过程中, 内存里会同时存在 2 棵`fiber树`:

- 其一: 代表当前界面的`fiber`树(已经被展示出来, 挂载到`fiberRoot.current`上). 如果是初次构造(`初始化渲染`), 页面还没有渲染, 此时界面对应的 fiber 树为空(`fiberRoot.current = null`).

- 其二: 正在构造的`fiber`树(即将展示出来, 挂载到`HostRootFiber.alternate`上, 正在构造的节点称为`workInProgress`). 当构造完成之后, 重新渲染页面, 最后切换`fiberRoot.current = workInProgress`, 使得`fiberRoot.current`重新指向代表当前界面的`fiber`树.

#### React入口初始化内存情况

在进入`react-reconciler`包之前,也就是还没`render`时, 内存状态图如下，和上面启动过程的图对应:

![](../../\images\react-init-update-9.jpg)

#### fiber 树构造方式

- 初次创建: 在`React`应用首次启动时, 界面还没有渲染, 此时并不会进入对比过程, 相当于直接构造一棵全新的树.

- 对比更新: `React`应用启动后, 界面已经渲染. 如果再次发生更新, 创建`新fiber`之前需要和`旧fiber`进行对比. 最后构造的 fiber 树有可能是全新的, 也可能是部分更新的.

在深度优先遍历中, **每个**`fiber`节点都会经历 2 个阶段:

- 探寻阶段 `beginWork`

- 回溯阶段 `completeWork`

#### `beginWork`探寻阶段发生了什么[源码地址](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberBeginWork.old.js#L3083-L3494)

- 创建节点：根据 `ReactElement`对象创建所有的`fiber`节点, 最终构造出fiber树形结构(设置`return`和`sibling`指针)

- 给节点打标签：设置`fiber.flags`(二进制形式变量, 用来标记 fiber节点 的增,删,改状态, 等待`completeWork`阶段处理)

- 设置真实DOM的局部状态：设置`fiber.stateNode`局部状态(如Class类型节点: `fiber.stateNode=new Class()`)

#### `completeWork`回溯阶段发生了什么[源码地址](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L1670-L1802)

- 调用`completeWork`
  
  - 给`fiber`节点(tag=HostComponent, HostText)创建 DOM 实例, 设置`fiber.stateNode`局部状态(如`tag=HostComponent, HostText`节点: fiber.stateNode 指向这个 DOM 实例).
  
  - 为 DOM 节点设置属性, 绑定事件(`合成事件原理`).
  
  - 设置`fiber.flags`标记

- 把当前 `fiber` 对象的副作用队列(`firstEffect`和`lastEffect`)添加到父节点的副作用队列之后, 更新父节点的`firstEffect`和`lastEffect`指针.

- 识别`beginWork`阶段设置的`fiber.flags`, 判断当前 `fiber` 是否有副作用(增,删,改), 如果有, 需要将当前 `fiber` 加入到父节点的`effects`队列, 等待`commit`阶段处理.

#### 初次创建

这有一个动画 [具体如果想看流程图可以点击](https://yzcoyzhsws.feishu.cn/docx/IB0gdVMjAo1TDdxLDXwcx7oan9c)

下面标注了生成时期的 `beginWork` 和 `completeWork` 执行过程

```js
  // 将最新的fiber树挂载到root.finishedWork节点上 下面绿色粗线表示指针
  const finishedWork: Fiber = (root.current.alternate: any);
  root.finishedWork = finishedWork;
  root.finishedLanes = lanes;
  // 进入commit阶段
  commitRoot(root);
```

动画演示了初次创建`fiber树`的全部过程, 跟踪了创建过程中内存引用的变化情况. `fiber树构造循环`负责构造新的`fiber`树, 构造过程中同时标记`fiber.flags`, 最终把所有被标记的`fiber`节点收集到一个副作用队列中, 这个副作用队列被挂载到根节点上(`HostRootFiber.alternate.firstEffect`). 此时的`fiber树`和与之对应的`DOM节点`都还在内存当中, 等待`commitRoot`阶段进行渲染

![](../../\images\react-init-update-11.jpg)

#### 对比更新的时候发生了什么

###### 优化原则

- 只对**同级节点**进行对比，如果DOM节点跨层级移动，则react不会复用
  
  - 我们可以从同级的节点数量将Diff分为两类：
    
    - 当newChild类型为JSX对象、number、string，代表同级只有一个节点
    - 当newChild类型为Array，同级有多个节点

- 不同类型的元素会产出不同的结构，会销毁老的结构，创建新的结构

- 可以通过key标示移动的元素

- 类型一致的节点才有继续diff的必要性
  
  - `单节点`对应演示,可以去浏览器的`Elements`->`Properties`查看
    
    ![](../../\images\react-init-update-12.jpg)
  
  - `多节点`对应演示
    
    ![](../../\images\react-init-update-13.jpg)

##### diff算法介绍

**单节点**

- 如果是新增节点, 直接新建 fiber, 没有多余的逻辑

- 如果是对比更新
  
  - 如果`key`和`type`都相同，则复用
  
  - 否则新建

单节点的逻辑比较简明, [源码](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactChildFiber.old.js#L1135-L1233)

**多节点**

- 多节点一般会存在两轮遍历，第一轮寻找公共序列，第二轮遍历剩余非公共序列

- **第一次循环** [源码](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react-reconciler/src/ReactChildFiber.new.js#L818)
  
  - `let i = 0`，遍历`newChildren`，将`newChildren[i]`与`oldFiber`比较，判断`DOM节点`是否可复用。
  
  - 如果可复用，`i++`，继续比较`newChildren[i]`与`oldFiber.sibling`，可以复用则继续遍历。
  
  - 如果不可复用，分两种情况：
    
    - `key`不同导致不可复用，立即跳出整个遍历，**第一轮遍历结束。**
    
    - `key`相同`type`不同导致不可复用，会将`oldFiber`标记为`DELETION`，并继续遍历
  
  - 如果`newChildren`遍历完（即`i === newChildren.length - 1`）或者`oldFiber`遍历完（即`oldFiber.sibling === null`），跳出遍历，**第一轮遍历结束。**
  
  ![](../../\images\react-init-update-14.jpg)
  
  ![](../../\images\react-init-update-15.jpg)

- **第二次循环**: 遍历剩余`非公共`序列, 优先复用 oldFiber 序列中的节点。
  
  - 如果`newChildren`与`oldFiber`同时遍历完，diff结束
  
  - 如果 `newChildren`没遍历完，`oldFiber`遍历完，意味着没有可以复用的节点了，遍历剩下的`newChildren`为生成的`workInProgress fiber`依次标记`Placement`。
  
  - 如果`newChildren`遍历完，`oldFiber`没遍历完，意味着有节点被删除了，需要遍历剩下的`oldFiber`，依次标记`Deletion`。
  
  - 如果`newChildren`与`oldFiber`都没遍历完 `(重点)`[源码](https://github.com/facebook/react/blob/1fb18e22ae66fdb1dc127347e169e73948778e5a/packages/react-reconciler/src/ReactChildFiber.new.js#L893)
    
    - 先去`声明map数据结构`，遍历一遍老节点，把老fiber的key做映射 {元素的key：老的fiber节点}，
    - 继续遍历新`jsx`，如果`map`有`key`，会把`key`从`map`中删除，说明可以复用，把当前节点标记为`更新`。新地位高的不动，新地位低的动（中间插入链表比链表屁股插入费劲）所以地位低的动动。
    - `lastPlaceIndex`指针，指向最后一个不需要动的老节点的`key`。每次新jsx复用到节点，`lastPlaceIndex`会指向老节点的最后一个成功复用的老`fiber`节点。如果新复用的节点key小于`lastPlaceIndex`，说明老`fiber`节点的顺序在新`jsx`之前，需要挪动位置接到新`jsx`节点后面。
    - 如果`jsx`没有复用的老`fiber`，直接插入新的
    - `map`中只剩还没被复用的节点，等着新的`jsx`数组遍历完，`map`里面的`fiber`节点全部设置为删除
  
  ![](../../\images\react-init-update-16.jpg)
  
  **下面动画展示了fiber的对比更新过程** [每一张流程图链接](https://yzcoyzhsws.feishu.cn/docx/NLDDdzRPLo9qU8x7ek2clIN6nWc)
  
  ![](../../\images\react-init-update-19.jpg)

## 检验学习成果

为什么网页会变成那个样子？

```js
import React, { useState } from "react";

function Demo2() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((i) => i + 1)}>点击Count+1</button>
      <h3 key={count}>大{count}</h3>
      <h2 key={count}>舌{count}</h2>
      <h1 key={count}>头{count}</h1>
    </div>
  );
}

export default Demo2;
```

![](../../\images\react-init-update-20.jpg)

![](../../\images\react-init-update-21.jpg)

![](../../\images\react-init-update-22.jpg)

![](../../\images\react-init-update-23.jpg)

## 参考

- 7km：[图解React](https://7kms.github.io/react-illustration-series/)

- 冴羽：[React 之 createElement 源码解读](https://juejin.cn/post/7160981608885927972)

- 卡颂：[React技术揭秘](https://react.iamkasong.com/preparation/idea.html)

- [人人都能读懂的react源码解析](https://xiaochen1024.com/article_item/600ac4384bf83f002edaf54a)
