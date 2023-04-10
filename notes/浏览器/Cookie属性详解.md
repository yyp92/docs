# Cookie属性详解

用浏览器打开一个网站，这里以[Node.js中文网站](http://nodejs.cn/)为例，打开控制台，切换到 Application/Cookies 标签，可以看到大量的 Cookie：

![](../../\images\cookie-1.jpg)

由于 HTTP 本身是无状态的，Cookie 可以用来跟踪用户，由于每次请求浏览器都会自动携带 Cookie，这样服务端就知道哪些请求是来自同一个用户了。

## Name 和 Value

Cookie 本质上就是一个键值对，其中 Name 表示 Cookie 的键，Value 表示 Cookie 的值，除此之外，还有一些额外的选项来标识这个键值对的属性，例如过期时间、是否安全等。我们用 Node.js 写一个简单的 Server 来操作 Cookie：

```js
const http = require('http')
const url = require('url') 

// 路由分发器
const routes = {
  '/': (req, res) => res.end('hello'),
  '/get': (req, res) => res.end(req.headers.cookie),
  '/set': (req, res) => {
    res.setHeader('Set-Cookie', ['name=keliq', 'age=10'])
    res.end('done')
  },
} 

// 响应网络请求
function onRequest(req, res) {
  const { pathname } = url.parse(req.url)
  const route = routes[pathname] // 根据路径选择不同的路由来处理
  if (route) return route(req, res)
  res.statusCode = 404 && res.end('Not Found') // 如果未匹配到路由则返回404
} 

// 创建 HTTP 服务
http.createServer(onRequest).listen(3000)
```

这样就在 3000 端口开启了一个 http 服务了，当我们访问 `http://localhost:3000/set` 的时候，检查网络响应头，可以看到：

```http
HTTP/1.1 200 OK
Set-Cookie: name=keliq
Set-Cookie: age=10
```

当我们访问 `http://localhost:3000/get` 的时候，查看网络请求头：

```http
GET /get HTTP/1.1
Host: localhost:3000
Cookie: name=keliq; age=10
```

也就是说浏览器已经帮我们自动带上之前设置的 Cookie 了，服务端通过获取 header 中的 Cookie 就能判断多次请求是不是来自同一个用户。

## Domain

Domain 表示 Cookie 的作用域，如果未设置默认为 `/`，为了方便演示，我们首先更新 HOST 文件：

```js
127.0.0.1   test.com
127.0.0.1   a.test.com
127.0.0.1   b.test.com
127.0.0.1   a.a.test.com
```

然后访问 `http://test.com:3000/set` 设置 Cookie，再访问 `http://test.com:3000/get` 自然是能获取到的，如果我们访问下面的地址，是否能获取到 Cookie 呢？

```js
http://a.test.com:3000/get
http://a.a.test.com:3000/get
```

答案是：不能，因为域不同。

> **另外 `localhost` 和 `127.0.0.1` 也不是一个域，它们之间不同共享 Cookie**

如果后端未指定 `Domain`，那么默认情况下，域的值就与 `document.domain` 或者 `location.hostname` 相等（大家可以输入到控制台看一下），这个时候是不包含子域名的，但实际上域名和子域名之间可以共享 Cookie，需要后端显示设置 `Domain`：

```js
res.setHeader('Set-Cookie', ['name=keliq; Domain=test.com;', 'age=10'])
```

这个时候观察一下 name 和 age 的 Domain 之间的区别：

| Name | Value | Domain    |
| ---- | ----- | --------- |
| name | keliq | .test.com |
| age  | 10    | test.com  |

我们发现 name 的 Domain 前面多了一个点，意味着可以在子域之间共享，此时再打开上面两个网站是可以看到 name 这个 Cookie 的，但是看不到 age，所以：

> **如果服务端设置了 Domain，无论前面是否加点，最终生效后一定会加点**

在设置域的时候，有一点要注意：

> **如果服务端设置的 Cookie 不包含在当前的 document.domain 中，那么会被浏览器拒绝。**

也就是说不能在 `test.com` 设置 Cookie 的域为 `a.test.com`，不过反过来是可以的，即在 `a.test.com` 中设置 Cookie 域为 `test.com`。同样， `a.test.com` 中不能设置 Cookie 域为 `b.test.com`，更不能设置成其他网站，例如 `baidu.com`，这样最大程度保证了安全性。

另外，关于 Cookie 的域，有两个特别容易混淆的问题，这里一起解释清楚：

- 端口号不同会共享 Cookie 吗？
  
  为了验证这一点，只需要在上面代码的最后一行加上一句:
  
  ```js
  http.createServer(onRequest).listen(4000)
  ```
  
  这样就可以同时监听 3000 和 4000 端口，我们访问：`http://localhost:4000/get` 的时候发现是可以获得 Cookie 的，所以：
  
  > **Cookie 的作用域与端口号无关。**

- 协议不同会共享 Cookie 吗？
  
  为了验证这一点，需要添加 HTTPS 支持，首先我们新建一个 `certs` 目录，用下面的命令生成一个自签名的证书:
  
  ```bash
  openssl genrsa -out key.pem
  openssl req -new -key key.pem -out csr.pem 
  openssl x509 -req -days 9999 -in csr.pem -signkey key.pem -out cert.pem
  ```
  
  然后新增以下代码：
  
  ```js
  const https = require('https')
  const fs = require('fs')
  const options = {
    key: fs.readFileSync('certs/key.pem'),
    cert: fs.readFileSync('certs/cert.pem')
  }
  https.createServer(options, onRequest).listen(5000)
  ```
  
  然后访问：`https://localhost:5000/get`，这个时候 Chrome 浏览器会出现醒目的提醒：
  
  ```js
  您的连接不是私密连接
  ```
  
  不要怕，这是因为 Chrome 不信任这些自签名 ssl 证书，为了安全起见，所以禁止你访问。但是你可以直接在当前页面输入 `thisisunsafe`（注意不是在地址栏输入，而是直接敲击键盘输入），页面会自动刷新进入网页。这个时候仍然能够看到 Cookie，所以：
  
  > **Cookie 的作用域与协议无关。**

所以这里千万不要跟跨域的同源策略搞混，Cookie 只区分域，不区分端口和协议，只要域相同，即使端口号或协议不同，cookie 也能共享。

## Path

这个属性可以指定可以共享 Cookie 的子目录，在开发中其实很少用到，基本上都不设置，默认就是 `/` 根目录，因为设置为根目录，所有子目录可以共享，如果指定子目录的话，其上级目录则无法访问该 Cookie，例如：

```js
res.setHeader('Set-Cookie', ['name=keliq; Domain=test.com; Path=/set;', 'age=10'])
```

那么这个 Cookie 只能在目录 `/set` 以及子目录 `/set/xx/xx` 中共享。如果访问其他目录，例如根目录 `/` 和 `/get` 目录中是看不到的。

## Expires/Max-Age

这个属性是用得最多的，用于设置 Cookie 的有效期，如果没有设置，默认是 Session，即会话期间有效。所谓的「会话期间」是指当客户端被关闭时，cookie 就会被移除。但是一定要注意，这个不是严格意义上的浏览器关了，Cookie 就没了，因为：

> **很多 Web 浏览器支持会话恢复功能，用户重新打开浏览器的时候 cookie 也会恢复**。

Expires 用于指定具体的过期时间：

```js
res.setHeader('Set-Cookie', [
  `name=keliq; expires=${new Date(Date.now() + 10 * 1000).toGMTString()}`,
])
```

> **注意这里一定要是 `toGMTString`，写成 `toISOString` 的话被认为是会话级别的 Cookie。**

而 Max-Age 则以秒为单位设置多少秒之后过期，例如 10 秒后过期：

```js
res.setHeader('Set-Cookie', ['name=keliq; max-age=10;'])
```

不过需要注意：

> **如果 Max-Age 和 Expires 同时存在，那么 Max-Age 优先级更高。**

## HttpOnly

设置了 HttpOnly 属性的 Cookie `不能被 JavaScript 获取到`，这是非常安全的，建议后端设置的 Cookie 都带上 HttpOnly 属性，例如：

```js
res.setHeader('Set-Cookie', ['name=keliq; httpOnly=true;', 'age=10'])
```

这个时候通过 `document.cookie` 是获取不到 name 值的，只能得到 age。

虽然加上 HttpOnly 是足够安全了，但是并非绝对安全，虽然 JS 代码获取不到了，但是可以手动改浏览器中的 Application/Cookies 啊，改完之后 JS 又能获取到了。当然，攻击者不太可能操作你的浏览器，于是他的 JS 攻击代码就失效了。

## Secure

这是 Cookie 的安全属性，只有当使用 SSL 和 HTTPS 协议的时候才会被发送。如果服务器是 HTTP 的，但是设置了 Secure，那么客户端是收不到这个 Cookie 的，例如：

```js
res.setHeader('Set-Cookie', ['name=keliq; httpOnly=true; Secure;', 'age=10; Secure;'])
```

浏览器会提示这个警告⚠️

```js
This Set-Cookie was blocked because it had the "Secure" attribute but was not received over a secure connection.
```

严格意义上来讲，是浏览器拒绝接受这个 Cookie，实际上 Set-Cookie 请求头里面是有这些数据的。

![](../../\images\cookie-2.png)

## SameSite

该属性用于限制第三方 Cookie 的发送场景，可以取值：

- Strict：完全禁止第三方 Cookie，跨站点时，任何情况下都不会发送 Cookie。
- Lax：默认值。除了下面三种情况外，不发送第三方 Cookie
  - 链接：`<a href="..."></a>`
  - 预加载请求：`<link rel="prerender" href="..."/>`
  - GET 表单：`<form method="GET" action="...">`
- None：跨站都发送 Cookie

需要注意的是，如果设置为 `None` 的话，必须开启 `Secure` 属性，否则会提示这个警告⚠️

```js
This Set-Cookie was blocked because it had the "SameSite=None" attribute but did not have the "Secure" attribute, which is required in order to use "SameSite=None"
```

![](../../\images\cookie-3.png)

SameSite 属性可以防范 CSRF 攻击和用户追踪。
