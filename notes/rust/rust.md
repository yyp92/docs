# rust

## 资料

[官网](https://www.rust-lang.org/zh-CN/)

[rust包管理平台](https://crates.io/)

[写给前端看的Rust教程（3）配置Visual Studio Code - 掘金](https://juejin.cn/post/7038877104175824909)

## 安装

### 安装rustup

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

### 常用工具

#### cargo-edit

[cargo-edit 命令](https://github.com/killercup/cargo-edit)

该工具扩展了[Cargo](http://doc.crates.io/)，允许您通过`Cargo.toml`从命令行修改文件来添加、删除和升级依赖项。

```bash
cargo install cargo-edit
```

如果在安装中遇到报错：

![](../..//imgs/rust-1.png)

解决方法：

1. 安装brew: https://zhuanlan.zhihu.com/p/59805070

2. 安装openssl: https://docs.rs/crate/openssl-sys/0.9.36

![](/Users/yangyipeng/Desktop/study/docs/imgs/rust-env-1.png)

#### just

在`JavaScript`中已经不需要`Makefile`，但在`Rust`中就没那么幸运了，`Makefile`还是很常见。不过 [just](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fcasey%2Fjust "https://github.com/casey/just") 正在被广泛接受，它弥补了`Makefile`的一些弱点，语法上也很相似

```bash
cargo install just
```

#### cargo-workspaces

`cargo workspaces`（或`cargo ws`）将`workspaces`的创建和管理进行了简化，受`Node.js`的`lerna`启发，最大的价值在于自动发布`workspaces`中的模块以及替换本地依赖。

```bash
cargo install cargo-workspaces
```

#### cargo-expand

在`Rust`中宏是如此的常见，以至于即便是你的第一个`hello word`程序也离不开宏的使用。宏可以帮助你减少冗余代码，但是却会增加调试的困难，`cargo-expand`有助于减少调试中遇到的困难。

`cargo-expand`依赖于`nightly`工具链：

```bash
rustup install nightly
```

安装`cargo-expand`：

```bash
cargo install cargo-expand

# 安装完成后，可以通过cargo expand [item]打印出完整的源码
```

> 注意：`cargo expand`接受一个名称，不是一个文件路径。`cargo expand main`命令不是展开`src/main.rs`，而是展开项目的根目录中的`main()`函数。通常来说，展开`src/some_module/another`文件中的模块，可以运行`cargo expand some_module::another`。 不用担心，后面会继续深入介绍。

### 常用命令

```bash
# 初始化项目
cargo new <文件名>


# cargo-edit
# 将新的依赖项添加到您的Cargo.toml. 未指定版本时，
# cargo add会尝试从crates.io查询最新版本号。
# -D, --dev 添加为开发依赖项； -B, --build 添加为构建依赖项
# cargo add
cargo-add add <包名> <参数>  
# 🌰
cargo-add add regex@0.1.41 --dev


# 删除依赖
cargo-rm rm <包名> <参数>
# 🌰
cargo-rm rm regex --dev


# cargo upgrade
```
