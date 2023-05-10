# react-hooks-源码数据结构

> react v18.2

## 数据结构

### Fiber

```ts
export type Fiber = {
  // dom节点的相关信息
  // 当前节点的类型，如 FunctionComponent, ClassComponent 等
  tag: WorkTag,
  /**
   * 唯一值
   * 
   * 这个字段和 react element 的 key 的含义和内容有一样（因为这个 key 是
   * 从 react element 的key 那里直接拷贝赋值过来的），作为 children 列表
   * 中每一个 item 的唯一标识。它被用于帮助 React 去计算出哪个 item 被修改了，
   * 哪个 item 是新增的，哪个 item 被删除了。
   */
  key: null | string,
  /**
   * 元素类型
   * 
   * fiber 中的 elmentType 与 element 中的type一样
   * this.elementType = element.type
   */
  elementType: any,

  /**
   * 判定fiber节点的类型，用于diff
   * 
   * 当前fiber节点的元素类型，与React Element里的type类型一样，若是原生的html标签，
   * 则 type 为该标签的类型（'div', 'span' 等）；若是自定义的Class Component或
   * Function Component等，则该type的值就是该class或function，后续会按照上面的tag字段，
   * 来决定是用new初始化一个实例（当前是 Class Component），然后执行该class内
   * 的render()方法；还是执行该type（当前是 Function Component），得到其返回值；
   */
  type: any,

  /**
   * 真实 dom 节点
   * 
   * 1. 若当前fiber节点是dom元素，则对应的是真实DOM元素；
   * 2. 若当前是function component，则值为null；
   * 3. 若当前是class component，则值为class初始化出来的实例；
   * 4. 若当前是 host component，即树的根节点，stateNode为 FiberRootNode；
   */
  stateNode: any,

  /**
   * fiber 链表树
   * 
   * 下面的return, child和sibling都是指针，用来指向到其他的fiber节点，
   * React会将jsx编译成的element结构，转为以fiber为节点的链表结构，
   * return: 指向到父级fiber节点；
   * child: 指向到该节点的第1个子节点；
   * sibling: 指向到该节点的下一个兄弟节点；
   * 如图所示：https://www.xiabingbao.com/upload/386262ff06785779c.jpg
   */
  return: Fiber | null,
  child: Fiber | null,
  sibling: Fiber | null,
  // 在父 fiber 下面的子 fiber 中的下标
  index: number,

  // The ref last used to attach this node.
  // I'll avoid adding an owner field for prod and model that as functions.
  ref:
    | null
    | (((handle: mixed) => void) & {_stringRef: ?string, ...})
    | RefObject,

  /**
   * 计算 state 和 props 渲染
   */
  // 本次渲染需要使用的 props
  pendingProps: any,
  // 上次渲染使用的 props
  memoizedProps: any,
  // 用于状态更新、回调函数、DOM更新的队列
  updateQueue: mixed,
  // 上次渲染后的 state 状态
  memoizedState: any,
  // contexts、events 等依赖
  dependencies: Dependencies | null,

  // Bitfield that describes properties about the fiber and its subtree. E.g.
  // the ConcurrentMode flag indicates whether the subtree should be async-by-
  // default. When a fiber is created, it inherits the mode of its
  // parent. Additional flags can be set at creation time, but after that the
  // value should remain unchanged throughout the fiber's lifetime, particularly
  // before its child fibers are created.
  mode: TypeOfMode,

  // Effects
  // 该节点更新的优先级，若为NoFlags时，则表示不更新
  flags: Flags,
  // 子节点的更新情况，若为NoFlags，则表示其子节点不更新，在diff时可以直接跳过
  subtreeFlags: Flags,
  // 子节点中需要删除的节点
  deletions: Array<Fiber> | null,
  // 下一个有副作用的 fiber
  nextEffect: Fiber | null,
  // 指向第一个有副作用的 fiber
  firstEffect: Fiber | null,
  // 指向最后一个有副作用的 fiber 
  lastEffect: Fiber | null,

  // 渲染优先级
  lanes: Lanes,
  childLanes: Lanes,

  /**
   * 指向 workInProgress fiber 树中对应的节点
   * 
   * 双缓冲：防止数据丢失，提高效率（之后Dom-diff的时候可以直接比较或者使用
   * React在进行diff更新时，会维护两颗fiber树，一个是当前正在展示的，一个是
   * 通过diff对比后要更新的树，这两棵树中的每个fiber节点通过 alternate 属性
   * 进行互相指向。
   */
  alternate: Fiber | null,

  // Time spent rendering this Fiber and its descendants for the current update.
  // This tells us how well the tree makes use of sCU for memoization.
  // It is reset to 0 each time we render and only updated when we don't bailout.
  // This field is only set when the enableProfilerTimer flag is enabled.
  actualDuration?: number,
  // If the Fiber is currently active in the "render" phase,
  // This marks the time at which the work began.
  // This field is only set when the enableProfilerTimer flag is enabled.
  actualStartTime?: number,
  // Duration of the most recent render time for this Fiber.
  // This value is not updated when we bailout for memoization purposes.
  // This field is only set when the enableProfilerTimer flag is enabled.
  selfBaseDuration?: number,
  // Sum of base times for all descendants of this Fiber.
  // This value bubbles up during the "complete" phase.
  // This field is only set when the enableProfilerTimer flag is enabled.
  treeBaseDuration?: number,
}
```

