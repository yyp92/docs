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
