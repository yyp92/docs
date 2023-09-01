# Node.js 版本管理工具

## nvm

⭐ *Github stars: 60K+*

[nvm](https://github.com/nvm-sh/nvm) 是一款 Node.js 版本管理工具，允许用户通过命令行快速安装、切换和管理不同的 Node.js 版本。

nvm 只适用于 macOS 和 Linux 用户的项目，如果是 Windows 用户，可以使用 [nvm-windows](https://github.com/coreybutler/nvm-windows) 、[nodist](https://github.com/marcelklehr/nodist) 或 [nvs](https://github.com/jasongin/nvs) 替换。

### 安装方式

macOS 下载方式：

```bash
# 方式1 浏览器打开下面链接下载
https://github.com/nvm-sh/nvm/blob/v0.39.1/install.sh
# 下载完成后，通过命令安装
sh install.sh

# 方式2 推荐
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

# 方式3
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

安装过程中如果遇到一些奇怪的问题，可以查看下 [nvm 补充说明](https://github.com/nvm-sh/nvm#additional-notes)。

### 常用命令

```bash
nvm ls                # 查看版本安装所有版本
nvm ls-remote         # 查看远程所有的 Node.js 版本
nvm install 17.0.0    # 安装指定的 Node.js 版本
nvm use 17.0.0        # 使用指定的 Node.js 版本
nvm alias default 17.0.0  # 设置默认 Node.js 版本
nvm alias dev 17.0.0  # 设置指定版本的别名，如将 17.0.0 版本别名设置为 dev
```

## n

⭐ *Github stars: 16.7K+*

[n](https://github.com/tj/n) 是一款交互式的 Node.js 版本管理工具，没有子脚本，没有配置文件，也没有复杂的 API，使用起来非常简单。

n 只适用于 macOS 和 Linux ，不适用于 Windows。

### 安装方式

可以使用 npm 直接安装到全局：

```bash
npm install n -g
```

### 常用命令

```bash
# 显示所有已下载版本
n          

# 下载指定版本
n 10.16.0 

# 查看远程所有 LTS Node.js 版本 
n lts 

# 运行指定的 Node.js 版本    
n run 10.16.0 
```

输入 `n -h`查看帮助信息。

## fnm

⭐ *Github stars: 8.4K+*

[fnm](https://github.com/Schniz/fnm) 是一款快速简单 🚀 的 Node.js 版本管理器，使用 Rust 构建。

主要特点包括：

- 🌎 跨平台支持，包括：macOS、Windows、Linux；
- ✨ 单一文件，轻松安装，即时启动 ；
- 🚀 以速度为设计理念；
- 📂 适用于 `.node-version` 和 `.nvmrc` 文件；

### 安装方式

macOS / Linux 环境：

```bash
# bash, zsh and fish shells
curl -fsSL https://fnm.vercel.app/install | bash
```

Windows 环境：

```bash
# 管理员模式打开终端，安装后只能使用管理员模式打开使用
choco install fnm

# 安装完成还需要手动设置环境变量
```

Linux/macOS/Windows 环境也可以直接下载二进制文件安装，下载地址：[github.com/Schniz/fnm/…](https://github.com/Schniz/fnm/releases)

### 常用命令

```bash
# 查看帮助
fnm -h 
# 安装指定 Node.js 版本             
fnm install 17.0.0 
# 使用指定 Node.js 版本 
fnm use 17.0.0 
# 设置默认 Node.js 版本     
fnm default 17.0.0 
```