### Hook

```ts
export type Hook = {
  // 该hook上次运行的状态
  memoizedState: any,

  // 该hook本次运行的状态
  baseState: any,

  // 由于之前某些高优先级任务导致更新中断，baseQueue 记录的就是尚未处理的最后一个 update
  baseQueue: Update<any, any> | null,

  // 内部存储调用 setValue 产生的 update 更新信息，是个环状单向链表
  queue: any,

  // 下一个hook
  next: Hook | null,
};
```

### UpdateQueue

```ts
export type UpdateQueue<State> = {
  // 本次更新前该Fiber节点的state，此后的计算是基于该state计算更新后的state
  baseState: State, 

  // 上次渲染时遗留下来的低优先级任务会组成一个链表，该字段指向到该链表的头节点
  firstBaseUpdate: Update<State> | null, 

  // 该字段指向到该链表的尾节点
  lastBaseUpdate: Update<State> | null, 

  // 本次渲染时要执行的任务，会存放在shared.pending中，这里是环形链表，更新时，会将其拆开，链接到 lastBaseUpdate 的后面
  shared: SharedQueue<State>, 

  // 存放 update.callback 不为null的update
  callbacks: Array<() => mixed> | null,
};
```

### Effect

```ts
export type Effect = {
  // effect的类型，区分是 useEffect 还是 useLayoutEffect
  tag: HookFlags,

  // 传入use（Layout）Effect函数的第一个参数，即回调函数
  create: () => (() => void) | void,

  // 销毁函数
  destroy: (() => void) | void,

  // 依赖项
  deps: Array<mixed> | void | null,

  // 下一个 effect
  next: Effect,
};
```

## mountWorkInProgressHook

```js
function mountWorkInProgressHook() {
    // 创建一个hooks对象
    const hook  = { 
        // useState中保存state信息，
        // useEffect中保存Effect对象，
        // useMemo中保存缓存的值和依赖；
        // useRef保存的是ref对象
        memoizedState: null,

        // useState和useReducer中保存最新的state 
        baseState: null,

        // useState和useReducer中保存最新的更新队列 
        baseQueue: null,

        // 自己的更新队列，形成环状链表
        queue: null,

        // 下一个更新，就是我们下的页面中下一个hooks 
        next: null, 
    };

    if (workInProgressHook === null) {
        // 说明这是我们的第一个hook
        currentlyRenderingFiber.memoizedState = workInProgressHook = hook;
    } else {
        // 说明函数组件中不止一个hooks
        workInProgressHook = workInProgressHook.next = hook;
    }

    return workInProgressHook;
}
```

> 构建hooks单向链表，将组件中的hooks函数以链表的形式串连起来，并赋值给workInProgress的memoizedState。

## useState

### 挂载阶段（状态初始化）

#### mountState

