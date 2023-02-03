# 前端封装axios

## 效果

```ts
const [e, r] = await api.getUserInfo(userid)
if (!e && r) this.userInfo = r.data.userinfo
```

一步一步封装一套属于我们自己的 **接口请求工具**。

> 注：
> 
> - 这里以 `axios` 作示范，同样换成 `fetch` 、 小程序的 `request` 都是可以的
> 
> - 将会采用 `typeScript` 书写

## 思路

![](../../\images\axios-package-1.png)

有了一个清晰的请求流程图，我们便可以区分出来两块重要的内容来进行拆分：**基础请求流程** 、 **拦截器** 。

## 基础请求流程

基础请求流程，我们大致可以分为三块， 一是 **请求进入请求拦截前** 、二是 **真正发起的请求** 、三是 **请求从响应拦截出来后** 。

这其中可以归为两类： 
一类是 **针对单独接口的处理** 
二类是 **针对所有接口需要的内容**

- 针对单独接口的处理
  
  - 请求前的参数处理
  - 请求后的返回值处理

- 针对所有接口的处理
  
  - Post
  
  - Get
  
  - Put
  
  - Del

## 拦截器

拦截器，我们大致可以分为两类， 一类是 **请求接口前的统一处理（请求拦截）** 、 一类是 **请求接口后的统一处理（响应拦截）**

- 请求拦截
  
  - 请求调整
  - 用户标识

- 响应拦截
  
  - 网络错误处理
  - 授权错误处理
  - 普通错误处理
  - 代码异常处理

## 统一调用

随着我们的 `Api` 越来越多，我们可能需要给他们不同的分类，但我们并不希望每次调用都从不同的文件夹引入不同的 `Api` ，因此在 基础请求 + 拦截器 之外，我们还需要一个封包操作。

## 开发顺序

随着我们要做的内容越来越多，我们希望它有一个顺序以便于我们按部就班的开发（相信大家对开发中出现的不确定性都深恶痛绝）。 
以便于我们按照流程，无意外、无惊喜 的完成此次封装。

在我们的开发中，我们基本要遵循先处理通用内容在处理个性化内容的逻辑：

- 针对所有接口的处理（Get）

- 请求拦截

- 响应拦截

- 针对单独接口的处理

- 封包处理

- 针对所有接口的处理（Post、Put、Del）

## 针对所有接口的处理（Get）

我们希望以 `const [e, r] = await api.getUserInfo(id)` 的方式调用，代表着我们需要保证返回值稳定的返回 `[err, result]` ，所以我们需要在请求无论成功失败时，都以 `resolve` 方式调用。

同时，我们希望我们可以处理返回值，因此在这里封装了 `clearFn` 的回调函数。

```ts
type Fn = (data: FcResponse<any>) => unknown

interface IAnyObj {
  [index: string]: unknown
}

interface FcResponse<T> {
  errno: string
  errmsg: string
  data: T
}

const get = <T,>(url: string, params: IAnyObj = {}, clearFn?: Fn): Promise<[any, FcResponse<T> | undefined]> => {
    return new Promise((resolve) => {
        axios
            .get(url, { params })
            .then((result) => {
                let res: FcResponse<T> 

                if (clearFn !== undefined) {
                    res = clearFn(result.data) as unknown as FcResponse<T>
                }
                else {
                    res = result.data as FcResponse<T>
                } 

                resolve([null, res as FcResponse<T>])
            })
            .catch((err) => {
                resolve([err, undefined])
            })
    })
}
```

## 请求拦截

请求拦截中，我们需要两块内容，一是 **请求的调整** ，二是 **配置用户标识**

```ts
const handleRequestHeader = (config) => {
    config['xxxx'] = 'xxx'
    return config
}

const handleAuth = (config) => {
    config.header['token'] = localStorage.getItem('token') || token || ''
    return config
}

axios.interceptors.request.use((config) => {
    config = handleChangeRequestHeader(config)
    config = handleConfigureAuth(config)
    return config
})
```

## 响应拦截

响应错误由三类错误组成：

- 网络错误处理

- 授权错误处理

- 普通错误处理

因此，要优雅的处理响应拦截，我们必须先将三类错误函数写好，以便于我们增强代码扩展性及后期维护。

### 错误处理函数

**handleNetworkError**

