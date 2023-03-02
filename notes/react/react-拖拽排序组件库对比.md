# React拖拽排序组件库对比

## 用法简介

基于react的拖拽功能，有这么几个比较流行的库：

- [react-dnd](https://github.com/react-dnd/react-dnd)

- [react-beautiful-dnd](https://github.com/atlassian/react-beautiful-dnd)

- [dnd-kit](https://github.com/clauderic/dnd-kit)

- [react-sortable-hoc](https://github.com/clauderic/react-sortable-hoc)

### React-dnd

**基本概念**

- **Backend**：后端主要用来抹平浏览器差异，处理 DOM 事件，同时把 DOM 事件转换为 React DnD 内部的 redux action，你可以使用 HTML5 拖拽后端，也可以自定义 touch、mouse 事件模拟的后端实现

- **Item**：用一个数据对象来描述当前被拖拽的元素，例如{ cardId: 42 }

- **Type**：类似于 redux 里面的actions types 枚举常量，定义了应用程序里支持的拖拽类型

- **Monitor：** 拖放本质上是有状态的。要么正在进行拖动操作，要么不在。要么有当前类型和当前项目，要么没有，React DnD 通过 Monitor 来存储这些状态并且提供查询

- **Connector**：连接组件和 Backend ，可以让 Backend 获取到 DOM

- **DragSource**： 这是一个高阶组件，使用它包裹住你的组件使它变为拖拽源

- **DropTarget**：这是一个高阶组件，使用它包裹住你的组件使它变为放置源

- **DragDropContext**：包裹根组件，提供拖拽的上下文环境

**简单demo**

[react-dnd/react-dnd: simple - CodeSandbox](https://codesandbox.io/s/github/react-dnd/react-dnd/tree/gh-pages/examples_hooks_ts/04-sortable/simple%3Ffrom-embed%3D%26file%3D/src/Container.tsx)

### react-beautiful-dnd

**基本概念**

主要包含三个组件.

- DragDropContext : 用于包装拖拽根组件，Draggable和Droppable都需要包裹在DragDropContext内

- Draggable 用于包装你需要拖动的组件，使组件能够被拖拽（make it draggable）

- Droppable 用于包装接收拖拽元素的组件，使组件能够放置（dropped on it）

**简单demo**

[vertical list - CodeSandbox](https://codesandbox.io/s/k260nyxq9v)

### dnd-kit

[文档](https://docs.dndkit.com/)

**基本概念**

- DndContext 用于包装拖拽根组件，Draggable和Droppable都需要包裹在DndContext 内 

- Droppable 用于包装接收拖拽元素的组件，使组件能够放置 

- Draggable 用于包装你需要拖动的组件，使组件能够被拖拽

- Sensors 用于检测不同的输入方法，以启动拖动操作、响应移动以及结束或取消操作，内置传感器有：
  
  - 指针
  
  - 鼠标
  
  - 触摸
  
  - 键盘

- Modifiers 可让您动态修改传感器检测到的运动坐标。它们可用于广泛的用例，例如：
  
  - 将运动限制在单个轴上
  
  - 限制可拖动节点容器的边界矩形的运动
  
  - 限制可拖动节点的滚动容器边界矩形的运动
  
  - 施加阻力或夹紧运动

**简单demo**

[dnd-kit demo](https://master--5fc05e08a4a65d0021ae0bf2.chromatic.com/?path=/story/core-draggable-hooks-usedraggable--basic-setup)

### react-sortable-hoc

**基本概念** 

- SortableContainer 拖拽排序的容器 

- SortableElement 拖拽排序的元素

**简单demo**

[react-sortable-hoc-starter - CodeSandbox](https://codesandbox.io/s/react-sortable-hoc-starter-o104x95y86)

## 兼容antd的table

如何配合antd的table组件进行使用？

- [**react-beautiful-dnd-antd-table**](https://codesandbox.io/s/react-beautiful-dnd-examples-multi-drag-table-with-antd-forked-rln36?file=/src/multi-table-drag/index.js)

- [react-dnd-antd-table (forked) - CodeSandbox](https://codesandbox.io/s/react-dnd-antd-table-forked-q13273)

- [react-sortable-hoc-antd-table - CodeSandbox](https://codesandbox.io/s/tuo-zhuai-shou-bing-lie-antd-4-17-0-alpha-0-forked-c5l4x)

## 树兼容

[antd自带的tree拖拽排序](https://codepen.io/huxinmin/embed/bGRLmjP)

**自带的tree拖拽缺点是**

- 无法实现动态实时拖拽更换位置效果，必须拖拽结束后才发生位置变化

- 需要修改大量的自带的样式

可以简单地把树看做是互相嵌套的列表。

- [react-dnd-tree - CodeSandbox](https://codesandbox.io/s/crazy-hoover-vwcy9)

- [react-sortable-hoc-tree - CodeSandbox](https://codesandbox.io/embed/react-sortable-hoc-tree-386oh)

- [react-beautiful-dnd-tree - CodeSandbox](https://codesandbox.io/embed/react-beautiful-dnd-tree-63f3j)

- [react-dnd-kit-tree - CodeSandbox](https://codesandbox.io/embed/react-dnd-kit-tree-uiujv)

## 移动端兼容

- [react-dnd-antd-table-mobile - CodeSandbox](https://codesandbox.io/embed/react-dnd-antd-table-mobile-j1b0l)

- [react-beautiful-dnd table with antd - CodeSandbox](https://codesandbox.io/s/react-beautiful-dnd-examples-multi-drag-table-with-antd-forked-rln36)

- [react-sortable-hoc-antd-table - CodeSandbox](https://codesandbox.io/s/tuo-zhuai-shou-bing-lie-antd-4-17-0-alpha-0-forked-c5l4x)

- [Storybook](https://5fc05e08a4a65d0021ae0bf2-hbqxtqukzi.chromatic.com/?path=/story/core-draggable-hooks-usedraggable--basic-setup)

## 无限滚动

最佳的方法就是使用virtual-list，不过这几个库的支持情况也不一样。

- [react-dnd-stress-test - CodeSandbox](https://codesandbox.io/embed/react-dnd-stress-test-pbbjk)
  
  - 使用requestAnimationFrame进行性能优化，也可以配合其他的虚拟list库进行使用。

- [**dnd-kit**](https://5fc05e08a4a65d0021ae0bf2-hbqxtqukzi.chromatic.com/?path=/story/presets-sortable-virtualized--basic-setup)
  
  - 这个demo使用了[react-tiny-virtual-list](http://github.com/clauderic/react-tiny-virtual-list)库。

- [**react-sortable-hoc**](http://clauderic.github.io/react-sortable-hoc/#/basic-configuration/basic-usage?_k=ce4tz4)
  
  - 使用了react-virtualized

- [**react-beautifule-dnd**](https://react-beautiful-dnd.netlify.app/?path=/story/single-vertical-list--basic)
  
  - 使用了react-virtualized

## 总结对比

- react-dnd
  
  - 文档齐全
  
  - github star星数16.4k
  
  - 维护更新良好，最近一月内有更新维护
  
  - 学习成本较高
  
  - 功能中等
  
  - 移动端兼容情况，良好
  
  - 示例数量中等
  
  - 概念较多，使用复杂
  
  - 组件间能解耦

- react-beautiful-dnd
  
  - 文档齐全
  
  - github star星数24.8k
  
  - 维护更新良好，最近三月内有更新维护
  
  - 学习成本较高
  
  - 使用易度中等
  
  - 功能丰富
  
  - 移动端兼容情况，优秀
  
  - 示例数量丰富
  
  - 是为垂直和水平列表专门构建的更高级别的抽象，没有提供 react-dnd 提供的广泛功能
  
  - 外观漂亮，可访问性好，物理感知让人感觉更真实的在移动物体
  
  - 开发理念上是拖拽，不支持copy/clone

- dnd-kit
  
  - 文档齐全
  
  - github star星数2.8k
  
  - 维护更新良好，最近一月内有更新维护
  
  - 学习成本中等
  
  - 使用易度中等
  
  - 功能中等
  
  - 移动端兼容情况，中等
  
  - 示例数量丰富
  
  - 未看到copy/clone

- react-sortable-hoc
  
  - 文档较少
  
  - github star星数9.5k
  
  - 维护更新良好，最近三月内有更新维护
  
  - 学习成本较低
  
  - 使用易度较低
  
  - 功能简单
  
  - 移动端兼容情况，中等
  
  - 示例数量中等
  
  - 不支持拖拽到另一个容器中
  
  - 未看到copy/clone
  
  - 主要集中于排序功能，其余拖拽功能不丰富

如果是要结合antd的table使用，最简单的组件是react-sortable-hoc，如果是无限滚动react-sortable-hoc示例虽然多，但是源码很少，可以考虑使用react-beautiful-dnd。如果是树形拖拽，要求不高的情况可以使用antd自带的tree，要求高点可以使用react-beautiful-dnd。兼容移动端，可以考虑使用react-sortable-hoc或者react-beautiful-dnd。

## 如何自己封装一个简单的拖拽组件

### HTML5拖放API

首先，为了使元素可以拖动，需要设置draggable属性：

```html
<img draggable="true">
```

然后有这么几个拖拽处理的函数：

- `ondrag` 拖放进行中

- `ondragstart/ondragend` 开始拖放和结束拖放

- `ondragover` 当元素或选中的文本被拖到一个目标目标上（每100毫秒触发一次）。

- `ondragenter/ondragleave` 源对象开始进入/离开目标对象范围内

- `ondrop` 源对象被拖放到目标对象上

数据的传输，使用event.dataTransfer，它有如下这些api：

- `setData`  添加拖拽数据，这个方法接收两个参数，第一个参数是数据类型（可自定义），第二个参数是对应的数据
- `getData`  反向操作，获取数据，只接收一个参数，即数据类型
- `clearData`  清除数据
- `setDragImage`  可自定义拖放过程中鼠标旁边的图像
- `effectAllowed`  属性指定拖放操作所允许的一个效果。*copy* 操作用于指示被拖动的数据将从当前位置复制到放置位置。_move操作用于指定被拖动的数据将被移动。 link_操作用于指示将在源和放置位置之间创建某种形式的关系或连接。

### 功能与架构设计

- 使用react-hooks

- 拖拽对象drag组件，拖放对象drop组件，拖拽上下文dndContext

- 支持移动端

- 支持排序

### 代码

Drag组件使用：

```tsx
<Drag index={1} id='1'>
  <div>被包裹的可以拖拽的组件</div>
</Drag>
```

Drag组件实现：

```tsx
import { FC } from "react";

interface DragProps {
  index: number;
  id: string | number;
}

const Drag: FC<DragProps> = (props) => {
  const startDrag = (ev) => {
    // 传输数据
    ev.dataTransfer.setData("index", props.index);
    ev.dataTransfer.setData("id", props.id);
  };

  return (
    <div draggable onDragStart={startDrag}>
      {props.children}
    </div>
  );
};

export default Drag;
```

Drop组件使用：

```tsx
<Drop>
    <Drag index={1} id='1'>
      <div>被包裹的可以拖拽的组件</div>
    </Drag>
</Drop>
```

Drop组件实现：

```tsx
import { FC, useContext } from "react";
import { Context } from "./DndContext";

const Drop: FC = (props) => {
  const { onDragOver, onDragEnd } = useContext(Context);
  const dragOver = (ev) => {
    ev.preventDefault();
    if (onDragOver) onDragOver();
  };

  const drop = (ev) => {
    // 获取数据
    const oldIndex = ev.dataTransfer.getData("index");
    // 获取拖拽结束时的Y轴坐标
    const Y = ev.clientY;
    // 简便计算，设定高度为20
    // 我这里很偷懒，实际计算情况很复杂
    //一般有两种实现思路，一种就是根据位置计算，另外一种就是给拖拽源设置可放置，然后获取
    const height = 20;
    const newIndex = Math.floor(Y / height);

    if (oldIndex) {
      if (onDragEnd) onDragEnd(Number(oldIndex), newIndex);
    }
  };

  return (
    <div onDragOver={dragOver} onDrop={drop}>
      {props.children}
    </div>
  );
};

export default Drop;
```

DndContext组件使用：

```tsx
<DndContext
    onDragEnd={(oldIndex, newIndex) => {
        setData(arrayMove(data, oldIndex, newIndex));
    }}
    onDragOver={() => {}}
>
    <Drop>
        {data.map((i, index) => (
            <Drag key={i.id} id={i.id} index={index}>
                <div className="item">{i.text}</div>
            </Drag>
        ))}
    </Drop>
</DndContext>
```

DndContext组件实现：

```tsx
import { createContext, FC } from "react";

export interface TContext {
  onDragOver: () => void;
  onDragEnd: (oldIndex: number, newIndex: number) => void;
}

const Context = createContext<TContext>({} as TContext);

const DndContext: FC<TContext> = (props) => {
  return (
    <Context.Provider
      value={{
        onDragEnd: (oldIndex, newIndex) => {
          props.onDragEnd(oldIndex, newIndex);
        },
        onDragOver: () => {
          props.onDragOver();
        }
      }}
    >
      {props.children}
    </Context.Provider>
  );
};

export { Context };
export default DndContext; 
```

[如果处理移动端兼容性](https://github.com/timruffles/mobile-drag-drop)

### 优化空间

- 拖拽结束，所在位置计算

- 拖拽过程中实时交换位置

- 性能优化

- 异常处理等

- 拖拽过程样式

- 拖拽方向，x轴和Y轴

### 在线代码

[react-drag 具体在线代码示例](https://codesandbox.io/embed/react-drag-w03jd)