```js
function mountState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {
  // 返回当前正在运行的hook对象,构建hook单项链表
  const hook = mountWorkInProgressHook();

  /*
    初始值如果是函数，就执行函数拿到初始值
    useState((preState)=> return '初始值')
  */
  if (typeof initialState === 'function') {
    // $FlowFixMe: Flow doesn't like mixed types
    initialState = initialState();
  }

  // 把初始值赋值给 hook.baseState和hook.memoizedState
  hook.memoizedState = hook.baseState = initialState;

  // 定义一个队列
  const queue: UpdateQueue<S, BasicStateAction<S>> = {
    // 存放update对象
    pending: null,
    // 优先级
    lanes: NoLanes,
    // 放 hooks 更新函数
    dispatch: null,
    // 它是一个函数， 用于得到最新的 state
    lastRenderedReducer: basicStateReducer,
    // 最后一次得到的 state
    lastRenderedState: (initialState: any),
  };

  // 把对列放到hook对象上
  hook.queue = queue;

  /*  
    dispatchSetState 是负责更新的函数,就是代表下面的setState函数
    const [state,setState]=useState()
  */
  // 更新 state 的方法
  const dispatch: Dispatch<BasicStateAction<S>> = (queue.dispatch =
    (dispatchSetState.bind(null, currentlyRenderingFiber, queue): any));

  // 返回我们经常用的 [state, setState]
  return [hook.memoizedState, dispatch];
}
```

> mountState主要做的事情：
> 
> - 创建hook对象，在上面存上hooks信息，下次更新的时候可以从对象上获取。
> 
> - 返回一个数组，包括初始化的值和更新函数

#### dispatchSetState

```js
function dispatchSetState<S, A>(
  fiber: Fiber,
  queue: UpdateQueue<S, A>,
  action: A,
) {
  const lane = requestUpdateLane(fiber);

  // 创建一个 update 更新对象
  const update: Update<S, A> = {
    lane,
    action,
    hasEagerState: false,
    eagerState: null,
    next: (null: any),
  };

  // 渲染阶段更新，先不讨论这种特殊情况
  if (isRenderPhaseUpdate(fiber)) {
    enqueueRenderPhaseUpdate(queue, update);
  } else {
    const alternate = fiber.alternate;

    // 第二次 setState 时，fiber.lanes 为 SyncLane
    if (
      fiber.lanes === NoLanes &&
      (alternate === null || alternate.lanes === NoLanes)
    ) {
      // The queue is currently empty, which means we can eagerly compute the
      // next state before entering the render phase. If the new state is the
      // same as the current state, we may be able to bail out entirely.
      const lastRenderedReducer = queue.lastRenderedReducer;

      if (lastRenderedReducer !== null) {
        let prevDispatcher;

        try {
          const currentState: S = (queue.lastRenderedState: any);

          // 计算新状态
          const eagerState = lastRenderedReducer(currentState, action);
          // Stash the eagerly computed state, and the reducer used to compute
          // it, on the update object. If the reducer hasn't changed by the
          // time we enter the render phase, then the eager state can be used
          // without calling the reducer again.
          update.hasEagerState = true;
          update.eagerState = eagerState;

          // 对比新旧状态是否不同
          if (is(eagerState, currentState)) {
            // 状态没改变，当前 setState 无效，return 结束，无事发生
            enqueueConcurrentHookUpdateAndEagerlyBailout(fiber, queue, update);

            return;
          }
        } catch (error) {
          // Suppress the error. It will throw again in the render phase.
        }
      }
    }

    // 将 update 加到 queue 链表末尾
    // 将 fiber 标记为 SyncLane
    const root = enqueueConcurrentHookUpdate(fiber, queue, update, lane);
    if (root !== null) {
      const eventTime = requestEventTime();
      // 调度 fiber 更新
      scheduleUpdateOnFiber(root, fiber, lane, eventTime);
      entangleTransitionUpdate(root, queue, lane);
    }
  }

  markUpdateInDevTools(fiber, lane, action);
}
```

#### enqueueRenderPhaseUpdate

