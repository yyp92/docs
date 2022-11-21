# vscode插件

*创建代码块的快捷键 --> ```[语言名]*

## 防止flow语言提示错误

在设置(最好在当前工作区下) --> "javascript.validate.enable" --> 设置成false

## react模板 - (ES7)

```js
// rafce --> react arrow function component
// $1 默认是文件的名称

import React from 'react'

const $1 = () => {
  return <div>$0</div>
}

export default $1
```

## React Hooks Snippets

react hooks的快捷键。

## Postcode - vscode postman

```js
// 快捷键
command + p  --> 输入 >create  --> 选择如下图所示
```

![vscode-postcode](../../imgs/vscode-postcode.png)

## Markdown All in One

```js
// 在线编辑
```

## Draw.io Integration

```js
// 流程图
支持编辑 .drawio, .dio, .drawio.svg 或者 .drawio.png 文件
```

## Pretty JSON

```js
// 使用
格式化JSON文件，在JSON文件内打开右键菜单会发现多了一个选项【Format Document】快捷键为【ALT+SHILF+F】。这时就可以格式美化JSON数据啦。
```

## GitLens

```js
// 代码中每行自动会显示，Git提交的信息
```

## Git History

```js
// command + p --> 输入：> Git V 
// Git: View File History --> 显示文件夹的提交历史
// Git: View History(git log) --> 显示整体的提交记录
```

![](../../imgs/vscode-git.png)

效果：

![vscode-git-result](../../imgs/vscode-git-result.png)

## Local History

```js
// 本地保存文件修改，会在项目根目录生成一个.history文件
```

## viscose-icons

### Material Icon Theme（更好）

```js
// 文件图标
```

## TODO Heighlight

```js
// todo 高亮
```

## open in browser

```js
// 打开浏览器
```

## Docker

```js
// docker 镜像服务器
```

## Debugger for Chrome

```js
// chrome 断点调试
```

## Code Runner

```js
// 本地运行代码
```

## Chinese (Simplified) Language

```js
// 汉语化
```

## Beautify

```js
// 代码格式化
```

## Auto Close Tag

```js
// 自动闭合标签
```

## px to rem & rpx (cssrem)

```js
// px 转化 rem和rpx
```

## Import Cost

```js
// 该插件会在行尾显示导入的包的大小。为了计算包大小，该插件要使用 Webpack 和 babili-webpack-plugin。
```

## Console helper

```js
// 帮助开发者更快的输入/移除 log，醒目的主题搭配，极大的提高开发效率
// 快捷键 macOS: cmd + shift + l
```

## JSON TS

```js
// json转为ts类型，转换接口返回的数据很有用
// Shift + Control + option + s

// eg
{
    "qq": "122",
    "bb": [
        "1",
        2,
        5
    ]
}

// 转后
interface RootObject {
  qq: string;
  bb: (number | string)[];
}
```

## Code Spell Checker

```js
// 检验单词是否书写错误
```

## mindmap

```bash
# 
```

## bookmarks

```bash
# 再代码行上打上书签，方便调试
```

## Svg Preview

效果预览，需要选中 `.svg` 后缀的文件，右上角才会有按钮进入预览。

## Image preview

在链接上悬浮显示图片。

## Prettier

美化工具。

## Flow Language Support（不重要）

支持facebook flow(类似于ts)语言，看react源码不提示类型错误。

## CodeSnap

CodeSnap 是一个代码截图插件，只需选中项目中相应的代码段，即可快速创建代码的截图。

## CSS Peek

CSS Peek 插件允许我们在 HTML 中选择某个 class 或者 id 名称按住Ctrl键+鼠标左键可以直接定位到该名称的CSS的位置。

## vscode-mindmap

xmind编辑图。

## better comments

代码注释高亮。

正常注释，前面使用相应的前缀即可（vs code 会结合插件自动用配置的颜色去渲染，插件自带5种高亮注释）

```js
// ! 红色的高亮注释  
// ? 蓝色的高亮注释  
// * 绿色的高亮注释  
// todo 橙色的高亮注释  
// // 灰色带删除线的注释  
// 普通的注释

/**  
 // ! 红色的高亮注释  
 // ? 蓝色的高亮注释  
 // * 绿色的高亮注释  
 // todo 橙色的高亮注释  
 // // 灰色带删除线的注释  
*/
```

## Regex Previewer

这是一个用于实时测试正则表达式的实用工具。它可以将正则表达式模式应用在任何打开的文件上，并高亮所有的匹配项。

## VS Code JavaScript(ES6) snippets

当前最流行的，已有超过 120 万的下载量。这个插件为 JavaScript、TypeScript、HTML、React 和 Vue 提供了 ES6 的语法支持。

## Git Blame
可以快速的查看某一行最近的一次修改是谁、什么时候、哪次提交修改的
