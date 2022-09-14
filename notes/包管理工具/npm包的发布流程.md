# npm 包的发布流程

## 代码准备

每个人要发布的npm包类型都不尽相同，有UI组件库，有工具函数库，还有使用的插件等。笔者要发布的npm包是在项目中常用的工具函数组成的工具函数库，构建工具使用的是rollup，代码托管在github上。下面简述一下一些关键点：

- 首先在github上新建仓库，新建仓库时License 选择MIT, 此步骤不选择也无妨，后续添加license也可以。但是一定要有License才能发布npm包。

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-1.jpg)

- 拉取代码到本地

- 初始化项目，安装依赖等

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-2.jpg)

- 完善功能

- 打包，并在package.json中指明入口

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-3.jpg)

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-3-1.jpg)

另外如果发布公有包需要在package.json中增加publishConfig的配置

```json
"publishConfig": {
  "access": "public"
},
```

更多关于项目的搭建以及一些配置方面的内容建议阅读文末的参考资料。

## 账号注册

先看下图了解注册的流程：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-4.jpg)

网址：www.npmjs.com/signup[2]

输入网址后会进行安全性检查，之后界面如下：

![](../../\imgs\npm-publish-5.jpg)

点击"我是人类" 会进行图片验证，如下图：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-6.jpg)

图片验证完就是输入用户名、密码、邮箱过程，

最后让输入one-time-password，这个一次性密码（相当于验证码）会发到你预留的邮箱里面。填写之后应该会注册成功的。

## npm包发布

### 登录npm账号

#### 登录失败

执行`npm login` 命令登录npm ：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-6-1.jpg)

如上图所示，登录失败了。解决办法：使用nrm切换镜像，将镜像改为npm。下面简要介绍一下nrm。

#### nrm 介绍

nrm 用于管理镜像，是一个可以切换npm镜像的管理工具。如下是安装和查看是否安装成功的命令：

```bash
npm i -g nrm
nrm -V
```

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-6-2.jpg)

常用nrm命令如下：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-7.jpg)

想了解更多关于nrm[3]的内容请查看文档和资料[4]。

下图是使用nrm ls命令查看镜像：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-8.jpg)

下图是将镜像切换为npm：

![](../../\imgs\npm-publish-9.jpg)

#### 成功登录

切换镜像之后再登录：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-10.jpg)

如上图登录时需要输入OTP，要查看邮箱。输入OTP回车之后就可以成功登录了

### 如何发布npm包

#### 首次发布成功

登录成功之后即可执行发布命令：`npm publish`，如下图：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-11.jpg)

此时npm包发布成功了。

#### 名字相似发布失败

但是感觉名字'mxdevutil'可读性不咋好啊，所以改了一下名字，新名字为'mx-dev-util'重新发布，但却报错，如下图所示：

![](../../\imgs\npm-publish-12.jpg)

上图报错信息告诉我们：新包的名字和已有的包名字很相似，所以没有发布成功。解决的方法之一是可以起区分度较大的名字，但删掉重新发布更好。

### 如何删除npm 包

#### 废弃npm 包

查资料所如下命令可以删掉发布错误的npm包：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-13.jpg)

但实际上此命令是表示废弃已发布的npm包，并不是删除。

在npm网站上仍然能够查到已废弃的npm包，如下图所示：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-14.jpg)

废弃之后是否可以发布成功呢？重新执行npm publish

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-15.jpg)

执行结果如下图：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-16.jpg)

还是报错，所以单单废弃原有包还是不能发布新包的。

#### 删除npm包

正确的解决办法是：`npm unpublish <包名> \-force` ,命令执行效果：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-17.jpg)

再在npm网站上查找则查不到了：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-18.jpg)

删掉已发布的包之后，终于可以重新发布了：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-19.jpg)

发布成功！在npm网站上也能够看到新包了：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-20.jpg)

### 如何使用npm包

首先安装我们发布的npm包，执行命令 `npm i mx-dev-util`, 如下图：

![](../../\imgs\npm-publish-21.jpg)

可以查看package.json文件，返现已经将mx-dev-util加添为dependiences:

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-22.jpg)

接着就是在项目中使用npm包提供的方法了：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-23.jpg)

### 更新npm包版本

更新npm包两步走：

- 第一步：执行npm version <版本号类型>

- 第二步：执行 npm publish

#### npm version介绍

npm version命令使用方式如下：

```bash
npm version  major | minor | patch | premajor | preminor | prepatch | prerelease
```

这里简单介绍一下major | minor | patch 的区别：在package.json中有一个version字段，结构为 "x.y.z" ，也就是三位的版本号。分别对应 version里面的major、minor，patch。

如果当前版本为 0.0.1 则发布major、minor，patch版本之后会变为 1.0.0 ，0.1.0， 0.0.2。导图总结如下：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-24.jpg)

了解更多可查看npm version文档和相关资料[5]。

了解了npm version命令之后，执行`npm version major` :

![](../../\imgs\npm-publish-25.jpg)

执行命令失败，提示需要先提交代码，下图为提交代码过程：

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-26.jpg)

提交代码后，再执行版本更新命令：

![](../../\imgs\npm-publish-27.jpg)

#### 改版后发布

提示版本已经更新为2.0.0版本，然后执行 `npm publish` 命令：

![](../../\imgs\npm-publish-28.jpg)

可以看到版本更新成功。

下面总结一下用到发布npm包用到的npm命令

![](C:\Users\Administrator\Desktop\docs\imgs\npm-publish-29.jpg)

## 总结

- 本文介绍发布一个npm包的三个关键环节：
  
  - 发布内容。也就是代码，这是npm包的基础
  
  - 注册npm账号。这是能够成功执行npm 发布命令的前提
  
  - npm包发布。掌握npm 包发布的这些命令是关键

- 本文介绍了如何使用nrm 切换npm的镜像

## 参考资料：

[1] [npm包发布详细教程](https://link.juejin.cn/?target=https%3A%2F%2Fblog.csdn.net%2Fu010059669%2Farticle%2Fdetails%2F109715342 "https://blog.csdn.net/u010059669/article/details/109715342")

[2] [如何发布自己的npm包（超详细步骤，博主都在用）](https://link.juejin.cn/?target=https%3A%2F%2Fblog.csdn.net%2Fqq_33286909%2Farticle%2Fdetails%2F108421181 "https://blog.csdn.net/qq_33286909/article/details/108421181")

[3] [如何发布一个npm包](https://link.juejin.cn/?target=https%3A%2F%2Fblog.csdn.net%2Fweixin_45032067%2Farticle%2Fdetails%2F125434872 "https://blog.csdn.net/weixin_45032067/article/details/125434872")