```js
/**
 * enqueueRenderPhaseUpdate
 * @param {*} queue 队列的初始对象
 * @param {*} update 更新的队列
 */
function enqueueRenderPhaseUpdate<S, A>(
  queue: UpdateQueue<S, A>,
  update: Update<S, A>,
) {
  // This is a render phase update. Stash it in a lazily-created map of
  // queue -> linked list of updates. After this render pass, we'll restart
  // and apply the stashed updates on top of the work-in-progress hook.
  didScheduleRenderPhaseUpdateDuringThisPass =
    didScheduleRenderPhaseUpdate = true;

  const pending = queue.pending;

  // 证明第一次更新
  if (pending === null) {
    // 让自己和自己构建成一个环状链表
    update.next = update;
  }
  // 不是第一次更新
  else {
    // 否则这个 update 会被添加到链表称为尾节点
    // 先将这个 update 指向头节点
    update.next = pending.next;
    // 再将当前的尾节点指向该节点 
    pending.next = update;
  }

  // queue的pending 指针始终指向链表的最后一个节点
  queue.pending = update;
}
```

### 更新阶段（获取最新状态）

#### updateWorkInProgressHook

```js
function updateWorkInProgressHook(): Hook {
  // 1. 移动 currentHook 指针
  //（来自 current.memoizedState 链表）
  let nextCurrentHook: null | Hook;

  if (currentHook === null) {
    const current = currentlyRenderingFiber.alternate;
    if (current !== null) {
      nextCurrentHook = current.memoizedState;
    } else {
      nextCurrentHook = null;
    }
  } else {
    nextCurrentHook = currentHook.next;
  }

  // 2. 移动 workInProgressHook 指针
  //（来自 currentlyRenderingFiber.memoizedState 链表）
  let nextWorkInProgressHook: null | Hook;
  if (workInProgressHook === null) {
    nextWorkInProgressHook = currentlyRenderingFiber.memoizedState;
  } else {
    nextWorkInProgressHook = workInProgressHook.next;
  }

  if (nextWorkInProgressHook !== null) {
    // 这种情况为 “渲染时更新逻辑”（在 render 时调用了 setState）
    // 为了更聚焦普通情况，这里不讨论
    workInProgressHook = nextWorkInProgressHook;
    nextWorkInProgressHook = workInProgressHook.next;

    currentHook = nextCurrentHook;
  } else {
    // 3. 渲染时不更新，nextWorkInProgressHook 就一定是 null
    if (nextCurrentHook === null) {
      throw new Error('Rendered more hooks than during the previous render.');
    }

    currentHook = nextCurrentHook;

    const newHook: Hook = {
      memoizedState: currentHook.memoizedState,

      baseState: currentHook.baseState,
      baseQueue: currentHook.baseQueue,
      queue: currentHook.queue,

      next: null,
    };

    // 4. 经典单链表末尾加节点写法
    if (workInProgressHook === null) {
      // This is the first hook in the list.
      currentlyRenderingFiber.memoizedState = workInProgressHook = newHook;
    } else {
      // Append to the end of the list.
      workInProgressHook = workInProgressHook.next = newHook;
    }
  }

  // 5. 返回拷贝 hook 对象
  return workInProgressHook;
}
```

#### updateState

```js
function updateState<S>( 
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] { 
  // 实际用的是 updateReducer
  return updateReducer(basicStateReducer, (initialState: any));
}
```

#### updateReducer

