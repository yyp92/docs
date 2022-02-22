## api

```js
// nodejs
// 方法会返回 Node.js 进程的当前工作目录
process.cwd() 

// 方法会将路径或路径片段的序列解析为绝对路径, ('/'、'./'、'../')
path.resolve()

// 流（stream）是 Node.js 中处理流式数据的抽象接口。 stream 模块用于构建实现了流接口的对象。
stream




// koa
// 一个提供静态资源访问的中间件
koa-static
```

## path

```js
// path.join 和 path.resolve 的区别
// 路径：带有/, 非路径：不带/
// 1. join是把各个path片段连接在一起， resolve把‘／’当成根目录
// /a/b
path.join('/a', '/b'); 
// /b
path.resolve('/a', '/b');

// 2. resolve在传入非/路径时，会自动加上当前目录形成一个绝对路径，而join仅仅用于路径拼接
// 当前路径为 /Users/xiao/work/test
// a/d
path.join('a', 'b', '..', 'd');

// /Users/xiao/work/test/a/d
path.resolve('a', 'b', '..', 'd');
```

## 第三方包

```js
// 魔术字符串，把字符串变为对象
// github: https://github.com/Rich-Harris/magic-string
magic-string 


// es-module-lexer 是一个可以对 ES Module 语句进行词法分析的工具包
es-module-lexer
```

## v8引擎

```bash
# 该命令可以用来查看node中可用的V8引擎的选项及其含义
node --v8-options

# 设置新生代内存中单个半空间的内存最小值，单位MB
node --min-semi-space-size=1024 xxx.js

# 设置新生代内存中单个半空间的内存最大值，单位MB
node --max-semi-space-size=1024 xxx.js

# 设置老生代内存最大值，单位MB
node --max-old-space-size=2048 xxx.js

# 可以查看当前node进程所占用的实际内存大小
process.memoryUsage()
# heapTotal：表示V8当前申请到的堆内存总大小。
# heapUsed：表示当前内存使用量。
# external：表示V8内部的C++对象所占用的内存。
# rss(resident set size)：表示驻留集大小，是给这个node进程分配了多少物理内存，这些物理内存中包含堆，栈和代码片段。对象，闭包等存于堆内存，变量存于栈内存，实际的JavaScript源代码存于代码段内存。使用Worker线程时，rss将会是一个对整个进程有效的值，而其他字段则只针对当前线程。
```

# 
