# docker

## 一、`Docker`的四大组成对象

> 在 `Docker` 体系里，有四个对象 (`Object`) 是我们不得不进行介绍的，因为几乎所有 `Docker` 以及周边生态的功能，都是围绕着它们所展开的。它们分别是：镜像 ( `Image` )、容器 ( `Container` )、网络 ( `Network` )、数据卷 ( `Volume` )...

### 1.1 镜像

> 所谓镜像，可以理解为一个只读的文件包，其中包含了虚拟环境运行最原始文件系统的内容

### 1.2 容器

> 容器就是用来隔离虚拟环境的基础设施，而在 `Docker` 里，它也被引申为隔离出来的虚拟环境。

- 如果把镜像理解为编程中的类，那么容器就可以理解为类的实例。镜像内存放的是不可变化的东西，当以它们为基础的容器启动后，容器内也就成为了一个“活”的空间

**`Docker` 的容器应该有三项内容组成**

- 一个 `Docker` 镜像
- 一个程序运行环境
- 一个指令集合

### 1.3 网络

> 在 `Docker` 中，实现了强大的网络功能，我们不但能够十分轻松的对每个容器的网络进行配置，还能在容器间建立虚拟网络，将数个容器包裹其中，同时与其他网络环境隔离

![](C:\Users\Administrator\Desktop\docs\imgs\interview-docker-1.png)

> `Docker` 能够在容器中营造独立的域名解析环境，这使得我们可以在不修改代码和配置的前提下直接迁移容器，`Docker` 会为我们完成新环境的网络适配。对于这个功能，我们甚至能够在不同的物理服务器间实现，让处在两台物理机上的两个 Docker 所提供的容器，加入到同一个虚拟网络中，形成完全屏蔽硬件的效果...

### 1.4 数据卷

1. 除了网络之外，文件也是重要的进行数据交互的资源。在以往的虚拟机中，我们通常直接采用虚拟机的文件系统作为应用数据等文件的存储位置。然而这种方式其实并非完全安全的，当虚拟机或者容器出现问题导致文件系统无法使用时，虽然我们可以很快的通过镜像重置文件系统使得应用快速恢复运行，但是之前存放的数据也就消失了。
2. 为了保证数据的独立性，我们通常会单独挂载一个文件系统来存放数据。这种操作在虚拟机中是繁琐的，因为我们不但要搞定挂载在不同宿主机中实现的方法，还要考虑挂载文件系统兼容性，虚拟操作系统配置等问题。值得庆幸的是，这些在 Docker 里都已经为我们轻松的实现了，我们只需要简单的一两个命令或参数，就能完成文件系统目录的挂载。
3. 能够这么简单的实现挂载，主要还是得益于 `Docker` 底层的 Union File System 技术。在 `UnionFS` 的加持下，除了能够从宿主操作系统中挂载目录外，还能够建立独立的目录持久存放数据，或者在容器间共享。
4. 在 `Docker` 中，通过这几种方式进行数据共享或持久化的文件或目录，我们都称为数据卷 ( `Volume` )...

## 二、搭建运行Docker环境

### 2.1 Docker Engine 的版本

**对于 Docker Engine 来说，其主要分为两个系列**

- 社区版 ( `CE`, Community Edition )
- 企业版 ( `EE`, Enterprise Edition )
1. 社区版 ( `Docker Engine CE` ) 主要提供了 `Docker` 中的容器管理等基础功能，主要针对开发者和小型团队进行开发和试验。而企业版 ( `Docker Engine EE` ) 则在社区版的基础上增加了诸如容器管理、镜像管理、插件、安全等额外服务与功能，为容器的稳定运行提供了支持，适合于中大型项目的线上运行...
2. 社区版和企业版的另一区别就是免费与收费了。对于我们开发者来说，社区版已经提供了 `Docker` 所有核心的功能，足够满足我们在开发、测试中的需求，所以我们直接选择使用社区版进行开发即可。在这本小册中，所有的内容也是围绕着社区版的 `Docker Engine` 展开的...
3. 从另外一个角度，`Docker Engine` 的迭代版本又会分为稳定版 ( `Stable release` ) 和预览版 ( `Edge release` )。不论是稳定版还是预览版，它们都会以发布时的年月来命名版本号，例如如 17 年 3 月的版本，版本号就是 17.03...

![](../../\imgs\interview-docker-2.png)

1. `Docker Engine` 的稳定版固定为每三个月更新一次，而预览版则每月都会更新。在预览版中可以及时掌握到最新的功能特性，不过这对于我们仅是使用 `Docker` 的开发者来说，意义并不是特别重大的，所以我还是更推荐安装更有保障的稳定版本。
2. 在主要版本之外，`Docker` 官方也以解决 `Bug` 为主要目的，不定期发布次要版本。次要版本的版本号由主要版本和发布序号组成，如：`17.03.2`就是对 `17.03` 版本的第二次修正...

### 2.2 Docker 的环境依赖

