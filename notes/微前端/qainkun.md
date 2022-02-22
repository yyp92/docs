# 乾坤 - 蚂蚁

## 资料

[官网](https://qiankun.umijs.org/zh)

## 定义

基于single-spa提供了开箱即用的api（single-spa + sandbox + import-html-entry）。

## 解决方案

![qiankun](../../imgs/qiankun.jpeg)

## 特点

子应用可以独立构建，运行时动态加载，主子应用完全解耦，技术栈无关，靠的是协议接入（子应用必须导出 bootstrap、mount、unmount方法）。

## 这不是 iframe 吗？

- 如果使用 iframe，iframe 中的子应用切换路由时用户刷新页面就尴尬了。

## 应用通信 - （看业务场景选择合适的通信方案）

- 基于URL来进行数据传递，但是传递消息能力弱
- 基于 CustomEvent（原生api） 实现通信
- 基于 props 主子应用间通信
- 使用全局变量、Redux 进行通信

## 公共依赖

- CDN - externals
- webpack5 联邦模块（*2021-08-30 可能版本不太稳定，待确认？？*）

### 分类

#### 基础库（React，React Router - 期望在整个访问周期中不要重复加载的资源）

在集成访问时，主应用可以动态加载子应用强依赖的库，子应用自身不再加载；独立访问时，子应用本身又可以自主加载自身需要的依赖。

**此时需要处理好两个问题**：

- 主应用怎么搜集和动态加载子应用的依赖
  
  - 我们需要维护一个公共依赖的定义，即在主应用中定义每个子应用所依赖的公共资源，在 qiankun 的全局微应用生命周期钩子 beforeLoad 中通过插入 `<script>` 标签的方式，加载当前子应用所需的 Javascript 资源。
    
    ```js
    // 定义子应用的公共依赖
    const dependencies = {
        look_backend: ['react', 'react-dom']
    };
    
    // 返回依赖名称
    const getDependencies = appName => dependencies[appName];
    
    // 构建script标签
    const loadScript = (url) => {
        const script = document.createElement('script');
        script.type = 'text/javascript';
        script.src = url;
          // 避免重复加载
        script.setAttribute('ignore', 'true');
        script.onerror = () => {
            Message.error(`加载失败${url}，请刷新重试`);
        };
    
        document.head.appendChild(script);
    };
    
    // 加载某个子应用前加载当前子应用的所需资源
    beforeLoad: [
        (app) => {
            getDependencies(app.name).forEach((dependency) => {
                loadScript(`${window.location.origin}/${$config.rootPath}${dependency}.js`);
            });
        }
    ]
    ```
  
  - 用Webpack 来生产好相应的依赖资源，我们使用的是 copy-webpack-plugin 插件将 node_modules 下的 release 资源转换成包可以通过独立 URL 访问的资源。
    
    ```js
    // 开发
    plugins: [
        new webpack.DefinePlugin({
            'process.env': {
                NODE_ENV: JSON.stringify('development')
            }
        }),
        new webpack.NoEmitOnErrorsPlugin(),
        new CopyWebpackPlugin({
            patterns: [
                { from: path.join(__dirname, '../node_modules/react/umd/react.development.js'), to: '../s/react.js' },
                { from: path.join(__dirname, '../node_modules/react-dom/umd/react-dom.development.js'), to: '../s/react-dom.js' }
            ]
        })
    ],
    
    // 生产
    new CopyWebpackPlugin({
        patterns: [
            { from: path.join(__dirname, '../node_modules/react/umd/react.production.js'), to: '../s/react.js' },
            { from: path.join(__dirname, '../node_modules/react-dom/umd/react-dom.production.js'), to: '../s/react-dom.js' }
        ]
    })
    ```

- 子应用怎么做到集成和独立访问时对资源加载的不同表现
  
  - 子应用集成和独立访问时，对公共依赖的二次加载问题，我们采用的方法是，首先子应用将主应用已经定义的公共依赖通过 ***copy-webpack-plugin*** 和 ***html-webpack-externals-plugin*** 这两个插件使用 external 的方式独立出来，不打包到 Webpack bundle 中，同时通过插件的配置，给 `<script>` 标签加上 ignore 属性，那么在 qiankun 加载这个子应用时使用，qiankun 依赖的 import-html-entry 插件分析到 `<script>` 标签时，会忽略加载有 ignore 属性的 `<script>` 标签，而独立访问时子应用本身可以正常加载这个 Javascript 资源。
    
    ```js
    plugins: [
        new CopyWebpackPlugin({
            patterns: [
                { from: path.join(__dirname, '../node_modules/regularjs/dist/regular.js'), to: '../s/regular.js' },
                { from: path.join(__dirname, '../node_modules/regular-state/restate.pack.js'), to: '../s/restate.js' },
            ]
        }),
    
          // 基础库不打包，配置选项提供了「从输出的 bundle 中排除依赖」的方法
        new HtmlWebpackExternalsPlugin({
            externals: [{
                module: 'remoteEntry',
                entry: 'http://localhost:3000/remoteEntry.js'
            }, {
                module: 'regularjs',
                entry: {
                    path: 'http://localhost:3001/regular.js',
                    attributes: { ignore: 'true' }
                },
                global: 'Regular'
            }, {
                module: 'regular-state',
                entry: {
                    path: 'http://localhost:3001/restate.js',
                    attributes: { ignore: 'true' }
                },
                global: 'restate'
            }],
        })
    ],
    ```

#### 公共组件 （React 组件需要在各子应用之间互相共享，不需要进行工程间的代码拷贝）

##### 调研了 Webpack 5 的 module federation plugin， 通过应用之间引用对方导入导出的 Webpack 编译公共资源信息，来异步加载公共代码，从而实现代码共享。

- 主应用同时提供基于 react的Button 组件分别共享给 子应用。

- 对于主应用，我们定义 Webpack5 module federation plugin 如下:
  
  ```js
  // 主应用
  plugins: [
      new ModuleFederationPlugin({
        // 必须， 唯一ID，用于标记当前服务，作为输出的模块名，使用的时通过 {name}/{expose} 的方式使用
        name: 'app2',
  
        // 必须，其中这里的 name 为作为 umd 的 name；
        library: {
          // 通用模块定义
          type: 'umd',
          name: 'app2'
        },
  
        // 必须， 提供给其他服务加载的文件
        filename: 'remoteEntry.js',
  
        // 可选，需要暴露的模块，使用时通过 `${name}/${expose}` 引入
        exposes: {
          TButton: path.join(__dirname, '../client/exports/rgbtn.js'),
          RButton: path.join(__dirname, '../client/exports/rcbtn.js'),
        },
  
        // 可选，作为引用方最关键的配置项，用于声明需要引用的远程资源包的名称与模块名称，作为 Host 时，去消费哪些 Remote；
        remotes: {
          app1: "app1@http://localhost:3001/remoteEntry.js",
        },
  
        /*
            可选
            公共依赖 - 避免项目出现多个公共依赖
            1. 若是配置了这个属性。webpack在加载的时候会先判断本地应用是否存在对应的包，若是不存在，则加载远程应用的依赖包。
            2. 以app1来讲，由于它是一个远程应用，配置了["react"] ，而它被app1所消费，因此webpack会先查找app1是否存在这个包，若是不存在就使用app2自带包。 app2(app2 之后会以案例的形式展现)里面一样申明了这两个参数，由于app2是本地应用，因此会直接用app2的依赖 在引用远程资源的项目中使用时，需要先远程资源入口文件引入，可以异步加载，也可以使用script标签引入。
        */
        // shared: ['react', 'regularjs']
        shared: {
          react: {
            // 所需的软件包版本
            requiredVersion: deps.react,
            // 此提示仅允许共享范围内的共享模块的单个版本（默认情况下禁用）。一些库使用全局内部状态（例如 react、react-dom）。因此，一次只运行一个库实例至关重要。
            singleton: true,
          }
        }
      }),
  ],
  
  // 使用 
  import("app2/TButton")
  ```
  
  - module federation plugin（**双向共享**） - 🌰
    
    - 提供了 `exposes` 选项的表示当前应用是一个 `Remote`，`exposes` 内的模块可以被其他的 `Host` 引用，引用方式为 `import(${name}/${expose})`。
    - 提供了 `remotes` 选项的表示当前应用是一个 `Host`，可以引用 `remote` 中 `expose` 的模块。
    
    ```js
    // 项目 B 的 webpack 配置
    const { ModuleFederationPlugin } = require("webpack").container;
    module.exports = {
      plugins: [
        new ModuleFederationPlugin({
          // 提供给其他服务加载的文件
          filename: "remoteEntry.js",
          // 唯一ID，用于标记当前服务
          name: "app2",
          // 需要暴露的模块，使用时通过 `${name}/${expose}` 引入
          exposes: {
            "./NewsList": "./src/NewsList",
          }
        })
      ]
    };
    
    // 项目 A 的 webpack 配置
    const { ModuleFederationPlugin } = require("webpack").container;
    module.exports = {
      plugins: [
        new ModuleFederationPlugin({
          name: "app1",
          // 引用 app2 的服务
          remotes: {
            app2: "app2@http://localhost:3002/remoteEntry.js",
          }
        })
      ]
    };
    ```
    
    ```js
    // A项目
    // 引入项目B的组件
    const RemoteNewsList = React.lazy(() => import("app2/NewsList"));
    ```

## 基座（主应用）

### 路由

- 自己的路由
- 引用其他子应用的路由

### 用法

```js
// 只需要在基座（主应用）中安装
import {registerMicroApps, start} from 'qiankun'

/*
    当微应用都是 hash 模式时可以使用 hash 区分微应用，主应用的路由模式不限。
    注册微应用时 activeRule 需要这样写：
*/
const getActiveRule = (hash: string) => (location: Location):boolean => location.hash.startsWith(hash)

const apps = [
  {
    // 子应用名字，package.json中的name
    name: 'childApp',

    // 默认会加载这个html，解析里面的js, 动态执行（子应用必须支持跨域，qiankun用fetch来读取js文件）
    entry: '//localhost: 10000,

       // 挂载容器
    container: '#child-app',

    // 激活的路径
    // 这里也可以直接写 activeRule: '#/childApp'，但是如果主应用是 history 模式或者主应用部署在非根目录，这样写不会生效。
    activeRule: getActiveRule('#/childApp'),

      // 传给子应用的数据
      props: {test: 'test'}
  }
]

// 注册应用
registerMicroApps(apps)

// 启动
start({
  // 取消预加载
  prefetch: false
})
```

### 子应用

```js
// react 
// .env 文件配置react的环境变量
PORT=20000
// 防止热更新出错
WDS_SOCKET_PORT=20000

// create-react-app
// 根目录 config-overrides.js
module.exports = {
  webpack: (config) => {
    config.output = {
      ...config.output,
      library: 'reactApp',
      libraryTarget: 'umd',
      publicPath: 'http://localhost: 20000/'    
    }

    return config
  },

  devServer: (configFunction) => {
    return function(proxy, allowedHost) {
      const config = configFunction(proxy, allowedHost)

      config.headers = {
        "Access-Control-Allow-Origin": '*'
      }
    }

    return config
  }
}

// 入口文件 index.js
import ReactDom from 'react-dom'

// publickPath
if (window.__POWERED_BY_QIANKUN__) {
    __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
}

// 挂载容器
const getContainer = (container: any) => {
  const id = '#sub'

  return container
    ? container.querySelector(id)
    : document.querySelector(id)
}

const render = (props) => {
  const {
    container,
    onGlobalStateChange
  } = props

  ReactDom.render(
    <App />,
    getContainer(container)
  )
}

// 不是主应用调用
if (!window.__POWERED_BY_QIANKUN__) {
    render({})
}

export async function bootstrap(props) {}

export async function mount(props) {
  render(props)
}

export async function unmount(props) {
  const {container} = props
  ReactDom.unmountComponentAtNode(getContainer(container))
}


// 子应用的webpack配置
output: {
  publicPath: devMode
    ? '/'
      : '线上部署的资源地址',
     // 这里改成跟主应用中注册的一致， packageName --> package.json中对应的name
  library: `${packageName}-[name]`,
  libraryTarget: 'umd',
  jsonpFunction: `webpackJsonp_${packageName}`,

  // 全局对象指向window
  globalObject: 'window'
}
```

## 部署

### 主应用和微应用部署到同一个服务器（同一个 IP 和端口）

- 主应用部署在一级目录，微应用部署在二/三级目录。

- 微应用想部署在非根目录，在微应用打包之前需要做两件事：
  
  - 必须配置 `webpack` 构建时的 `publicPath` 为**目录名称**。
  - `history` 路由的微应用需要设置 `base` ，值为**目录名称**，用于独立访问时使用。

- 部署之后注意三点：
  
  - `activeRule` 不能和**微应用的真实访问路径一样**，否则在主应用页面刷新会直接变成微应用页面。
  - 微应用的真实访问路径就是微应用的 `entry`，`entry` 可以为相对路径。
  - 微应用的 `entry` 路径最后面的 `/` 不可省略，否则 `publicPath` 会设置错误，例如子项的访问路径是 `http://localhost:8080/app1`,那么 `entry` 就是 `http://localhost:8080/app1/`。

### 目录结构

- 方案 1：微应用都放在在一个特殊名称（**不会和微应用重名**）的文件夹下（官网-**建议使用**）。
  
  ```BASH
  └── html/                     # 根文件夹
      |
      ├── child/                # 存放所有微应用的文件夹
      |   ├── vue-hash/         # 存放微应用 vue-hash 的文件夹
      |   ├── vue-history/      # 存放微应用 vue-history 的文件夹
      |   ├── react-hash/       # 存放微应用 react-hash 的文件夹
      |   ├── react-history/    # 存放微应用 react-history 的文件夹
      |   ├── angular-hash/     # 存放微应用 angular-hash 的文件夹
      |   ├── angular-history/  # 存放微应用 angular-history 的文件夹
      ├── index.html            # 主应用的index.html
      ├── css/                  # 主应用的css文件夹
      ├── js/                   # 主应用的js文件夹
  ```
  
  此时需要设置微应用构建时的 `publicPath` 和 `history` 模式的路由 `base`，然后才能打包放到对应的目录里。

- 方案 2：微应用直接放在二级目录，但是设置特殊的 `activeRule`。
  
  ```bash
  └── html/                     # 根文件夹
      |
      ├── vue-hash/             # 存放微应用 vue-hash 的文件夹
      ├── vue-history/          # 存放微应用 vue-history 的文件夹
      ├── react-hash/           # 存放微应用 react-hash 的文件夹
      ├── react-history/        # 存放微应用 react-history 的文件夹
      ├── angular-hash/         # 存放微应用 angular-hash 的文件夹
      ├── angular-history/      # 存放微应用 angular-history 的文件夹
      ├── index.html            # 主应用的index.html
      ├── css/                  # 主应用的css文件夹
      ├── js/                   # 主应用的js文件夹
  ```
  
  基本操作和上面是一样的，只要保证 `activeRule` 和微应用的存放路径名不一样即可。

### 注意问题

- 主应用，微应用都是 `history` 路由，需要解决刷新 404 的问题
  
  ```bash
  # nginx 配置
  server {
    listen       8080;
    server_name  localhost;
  
    location / {
      root   html;
      index  index.html index.htm;
      try_files $uri $uri/ /index.html;
    }
  
    location /child/vue-history {
      root   html;
      index  index.html index.htm;
      try_files $uri $uri/ /child/vue-history/index.html;
    }
    # angular 和 react 的history 配置同上
  }
  ```