```ts
const handleNetworkError = (errStatus) => {
    let errMessage = '未知错误'
    if (errStatus) {
        switch (errStatus) {
            case 400:
                errMessage = '错误的请求'
                break
            case 401:
                errMessage = '未授权，请重新登录'
                break
            case 403:
                errMessage = '拒绝访问'
                break
            case 404:
                errMessage = '请求错误,未找到该资源'
                break
            case 405:
                errMessage = '请求方法未允许'
                break
            case 408:
                errMessage = '请求超时'
                break
            case 500:
                errMessage = '服务器端出错'
                break
            case 501:
                errMessage = '网络未实现'
                break
            case 502:
                errMessage = '网络错误'
                break
            case 503:
                errMessage = '服务不可用'
                break
            case 504:
                errMessage = '网络超时'
                break
            case 505:
                errMessage = 'http版本不支持该请求'
                break
            default:
                errMessage = `其他连接错误 --${errStatus}`
        }
    } else {
        errMessage = `无法连接到服务器！`
    }

    message.error(errMessage)
}
```

**handleAuthError**

```ts
const handleAuthError = (errno) => {
    const authErrMap: any = {
        '10031': '登录失效，需要重新登录', // token 失效
        '10032': '您太久没登录，请重新登录~', // token 过期
        '10033': '账户未绑定角色，请联系管理员绑定角色',
        '10034': '该用户未注册，请联系管理员注册用户',
        '10035': 'code 无法获取对应第三方平台用户',
        '10036': '该账户未关联员工，请联系管理员做关联',
        '10037': '账号已无效',
        '10038': '账号未找到',
    }

    if (authErrMap.hasOwnProperty(errno)) {
        message.error(authErrMap[errno])

        // 授权错误，登出账户
        logout()

        return false
    }

    return true
}  
    return true
}
```

**handleGeneralError**

```ts
const handleGeneralError = (errno, errmsg) => {
    if (err.errno !== '0') {
        meessage.error(err.errmsg)
        return false
    }

    return true
}
```

### 适配

当我们将所有的错误类型处理函数写完，在 `axios` 的拦截器中进行调用即可。

```ts
axios.interceptors.response.use(
    (response) => {
        if (response.status !== 200) return Promise.reject(response.data)

        handleAuthError(response.data.errno)
        handleGeneralError(response.data.errno, response.data.errmsg)

        return response
    },
    (err) => {
        handleNetworkError(err.response.status)
        Promise.reject(err.response)
    }
)
```

## 针对单独接口的处理

基于上面的几类通用处理，我们这个请求的封装基本已经可用了。

但是我们还有一些额外的操作无处存放（参数处理、返回值处理），且我们并不想将他们耦合在页面中每次调用进行处理，那么我们显然需要一个位置来处理这些内容。

```ts
import { Get } from "../server"

interface FcResponse<T> {
    errno: string
    errmsg: string
    data: T
}

type ApiResponse<T> = Promise<[any, FcResponse<T> | undefined]>

function getUserInfo<T extends { id: string; name: string; }>(id): ApiResponse<T> {
    return Get<T>('/user/info', { userid: id })
}
```

## 封包处理

### 接口分类封包

用户数据：`api/path/user.ts`

```ts
import { Get } from "../server"

export function getUserInfo(id) { ... }

export function getUserName(id) { ... }

export const userApi = {
    getUserInfo,
    getUserName
}
```

订单数据：`api/path/shoporder.ts`

```ts
import { Get } from "../server"

function getShoporderDetail() { ... }

function getShoporderList() { ... }

export const shoporderApi = {
    getShoporderDetail,
    getShoporderList
}
```

### 调用点统一

`api/index.ts`

```ts
import { userApi } from "./path/user"
import { shoporderApi } from "./path/shoporder"

export const api = {
    ...userApi,
    ...shoporderApi
}
```

### 针对所有接口的处理（Post、Put、Del）

```ts
export const post = <T,>(url: string, data: IAnyObj, params: IAnyObj = {}): Promise<[any, FcResponse<T> | undefined]> => {
  return new Promise((resolve) => {
    axios
      .post(url, data, { params })
      .then((result) => {
          resolve([null, result.data as FcResponse<T>])
      })
      .catch((err) => {
          resolve([err, undefined])
      })
  })
}

// Put / Del 同理
```

## 完整代码

业务处理函数：`src/api/tool.ts`