- 由于 `Docker` 的容器隔离依赖于 `Linux` 内核中的相关支持，所以使用 `Docker` 首先需要确保安装机器的 `Linux kernel`中包含 `Docker` 所需要使用的特性。以目前 Docker 官方主要维护的版本为例，我们需要使用基于 `Linux kernel 3.10` 以上版本的 `Linux` 系统来安装 `Docker.`..
- 也许 `Linux kernel` 的版本还不够直观，下面的表格就直接展示了 `Docker` 对主流几款 `Linux` 系统版本的要求

| 操作系统     | 支持的系统版本                     |
| -------- | --------------------------- |
| `CentOS` | `CentOS 7`                  |
| `Debian` | `Debian Wheezy 7.7 (LTS)`   |
| `Debian` | `Jessie 8 (LTS)`            |
| `Debian` | `Stretch 9`                 |
| `Debian` | `Buster 10`                 |
| `Fedora` | `Fedora 26`、`Fedora 27`     |
| `Ubuntu` | `Ubuntu Trusty 14.04 (LTS)` |
| `Ubuntu` | `Xenial 16.04 (LTS)`        |
| `Ubuntu` | `Artful 17.10...`           |

### 2.3 在 Linux 系统中安装 Docker

> 因为 `Docker` 本身就基于 `Linux` 的核心能力，同时目前主流的 `Linux` 系统中所拥有的软件包管理程序，已经可以很轻松的帮助我们处理各种依赖问题，所以在 `Linux`中安装 `Docker` 并非什么难事

**CentOS**

```bash
sudo yum install yum-utils device-mapper-persistent-data lvm2

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce

sudo systemctl enable docker
sudo systemctl start docker...
```

**Debian**

```bash
sudo apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce

sudo systemctl enable docker
sudo systemctl start docker...
```

**Fedora**

```bash
sudo dnf -y install dnf-plugins-core

sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
sudo dnf install docker-ce

sudo systemctl enable docker
sudo systemctl start docker...
```

**Ubuntu**

```bash
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce

sudo systemctl enable docker
sudo systemctl start docker...
```

### 2.4 上手使用

> 在安装 `Docker` 完成之后，我们需要先启动 `docker daemon` 使其能够为我们提供 `Docker` 服务，这样我们才能正常使用 `Docker`

在我们通过软件包的形式安装 `Docker Engine` 时，安装包已经为我们在 `Linux` 系统中注册了一个 `Docker` 服务，所以我们不需要直接启动 `docker daemon` 对应的 `dockerd` 这个程序，而是直接启动 `Docker` 服务即可。启动的 `Docker` 服务的命令其实我已经包含在了前面谈到的安装命令中，也就是...

```bash
sudo systemctl start docker
```

> 当然，为了实现 `Docker` 服务开机自启动，我们还可以运行这个命令

```bash
sudo systemctl enable docker
```

**docker version**

> 在 `Docker`服务启动之后，我们先来尝试一个最简单的查看 `Docker` 版本的命令：`docker version`

```bash
sudo docker version

Client:
 Version:           18.06.1-ce
 API version:       1.38
 Go version:        go1.10.3
 Git commit:        e68fc7a
 Built:             Tue Aug 21 17:23:03 2018
 OS/Arch:           linux/amd64
 Experimental:      false

Server:
 Engine:
  Version:          18.06.1-ce
  API version:      1.38 (minimum version 1.12)
  Go version:       go1.10.3
  Git commit:       e68fc7a
  Built:            Tue Aug 21 17:25:29 2018
  OS/Arch:          linux/amd64
  Experimental:     false...
```

> 这个命令能够显示 `Docker C/S`结构中的服务端 ( `docker daemon` ) 和客户端 ( `docker CLI` ) 相关的版本信息。在默认情况下，`docker CLI` 连接的是本机运行的 `docker daemon` ，由于 `docker daemon` 和 `docker CLI` 通过`RESTful` 接口进行了解耦，所以我们也能修改配置用于操作其他机器上运行的 `docker daemon`...

**docker info**

> 如果想要了解 `Docker Engine` 更多相关的信息，我们还可以通过 `docker info` 这个命令

```bash
sudo docker info

Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 18.06.0-ce
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
## ......
Live Restore Enabled: false...
```

**配置国内镜像源**

> 一个由 Docker 官方提供的国内镜像源 registry.docker-cn.com

那么有了地址，我们要如何将其配置到 Docker 中呢？

> 在 Linux 环境下，我们可以通过修改 `/etc/docker/daemon.json` ( 如果文件不存在，你可以直接创建它 ) 这个 Docker 服务的配置文件达到效果

```yml
{
    "registry-mirrors": [
        "https://registry.docker-cn.com"
    ]
}
```

在修改之后，别忘了重新启动 `docker daemon` 来让配置生效

```bash
sudo systemctl restart docker
```

要验证我们配置的镜像源是否生效，我们可以通过 `docker info` 来查阅当前注册的镜像源列表

```bash
sudo docker info
## ......
Registry Mirrors:
 https://registry.docker-cn.com/
## ......
```

## 三、在 Windows 和 Mac 中使用 Docker

### 3.1 Docker Desktop

**对于 Windows 系统来说，安装 Docker for Windows 需要符合以下条件**

- 必须使用 `Windows 10 Pro` ( 专业版 )
- 必须使用 `64 bit` 版本的 `Windows`

