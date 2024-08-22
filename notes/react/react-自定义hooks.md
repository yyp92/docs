# 10个实用的 React 自定义hooks

## 什么是自定义 hook

在 React 中，自定义 hook 是一个利用一个或多个内置 hook（如 useState、useEffect、useContext 等）的 JavaScript 函数。

自定义 hook 提供了一种提取和共享 React 组件逻辑的方法，促进代码的清晰、组织的改善和生产力的提升。

创建 React 中的自定义 hook 遵循一套特定的规则和语法。

- 自定义 hook 应始终以“use”开头，以遵循 React 设置的约定。例如，用于处理表单输入的自定义 hook 可以命名为 useFormInput。

- 自定义 hook 可以利用内置 hook，如 useState、useEffect、useContext 等。它们也可以包含任何必要的自定义逻辑，以封装和抽象复杂功能。

- 自定义 hook 的主要目标是可重用性。它应设计为可在应用程序的多个组件中使用，允许你抽象和共享常见逻辑。

- 自定义 hook 不应包含任何 JSX 或导致组件渲染。它们旨在封装逻辑和状态管理，而不是定义 UI。

## 10个有用的自定义 hook 列表

### useFocus

useFocus 自定义 hook 返回一个包含 ref 和一个函数的数组。ref 用于存储对 DOM 元素的引用，而 focusElement 函数在被调用时用于聚焦该元素。

```tsx
import { useRef, useCallback } from "react";

export const useFocus = () => {
  const ref = useRef(null);

  const focusElement = useCallback(() => {
    if (ref.current) {
      ref.current.focus();
    }
  }, []);

  return [ref, focusElement];
};

export default function App() {
  const [inputRef, focusInput] = useFocus();

  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <div>
        <input ref={inputRef} type="text" />
        <button onClick={focusInput}>Focus Input</button>
      </div>
    </div>
  );
}
```

## useDelay

useDelay 在 App 组件中被用来引入 2000 毫秒的延迟。组件根据延迟是否完成来渲染不同的内容。

```tsx
import { useState, useEffect } from "react";

export const useDelay = (delayTime) => {
  const [done, setDone] = useState(false);

  useEffect(() => {
    const delay = setTimeout(() => {
      setDone(true);
    }, delayTime);

    return () => clearTimeout(delay);
  }, [delayTime]);

  return done;
};

export default function App() {
  const isDone = useDelay(2000);

  return (
    <div>
      {isDone ? (
        <p>Welcome to JavaScript Centric!</p>
      ) : (
        <p>Page is Loading ...</p>
      )}
    </div>
  );
}
```

## useWindowSize

useWindowSize 在 App 组件中用于追踪窗口尺寸并在组件中显示。这个 useWindowSize 自定义 hook 封装了追踪窗口尺寸的逻辑，使其可以在不同的组件中重用，促进了更响应和适应性更强的用户界面。

```tsx
import { useState, useEffect } from "react";

export const useWindowSize = () => {
  const [windowSize, setWindowSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    const handleResize = () => {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    };

    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return windowSize;
};

export default function App() {
  const { width, height } = useWindowSize();

  return (
    <div>
      <p>Window Width: {width}</p>
      <p>Window Height: {height}</p>
    </div>
  );
}
```

### useOnClickOutside

```tsx
import React, { useRef, useEffect } from "react";

export const useOnClickOutside = (ref, handler) => {
  useEffect(() => {
    const listener = (event) => {
      if (!ref.current || ref.current.contains(event.target)) {
        return;
      }
      handler(event);
    };

    document.addEventListener("mousedown", listener);
    document.addEventListener("touchstart", listener);
    return () => {
      document.removeEventListener("mousedown", listener);
      document.removeEventListener("touchstart", listener);
    };
  }, [ref, handler]);
};

export default function App() {
  const ref = useRef(null);

  const onClose = () => {
    alert("clicked outside");
  };

  useOnClickOutside(ref, onClose);

  return <div ref={ref}>Click outside this element to close</div>;
}
```

### useFormUpdate

useFormUpdate 在 App 组件中被用于自动监测所有表单字段的变化，包括 input、textarea 和 select 元素。当用户修改任何表单字段时，表单被标记为已更新，并且在用户尝试带着未保存的更改离开时触发 beforeunload 事件监听器。

```tsx
import React, { useState, useEffect } from "react";

export const useFormUpdate = () => {
  const [isFormUpdated, setIsFormUpdated] = useState(false);

  useEffect(() => {
    const handleBeforeUnload = (event) => {
      if (isFormUpdated) {
        const message =
          "You have unsaved changes. Are you sure you want to leave?";
        event.returnValue = message;
        return message;
      }
    };

    window.addEventListener("beforeunload", handleBeforeUnload);

    return () => {
      window.removeEventListener("beforeunload", handleBeforeUnload);
    };
  }, [isFormUpdated]);

  const handleFormUpdate = () => {
    setIsFormUpdated(true);
  };

  useEffect(() => {
    const formElements = document.querySelectorAll(
      "form input, form select, form textarea"
    );
    const handleFieldChange = () => {
      setIsFormUpdated(true);
    };

    formElements.forEach((element) => {
      element.addEventListener("change", handleFieldChange);
    });

    return () => {
      formElements.forEach((element) => {
        element.removeEventListener("change", handleFieldChange);
      });
    };
  }, []);

  return handleFormUpdate;
};

export default function App() {
  useFormUpdate();

  return (
    <form>
      <input type="text" />
      <input type="email" />
      <textarea />
      <select>
        <option value="1">Option 1</option>
        <option value="2">Option 2</option>
      </select>
      <input type="submit" value="Submit" />
    </form>
  );
}
```