```ts
const handleRequestHeader = (config) => {
    config['xxxx'] = 'xxx'

    return config
}

const handleAuth = (config) => {
    config.header['token'] = localStorage.getItem('token') || token || ''

    return config
}

const handleNetworkError = (errStatus) => {
    let errMessage = '未知错误'

    if (errStatus) {
        switch (errStatus) {
            case 400:
                errMessage = '错误的请求'
                break
            case 401:
                errMessage = '未授权，请重新登录'
                break
            case 403:
                errMessage = '拒绝访问'
                break
            case 404:
                errMessage = '请求错误,未找到该资源'
                break
            case 405:
                errMessage = '请求方法未允许'
                break
            case 408:
                errMessage = '请求超时'
                break
            case 500:
                errMessage = '服务器端出错'
                break
            case 501:
                errMessage = '网络未实现'
                break
            case 502:
                errMessage = '网络错误'
                break
            case 503:
                errMessage = '服务不可用'
                break
            case 504:
                errMessage = '网络超时'
                break
            case 505:
                errMessage = 'http版本不支持该请求'
                break
            default:
                errMessage = `其他连接错误 --${errStatus}`
        }
    }
    else {
        errMessage = `无法连接到服务器！`
    }

    message.error(errMessage)
}

const handleAuthError = (errno) => {
    const authErrMap: any = {
        '10031': '登录失效，需要重新登录', // token 失效
        '10032': '您太久没登录，请重新登录~', // token 过期
        '10033': '账户未绑定角色，请联系管理员绑定角色',
        '10034': '该用户未注册，请联系管理员注册用户',
        '10035': 'code 无法获取对应第三方平台用户',
        '10036': '该账户未关联员工，请联系管理员做关联',
        '10037': '账号已无效',
        '10038': '账号未找到',
    }

    if (authErrMap.hasOwnProperty(errno)) {
        message.error(authErrMap[errno])
        // 授权错误，登出账户
        logout()

        return false
    }

    return true
}

const handleGeneralError = (errno, errmsg) => {
    if (err.errno !== '0') {
        meessage.error(err.errmsg)
        return false
    }

    return true
}
```

通用操作封装：`src/api/server.ts`

```ts
import axios from 'axios'
import { message } from 'antd'

import {
    handleChangeRequestHeader,
    handleConfigureAuth,
    handleAuthError,
    handleGeneralError,
    handleNetworkError
} from './tools'

type Fn = (data: FcResponse<any>) => unknown

interface IAnyObj {
    [index: string]: unknown
}

interface FcResponse<T> {
    errno: string
    errmsg: string
    data: T
}

axios.interceptors.request.use((config) => {
    config = handleChangeRequestHeader(config)
    config = handleConfigureAuth(config)
    return config
})

axios.interceptors.response.use(
    (response) => {
        if (response.status !== 200) return Promise.reject(response.data)
        handleAuthError(response.data.errno)
        handleGeneralError(response.data.errno, response.data.errmsg)
        return response
    },
    (err) => {
        handleNetworkError(err.response.status)
        Promise.reject(err.response)
    }
)

export const Get = <T,>(url: string, params: IAnyObj = {}, clearFn?: Fn): Promise<[any, FcResponse<T> | undefined]> =>
    new Promise((resolve) => {
        axios
        .get(url, { params })
        .then((result) => {
            let res: FcResponse<T>

            if (clearFn !== undefined) {
                res = clearFn(result.data) as unknown as FcResponse<T>
            }
            else {
                res = result.data as FcResponse<T>
            }

            resolve([null, res as FcResponse<T>])
        })
        .catch((err) => {
            resolve([err, undefined])
        })
    })

export const Post = <T,>(url: string, data: IAnyObj, params: IAnyObj = {}): Promise<[any, FcResponse<T> | undefined]> => {
    return new Promise((resolve) => {
        axios
            .post(url, data, { params })
            .then((result) => {
                resolve([null, result.data as FcResponse<T>])
            })
            .catch((err) => {
                resolve([err, undefined])
            })
    })
}
```

统一调用点：`src/api/index.ts`

```ts
import { userApi } from "./path/user"
import { shoporderApi } from "./path/shoporder"

export const api = {
    ...userApi,
    ...shoporderApi
}
```

接口：`src/api/path/user.ts` | `src/api/path/shoporder.ts`

```ts
import { Get } from "../server"

export function getUserInfo(id) { ... }

export function getUserName(id) { ... }

export const userApi = {
    getUserInfo,
    getUserName
}
```

```ts
import { Get } from "../server"

function getShoporderDetail() { ... }

function getShoporderList() { ... }

export const shoporderApi = {
    getShoporderDetail,
getShoporderList
}
```