**对于 macOS 系统来说，安装 Docker for Mac 需要符合以下条件**

- `Mac`硬件必须为 `2010` 年以后的型号
- 必须使用 `macOS El Capitan 10.11` 及以后的版本

> 另外，虚拟机软件 `VirtualBox` 与 `Docker Desktop` 兼容性不佳，建议在安装 `Docker for Windows`和 `Docker for Mac` 之前先卸载 `VirtualBox`

在确认系统能够支持 `Docker Desktop` 之后，我们就从 `Docker` 官方网站下载这两个软件的安装程序，这里直接附上 `Docker Store` 的下载链接，供大家直接下载

- [Docker for Windows(opens new window)](https://store.docker.com/editions/community/docker-ce-desktop-windows)
- [Docker for Mac(opens new window)](https://store.docker.com/editions/community/docker-ce-desktop-mac)

### 3.2 启动 Docker

- 像 `Linux` 中一样，我们要在 `Windows` 和 `macOS` 中使用 `Docker` 前，我们需要先将 `Docker`服务启动起来。在这两个系统中，我们需要启动的就是刚才我们安装的 `Docker for Windows` 和 `Docker for Mac` 了...
- 启动两个软件的方式很简单，我们只需要通过操作系统的快捷访问功能查找到 `Docker for Windows` 或 `Docker for Mac` 并启动即可
- 打开软件之后，我们会在`Windows` 的任务栏或者 `macOS` 的状态栏中看到 `Docker` 的大鲸鱼图标

![](C:\Users\Administrator\Desktop\docs\imgs\interview-docker-3.png)

> `Docker Desktop` 为我们在 `Windows` 和 `macOS` 中使用 `Docker`提供了与 `Linux` 中几乎一致的方法，我们只需要打开 `Windows` 中的 `PowerShell` 获得 `macOS` 中的 Terminal，亦或者 `Git Bash`、`Cmder`、`iTerm`等控制台类软件，输入 `docker` 命令即可...

使用 `docker version` 能够看到 `Docker` 客户端的信息，我们可以在这里发现程序运行的平台

```bash
docker version

Client:
## ......
 OS/Arch:  windows/amd64
## ......
```

### 3.3 Docker Desktop 的实现原理

> 我们知道 `Docker` 的核心功能，也就是容器实现，是基于 `Linux`内核中 `Namespaces`、`CGroups` 等功能的。那么大体上可以说，`Docker` 是依赖于 `Linux` 而存在的。那么问题来了，`Docker Desktop` 是如何实现让我们在 `Windows`和 `macOS` 中如此顺畅的使用 Docker 的呢？...

- 其实 `Docker Desktop` 的实现逻辑很简单：既然 `Windows` 和 `macOS` 中没有 `Docker`能够利用的 `Linux` 环境，那么我们生造一个 `Linux` 环境就行啦！`Docker for Windows`和 `Docker for Mac` 正是这么实现的...
- 由于虚拟化在云计算时代的广泛使用，`Windows` 和 `MacOS` 也将虚拟化引入到了系统本身的实现中，这其中就包含了之前我们所提到的通过 `Hypervisor`实现虚拟化的功能。在 `Windows` 中，我们可以通过 `Hyper-V` 实现虚拟化，而在 `macOS` 中，我们可以通过 HyperKit 实现虚拟化...
- `Docker for Windows` 和 `Docker for Mac` 这里利用了这两个操作系统提供的功能来搭建一个虚拟 `Linux` 系统，并在其之上安装和运行 `docker daemon`。

![](../../\imgs\interview-docker-4.png)

> 除了搭建 `Linux` 系统并运行 `docker daemon` 之外，`Docker Desktop` 系列最突出的一项功能就是我们能够直接通过 `PowerShell`、`Terminal` 这类的控制台软件在 `Windows` 和 `macOS` 中直接操作虚拟 Linux 系统中运行的 docker daemon...

### 3.4 主机文件挂载

1. 控制能够直接在主机操作系统中进行，给我们使用 Docker Desktop 系列软件提供了极大的方便。除此之外，文件的挂载也是 Docker Desktop 所提供的大幅简化我们工作效率且简化使用的功能之一。
2. 之前我们谈到了，Docker 容器中能够通过数据卷的方式挂载宿主操作系统中的文件或目录，宿主操作系统在 Windows 和 macOS 环境下的 Docker Desktop 中，指的是虚拟的 Linux 系统。
3. 当然，如果只能从虚拟的 Linux 系统中进行挂载，显然不足以达到我们的期望，因为最方便的方式必然是直接从 Windows 和 macOS 里挂载文件了。
4. 要实现我们所期望的效果，也就是 Docker 容器直接挂载主机系统的目录，我们可以先将目录挂载到虚拟 Linux 系统上，再利用 Docker 挂载到容器之中。这个过程被集成在了 Docker Desktop 系列软件中，我们不需要人工进行任何操作，整个过程已经实现了自动化...

![](../../\imgs\interview-docker-5.png)

> `Docker Desktop` 对 `Windows` 和 `macOS` 到虚拟 `Linux` 系统，再到 `Docker` 容器中的挂载进行了实现，我们只需要直接选择能够被挂载的主机目录 ( 这个过程更多也是为了安全所考虑 )，剩下的过程全部由 Docker Desktop 代替我们完成。...

## 四、使用容器：镜像与容器

### 4.1 Docker 镜像

1. 如果进行形象的表述，我们可以将 `Docker` 镜像理解为包含应用程序以及其相关依赖的一个基础文件系统，在 `Docker` 容器启动的过程中，它以只读的方式被用于创建容器的运行环境。
2. 从另一个角度看，在之前的小节里我们讲到了，`Docker` 镜像其实是由基于 `UnionFS` 文件系统的一组镜像层依次挂载而得，而每个镜像层包含的其实是对上一镜像层的修改，这些修改其实是发生在容器运行的过程中的。所以，我们也可以反过来理解，镜像是对容器运行环境进行持久化存储的结果...

### 4.2 查看镜像

1. 镜像是由 `Docker` 进行管理的，所以它们的存储位置和存储方式等我们并不需要过多的关心，我们只需要利用 `Docker` 所提供的一些接口或命令对它们进行控制即可。
2. 如果要查看当前连接的 `docker daemon` 中存放和管理了哪些镜像，我们可以使用 `docker images`这个命令 ( `Linux`、`macOS` 还是 `Windows` 上都是一致的 )

```bash
docker images

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
php                 7-fpm               f214b5c48a25        9 days ago          368MB
redis               3.2                 2fef532eadb3        11 days ago         76MB
redis               4.0                 e1a73233e3be        11 days ago         83.4MB
cogset/cron         latest              c01d5ac6fc8a        15 months ago       125MB...
```

> 在 `docker images` 命令的结果中，我们可以看到镜像的 `ID` ( `IMAGE ID`)、构建时间 ( `CREATED` )、占用空间 ( `SIZE` ) 等数据

这里需要注意一点，我们发现在结果中镜像 `ID` 的长度只有 `12` 个字符，这和我们之前说的 `64` 个字符貌似不一致。其实为了避免屏幕的空间都被这些看似“乱码”的镜像 `ID` 所挤占，所以 `Docker` 只显示了镜像 `ID` 的前 `12` 个字符，大部分情况下，它们已经能够让我们在单一主机中识别出不同的镜像了...

### 4.3 镜像命名

- 镜像层的 `ID` 既可以识别每个镜像层，也可以用来直接识别镜像 ( 因为根据最上层镜像能够找出所有依赖的下层镜像，所以最上层进行的镜像层 ID 就能表示镜像的 `ID` )，但是使用这种无意义的超长哈希码显然是违背人性的，所以这里我们还要介绍镜像的命名，通过镜像名我们能够更容易的识别镜像...
- 在 `docker images` 命令打印出的内容中，我们还能看到两个与镜像命名有关的数据：`REPOSITORY` 和 `TAG`，这两者其实就组成了 `docker` 对镜像的命名规则

![](C:\Users\Administrator\Desktop\docs\imgs\interview-docker-6.png)

**准确的来说，镜像的命名我们可以分成三个部分：username、repository 和 tag**

- `username`： 主要用于识别上传镜像的不同用户，与 GitHub 中的用户空间类似。
- `repository`：主要用于识别进行的内容，形成对镜像的表意描述。
- `tag`：主要用户表示镜像的版本，方便区分进行内容的不同细节

> 对于 `username` 来说，在上面我们展示的 `docker images` 结果中，有的镜像有 `username` 这个部分，而有的镜像是没有的。没有 `username` 这个部分的镜像，表示镜像是由 `Docker` 官方所维护和提供的，所以就不单独标记用户了...

### 4.4 容器的生命周期

**容器运行的状态流转图**

![](C:\Users\Administrator\Desktop\docs\imgs\interview-docker-7.png)

> 图中展示了几种常见对 `Docker` 容器的操作命令，以及执行它们之后容器运行状态的变化。这里我们撇开命令，着重看看容器的几个核心状态，也就是图中色块表示的：`Created`、`Running`、`Paused`、`Stopped`、`Deleted`

在这几种状态中，`Running` 是最为关键的状态，在这种状态中的容器，就是真正正在运行的容器了

### 4.5 主进程

1. 如果单纯去看容器的生命周期会有一些难理解的地方，而 `Docker` 中对容器生命周期的定义其实并不是独立存在的。
2. 在 `Docker` 的设计中，容器的生命周期其实与容器中 `PID` 为 `1` 这个进程有着密切的关系。更确切的说，它们其实是共患难，同生死的兄弟。容器的启动，本质上可以理解为这个进程的启动，而容器的停止也就意味着这个进程的停止，反过来理解亦然。
3. 当我们启动容器时，`Docker` 其实会按照镜像中的定义，启动对应的程序，并将这个程序的主进程作为容器的主进程 ( 也就是 `PID` 为 `1` 的进程 )。而当我们控制容器停止时，`Docker` 会向主进程发送结束信号，通知程序退出。
4. 而当容器中的主进程主动关闭时 ( 正常结束或出错停止 )，也会让容器随之停止。
5. 通过之前提到的几个方面来看，`Docker` 不仅是从设计上推崇轻量化的容器，也是许多机制上是以此为原则去实现的。所以，我们最佳的 `Docker` 实践方法是遵循着它的逻辑，逐渐习惯这种容器即应用，应用即容器的虚拟化方式。虽然在 `Docker` 中我们也能够实现在同一个容器中运行多个不同类型的程序，但这么做的话，`Docker` 就无法跟踪不同应用的生命周期，有可能造成应用的非正常关闭，进而影响系统、数据的稳定性...

## 五、使用容器：从镜像仓库获得镜像

### 5.1 镜像仓库

> 如果说我们把镜像的结构用 `Git` 项目的结构做类比，那么镜像仓库就可以看似 `GitLab`、`GitHub` 等的托管平台，只不过 `Docker` 的镜像仓库托管的不是代码项目，而是镜像

当然，存储镜像并不是镜像仓库最值得炫耀的功能，其最大的作用是实现了 `Docker` 镜像的分发。借助镜像仓库，我们得到了一个镜像的中转站，我们可以将开发环境上所使用的镜像推送至镜像仓库，并在测试或生产环境上拉取到它们，而这个过程仅需要几个命令，甚至自动化完成...

![](C:\Users\Administrator\Desktop\docs\imgs\interview-docker-8.png)

### 5.2 获取镜像

> 虽然有很多种方式将镜像引入到 `Docker` 之中，但我们最为常用的获取现有镜像的方式还是直接从镜像仓库中拉取，因为这种方式简单、快速、有保障

要拉取镜像，我们可以使用 `docker pull` 命令，命令的参数就是我们之前所提到的镜像仓库名。

```bash
sudo docker pull ubuntu

Using default tag: latest
latest: Pulling from library/ubuntu
124c757242f8: Downloading [===============================================>   ]  30.19MB/31.76MB
9d866f8bde2a: Download complete 
fa3f2f277e67: Download complete 
398d32b153e8: Download complete 
afde35469481: Download complete...
```

- 当我们运行这个命令后，`Docker` 就会开始从镜像仓库中拉取我们所指定的镜像了，在控制台中，我们可以看到镜像拉取的进度。下载进度会分为几行，其实每一行代表的就是一个镜像层。`Docker` 首先会拉取镜像所基于的所有镜像层，之后再单独拉取每一个镜像层并组合成这个镜像。当然，如果在本地已经存在相同的镜像层 ( 共享于其他的镜像 )，那么 `Docker` 就直接略过这个镜像层的拉取而直接采用本地的内容。
- 上面是一个拉取官方镜像并且没有给出镜像标签的例子，大家注意到，当我们没有提供镜像标签时，`Docker` 会默认使用 `latest` 这个标签...

我们也能够使用完整的镜像命名来拉取镜像

```bash
sudo docker pull openresty/openresty:1.13.6.2-alpine

1.13.6.2-alpine: Pulling from openresty/openresty
ff3a5c916c92: Pull complete 
ede0a2a1012b: Pull complete 
0e0a11843023: Pull complete 
246b2c6f4992: Pull complete 
Digest: sha256:23ff32a1e7d5a10824ab44b24a0daf86c2df1426defe8b162d8376079a548bf2
Status: Downloaded newer image for openresty/openresty:1.13.6.2-alpine...
```

镜像在被拉取之后，就存放到了本地，接受当前这个 `Docker` 实例管理了，我们可以通过 `docker images` 命令看到它们

```bash
sudo docker images

REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
ubuntu                latest              cd6d8154f1e1        12 days ago         84.1MB
openresty/openresty   1.13.6.2-alpine     08d5c926e4b6        3 months ago        49.3MB...
```

### 5.3 Docker Hub

> `Docker Hub` 是 `Docker` 官方建立的中央镜像仓库，除了普通镜像仓库的功能外，它内部还有更加细致的权限管理，支持构建钩子和自动构建，并且有一套精致的 Web 操作页面

Docker Hub 的地址是：hub.docker.com/

![](C:\Users\Administrator\Desktop\docs\imgs\interview-docker-9.png)

- 由于定位是 `Docker` 的中央镜像仓库系统，同时也是 `Docker Engine` 的默认镜像仓库，所以 `Docker Hub` 是开发者共享镜像的首选，那么也就意味着其中的镜像足够丰富
- 常用服务软件的镜像，我们都能在 `Docker Hub` 中找到，甚至能找到针对它们不同用法的不同镜像。
- 同时，`Docker Hub` 也允许我们将我们制作好的镜像上传到其中，与广大 `Docker` 用户共享你的成果

### 5.4 搜索镜像

- 由于 `Docker Hub` 提供了一套完整的 `Web` 操作界面，所以我们搜索其中的镜像会非常方便

![](C:\Users\Administrator\Desktop\docs\imgs\interview-docker-10.png)

> 在 Docker Hub 的搜索结果中，有几项关键的信息有助于我们选择合适的镜像：

- `OFFICIAL`代表镜像为 `Docker` 官方提供和维护，相对来说稳定性和安全性较高
- `STARS` 代表镜像的关注人数，这类似 `GitHub` 的 `Stars`，可以理解为热度
- `PULLS` 代表镜像被拉取的次数，基本上能够表示镜像被使用的频度...

> 除了直接通过 `Docker Hub` 网站搜索镜像这种方式外，我们还可以用 docker CLI 中的 `docker search` 这个命令搜索 `Docker Hub` 中的镜像

```bash
sudo docker search ubuntu

NAME                                                   DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
ubuntu                                                 Ubuntu is a Debian-based Linux operating sys…   8397                [OK]                
dorowu/ubuntu-desktop-lxde-vnc                         Ubuntu with openssh-server and NoVNC            220                                     [OK]
rastasheep/ubuntu-sshd                                 Dockerized SSH service, built on top of offi…   171                                     [OK]
consol/ubuntu-xfce-vnc                                 Ubuntu container with "headless" VNC session…   129                                     [OK]
ansible/ubuntu14.04-ansible                            Ubuntu 14.04 LTS with ansible                   95                                      [OK]
ubuntu-upstart                                         Upstart is an event-based replacement for th…   89  ...
```

### 5.5 管理镜像

> 对镜像的管理要比搜索和获取镜像更常用，所以了解镜像管理相关的操作以及知识是非常有必要的

除了之前我们所提到的 `docker images`可以列出本地 `Docker` 中的所有镜像外，如果我们要获得镜像更详细的信息，我们可以通过 `docker inspect` 这个命令

```bash
sudo docker inspect redis:3.2

[
    {
        "Id": "sha256:2fef532eadb328740479f93b4a1b7595d412b9105ca8face42d3245485c39ddc",
        "RepoTags": [
            "redis:3.2"
        ],
        "RepoDigests": [
            "redis@sha256:745bdd82bad441a666ee4c23adb7a4c8fac4b564a1c7ac4454aa81e91057d977"
        ],
## ......
    }
]...
```

> 在 `docker inspect` 的结果中我们可以看到关于镜像相当完备的信息

除了能够查看镜像的信息外，·`docker inspect·` 还能查看容器等之前我们所提到的 `Docker` 对象的信息，而传参的方式除了传递镜像或容器的名称外，还可以传入镜像 ID 或容器 `ID`。

```bash
sudo docker inspect redis:4.0
sudo docker inspect 2fef532e
```

**参数识别**

> 之前我们所谈到镜像 `ID` 是 `64` 个字符，而 `docker images` 命令里的缩写也有 `12`个字符，为什么我这里展示的操作命令里只填写了 `8` 个字符呢

- 不论我们是通过镜像名还是镜像 `ID` 传递到 `docker inspect` 或者其他类似的命令 ( 需要指定 `Docker` 对象的命令 ) 里，`Docker` 都会根据我们传入的内容去寻找与之匹配的内容，只要我们所给出的内容能够找出唯一的镜像，那么 `Docker` 就会对这个镜像执行给定的操作。反之，如果找不到唯一的镜像，那么操作不会进行，`Docker` 也会显示错误
- 也就是说，只要我们提供了能够唯一识别镜像或容器的信息，即使它短到只有 1 个字符，`Docker` 都是可以处理的

> 例如我们有五个镜像：

```bash
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
php                   7-fpm               f214b5c48a25        11 days ago         368MB
ubuntu                latest              cd6d8154f1e1        13 days ago         84.1MB
redis                 3.2                 2fef532eadb3        13 days ago         76MB
redis                 4.0                 e1a73233e3be        13 days ago         83.4MB
openresty/openresty   1.13.6.2-alpine     08d5c926e4b6        3 months ago        49.3MB
cogset/cron           latest              c01d5ac6fc8a        16 months ago       125MB...
```

> 我们注意到镜像 `ID` 前缀为`2` 的只有 `redis:3.2` 这个镜像，那么我们就可以使用 2 来指代这个镜像

```bash
sudo docker inspect 2
```

> 而前缀为 `c` 的镜像有两个，这时候如果我们直接使用 `c` 来指代镜像的话，`Docker` 会提示未能匹配到镜像

```bash
sudo docker inspect c

[]
Error: No such object: c
```

### 5.6 删除镜像

> 虽然 `Docker` 镜像占用的空间比较小，但日渐冗杂的镜像和凌乱的镜像版本会让管理越来越困难，所以有时候我们需要清理一些无用的镜像，将它们从本地的 `Docker Engine` 中移除

删除镜像的命令是 `docker rmi`，参数是镜像的名称或 `ID`

```bash
sudo docker rmi ubuntu:latest

Untagged: ubuntu:latest
Untagged: ubuntu@sha256:de774a3145f7ca4f0bd144c7d4ffb2931e06634f11529653b23eba85aef8e378
Deleted: sha256:cd6d8154f1e16e38493c3c2798977c5e142be5e5d41403ca89883840c6d51762
Deleted: sha256:2416e906f135eea2d08b4a8a8ae539328482eacb6cf39100f7c8f99e98a78d84
Deleted: sha256:7f8291c73f3ecc4dc9317076ad01a567dd44510e789242368cd061c709e0e36d
Deleted: sha256:4b3d88bd6e729deea28b2390d1ddfdbfa3db603160a1129f06f85f26e7bcf4a2
Deleted: sha256:f51700a4e396a235cee37249ffc260cdbeb33268225eb8f7345970f5ae309312
Deleted: sha256:a30b835850bfd4c7e9495edf7085cedfad918219227c7157ff71e8afe2661f63...
```

> - 删除镜像的过程其实是删除镜像内的镜像层，在删除镜像命令打印的结果里，我们可以看到被删除的镜像层以及它们的 `ID`。当然，如果存在两个镜像共用一个镜像层的情况，你也不需要担心 `Docker` 会删除被共享的那部分镜像层，只有当镜像层只被当前被删除的镜像所引用时，`Docker`才会将它们从硬盘空间中移除...
> - `docker rmi` 命令也支持同时删除多个镜像，只需要通过空格传递多个镜像 `ID` 或镜像名即可

```bash
sudo docker rmi redis:3.2 redis:4.0

Untagged: redis:3.2
Untagged: redis@sha256:745bdd82bad441a666ee4c23adb7a4c8fac4b564a1c7ac4454aa81e91057d977
Deleted: sha256:2fef532eadb328740479f93b4a1b7595d412b9105ca8face42d3245485c39ddc
## ......
Untagged: redis:4.0
Untagged: redis@sha256:b77926b30ca2f126431e4c2055efcf2891ebd4b4c4a86a53cf85ec3d4c98a4c9
Deleted: sha256:e1a73233e3beffea70442fc2cfae2c2bab0f657c3eebb3bdec1e84b6cc778b75
## .........
```

## 六、使用容器：运行和管理容器

### 6.1 容器的创建和启动

在了解容器的各项操作之前，我们再来回顾一下之前我们所提及的容器状态流转。

![](../../\imgs\interview-docker-11.png)

> 在这幅图中，我们可以看到，Docker 容器的生命周期里分为五种状态，其分别代表着：

- `Created`：容器已经被创建，容器所需的相关资源已经准备就绪，但容器中的程序还未处于运行状态。
- `Running`：容器正在运行，也就是容器中的应用正在运行。
- `Paused`：容器已暂停，表示容器中的所有程序都处于暂停 ( 不是停止 ) 状态。
- `Stopped`：容器处于停止状态，占用的资源和沙盒环境都依然存在，只是容器中的应用程序均已停止。
- `Deleted`：容器已删除，相关占用的资源及存储在 `Docker` 中的管理信息也都已释放和移除...

### 6.2 创建容器

> 当我们选择好镜像以后，就可以通过 `docker create`这个命令来创建容器了

```bash
sudo docker create nginx:1.12

34f277e22be252b51d204acbb32ce21181df86520de0c337a835de6932ca06c3
```

> 执行`docker create` 后，`Docker` 会根据我们所给出的镜像创建容器，在控制台中会打印出 `Docker` 为容器所分配的容器 `ID`，此时容器是处于 `Created` 状态的

- 之后我们对容器的操作可以通过这个容器 `ID` 或者它的缩略形式进行，但用容器 `ID` 操作容器就和用镜像 `ID` 操作镜像一样烦闷，所以我们更习惯于使用容器名来操作容器。
- 要使用容器名操作容器，就先得给容器命名，在创建容器时，我们可以通过 `--name` 这个选项来配置容器名...

```bash
sudo docker create --name nginx nginx:1.12
```

### 6.3 启动容器

> 通过 `docker create` 创建的容器，是处于 `Created` 状态的，其内部的应用程序还没有启动，所以我们需要通过`docker start` 命令来启动它。

```bash
sudo docker start nginx
```

1. 由于我们为容器指定了名称，这样的操作会更加自然，所以我们非常推荐为每个被创建的容器都进行命名
2. 当容器启动后，其中的应用就会运行起来，容器的几个生命周期也会绑定到了这个应用上，这个之前我们已经提及，这里就不在赘述。只要应用程序还在运行，那么容器的状态就会是 `Running`，除非进行一些修改容器的操作。
3. 在 `Docker` 里，还允许我们通过 `docker run` 这个命令将 `docker create` 和 `docker start` 这两步操作合成为一步，进一步提高工作效率...

```bash
sudo docker run --name nginx -d nginx:1.12

89f2b769498a50f5c35a314ab82300ce9945cbb69da9cda4b022646125db8ca7
```

- 通过 `docker run` 创建的容器，在创建完成之后会直接启动起来，不需要我们再使用 `docker start` 去启动了。
- 这里需要注意的一点是，通常来说我们启动容器会期望它运行在“后台”，而 `docker` run 在启动容器时，会采用“前台”运行这种方式，这时候我们的控制台就会衔接到容器上，不能再进行其他操作了。我们可以通过 `-d` 或 `--detach` 这个选项告诉 `Docker` 在启动后将程序与控制台分离，使其进入“后台”运行...

### 6.4 管理容器

- 容器创建和启动后，除了关注应用程序是否功能正常外，我们也会关注容器的状态等内容。
- 通过 `docker ps`这个命令，我们可以罗列出 `Docker` 中的容器

```bash
sudo docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
89f2b769498a        nginx:1.12          "nginx -g 'daemon of…"   About an hour ago   Up About an hour    80/tcp              nginx...
```

> 默认情况下，`docker ps` 列出的容器是处于运行中的容器，如果要列出所有状态的容器，需要增加 `-a` 或 `--all`选项

```bash
sudo docker ps -a

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
425a0d3cd18b        redis:3.2           "docker-entrypoint.s…"   2 minutes ago       Created                                 redis
89f2b769498a        nginx:1.12          "nginx -g 'daemon of…"   About an hour ago   Up About an hour    80/tcp              nginx...
```

1. 在 `docker ps` 的结果中，我们可以看到几项关于容器的信息。其中 `CONTAINER ID`、`IMAGE`、`CREATED`、`NAMES` 大家都比较容易理解，分别表示容器 `ID`，容器所基于的镜像，容器的创建时间和容器的名称。
2. 结果中的 `COMMAND` 表示的是容器中主程序 ( 也就是与容器生命周期所绑定进程所关联的程序 ) 的启动命令，这条命令是在镜像内定义的，而容器的启动其实质就是启动这条命令
3. 结果中的 `STATUS` 表示容器所处的状态，其值和我们之前所谈到的状态有所区别，主要是因为这里还记录了其他的一些信息。在这里，常见的状态表示有三种：
- `Created` 此时容器已创建，但还没有被启动过。
- `Up [ Time ]` 这时候容器处于正在运行状态，而这里的 `Time` 表示容器从开始运行到查看时的时间。
- `Exited ([ Code ]) [ Time ]` 容器已经结束运行，这里的 `Code` 表示容器结束运行时，主程序返回的程序退出码，而 `Time` 则表示容器结束到查看时的时间...

> 当然，在 `Docker` 逐渐成熟后，命令的命名也没有原来那么随意了，已经逐渐转换为使用大家广泛认可的形式。只是 `docker ps` 这条命令，还保留着复古的风格。

### 6.5 停止和删除容器

> 要将正在运行的容器停止，我们可以使用 `docker stop` 命令

```bash
sudo docker stop nginx
```

正在运行中的容器默认情况下是不能被删除的，我们可以通过增加 `-f` 或 `--force` 选项来让 `docker rm` 强制停止并删除容器，不过这种做法并不妥当

### 6.6 随手删除容器

> 与其他虚拟机不同，`Docker` 的轻量级容器设计，讲究随用随开，随关随删。也就是说，当我们短时间内不需要使用容器时，最佳的做法是删除它而不是仅仅停止它

### 6.7 进入容器

- 很多时间，我们需要的操作并不仅仅是按镜像所给出的命令启动容器而已，我们还会希望进一步了解容器或操作容器，这时候最佳的方式就是让我们进入到容器了。
- 我们知道，容器是一个隔离运行环境的东西，它里面除了镜像所规定的主进程外，其他的进程也是能够运行的，`Docker` 为我们提供了一个命令 `docker exec` 来让容器运行我们所给出的命令...

这里我们试试用容器中的 `more` 命令查看容器的主机名定义。

```bash
sudo docker exec nginx more /etc/hostname

::::::::::::::
/etc/hostname
::::::::::::::
83821ea220ed...
```

- `docker exec` 命令能帮助我们在正在运行的容器中运行指定命令，这对于服务控制，运维监控等有着不错的应用场景。但是在开发过程中，我们更常使用它来作为我们进入容器的桥梁
- 熟悉 `Linux` 的朋友们知道，我们操作 `Linux`这个过程，并不是 `Linux` 内部的某些机能，而是通过控制台软件来完成的。控制台软件分析我们的命令，将其转化为对 `Linux` 的系统调用，实现了我们对 `Linux` 的操作。若不是这样，生涩的系统调用方法对普通开发者来说简直就是黑洞一般的存在，更别提用它们控制系统了...
- 在 `Linux` 中，大家熟悉的控制台软件应该是 `Shell` 和 `Bash`了，它们分别由 `sh` 和 `bash` 这两个程序启动
- 由于 `bash` 的功能要比 `sh`丰富，所以在能够使用 `bash` 的容器里，我们优先选择它作为控制台程序。

```bash
sudo docker exec -it nginx bash

root@83821ea220ed:/#
```

> 在借助 `docker exec` 进入容器的时候，我们需要特别注意命令中的两个选项不可或缺，即 `-i` 和 `-t`( 它们俩可以利用简写机制合并成 `-it` )。

其中 `-i` ( `--interactive` ) 表示保持我们的输入流，只有使用它才能保证控制台程序能够正确识别我们的命令。而 `-t` ( `--tty` ) 表示启用一个伪终端，形成我们与 `bash` 的交互，如果没有它，我们无法看到 `bash` 内部的执行结果...

### 6.8 衔接到容器

> `Docker` 为我们提供了一个 `docker attach` 命令，用于将当前的输入输出流连接到指定的容器上。

```bash
sudo docker attach nginx
```

- 这个命令最直观的效果可以理解为我们将容器中的主程序转为了“前台”运行 ( 与 `docker run` 中的 `-d` 选项有相反的意思 )。
- 由于我们的输入输出流衔接到了容器的主程序上，我们的输入输出操作也就直接针对了这个程序，而我们发送的 `Linux` 信号也会转移到这个程序上。例如我们可以通过 `Ctrl + C` 来向程序发送停止信号，让程序停止 ( 从而容器也会随之停止 )。...
