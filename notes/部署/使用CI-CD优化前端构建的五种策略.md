# 使用 CI/CD 优化前端构建的五种策略

如今使用 CI/CD 工具是网页应用程序开发的一个必要条件。作为关键开发路径的一部分，加快构建系统的速度对于提高开发人员的生产效率是至关重要的。

因此，在这篇文章中，我们将带你了解五种使用 CI/CD 优化前端构建时间的不同策略。

## 使用并行网络包 Parallel-Webpack

> Parallel-Webpack 让你能够一边运行一边进行构建应用程序，以减少应用程序构建时间。

你可以通过使用以下 NPM 命令轻松开始使用 Parallel-Webpack：

```bash
npm install parallel-webpack —-save-dev
```

为了更好地了解 Parallel-Webpack 的配置，我们来看个简单的示例。

```js
var path = require('path');
module.exports = [{
  entry: './firstjob.js',
  output: {
    path: path.resolve(__dirname, './dist'),
   filename: 'task1.bundle.js'
  }
}, 
{
  entry: './secondjob.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'task2.bundle.js'
  }
}];
```

上面的配置包括两个独立的构建任务，分别是 `firstjob` 和 `secondjob`。Parallel-Webpack 会同时运行这两个构建任务，你会发现 `task1.bundle.js` 和 `task2.bundle.js` 同时被构建。

> Parallel-Webpack 允许你控制并行性，包括 Webpack 的普通功能，例如观察者（watcher）和重传限制（retry limit）。

### 控制并行性

有时，你可能想限制 Parallel-Webpack 可用的 CPU 核心数量。在这种情况下，你可以使用 `parallel-webpack -p=2` 命令指定可用的 CPU 核心数量。

### 运行观察者

让 Webpack 如此有影响力的功能之一是它的观察者，它可以持续地重建你的应用程序。你可以在 Parallel-Webpack 中毫不费力地使用同样的功能，只要在命令中加入 watch 标志即可。

```bash
parallel-webpack --watch
```

同样地，Parallel-Webpack 中有许多令人兴奋的功能，可以集成到你的 CI/CD 管道中，以便加快它的速度。你也可以在文档中找到更多有关信息。

## 将应用程序拆分成微前端

假设考虑传统的单体前端系统，它们中的大部分是只有一个构建管道和一个发布管道。因此，如果有一个错误修复或新功能更新，就有可能破坏 CI/CD 管道中的整个构建阶段。

然而，如果我们使用微前端，我们可以将应用程序的功能拆分，并独立维护应用程序的构建和发布管道，以便不断提交更新和修复错误。

![](../../\imgs\ci-cd-1.png)

通常，可以独立地整合和部署每个应用程序，让你更快地修复重要功能。因此，这确实对 CI/CD 流程的提速有很大帮助。

## 组件驱动型 CI：Ripple CI

组件驱动型 CI 是指只在修改过的组件和它们的所有依赖关系（即受影响的组件）上运行的 CI，它不把整个项目作为一个单独实体。Ripple CI 的典型示例是 Bit。

## 优化 Webpack 的性能

我们通常使用 Webpack 的默认设置。然而，你是否知道如何通过使用插件和自定义配置进一步优化它吗？

### 使用 `uglifyjs-webpack-plugin v1` 插件

压缩是指在你的网页中压缩代码、标记和脚本文件的过程。它是减少构建时间的主要方法之一。

但是，随着项目规模的扩大，这个修改过程本身也会花费相当多的时间。

如果项目正在构建，可以使用插件 `uglifyjs-webpack-plugin v1` 来优化构建时间。这个插件提供了多进程并行运行的能力和缓存支持，大大提升了构建效率。

### 在压缩模块的过程中使用加载器

Webpack 使用加载器将其他类型的文件转化为有效模块。然后，这些模块被应用程序接收，并添加到依赖关系图中。

> 因此，必须指定相关的文件目录，以减少不必要的模块加载。

在 Webpack 配置中，你可以通过 `include` 选项轻松指定文件目录。

```js
const path = require('path');

module.exports = {

  module: {
    rules: [
      {
        test: /\.js$/,
        include: path.resolve(__dirname, 'src'),
        loader: 'css-loader',
      },
    ],
  },
};
```

## NPM 模块安装的管道缓存

我们都知道，安装节点模块需要耗费时间。我们发现了这个问题，特别是在管道中耗费更多时间，因为它们每次运行都会安装节点模块。

> NPM 缓存是一种简单的缓存机制，我们可以在构建管道中使用，以避免每次都运行 npm 安装。

这种缓存机制将使你的构建管道与你的本地开发环境相似。你只需要安装一次节点模块，同样的模块将被用于后续的构建。

例如，让我们来看一个 NodeJS 项目的 Azure DevOps 管道。

为 NodeJs 项目缓存 NPM 模块的最推荐方式是使用 NPM 的共享缓存目录。这个目录包括所有下载模块的缓存版本。每当我们运行 `npm install` 命令时，NPM 会首先检查这个目录，并在其中获取存储的包。

**示例代码**

```yml
variables: 
npm_config_cache: $(Pipeline.Workspace)/.npm 

steps: 
— task: Cache@2 
  inputs: 
    key: ‘npm | “$(Agent.OS)” | package-lock.json’ 
    restoreKeys: | 
      npm | “$(Agent.OS)” 
    path: $(npm_config_cache) 
  displayName: Cache npm

— script: npm ci
```

## 本文总结

正如你已经了解到的，有五种技术可以加快前端应用程序的构建时间。此外，还有许多其他技术可能适合技术开发和工作流程。你应该选择适合用例的方法。

同时，我希望这里的讨论能帮助你理解以上策略，以加快 CI/CD 流程的前端构建时间。
