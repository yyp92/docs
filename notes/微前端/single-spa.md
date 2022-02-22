# single-spa

## 资料

[github](https://github.com/single-spa/single-spa)

[官网](https://zh-hans.single-spa.js.org/docs/getting-started-overview)

## 定义

前端微服务话的 JavaScript 前端解决方案（本身没有处理样式隔离，js执行隔离）实现了路由劫持和应用加载。

## 子应用

- 在vue项目中，则引用single-spa-vue。
- 在react项目中，则引用single-spa-react。

### 子应用的整个状态

```js
// 应用初始状态
NOT_LOADED

// 加载资源
LOADING_SOURCE_CODE

// 还没有调用bootstrap方法
NOT_BOOTSTRAPPED

// 启动中
BOOTSTRAPING

// 没有调用mount方法
NOT_MOUNTED

// 正在挂载中
MOUNTING

// 挂载完毕
MOUNTED

// 更新中
UPDATING

// 解除挂载
UNMOUNTEDING

// 完全卸载中
UNLOADING

// 资源加载失败
LOAD_ERR

// 代码出错
SKIP_BECAUSE_BROKEN
```

## 用法

```js
// 子应用
// 协议接入 - 订好了协议 - 父应用回调这些方法
export const bootstrap = () => {}
export const mount = () => {}
export const unmount = () => {}

// 如果是父应用引用我
if (window.singleSpaNavigate) {
  // 绝对路径，子应用的路径
    __webpack_public_path__ = 'http://localhost:10000/'
}

// 如果不是父应用引用-就是子应用独立启动
if (!window.singleSpaNavigate) {
    // 正常启动项目的操作
}

// 需要父应用加载子应用，将子应用打包成一个个的lib去父应用使用
// webpack
{
  output: {
    library: 'child-app',
    libraryTarget: 'umd'
  }
}



// 主应用
// 得有一个子应用的占位
<div id="child-app"></div>

// 注册子应用
import {registerApplication, start} from 'single-spa'

// 生成script标签加载js资源
const loadScript = async (url) => {
  return new Promise((resolve, reject) => {
    let script = document.createElement('script')
    script.src = url
    script.onload = resolve
    script.onerror = reject

    document.head.
    appendChild(script)
  })
}

// 注册
registerApplication(
  'childApp',
  // 第二个参数必须是promise，必须返回那个三个方法：bootstrap，mount，unmount
  acync () => {
    // 推荐 systemJS: 一个通用Javascript模块加载器，可以在浏览器和NodeJS中加载ES6模块AMD CommonJS 和全局脚本。
    // 必须先加载公共模块，再加载app.js

    // 公共模块
    await loadScript(`http://localhost:10000/js/chunk-vendor.js`)

    // app.js
    await loadScript(`http://localhost:10000/js/app.js`)

    // bootstrap，mount，unmount
    return window.singleChild
  },

  /*
      什么时候加载子应用
      eg: 用户切换到/child的路径下，我们需要加载刚才定义的子应用
  */
  location => location.pathname.startWith('/child'),

  // 传参，可以实现父子应用通信
  {aa: 1}
)

start()
```

## 优点

- 在同一页面上[使用多个前端框架](https://zh-hans.single-spa.js.org/docs/ecosystem#help-for-frameworks) [而不用刷新页面](https://zh-hans.single-spa.js.org/docs/building-applications) ([React](https://zh-hans.single-spa.js.org/docs/ecosystem-react), [AngularJS](https://zh-hans.single-spa.js.org/docs/ecosystem-angularjs), [Angular](https://zh-hans.single-spa.js.org/docs/ecosystem-angular), [Ember](https://zh-hans.single-spa.js.org/docs/ecosystem-ember), 你正在使用的框架)
- 独立部署每一个单页面应用
- 新功能使用新框架，旧的单页应用不用重写可以共存
- 改善初始加载时间，迟加载代码

## 缺点

- 不够灵活，不能动态加载js文件
- 样式不隔离，没有js沙箱的机制
