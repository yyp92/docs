# react hooks 链表结构

## React中的链表

React Fiber版本的源码中使用到链表的数据结构（为了实现低优先级任务的暂停与重启），包括单向链表和环状链表。

- 首先，Hooks是以单向链表的形式存储在 Fiber 的 memoizedState 属性身上
  
  ![](../../\images\react-fiber-data-constructor-1.png)

-  同时，每个hooks又拥有自己的更新队列queue，queue.pending 会指向一个环状链表
  
  ![](../../\images\react-fiber-data-constructor-2.png)

## 单向链表代码实现

```ts
function buildQueue(queue,action){
    const update = {action,next:null}
    const pending = queue.pending

    if(!pending){
        queue.pending = update
    }else{
        let current = queue.pending
        // 找到末尾的元素
        while(current.next){
            current = current.next
        }
        // 将update挂载到链表的末尾
        current.next = update
    }
}

// excute
let queue = {pending:null}
buildQueue(queue,'hooks1')
buildQueue(queue,'hooks2')

// output: queue.pending = {action:'hooks1',next:{action:'hooks2',next:null}}
```

## 环状链表代码实现

```ts
function dispatchAction(queue,action){
     const update = {action,next:null}
     const pending = queue.pending
     if(pending === null){
        // pending 为空，说明这是第一个 update
        // 自己与自己创建一个环状链表
        update.next = update 
     }else{
        // 否则这个 update 会被添加到链表称为尾节点
        // 先将这个 update 指向头节点
        update.next = pending.next
        // 再将当前的尾节点指向该节点
        pending.next = update
     }
     queue.pending = update
}

let queue = {pending:null}

/**
 * update.next === update
 * queue.pending === update(action) 
 */
dispatchAction(queue,'action')


/**
 * update(action1).next -> update(action).next [update(action)]    
 * update1 的next 指向 update
 * queue.pending.next [update(update)] -> update(action1)          
 * update 指向 update1
 * queue.pending -> update(action1)                                
 * queue.pending 指向 update1
 */
dispatchAction(queue,'action1')
```

![](../../\images\react-fiber-data-constructor-3.png)

-  由图可知，queue.pending 永远指向最后一个更新
-  由图可知，pending.next 永远指向第一个更新

## 为什么UpdateQueue 的结构为环状链表？

实际上使用环形链表的原因也很简单。普通链表如果只保存首节点的指针，则每次插入时需要先进行一次遍历找到尾节点，再进行插入。要么就需要保存首尾节点的指针。而环形链表的**尾节点的下一个节点就是首节点**，因此只需要保存一个尾节点就可以做到既方便插入又方便访问首节点。

## 参考资料

[React Fiber - updateQueue 原理分析 - 掘金](https://juejin.cn/post/7093082885363597349)