```ts
function updateReducer<S, I, A>(
  reducer: (S, A) => S,
  initialArg: I,
  init?: (I) => S,
): [S, Dispatch<A>] {
  // ----- 【1】 拷贝 hook（current -> workInProcess），并返回这个 hook ----
  const hook = updateWorkInProgressHook();

  // ----- 【2】 读取队列，计算出最新状态，更新 hook 的状态 -----
  // 取出 hook.queue 链表，添加到 current.baseQueue 末尾
  const queue = hook.queue;

  if (queue === null) {
    throw new Error(
      'Should have a queue. This is likely a bug in React. Please file an issue.',
    );
  }

  queue.lastRenderedReducer = reducer;

  const current: Hook = (currentHook: any);

  // The last rebase update that is NOT part of the base state.
  let baseQueue = current.baseQueue;

  // The last pending update that hasn't been processed yet.
  const pendingQueue = queue.pending;
  if (pendingQueue !== null) {
    // We have new updates that haven't been processed yet.
    // We'll add them to the base queue.
    if (baseQueue !== null) {
      // Merge the pending queue and the base queue.
      const baseFirst = baseQueue.next;
      const pendingFirst = pendingQueue.next;
      baseQueue.next = pendingFirst;
      pendingQueue.next = baseFirst;
    }

    current.baseQueue = baseQueue = pendingQueue;
    queue.pending = null;
  }

  // 处理更新队列
  if (baseQueue !== null) {
    // We have a queue to process.
    const first = baseQueue.next;
    let newState = current.baseState;

    let newBaseState = null;
    let newBaseQueueFirst = null;
    let newBaseQueueLast = null;
    let update = first;

    // 循环，根据 baseQueue 链表下的 update 对象计算新状态
    do {
      // ********* 一些跳过更新的逻辑 start *********
      // An extra OffscreenLane bit is added to updates that were made to
      // a hidden tree, so that we can distinguish them from updates that were
      // already there when the tree was hidden.
      const updateLane = removeLanes(update.lane, OffscreenLane);
      const isHiddenUpdate = updateLane !== update.lane;

      // Check if this update was made while the tree was hidden. If so, then
      // it's not a "base" update and we should disregard the extra base lanes
      // that were added to renderLanes when we entered the Offscreen tree.
      const shouldSkipUpdate = isHiddenUpdate
        ? !isSubsetOfLanes(getWorkInProgressRootRenderLanes(), updateLane)
        : !isSubsetOfLanes(renderLanes, updateLane);

      if (shouldSkipUpdate) {
        // Priority is insufficient. Skip this update. If this is the first
        // skipped update, the previous update/state is the new base
        // update/state.
        const clone: Update<S, A> = {
          lane: updateLane,
          action: update.action,
          hasEagerState: update.hasEagerState,
          eagerState: update.eagerState,
          next: (null: any),
        };
        if (newBaseQueueLast === null) {
          newBaseQueueFirst = newBaseQueueLast = clone;
          newBaseState = newState;
        } else {
          newBaseQueueLast = newBaseQueueLast.next = clone;
        }
        // Update the remaining priority in the queue.
        // TODO: Don't need to accumulate this. Instead, we can remove
        // renderLanes from the original lanes.
        currentlyRenderingFiber.lanes = mergeLanes(
          currentlyRenderingFiber.lanes,
          updateLane,
        );
        markSkippedUpdateLanes(updateLane);
      } else {
        // This update does have sufficient priority.

        if (newBaseQueueLast !== null) {
          const clone: Update<S, A> = {
            // This update is going to be committed so we never want uncommit
            // it. Using NoLane works because 0 is a subset of all bitmasks, so
            // this will never be skipped by the check above.
            lane: NoLane,
            action: update.action,
            hasEagerState: update.hasEagerState,
            eagerState: update.eagerState,
            next: (null: any),
          };
          newBaseQueueLast = newBaseQueueLast.next = clone;
        }

        // ********* 一些跳过更新的逻辑 end *********

        // Process this update.
        if (update.hasEagerState) {
          // 为了对比新旧状态来决定是否更新，所计算的新状态。
          // 如果不同，给 update.hasEagerState 设置为 true
          // 新状态赋值给 update.eagerState
          newState = ((update.eagerState: any): S);
        } else {
          // 计算新状态
          const action = update.action;
          newState = reducer(newState, action);
        }
      }
      update = update.next;
    } while (update !== null && update !== first);

    if (newBaseQueueLast === null) {
      newBaseState = newState;
    } else {
      newBaseQueueLast.next = (newBaseQueueFirst: any);
    }

    // Mark that the fiber performed work, but only if the new state is
    // different from the current state.
    if (!is(newState, hook.memoizedState)) {
      markWorkInProgressReceivedUpdate();
    }

    // 更新 hook 状态
    hook.memoizedState = newState;
    hook.baseState = newBaseState;
    hook.baseQueue = newBaseQueueLast;

    queue.lastRenderedState = newState;
  }

  if (baseQueue === null) {
    // `queue.lanes` is used for entangling transitions. We can set it back to
    // zero once the queue is empty.
    queue.lanes = NoLanes;
  }

  const dispatch: Dispatch<A> = (queue.dispatch: any);
  return [hook.memoizedState, dispatch];
}
```
