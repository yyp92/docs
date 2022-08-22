# npm install 安装机制

## 为什么执行npm install会更新package-lock.json？

你是否也有疑惑，为什么介入新项目初始化的时候，执行npm install安装依赖，项目的package-lock.json有时候会更新？package-lock.json就是用来固定依赖版本的，按理说依赖的版本都固定了，又没有安装新的依赖就不应该改变的，这是为什么？如果你也有这样的疑问，那么接下来可以进一步跟随我去了解npm install的模块安装机制。当然需要明确一下，这里提到的npm版本version 5及其之后的版本，因为在此之前，还未有package-lock.json之类的配置文件。

## npm install（不带参数）安装机制

![](C:\Users\Administrator\Desktop\docs\imgs\npm-install-1.jpg)

### 确定首层依赖

先检查package.json，通过dependencies和devDependencies属性中直接指定的模块。

### 构建依赖树

确定首层依赖后，会去构建依赖树。这是一个递归的过程，工程本身是整棵依赖树的根节点，每个首层依赖模块都是根节点下面的一棵子树，npm 会开启多进程从每个首层依赖模块开始逐步寻找更深层级的节点，形成依赖树。每个节点在递归的过程中会去确定节点模块的信息，比如版本、下载地址、压缩包地址等。

版本的确定机制：

package.json 中往往是 semantic version（semver，语义化版本）。此时如果版本描述文件（npm-shrinkwrap.json 或 package-lock.json）中有该模块信息直接拿即可，如果没有则从远程仓库获取。如 packaeg.json 中某个包的版本是 ^1.0.0，npm 就会去仓库中获取符合 1.x.x 形式的最新版本。

### 依赖树扁平化

上一步得到的依赖树，一般是包含很多重复模块的，比如A模块依赖了moment，B模块也依赖了moment，npm在npm3之后进行了优化，不在严格按照依赖树来进行安装，因为这个过程会浪费大量资源和时间。做法就是对这颗树进行扁平化处理。即简单说来，它会遍历所有节点，逐个将模块放在根节点下面，也就是 node_modules 的第一层。当发现有重复模块时，则将其丢弃。

比如 node_modules 下 A 模块依赖 moment@^1.0.0，B 模块依赖 moment@^1.1.0，则 ^1.1.0 为兼容版本。

而当 A 模块依赖 moment@^2.0.0，B 模块依赖 moment@^1.1.0，则依据 semver 的规则，二者不存在兼容版本。会将一个版本放在 node_modules 中，另一个仍保留在依赖树里。

举个例子，假设一个依赖树原本是这样：

node_modules

-- moduleA

---- moment@version1

-- moduleB

---- moment@version2

假设 version1 和 version2 是兼容版本，则经过 扁平化 会成为下面的形式：

node_modules

-- moduleA

-- moduleB

-- moment（保留的版本为兼容版本）

假设 version1 和 version2 为非兼容版本，则后面的版本保留在依赖树中：

node_modules

-- moduleA

-- moment@version1

-- moduleB

---- moment@version2

### 获取依赖树确定的模块

先会通过压缩包地址去判断是否在缓存中存在该版本模块，如果有，就直接拿过来，如果没有，就回去远程仓库下载，下载完后放入缓存，并解压放到node_modules目录中，最后会去新增或者更新lock文件。

## 总结

明确这个机制后，再结合上面构建依赖树的过程，npm install并不是完全按照lock文件去安装依赖的，是先通过package.json创建依赖，再去看lock(这个lock我认为是模块的lock，需要指明的是，很多npm模块没有lock类文件)中对应的模块固定的版本是否符合semver规则，符合就直接安装，不符合就会按照package.json的指定的semver规则去仓库拉最新的版本信息并更新lock文件（这个lock是工程的lock）。