### useFetch

`useFetch` 用于从指定的 URL 异步获取数据，并处理加载状态和错误状态。

```tsx
import React, { useState, useEffect } from "react";

export const useFetch = (url) => {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        const fetchData = async () => {
            try {
                const response = await fetch(url)

                if (!response.ok) {
                  throw new Error("Network response was not ok");
                }

                const result = await response.json();
                setData(result);
            }
            catch (error) {
                setError(error);
            }
            finally {
                setLoading(false);
            }
        };

        fetchData();
    }, [url]);

    return { data, loading, error };
};

export default function App() {
    const users = useFetch("https://jsonplaceholder.typicode.com/users")

    return (
        <div>
            {
                users.loading
                    ? (
                        <p>Loading...</p>
                    )
                    : users.error
                        ? (
                            <p>Error: {users.error.message}</p>
                        )
                        : (
                            <ul>
                                {
                                    users.data.map((user) => (
                                      <li key={user.id}>{user.name}</li>
                                    ))
                                }
                            </ul>
                        )
            }
        </div>
    )
}
```

### useDebounce

`useDebounce` 用于对文本输入字段的输入值进行去抖处理。经过指定延迟后，`debouncedValue` 会被更新，允许您更有效地处理输入变化，例如实现实时搜索功能。

```tsx
import React, { useState, useEffect } from "react";

export const useDebounce = (value, delay) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
};

export default function App() {
  const [inputValue, setInputValue] = useState("");
  const debouncedValue = useDebounce(inputValue, 500);

  const handleInputChange = (event) => {
    setInputValue(event.target.value);
  };

  return (
    <div>
      <input type="text" value={inputValue} onChange={handleInputChange} />
      <p>Debounced value: {debouncedValue}</p>
    </div>
  );
}
```

### useLocalStorage

`useLocalStorage` 在 App 组件中用于持久化和检索本地存储中 `tokenName` 键的值。组件渲染一个输入字段，允许用户更新持久化的值。

```tsx
import React, { useState, useEffect } from "react";

export const useLocalStorage = (key, initialValue) => {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  const setValue = (value) => {
    try {
      const valueToStore =
        value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };

  useEffect(() => {
    const handleStorageChange = () => {
      try {
        const item = window.localStorage.getItem(key);
        setStoredValue(item ? JSON.parse(item) : initialValue);
      } catch (error) {
        console.error(error);
      }
    };

    window.addEventListener("storage", handleStorageChange);

    return () => {
      window.removeEventListener("storage", handleStorageChange);
    };
  }, [key, initialValue]);

  return [storedValue, setValue];
};

export default function App() {
  const [token, setToken] = useLocalStorage("tokenName", "");

  const handleTokenChange = (event) => {
    setToken(event.target.value);
  };

  return (
    <div>
      <input type="text" value={token} onChange={handleTokenChange} />
    </div>
  );
}
```

### useMediaQuery

`useMediaQuery` 在 App 组件中用于根据指定的媒体查询检测视口大小的变化。组件根据 `useMediaQuery` 返回的 `isMobile` 状态有条件地渲染内容。

```tsx
import React, { useState, useEffect } from "react";

export const useMediaQuery = (query) => {
  const [matches, setMatches] = useState(false);

  useEffect(() => {
    const mediaQuery = window.matchMedia(query);

    const handleMediaQueryChange = (event) => {
      setMatches(event.matches);
    };

    mediaQuery.addListener(handleMediaQueryChange);
    setMatches(mediaQuery.matches);

    return () => {
      mediaQuery.removeListener(handleMediaQueryChange);
    };
  }, [query]);

  return matches;
};

export default function App() {
  const isMobile = useMediaQuery("(max-width: 768px)");

  return <div>{isMobile ? <p>Mobile View</p> : <p>Desktop View</p>}</div>;
}
```

### useToggle

`useToggle` 在 App 组件中用于切换一个值在 true 和 false 之间。

```tsx
import React, { useState } from "react";

export const useToggle = (defaultValue) => {
  const [value, setValue] = useState(defaultValue);

  function toggleValue(value) {
    setValue((currentValue) =>
      typeof value === "boolean" ? value : !currentValue
    );
  }

  return [value, toggleValue];
};

export default function App() {
  const [value, toggleValue] = useToggle(false);

  return (
    <div>
      <div>{value.toString()}</div>
      <button onClick={toggleValue}>Toggle</button>
      <button onClick={() => toggleValue(true)}>Make True</button>
      <button onClick={() => toggleValue(false)}>Make False</button>
    </div>
  );
}
```
