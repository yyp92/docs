# react v18 api使用

## 错误处理

ErrorBoundaryPage

```js
export default class ErrorBoundaryPage extends React.Component {
  state = {hasError: false, error: null};
  static getDerivedStateFromError(error) {
    return {
      hasError: true,
      error,
    };
  }
  render() {
    if (this.state.hasError) {
      return this.props.fallback;
    }
    return this.props.children;
  }
}
```

使用：

```js
import {useState, Suspense} from "react";
import User from "../components/User";
import Num from "../components/Num";
import {fetchData} from "../utils";
import ErrorBoundaryPage from "./ErrorBoundaryPage";

const initialResource = fetchData();

export default function SuspensePage(props) {
  const [resource, setResource] = useState(initialResource);

  return (
    <div>
      <h3>SuspensePage</h3>
      <ErrorBoundaryPage fallback={<h1>网络出错了</h1>}>
        <Suspense fallback={<h1>loading - user</h1>}>
          <User resource={resource} />
        </Suspense>
      </ErrorBoundaryPage>

      <Suspense fallback={<h1>loading-num</h1>}>
        <Num resource={resource} />
      </Suspense>

      <button onClick={() => setResource(fetchData())}>refresh</button>
    </div>
  );
}
```

## 强制更新组件

```ts
 // ! 方法1
 const [count, forceUpdate] = useState(0);
 // 调用 - 缺点：每次都得使用count+1，而且每个地方都得写一遍
 forceUpdate(count + 1);


 // ! 方法2
 const [, foceUpdate] = useReducer((x) => x + 1, 0);
 // 调用 - 缺点：每个地方都得写一遍
 forceUpdate();


 // ! 方法3 完美-自定义hooks
 function useForceUpdate() {
     const [state, setState] = useState(0);
     // const [, setState] = useReducer((x) => x + 1, 0);

     const update = useCallback(() => {
         setState((prev) => prev + 1);
         // setState();
     }, []);

     return update;
 } 
 // 调用 - 只需要引用调用一次就行
 forceUpdate();
```
