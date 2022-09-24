# nodejs模块

## package.json版本号规则

**major.minor.patch**

- 主版本号.次版本号.修补版本号(`major.minor.patch`)
- `major`：新的架构调整，不兼容老版本
- `minor`：新增功能，兼容老版本
- `patch`：修复`bug`，兼容老版本

**~和^的区别**

- `~`会匹配最近的小版本依赖包，比如`~1.2.3`会匹配所有`1.2.x`版本，但是不包括`1.3.0`
- `^`会匹配最新的大版本依赖包，比如`^1.2.3`会匹配所有`1.x.x`的包，包括`1.3.0`，但是不包括`2.0.0`
- `*` 安装最新版本的依赖包，比如 `*1.2.3` 会匹配 `x.x.x`

> 那么该如何选择呢？当然你可以指定特定的版本号，直接写`1.2.3`，前面什么前缀都没有，这样固然没问题，但是如果依赖包发布新版本修复了一些小bug，那么需要手动修改`package.json`文件；`~` 和 `^` 则可以解决这个问题

> - 但是需要注意 `^`版本更新可能比较大，会造成项目代码错误，所以 建议使用 `~`来标记版本号，这样可以保证项目不会出现大的问题，也能保证包中的小bug可以得到修复。
> - 版本号写 `*`，这意味着安装最新版本的依赖包，但缺点同上，可能会造成版本不兼容，慎用

我们举个例子：

> - 假设我们中安装了 `vue`, 当我们运行安装 `npm install vue -save`的时候，在项目中的`package.json` 的 `vue`版本是 `vue: ^3.0.0`, 我们电脑安装的`vue`版本就是 `3.0.0` 版本，我们把项目代码提交后，过了一段时间，`vue` 发布了新版本 `3.0.1`，这时新来一个同事，从新 `git clone`克隆项目，执行 `npm install`安装的时候，在他电脑的`vue`版本就是 `3.0.1`了，因为`^`只是锁了主要版本，这样我们电脑中的`vue`版本就会不一样，从理论上讲（大家都遵循语义版本控制的话），它们应该仍然是兼容的，但也许 `bugfix` 会影响我们正在使用的功能，而且当使用`vue`版本`3.0.0`和`3.0.1`运行时，我们的应用程序会产生不同的结果。
> - 大家思考思考，这样的话，不同人电脑安装的依赖版项目，是不是都有可能不一样，就会导致每个人电脑运行的应用程序产生不同的结果。就会存在bug的隐患。
> - 这时也许有同学想到，那么我们在package.json上面锁死依赖包的版本号不就可以了? 直接写 `vue: 3.0.0`锁死，这样大家安装`vue`的版本都是`3.0.0`版本了。
> - 这个想法固然是不错的，但是你只能控制你自己的项目锁死版本号，那你项目中依赖包的依赖包呢？你怎么控制限制别人锁死版本号呢？
> - 为了解决这个不同人电脑安装的所有依赖版本都是一致的，确保项目代码在安装所执行的运行结果都一样，这时 `package-lock.json` 就应运而生了

## package.json 与 package-lock.json 的关系

`package-lock.json` 是在 `npm(^5.x.x.x)`后才有，中途有几次更改

> `package-lock.json` 它会在 `npm` 更改 `node_modules` 目录树 或者 `package.json` 时自动生成的 ，它准确的描述了当前项目`npm`包的依赖树，并且在随后的安装中会根据 `package-lock.json` 来安装，保证是相同的一个依赖树，不考虑这个过程中是否有某个依赖有小版本的更新

**它的产生就是来对整个依赖树进行版本固定的（锁死）**

- 当我们在一个项目中`npm install`时候，会自动生成一个`package-lock.json`文件，和`package.json`在同一级目录下。`package-lock.json`记录了项目的一些信息和所依赖的模块。这样在每次安装都会出现相同的结果. 不管你在什么机器上面或什么时候安装
- 当我们下次再`npm install`时候，`npm` 发现如果项目中有 `package-lock.json` 文件，会根据 `package-lock.json` 里的内容来处理和安装依赖而不再根据 `package.json`

注意

使用`cnpm install`时候，并不会生成 `package-lock.json` 文件，也不会根据 `package-lock.json` 来安装依赖包，还是会使用 `package.json` 来安装。

**`package-lock.json` 可能被意外更改的原因**

- `package.json` 文件修改了
- 挪动了包的位置：将部分包的位置从 `dependencies` 移动到 `devDependencies` 这种操作，虽然包未变，但是也会影响 `package-lock.json`

## npm 模块安装机制

- 发出 `npm install` 命令 `1` 查询 `node_modules` 目录之中是否已经存在指定模块
- 若存在，不再重新安装
- 若不存在
- `npm` 向 `registry` 查询模块压缩包的网址
- 下载压缩包，存放在根目录下的`.npm` 目录里
- 解压压缩包到当前项目的 `node_modules` 目录

## 模块化的差异 AMD CMD COMMONJS ESMODULE

- `AMD`是依赖前置，`define`写法
- `CMD` 语法`require`
- `AMD`和`CMD`是动态引入，运行时才知道的
- `ESMODULE`是静态引入，好处方便`wepback`打包依赖图谱分析
- `CommonJs` 是单个值导出，`ES6 Module` 可以导出多个
- `CommonJs` 是动态语法可以写在判断里，`ES6 Module` 静态语法只能写在顶层
- `CommonJs` 的 `this` 是当前模块，`ES6 Module`的 `this` 是 `undefined`

## Node 的 Event Loop: 6个阶段

- `timer` 阶段: 执行到期的`setTimeout / setInterval`队列回调

- `I/O` 阶段: 执行上轮循环残流的`callback`

- `idle`, `prepare`

- `poll`: 等待回调
  
  - 1. 执行回调
  
  - 2. 执行定时器
    - 如有到期的`setTimeout / setInterval`， 则返回 `timer` 阶段
    - 如有`setImmediate`，则前往 `check` 阶段

- `check`
  
  - 执行`setImmediate`

- `close callbacks`
