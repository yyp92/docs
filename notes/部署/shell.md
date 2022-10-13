# shell

## 前言

让我们来看看对于 shell 而言怎么实现上面的需求：删除占用内存最多的两个进程

1. 创建 shell 文件，作为函数库，假定取名为`shell-libs.sh`

2. 定义对应函数，假定取名为`killProcessByMemory`，实现功能为，传递进程数 n，杀死占用内存排名前 n 位进程

3. 引入函数库文件：`source shell-libs.sh`

shell-libs.sh

```shell
function arr_includes () {
    local name=$1
    eval local innerArr=(\${${name}[@]})
    local item=$2
    if [[ "${innerArr[@]}"  =~ "${item}" ]]; then
        echo 0
    else
        echo 1
    fi
}
function boolean () {
    local bool=1
    if [[ $1 == 0 ]]; then
        # source ../install/git/git.sh
        bool=0
    fi
    return $bool
}
function killProcessByMemory() {
    params=($*)
    # 杀死进程的命令
    killCmd="kill -9"
    # 查询进程号的命令
    memoryInfoCmd='ps aux | sort -rn -k +3 | head -${1} | awk "{print $2}"'
    # echo memoryInfo：$memoryInfo
    needkill=`arr_includes params kill`
    if boolean $needkill; then
        echo "需要删除"
        handledCmd="${memoryInfoCmd} | ${killCmd}"
    else
        echo "只需要查询"
        handledCmd=$memoryInfoCmd
    fi
    echo '最终需要的命令为'
    echo $handledCmd
}
```

当做完这几步初始动作后，我们要实现需求只需要执行下面命令（执行函数）即可

```shell
# 只需要查询进程号 最终需要的命令为 ps aux | sort -rn -k +3 | head -${1} | awk "{print $2}"
killProcessByMemory 2 
 # 需要删除 最终需要的命令为 ps aux | sort -rn -k +3 | head -${1} | awk "{print $2}" | kill -9
killProcessByMemory 2 kill
```

函数库很多，但最重要的是，我们的初始动作只需要做一遍，一劳永逸！！

其实不只是做一个常用命令的封装，我们还可以做到

- 操作系统的初始化：git、nvm、node 等等等的安装一键完成、常见软件的自动安装（win、mac 也可以！）

- 远程服务器配置：远程服务器免密登陆一键完成

- 爬虫动作的封装：定时任务不再是执行命令，而是一系列动作的合集

- 等等等

好了，好处显而易见，我们现在对于 shell 有啥念想？兄弟姐妹们，学它！

### 整体定义

诱惑了这么久，还是来给个定义吧：shell 是一个命令行解释器，它为用户提供了一个向 Linux 内核发送请求以便运行程序的界面系统级程序；用户可以用 Shell 来启动、挂起、停止或者编写一些程序；Shell 还是一个功能相当强大的编程语言，易编写，易调试，灵活性较强，是解释执行的脚本语言，在 Shell 中可以直接调用 Linux 系统命令。

### 乱入一下：案例解释

对于上面【传递进程数 n，杀死占用内存排名前 n 位进程】案例而言

```shell
ps aux|sort -rn -k +3|head -2  | awk '{print $2}' | xargs kill -9
```

ps 命令查找与进程相关的 PID 号：

- a：显示现行终端机下的所有程序，包括其他用户的程序

- u：以用户为主的格式来显示程序状况

- x：显示所有程序，不以终端机来区分

sort 对内容根据指定列进行排序

- r：表示是结果倒序排列

- n：以数值大小排序

- -k +3：则是针对第 3 列的内容进行排序（第三列是 cpu，第四列是内存）

head 只显示前指定行的数据，获取默认前 10 行数据

awk 数据处理神器，这里用于获取第二列（PID，进程 id）

## 如何学习一门语言

语言大同小异，核心不过【变量、运算、语句、函数、框架】，shell 也是门语言，所以我们也会根据这个脉络进行学习；

- 变量：定义、赋值、作用域

- 运算：算术运算符， 逻辑运算符

- 语句：测试，顺序，分支，循环

- 函数，类，接口，包

- 框架：系统函数库之上的封装

- 特殊点：脚本控制、计划任务、管道、重定向、可视化调试 shell 脚本（vscode 插件 Bash Debug）

但 shell 又有所不同，因为它的出现强依赖【linux】，linux的哲学：一条命令只做一件事情；为了组合命令和多次执行，最先是分号;用于同行组合，但不利于展示，于是出现了 shell 脚本文件，用来保存需要执行的命令，所以有别于其他语言又会出【环境变量、配置文件等等概念】，也强依赖 linux的各个命令，这点是需要贯彻整个 shell 学习脉络的。

注意：本文不会过多的牵扯 linux 的知识，awk、xarg、print 等等等，小伙伴放心，我们学习最核心的语言逻辑，至于工具，多用即可，放心食用

### shell 脚本基础概念

- 什么是 shell

- linux 的启动过程

- 怎么编写一个 shell 脚本

- shell 脚本的执行方式

- 内建命令和外部命令的区别

#### 什么是 shell

命令解释器，用于解释用户对操作系统的操作，命令解释器有很多种，具体可见：（默认的是 bash，其中的 a 是指 again，意思是汇总其他 shell 解释器的优点，重写实现）

```shell
cat /etc/shells
```

![](../../\imgs\shell-1.jpg)

### linux 的启动过程

BIOS - MBR - BootLoader(grub) - kernel - systemd - 系统初始化 - shell

```shell
六步骤
1. BIOS 引导，BIOS 系统是基本的输入输出系统，功能嵌在主板上，用于选择使用哪个介质（硬盘、光盘、网络）
2. MBR（主引导记录）：用于确定硬盘是否可以被引导
3. BootLoader（grub）：用于启动和引导内核（种类和版本）
4. kernel：
5. systemd：1 号进程
```

### shell 脚本的创建与执行

##### 怎么编写一个 shell 脚本

linux的哲学：一条命令只做一件事情；

为了组合命令和多次执行，于是出现了 shell 脚本文件，用来保存需要执行的命令。

1. 指定解释器：`#!/bin/xxx`，默认是`#!/bin/bash`；这其实是后缀，因为在 Linux 中，文件后缀是没有意义的，所以操作系统要知道这个脚本文件该用什么应用来执行，就需要这个注释来指明，比如 node 就是`#!/usr/bin/node`

2. 为文件赋予可执行权限，即`chmod u+x [filename]`

##### 案例：实现 sh 脚本文件，查看/var 下所有子目录空间

- 显示当前目录下的所有子目录所占空间：du -sh *

```shell
cat >> memo.sh << EOF
#!/bin/bash
cd /var
du -sh *
EOF
# 赋予权限 chmod u+x memo.sh
# 执行 ./memo.sh
```

##### 怎么执行一个 shell 脚本

存在四种执行方式

- [解释器] [文件名]：bash ./filename.sh，不需要赋予可执行权限，新创建进程，采用解释器进行执行

- [./文件名]：./filename.sh，新创建进程，需要赋予可执行权限，采用#!指定解释器进行执行

- source [./文件名]: source ./filename.sh，在当前进程进行执行

- .[文件名]：在当前进程进行执行

| 执行方式           | 案例                   | 是否需要可执行权限 | 是否会创建新进程 | 补充              |
| -------------- | -------------------- | --------- | -------- | --------------- |
| [解释器] [文件名]    | bash ./filename.sh   | 不需要       | 会        | 内部和当前的终端变量不互通   |
| [./文件名]        | ./filename.sh        | 需要        | 会        | 当使用默认解释器时的缩写    |
| source [./文件名] | source ./filename.sh | 不需要       | 不会       | 内部和当前的终端变量互通    |
| .[文件名]         | .filename.sh         | 不需要       | 不会       | 其实就是 source 的缩写 |

- 内建命令不需要创建子进程

- 内建命令只对当前 shell 生效

## shell 之变量

- 增删改查：定义、赋值、引用+查询、删除

- 作用范围

- 系统环境变量

- 环境变量配置文件

### 增删改查：定义、赋值、引用、删除、类型处理

#### 定义

即变化的量，字母、数字、下划线的组合，且不能以数字开头（shell 变量是弱类型，不区分变量类型）；

- 变量必须以字母或下划线开头，名字中间只能由字母，数字和下划线组成

- 变量名的长度不得超过 255 个字符

- 变量名在有效范围内必须唯一

- 变量默认类型都是字符串（存在类型【字符串、整型、浮点型、日期型】）

```shell
x=1
y=2
z=3
k=$x+$y+$z
echo $k
1+2+3
```

![](../../\imgs\shell-2.jpg)

#### 赋值

非交互式赋值存在四种赋值方式

- 变量名=变量值（等号左右不能有空格）： a=20

- let 进行赋值：let a=20

- 将命令赋值给变量：l=ls

- 将命令的结果复制给变量（常用）：使用或者：(ls -l /etc)

- 变量间的赋值可以设定默认值：`a=${[变量 b]-[默认值]}`，假设默认值是*，`a=${b-*}`

注意：变量值如果有空格等特殊字符，可以包含在""或''中

#### 引用+查询

在 shell 中存在四种引用：单引号、双引号、${}、``

- 双引号：部分引用，使用这种引用时，$、`(反引号)、(转义符) 这 3 个还是会解析成特殊的意义

- 单引号：完全引用，只原样输出

- 反引号`：命令替换

变量名即对变量的引用，在部分情况下可以缩写为变量名（不产生歧义，比如字符串拼接就不能缩写）；

而查询则可以使用`set`命令，其会默认查询系统中默认所有已经生效的变量，包括系统变量,也包括自定义变量，结合管道运算符可以精准查询

```shell
set | grep []
```

举例：查询`wzy`变量的值

![](../../\imgs\shell-3.jpg)

#### 删除

删除则是`unset`命令，使用方式如下

```shell
unset [变量名]
```

#### 特殊数据结构之数组

- 定义：[变量名]=(a b c)

- 显示所有元素：echo ${变量名[@]}

- 显示数组元素个数：echo ${#变量名[@]}

- 显示数组第一个元素：echo ${变量名[0]}（如果直接访问时也会只显示第一个元素）

```shell
colors=( yellow red blue )
echo ${colors[@]}
echo ${#colors[@]}
echo ${colors[0]}
echo $colors
```

#### 类型处理

shell 变量为弱类型并且默认是字符串类型，要想设置变量的类型，可以使用`declare`命令。

declare 命令用来声明变量类型

```shell
declare [+/-] [选项] 变量名
```

| 选项  | 含义            |
| --- | ------------- |
| -   | 给变量设定类型属性     |
| +   | 取消变量的类型属性     |
| -a  | 将变量声明为数组类型    |
| -i  | 将变量声明为整数型     |
| -x  | 将变量声明为环境变量    |
| -r  | 将变量声明为只读变量    |
| -p  | 显示指定变量的被声明的类型 |

#### 设定类型

###### 整数型

举例，将变量设定为整数型从而实现加和，通过配置项`-i`进行设定

![](../../\imgs\shell-4.jpg)

###### 数组类型

通过配置项`-a`进行设定

定义

![](../../\imgs\shell-5.jpg)

取第一个值

![](../../\imgs\shell-6.jpg)

取第二个值

![](../../\imgs\shell-7.jpg)

输出所有

![](../../\imgs\shell-8.jpg)

##### 取消变量的类型属性

承接上面，此时变量`c`已经是整型了，我们希望实现取消变量`c`的类型（即变回字符型），从而实现拼接的效果

![](../../\imgs\shell-9.jpg)

##### 设定为只读变量

通过配置项`-r`进行设定

```shell
declare -r [变量名]
```

![](../../\imgs\shell-10.jpg)

##### 查询变量

此时也可以通过配置项`-p`查询变量类型

```shell
declare -p [变量名] #显示指定变量的被声明的类型
```

![](../../\imgs\shell-11.jpg)

##### 设置为环境变量

通过配置项`-x`设置变量为环境变量

```shell
declare -x [变量名]
```

![](../../\imgs\shell-12.jpg)

由此也可以看到，之前定义全局环境变量的方式`export [变量名]`其实就是`declare -x [变量名]`的语法糖

### 变量的作用范围

1. 默认范围：只针对当前的终端（shell）生效

2. 支持子进程访问父进程的变量：`export [变量名]=[变量值]`；（取消变量可以使用`unset [变量名]`）

3. 系统环境变量：每个 shell 打开都能获取到的变量

其中第一条可以使用`bash`命令创建一个新的 shell 进行测试；第二条就是 export 和 unset 关键字；关键是系统环境变量，重点分析：

#### 系统环境变量

环境变量主要根据两个角度进行划分：用户级别是属于系统还是属于用户；shell 级别是属于当前的 shell 还是所有 shell。而这些是通过配置文件进行区分和记录的，不同作用范围和功能的变量分属不同的配置文件中，主要有四个文件`/etc/profile`、`~/.bash_profile`、`~/.bashrc`、`/etc/bashrc`。系统环境变量的查询：env 和 set

##### 常见变量

| 变量名               | 含义                                            | 常见操作                 | 注意                                                   |
| ----------------- | --------------------------------------------- | -------------------- | ---------------------------------------------------- |
| $PATH             | 搜索路径；当执行全局命令（即直接执行命令名）时，会在 PATH 值内所有路径进行查找并执行 | PATH=$PATH:[新增的全局路径] | 当前定义的变量，只会会当前终端和其子 shell 生效（因为所有的环境变量都被 export 导出过了） |
| $PS1              | 当前提示的终端信息                                     | 添加完整路径信息、时间等等信息      | <br>                                                 |
| $?                | 上条命令是否正确执行                                    | 0（正确） \| 1（错误，非 0 值） | 预定义变量                                                |
| $$                | 当前进程的 PID                                     | <br>                 | 预定义变量                                                |
| $0                | 当前进程（或执行文件）的名称                                | <br>                 | 预定义变量；bash xx.sh 时是 bash；.xx.sh 时是 xx.sh             |
| $#                | 传递到脚本的参数的个数                                   | <br>                 | <br>                                                 |
| $*                | 以一个单字符串显示所有向脚本传递的参数                           | <br>                 | <br>                                                 |
| $1-9（10 之后要用{}包裹） | 用于获取命令执行参数                                    | <br>                 | 位置变量                                                 |
| <br>              | <br>                                          | <br>                 | <br>                                                 |

#### 环境变量配置文件

- /etc/profile：

- /etc/profile.d：目录

- ~/.bash_profile

- ~/.bashrc

- /etc/bashrc

需要注意的是

- etc 下的是所有用户通用，而~则是用户用户家目录，只对指定用户生效；

- profile 文件和 bashrc 文件的加载取决于 shell 的种类是 loginShell 还是 noLoginShell（在登陆时采用的是`su - [用户名]`，这时如果有这个-号就是 loginShell，不加则是 noLoginShell）

- noLoginShell 只会加载执行 bashrc

- loginShell 则会全部加载执行

加载顺序为

```shell
/etc/profile - ~/.bash_profile - ~/.bashrc - /etc/bashrc
```

如果修改了配置文件，是不会立即重新加载的，需要我们重启终端或者执行 source 命令

```shell
source [修改的配置文件地址]
```

在操作系统加载过程中，主要按如下顺序进行加载

![](../../\imgs\shell-13.jpg)

而作用范围如下图

![](../../\imgs\shell-14.jpg)

## shell 之运算

- 赋值运算符：用于字符串赋值，赋值使用=，取消赋值使用 unset（想到于 js 中的 delete）

- 算数运算符：用于算数赋值，+-*/**%，使用方式如下

- 使用 expr 声明，如 a=`expr 4 + 5`（不支持浮点数）

- 数字常量：比 expr 精简，可以使用 let 进行赋值操作，如 let a=1+2；

- 双圆括号：是 let 的缩写，如((a=10))、((a++))

其实 expr、let 或者(())都是为了向 shell 声明，我目前在做算数赋值的动作，这也就能理解为什么要把整个式子都放在双圆括号中了。

## shell 之特殊符号

##### 引号

- 双引号：部分引用，使用这种引用时，$、`(反引号)、(转义符) 这 3 个还是会解析成特殊的意义

- 单引号：完全引用，只原样输出

- 反引号：执行命令

##### 括号

- 圆括号
  
  - ()：单独使用圆括号会产生一个子 shell（这样执行的表达式的输出就不会在当前 shell 中显示），也可用于数组赋值（colors=(yellow red blue)）
  - (())：算数运算赋值，是 let 的缩写
  - $()：将命令赋值给变量

- 方括号
  
  - []：单独使用是测试或数组元素功能
  - [[]]：表示测试表达式

```shell
[ 5 -gt 4 ] # 5 是否大于 4
[[ 5 -gt 4 ]] # 5 是否大于 4
echo $? # 测试上条命令执行结果
```

- 尖括号<>：重定向符号

- 花括号{}：
  
  - 输出范围：echo {0..9}
  
  - 文件复制

```shell
# 下面两条命令等价
cp /etc/passwd{,.bak}
cp /etc/passwd /etc/passwd.bak
```

##### 运算符号和逻辑符号

- 算数运算符：+-*/ %

- 比较运算符：><=

- 逻辑运算符：&&||!

```shell
(( 5 > 4 ))
echo $?
(( 5 > 4 && 6 < 5))
echo $?
(( 5 > 4 || 6 < 5))
echo $?
(( ! 5 > 4 ))
echo $?
```

##### 转义符号

- \转义某字符
  - 普通字符转义后有不同的功能，如\n
  - 特殊字符转义后消除了特殊功能，如`\’`

##### 其他特殊字符

| 字符   | 含义            | 补充                           |
| ---- | ------------- | ---------------------------- |
| #    | 注释            | <br>                         |
| ;    | 命令分隔符         | case 语句的分隔符要转义 ;;            |
| :    | 空指令           | 返回值永远是真                      |
| .    | 和 source 命令相同 | . bash                       |
| ~    | 家目录           | cd ~回到家目录；cd -/+ 回到上/下一个目录；  |
| ,    | 分隔目录          | <br>                         |
| *    | 通配符           | <br>                         |
| ?    | 条件测试 或 通配符    | ls ?.sh 查询文件名为单个字或没有字的 sh 文件 |
| $    | 取值符号          | <br>                         |
| \|   | 管道符           | <br>                         |
| &    | 后台运行          | <br>                         |
| _    | 空格            | <br>                         |
| <br> | <br>          | <br>                         |

```shell
# if
if [ "$PS1" ]; then

enif
# case
case $TERM in
  xterm*|vte*)
   语句 1
  ;;
  screen*)
   语句 2
  ;;
  *)
   语句 3
  ;;
esac
```

##### 运算的优先级

![](../../\imgs\shell-15.jpg)

## shell 之语句

- 顺序

- 测试：退出与退出状态、测试命令 test

- 分支：if-then

- 循环：

### 测试命令（学习分支语句的前提）

参考文档：

- https://www.cnblogs.com/f-ck-need-u/p/7427357.html

#### 状态：$? 和 exit

根据程序是否正常执行（程序退出的状态）进行判断

- exit：手动退出 shell 的命令

- exit 10 返回 10 给 shell，返回值非 0 为不正常退出

- $? 用于判读昂当前 shell 前一个进程是否正常退出（非 0 为不正常退出）

#### 命令：test

tesh 命令可以用于检查文件或者比较值，可用于如下功能（可以用`man test`查看更具体的命令）

- 整数比较测试

- 字符串测试

- 文件测试

test 测试语句可以简化为[]符号，而[]符号还有扩展写法[[]] ，支持 &&、||、<、>（推荐使用[[]]）

##### 字符串测试

两个字符串是否相等：[str1 = str2] 和 [str1 != str2]

```shell
#!/bin/bash
str1='a'
str2='a'
if [ $str1 = $str2 ]; then
    echo '等于'

elif [ $str1 != $str2 ]; then
    echo '不等于'
fi
```

读取字符串长度是否是 0（空字符串）：-z

```shell
#!/bin/bash
str1='a'
str2=''
if [ -z $str1 ]; then
    echo 'str1 为空字符串'
else
    echo 'str1 不为空字符串'
fi

if [ -z $str2 ]; then
    echo 'str2 为空字符串'
else
    echo 'str2 不为空字符串'
fi
```

##### 整数比较测试

| 功能   | []中的符号      | [[]]中的支持 |
| ---- | ----------- | -------- |
| 等于   | -eq 和 = 均支持 | =        |
| 大于等于 | -ge         | 暂无       |
| 大于   | -gt         | >        |
| 小于等于 | -le         | 暂无       |
| 小于   | -lt         | <        |
| 不等于  | -ne         | !=       |

```shell
#!/bin/bash
a=1
b=2

# if [ $a = $b ]; then
if [ $a -eq $b ]; then
    echo 'a 等于 b'
elif [ $a -ge $b ]; then
    echo 'a 大于等于 b'
elif [ $a -gt $b ]; then
    echo 'a 大于 b'
elif [ $a -le $b ]; then
    echo 'a 小于等于 b'
elif [ $a -lt $b ]; then
    echo 'a 小于 b'
elif [ $a -ne $b ]; then
    echo 'a 不等于 b'
fi

if [[ $a = $b ]]; then
    echo 'a 等于 b'
    # elif [[ $a >= $b ]]; then
    # echo 'a 大于等于 b'
elif [[ $a > $b ]]; then
    echo 'a 大于 b'
# elif [[ $a <= $b ]]; then
#     echo 'a 小于等于 b'
elif [[ $a < $b ]]; then
    echo 'a 小于 b'
elif [[ $a != $b ]]; then
    echo 'a 不等于 b'
fi
```

##### 文件测试

| 功能  | 符号            | 例子                        |
| --- | ------------- |:------------------------- |
| -b  | <br>          | <br>                      |
| -e  | 路径存在，不区分文件和目录 | test -e [路径] 或 [ -e [路径]] |
| -d  | 目录是否存在        | test -d [路径] 或 [ -d [路径]] |
| -f  | 文件是否存在        | test -f [路径] 或 [ -f [路径]] |
| -x  | 文件是否可执行       | <br>                      |

### 分支语句

- 基础 if

- if-else

- if-elif

- 嵌套 if

- case 语句

#### 基本用法：基础 if

- 测试语句

- 命令返回值

```shell
if [ 测试条件成立 || 命令返回值为 0 ]; then
相关行为
fi # 结束
```

实例

```shell
if [ $UID = 0 ]; then
    echo " root user "
fi
if [ pwd ]; then
    echo " root user "
fi
```

#### 基本用法：if-else

```shell
if [ 测试条件成立 || 命令返回值为 0 ]; then
相关行为
else
相关行为
fi # 结束
```

实例

```shell
#!/bin/bash
if [ $UID = 0 ]; then
    echo " root user "
else
    echo " $UID user "
fi
```

#### 基本用法：if-elif-else

```shell
if [ 测试条件成立 || 命令返回值为 0 ]; then
相关行为
elif [ 测试条件成立 || 命令返回值为 0 ]; then
相关行为
else
相关行为
fi # 结束
```

实例

```shell
#!/bin/bash
if [ $UID = 0 ]; then
    echo " root user "
elif [ $UID = 501 ]; then
  echo " wangzy user "
else
    echo " $UID user "
fi
```

#### 基本用法：嵌套 if

```shell
#!/bin/bash
if [ 测试条件成立 || 命令返回值为 0 ]; then
 if [ 测试条件成立 || 命令返回值为 0 ]; then
  相关行为
  else
  相关行为
  fi # 结束
else
相关行为
fi # 结束
```

实例实现：先判断是不是王志远账户，是则再执行对应文件

```shell
#!/bin/bash
if [ $UID = 0 ]; then
    echo " root user "
elif [ $UID = 501 ]; then
    if [ -x /Users/wzyan/Documents/selfspace/kkb-down/demo/if/index.sh ]; then
        $(pwd)/index.sh
    else
        echo " wangzy user error $(pwd)"
    fi
else
    if [ $(pwd) = "/Users/wzyan/Documents/selfspace/kkb-down/demo/if" ]; then
        echo " wangzy user  path"
    else
        echo " wangzy user error $(pwd)"
    fi
fi
```

#### case 语句

```shell
case "$变量" in
"情况 1" )
命令...;;
"情况 2" )
命令...;;
* )
命令...;;
esac
```

实例实现：先判断是不是王志远账户，是则再执行对应文件

```shell
#!/bin/bash
case "$1" in
"start" | "restart")
    echo $0 starting...
    ;;
"stop")
    echo $0 stoping...
    ;;
*)
    echo "$0 传参错误 {start|stop|restart|reload}"
    ;;
esac
```

### 循环语句

- for

- c 语言风格的 for

- while

- util

- 嵌套循环

- 使用关键字 break 和 continue

#### for 循环

注意：

1. 当使用反引号或者$()执行命令时，其结果会被当作列表处理

2. 列表中包含多个变量，变量间用空格分隔

3. 对文本处理时，要用文本查看命令取出文本内容，默认装处理，如果出现空格就会当成多行处理

```shell
for 参数 in 列表
do 执行的命令
done #封闭一个循环
```

实例实现：遍历列表输出

```shell
#!/bin/bash
for i in {1..9}
do zzxzxxxzzzzzzzzzzzzzzzzzzzzzzzzzzzxxxxxecho hello; echo $i
done #封闭一个循环
# 读取指定目录下的所有可执行 sh 文件
for sc_name in /etc/profile.d/*.sh
do echo $sc_name; echo $i
done #封闭一个循环
```

实例实现：读取命令结果进行取出所有 mp3 文件的文件名（basename [文件路径] [文件后缀]）

```shell
#!/bin/bash
for filename in `ls *.mp3`
do
 mv $filename $(basename $filename .mp3).mp4
done
```

#### c 语言风格的 for

```shell
for((变量初始化;循环判断条件;变量变化))
do
 循环执行命令
done
```

实例实现：输出 1-10

```shell
#!/bin/bash
for((i=1;i<=10;i++))
do
 echo $i
done
```

### while

特点：直到输入为非 0 才中止

```shell
while [test 测试为假时中止]
do
命令
done
```

实例实现：输出 1-10

```shell
#!/bin/bash
a=1 # 小于 10
while [ $a -lt 10 ]
do
((a++)); echo $a;
done
```

实现死循环

```shell
while :; do echo ;done
```

### until

特点：与 while 相反，直到输入为 0 才中止

```shell
until [test 测试为真时中止]
do
命令
done
```

实例实现：输出 1-10

```shell
#!/bin/bash
while []
do
命令
done
```

#### 嵌套循环

找出/etc/profile.d 目录下所有可执行文件

```shell
for sc_name in /etc/profile.d/*.sh
do
 echo $sc_name
done
```

找出/etc/profile.d 目录下所有可执行文件并执行

```shell
for sc_name in /Users/wzyan/Documents/selfspace/kkb-down/demo/for/test-x/*.sh; do
    if [ -x $sc_name ]; then
        . $sc_name
    fi
done
```

#### 使用关键字 break 和 continue

找出/etc/profile.d 目录下所有可执行文件并执行

```shell
for num in {1..9}
do
 echo $num
done
```

### 实战：处理命令行参数

- 命令行参数可以使用2..{n}进行读取

- $0 代表脚本名称

- 和@代表所有位置参数

- $#代表位置参数的数量

```shell
for pos in $*; do
    if [ "$pos" = "help" ]; then
        echo $pos
    fi
done
```

## shell 之函数

用于重复命令逻辑的集合

- 函数的定义和执行

- 函数参数

- 函数的作用范围

- 模块化

- 系统自建函数库

### 函数的定义和执行

定义

```shell
# function 关键词可以省略
function fname(){
 命令
}
```

执行

```shell
fname
```

实例

```shell
# function 关键词可以省略
function cdls() {
    cd /var
    ls
}
cdls
```

#### 函数变量

分为内部定义的变量和外部的传参

##### 内部定义

```shell
local 变量名
```

##### 外部传参

调用

```shell
funcName a b
```

这里有巨坑：

1. 函数的返回值（return）只支持数字类型，所以我们需要使用 echo 输出流的形式巧妙的模拟返回其他类型数据

```shell
function funcName(){
 echo aaa
}
returnContent=`funcName`
```

    2.如果参数中包含了空格，一定要在传递时用引号包裹，不然会被解析成多个参数（比如数组）

```shell
arr=(a b c)
funcName "${arr[@]}"
```

3. 参数和返回值只支持字符串，所以如果想支持数组，可以采用如下方式：
   1. 入参：将数组的所有内容转为字符串传递，再在内部转为数组
   2. 返回值：将数组的所有内容转为字符串返回，再在外部转为数组

```shell
<<'COMMENT'
追加元素
@param arr 数组名
@param item 被追加的元素
@return  改变后的数组字符串
COMMENT

function push() {
    # 获取数组名
    local name=$1
    # 获取数组内容，创造一个对应的内部数组
    eval local innerArr=(\${${name}[@]})
    local item=$2
    innerArr+=($item)
    # 先清除的尝试 失败
    # local length=${#innerArr[@]}
    # eval unset $name
    # for((i=0;i<$length;i++)); do
    #     eval unset $name[$i]
    # done
    echo ${innerArr[@]}
}
arr=(a b c)
arr=(`push arr aaa`) # 这里的数组已经成为(a b c d)
echo arr===${arr[@]}
```

##### 内部使用

```shell
$1 $2 $3...$n
```

注意点

1. 如果两位数了，假如是 10，就需要使用${10}

2. $#：参数个数总数

3. $*：作为字符串输出所有参数

4. ：脚本运行的当前进程号

```shell
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```

##### 实例之传参

```shell
# function 关键词可以省略
function cdls() {
    cd $1
    ls
}
cdls /tmp
```

##### 实例之内部变量

```shell
# function 关键词可以省略
function checkpid() {
    local i
    for i in $*; do
        # linux 中，每个在运行的进程都会有一个在/proc 目录下对应的子目录，目录名为 pid，这也是 ps
        [ -d "/proc/$i" ] && return 0
    done
}
checkpid 5380
echo $?
```

### 模块化

其实 shell 的模块化（即函数的导入导出）非常简单，我们知道 shell 会话间的变量是互通的（函数中的 local 变量除外），这时我们只要以当前 shell 会话执行下定义函数的 sh，就能访问到对应的函数了。而如何以当前 shell 执行呢？就是之前讲到的知识：以 source 或者.方式执行 sh 文件。

##### 引入

引入方式很简单

```shell
source [函数库的绝对路径]
```

##### 案例

存在函数库 libs/os.sh

```shell
#!/bin/bash
# 获取当前系统类型
# @return  platForm
function getPlatForm() {
    local innerPlatForm
    if [[ $(uname) == 'Darwin' ]]; then
        innerPlatForm=mac
    fi
    if [[ $(uname) == 'Linux' ]]; then
        innerPlatForm=linux
    fi
    if [[ $(uname) == 'win32' ]]; then
        innerPlatForm=window
    fi
    # shell 的 function 只能返回整数值，所以如果想接收字符串类型 echo+$()方式获取
    echo $innerPlatForm
}
```

引入使用时

```shell
source ./libs/os.sh
# 执行函数
platForm=$(getPlatForm)
# 看输出结果
echo $platForm
```

##### 注意事项

1. shell 的 function 中的 return 语句只能返回整数值，所以如果想接收字符串类型 echo+$()方式获取

#### 系统自建函数库

linux 系统中存在自带的函数库，地址如下

```shell
/etc/init.d/functions
```

在配置文件中也是用了大量的循环判断，可以看`/etc/profile`或者`/etc/.bashrc`

## 语言特殊点

一门语言的出现必然存在其特殊点，java 的 jvm 和面向对象、js 的解释型、python 的自动化等等，shell 也不例外

- 脚本控制：优先级控制、捕获信号

- 计划任务：定时执行任务

- 延时计划任务：anacontab

- 计算任务加锁：flock

### 脚本控制

cpu 和资源的分配

- nice 和 renice 调整优先级

- 死循环的控制：cpu 占用和死机

当执行如下命令时，系统会进入假死状态

```shell
.()^C|.&}.
```

#### 捕获信号

捕获语句语法如下

```shell
trap "[行为]" [信号编号]
```

- kill 默认会发送 15 号信号给应用程序

- ctrl + c 发送 2 号信息给应用程序

- -9 号信号不可阻塞

##### 案例一：监听 15 号信号

循环监听 15 信号，当此信号被下发时，打印`sig 15`；

```shell
#!/bin/bash
trap "echo sig 15" 15
echo $$ # 打印端口
while :; do
    :
done
```

启动之后会首先打印端口口，然后我们执行如下命令像指定进程发送 15 信号

```shell
kill -15 [pid]
```

##### 案例二：监听 2 号信号

循环监听 15 信号，当此信号被下发时，打印`sig 15`；

```shell
#!/bin/bash
trap "echo sig 2" 2
echo $$ # 打印端口
while :; do
    :
done
```

启动之后我们`ctrl+c`实现触发

### 计划任务

- 一次性计划任务：at

- 周期性计划任务：cron

- 延时计划任务：anacrontab

- 计划任务加锁 flock

注意事项：

1. 定时任务是没有终端的，这也就意味这默认没有输出，如果需要查看输出结果就需要用到重定向符号`>`

#### 一次性计划任务

```shell
at [选项] [时间]
```

- 录入命令：回车后就会出现`at>`这种要求输入命令的提示，输入需要在指定时间输入命令

- 完成记录：输入完成后按下 ctrl + D 即完成了命令的存入

- 查看记录：可以使用命令`atq`查看当前一次性任务列表

- 删除记录：可以使用`atrm + [编号]`进行删除

##### 注意事项

1. at 命令是一次性定时计划任务，`at` 的守护进程 `atd` 会以后台模式运行，检查作业队列来运行。

2. 默认情况下，`atd` 守护进程每 `60` 秒检查作业队列，有作业时，会检查作业运行时间，如果时间与当前时间匹配，则运行此作业。

3. `at` 命令是一次性定时计划任务，执行完一个任务后不再执行此任务了

4. 在使用 `at` 命令的时候，一定要保证 `atd` 进程的启动 , 可以使用相关指令来查看

```shell
ps -ef | grep atd # 可以检测 atd 是否在运行
```

画一个示意图

![](../../\imgs\shell-16.jpg)

##### 选项

![](../../\imgs\shell-17.jpg)

##### 时间定义

| 支持格式                                                               | 案例               | 补充                                                                                            |
| ------------------------------------------------------------------ | ---------------- | --------------------------------------------------------------------------------------------- |
| 接受在当天的 hh:mm（小时:分钟）式的时间指定；假如该时间已过去，那么就放在第二天执行                      | 04:00            | <br>                                                                                          |
| 接受比较模糊的词语来指定时间                                                     | <br>             | midnight（深夜），noon（中午），teatime（饮茶时间，一般是下午 4 点）等                                                |
| 采用 12 小时计时制，即在时间后面加上 AM（上午）或 PM（下午）来说明是上午还是下午                      | 12pm             | <br>                                                                                          |
| 指定命令执行的具体日期，指定格式为 month day（月 日）或 mm/dd/yy（月/日/年）或 dd.mm.yy（日.月.年） | 04:00 2021-05-12 | 指定的日期必须跟在指定时间的后面                                                                              |
| 使用相对计时法。指定格式为：now + count time-units                               | now + 5 minutes  | now 就是当前时间，time-units 是时间单位，这里能够是 minutes（分钟）、hours（小时）、days（天）、weeks（星期）。count 是时间的数量，几天，几小时 |
| 直接使用 today（今天）、tomorrow（明天）来指定完成命令的时间。                             | <br>             | <br>                                                                                          |

##### 案例

1 分钟后将在/tmp 目录下创建`hello.txt`并写入内容`hello`

```shell
at now + 1 minutes
# at>内容框出现后输入如下内容，然后按 ctrl + D
echo hello > /tmp/hello.txt
# 验证
cat /tmp/hello.txt
```

周期性计划任务：cron

```shell
crontab [选项]
```

crontab 是根据选项执行对应行为的

- 录入命令：crontab -e ，回车后打开一个 vim 的编辑界面，输入格式为【分钟 小时 日期 月份 星期 执行的命令】，_代表所有，比如小时位是*，代表每小时；比如`_ \* \* \* \* /usr/bin/date >> /tmp/deepinout.com.txt`

- 查看计划执行记录：可以执行`tail -f /var/log/cron`查看定时任务执行记录

- 查看用户的计划：`crontab -l`，其实就等同于`cat /var/spool/cron/和用户同名的文件`，只是做了个文件读取到标准输出上操作而已

- 删除记录：可以使用`crontab -r [编号]`进行删除

##### 注意事项

1. 最小的设置时间单位是分钟（可以用第三方软件包实现秒）

2. 注意命令的路径问题

每个用户都有自己的周期性计划任务配置文件，保存在/var/spool/cron/下面，以用户名作为文件名。

#### crontab 示例

每分钟将日期保存在指定文件中

```shell
* * * * * /usr/bin/date >> /tmp/deepinout.com.txt
```

##### 固定星期几执行计划任务

周一每分钟执行

```shell
* * * * 1 /usr/bin/date >> /tmp/deepinout.com.txt
```

周五每分钟执行

```shell
* * * * 5 /usr/bin/date >> /tmp/deepinout.com.txt
```

周一和周五每分钟执行

```shell
* * * * 1,5 /usr/bin/date >> /tmp/deepinout.com.txt
```

周一至周五每分钟执行

```shell
* * * * 1-5 /usr/bin/date >> /tmp/deepinout.com.txt
```

##### 同时满足指定日期和星期才执行任务

7 月 8 日且是周一至周五，每分钟执行

```shell
* * 8 7 1-5 /usr/bin/date >> /tmp/deepinout.com.txt
```

#### 延时计划任务：anacontab

为了缓解 cron 中一瞬间大量任务并发执行而导致系统压力过大的问题。相关文件

- /etc/cron.d/0hourly：存储着运行任务的信息

- /etc/anacontab：

```shell
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days   delay in minutes   job-identifier   command
1       5       cron.daily              nice run-parts /etc/cron.daily
7       25      cron.weekly             nice run-parts /etc/cron.weekly
@monthly 45     cron.monthly            nice run-parts /etc/cron.monthly
```

##### 环境变量部分

- RANDOME_DELAY=45：表示 anacron 在执行任务前先延时一段随记的时间再执行，这段随机的时间为 0-45 分钟之内的随机数。

- START_HOURS_RANGE=3-22：指定了只有在凌晨 3 点到晚上 22 点这个时间段内才允许执行任务。

##### 执行计划部分

由四部分组成：

- period in days：轮回天数，表示任务多少天执行一次。

- delay in minutes：表示启动 Anacron 和运行作业时间之间的延迟，单位为分钟. 当然前提是自最后一次运行之后所经过的时间超出了轮回天数。 但是它并不是作业真正运行的时间，真正运行的时间还需要加上 RANDOME_DELAY 中设置的随机分钟数。

- job-identifier：作业的标识符。anacron 在执行任务时会将日期写入 `/var/spool/anacron/$job-identifier` 文件中

- command：实际运行的命令。这里的 `run-parts` 是一个运行指定目录中所有程序与脚本的命令，可以通过 `man run-parts` 来查看它的说明

##### 特点如下

- anacron 只运行每天/周/月的任务

- 如果一项任务在预定的时间机器处于关机状态，那么在下次开机的时候会执行；

- anacron 自己没有守护进程运行，需要依赖于外部工具（例如 crond）；

- 可以设置一个延时时间，当到指定时间时，等待一个延时时间再执行任务；

#### 计划任务加锁：flock

##### 为什么出现

在使用 crontab 管理定时脚本时，如果设定的脚本执行时间间隔较短，例如 5 分钟执行一次，正常情况下，脚本执行耗时 1 分钟，在非正常情况下（如服务器压力较大的情况下，或数据量突然增大），脚本执行时间超过 5 分钟，这时就会造成多个脚本同时执行，严重时甚至拖垮服务器，影响服务器上的其它服务。

当多个进程可能会执行同一个脚本，这些进程需要保证其它进程没有在操作，以免重复执行，这就是 flock 的作用。

##### 怎么做到的

通常，这样的进程会使用一个锁文件，也就是建立一个文件来告诉别的进程自己在运行，如果检测到那个文件存在则认为有操作同样数据的进程在工作。

##### 如何使用

```shell
flock -h

Usage:
 flock [options] <file|directory> <command> [command args]
 flock [options] <file|directory> -c <command>
 flock [options] <file descriptor number>

Options:
-s, --shared:    获得一个共享锁
-x, --exclusive: 获得一个独占锁
-u, --unlock:    移除一个锁，通常是不需要的，脚本执行完会自动丢弃锁
-n, --nonblock:  如果没有立即获得锁，直接失败而不是等待
-w, --timeout:   如果没有立即获得锁，等待指定时间
-o, --close:     在运行命令前关闭文件的描述符号。用于如果命令产生子进程时会不受锁的管控
-c, --command:   在 shell 中运行一个单独的命令
-h, --help       显示帮助
-V, --version:   显示版本
```

##### 使用案例

```shell
flock -xn "/tmp/f.lock" -c "/root/a.sh"
```

下图是两个终端同时运行这条命令，后执行者会因为抢不到锁而得不到执行，马上退出

![](../../\imgs\shell-18.jpg)

### 重点概念：管道

当我们单纯的运行多个程序而不需要它们彼此间有互通时，存在如下方式

| 多命令执行符 | 格式         | 作用                                               | 案例              |
| ------ | ---------- | ------------------------------------------------ | --------------- |
| ;      | 命令 1;命令 2  | 多个命令执行，命令之间没有任何逻辑联系                              | echo 1;echo 2;  |
| &&     | 命令 1&&命令 2 | 逻辑与 当命令 1 正确执行，则命令 2 才会执行 当命令 1 执行不正确，则命令 2 不会执行 | echo 1&&echo 2; |
| \      | 命令 1\ 命令 2 | 逻辑或 当命令 1 执行不正确，则命令 2 才会执行 当命令 1 正确执行，则命令 2 不会执行 | echo 1\echo 2;  |

```shell
echo 1;echo 2;
echo 1&&echo 2;
echo 1||echo 2;
```

而如果需要互通，比如第一个命令的返回传递给第二个命令，就需要用到管道了，管道的本质就是将多个程序进行了一个连接，和信号一样，也是进程通信的方式之一

- 管道与管道符（即匿名管道，|，作用是将前一个命令的结果传递给后面的命令，如`ls -l | more`）

- 子进程与子 shell

```shell
ls /etc/ | more
netstat -an | grep ESTABLISHED | wc -l
```

注意：因为管道是以子进程方式进行执行的，所以内建命令的执行不会传递给父进程。

### 重点概念：重定向

重定向的本质就是将文件和输入、输出（包含标准输出、错误输出）进行了一个连接

- 输入重定向符号：<

- 输出重定向符号：

- > ：覆盖写入文件

- > > ：追加写入文件

- 2>：错误输出写入文件

- &>：正确和错误输出统一写入到文件中

多行内容写入

```shell
cat > [文件名] << EOF
xxxx 内容
EOF
```

#### 具体整理

| 设备  | 设备文件名       | 文件描述符 | 类型     |
| --- | ----------- | ----- | ------ |
| 键盘  | /dev/stdin  | 0     | 标准输入   |
| 显示器 | /dev/stdout | 1     | 标准输出   |
| 显示器 | /dev/stderr | 2     | 标准错误输出 |

| 类型            | 符号              | 作用                                   |
| ------------- | --------------- | ------------------------------------ |
| 标准输出重定向       | 命令 > 文件         | 以覆盖的方式，把命令的正确输出输出到指定的文件或设备当中         |
| 标准输出重定向       | 命令 >> 文件        | 以追加的方式，把命令的正确输出输出到指定的文件或设备当中         |
| 错误输出重定向       | 命令 2>文件         | 以覆盖的方式，把命令的错误输出输出到指定的文件或设备当中         |
| 错误输出重定向       | 命令 2>>文件        | 以追加的方式，把命令的错误输出输出到指定的文件或设备当中         |
| 正确输出和错误输出同时保存 | 命令>文件 2>&1      | 以覆盖的方式，把正确输出和错误输出都保存到同一个文件当中         |
| 正确输出和错误输出同时保存 | 命令>文件 2>>&1     | 以追加的方式，把正确输出和错误输出都保存到同一个文件当中         |
| 正确输出和错误输出同时保存 | 命令&>文件（类似 2>&1） | 以覆盖的方式，把正确输出和错误输出都保存到同一个文件当中         |
| 正确输出和错误输出同时保存 | 命令&>>文件         | 以追加的方式，把正确输出和错误输出都保存到同一个文件当中         |
| 正确输出和错误输出同时保存 | 命令>>文件 1 2>文件 2 | 以覆盖的方式，正确的输出追加到文件 1 中，把错误输出追加到文件 2 中 |

案例

```shell
# 创建 a.txt 并写入 123
echo '123' > a.txt
# 将文件内容输入进变量
read a < a.txt
# 将 a 变量内容追加入 a.txt
echo $a >> a.txt
# 将命令执行错误的提示内容写入 error.log 中
nocmd 2> error.log
```

## shell 之调试

说了这么多，最后还是要落到写的程度来，【纸上得来终觉浅，绝知此事要躬行】，写自然免不了有问题，当有问题的时候我们就需要一些手段去调试我们的代码了。怎么调呢？对我个人而言经历了三个阶段

- log 时期：变量使用 echo 输出，个人通过 vscode 插件优化至一键生成日志语句

- 自动输出时期：`set`命令用来修改 Shell 环境的运行参数，也就是可以定制环境，这里就可以做到自动输出日志

- 运行时处理时期：也就是我们常说的 debugger 了，没错，我们可以和 debugger js 一样去可视化处理 shell 脚本（cool~）；个人通过 vscode 插件优化至一键完成搭建动作【如需试用可以搜索：weiyi-tools】

逐一分享啦

### log 时期

这个很简单，唯一要注意的就是 shell 变量类型导致的奇葩输出，在 shell 中默认变量是字符串类型，而其他类型的输出则有不同

- string：echo $变量名

- array：echo 变量名，日后直接变量名会只打印第一个字符

- function：无法打印函数体（个人没找到），echo 变量名会打印函数名，而且函数的引用不能加$

这些就是 🐢 的臀部--规定了，踩坑良久，献于诸君。我们来看看我说的那个 vscode 插件支持【一键输出打印语句】，目前支持 js 和 shell。【如需试用可以搜索：weiyi-tools】

![](../../\imgs\shell-19.gif)

### 自动输出时期：set

`set`命令用来修改 Shell 环境的运行参数，也就是可以定制环境。一共有十几个参数可以定制，官方手册有完整清单。

使用方式很简单，在脚本的顶部放上 set 命令+对应配置即可

```shell
set -[...options]
```

具体配置如下

| 配置         | 不加时情况                                                           | 加上后                                  | 补充                                                     |
| ---------- | --------------------------------------------------------------- | ------------------------------------ | ------------------------------------------------------ |
| u          | 如果遇到不存在的变量，Bash 默认忽略它。                                          | 遇到不存在的变量就会报错，并停止执行。                  | 另一种写法`-o nounset`，两者是等价的。                              |
| x          | 屏幕只显示运行结果，没有其他内容。如果多个命令连续执行，它们的运行结果就会连续输出。有时会分不清，某一段内容是什么命令产生的。 | 运行结果之前，先输出执行的那一行命令。                  | 还有另一种写法-o xtrace。                                      |
| e          | 有运行失败的命令（返回值非 0），Bash 默认会继续执行后面的命令。                             | 只要发生错误，就终止执行                         | 等价-o errexit；这个一定要注意！！！因为我们的函数入出参是用非 0 实现的，如果加了这个就会中止了 |
| o pipefail | `-e`有一个例外情况，就是不适用于管道命令。                                         | 管道符链接的，只要一个子命令失败，整个管道命令就失败，脚本就会终止执行。 | <br>                                                   |

顺便提一下，如果命令行下不带任何参数，直接运行`set`，会显示所有的环境变量和 Shell 函数。

##### 常见使用方式

将下面内容放在脚本顶部可以做到

- 只要发生错误，就终止执行 （建议不用）

- 遇到不存在的变量就会报错，并停止执行。

- 运行结果之前，先输出执行的那一行命令。

- 管道符链接的，只要一个子命令失败，整个管道命令就失败，脚本就会终止执行。

```shell
set -euxo pipefail
```

补充：其实本人还是喜欢下面这样就好啦，因为我会封装很多函数，如果加上了 e 就会导致中止

```shell
set -ux
```

### 运行时处理时期：debugger

在这，会发现自动化时期其实并没有做到随时随地查看自己想看的运行状态，甚至还会打印很多很多不需要看的内容。有没有一种办法，可以直接图形化的查看当前调试状态下参数的值、卡住程序快照、逐步运行呢？聪明的小伙伴肯定想到了，这不就是 debugger 调试嘛。

js 我们肯定是可以的，shell 呢？其实也可以，只是有几个小要求

1. 系统 bash 版本 > 4.x (mac 自带版本为 3.2，需要手动升级)：参考 Mac 升级最新版 bash

2. 需要安装 vsode 的插件：bash debug

3. 完成 bash debug 插件配置文件，即 launch.json 文件

这几步的动作原博主写的很赞，我就不赘述了，可见：https://liushiming.cn/article/debug-bash-on-macos.html；唯一有一点补充的就是，记得把 program 替换成`${file}`（原文复制下面的也可以），这代表要调试当前打开的 shell 文件。

```js
{
  // 使用 IntelliSense 了解相关属性。
  // 悬停以查看现有属性的描述。
  // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "bashdb",
      "request": "launch",
      "name": "Bash-Debug (simplest configuration)",
      "program": "${file}"
    }
  ]
}
```

最后，我们来看下效果

![](../../\imgs\shell-20.jpg)

### 关于个人的优化：一键完成搭建动作

直接看效果【如需试用可以搜索：weiyi-tools】

![](../../\imgs\shell-21.gif)

至此，shell 之调试篇，完成。

## 自实现 vscode 插件对 shell 的支持

除此之外的内容

- vscode 插件支持常见的 shell snippet

- vscode 插件支持【属性.xxx】形式使用函数库

写不动了，到时候另起一文，看下效果吧，对 vscode 插件开发想了解的同学也可以前往我的专栏查看：https://juejin.cn/column/7078626256777904165

![](../../\imgs\shell-22.gif)

# 

## 常用文档

- 写给前端的 shell 脚本编程详解 - SegmentFault 思否：https://segmentfault.com/a/1190000037797344

- Shell 流程控制 | 菜鸟教程：https://www.runoob.com/linux/linux-shell-process-control.html

- Shell 字符串截取（非常详细）：http://c.biancheng.net/view/1120.html

- Shell 脚本入门-阿里云开发者社区：https://developer.aliyun.com/learning/course/794?spm=a2c6h.21254954.graph.26.eabc4fe0ZUb86s

- Shell：常用的语句整理-阿里云开发者社区：https://developer.aliyun.com/article/1007026?spm=a2c6h.21258778.0.0.6a4f39d26z0Qhx

- Linux shell 脚本之 if 条件判断_doiido 的博客-CSDN 博客_shell 的 if 判断语句：https://blog.csdn.net/doiido/article/details/43966819

- Mac 常用命令清单 - 掘金：https://juejin.cn/post/6844903608996069390

- Shell 基础及实例 - 掘金：https://juejin.cn/post/6844903943886077960#heading-46

- shell 命令行参数（基本） - 苍青浪 - 博客园：https://www.cnblogs.com/cangqinglang/p/11942567.html

- 在 Shell 脚本中解析选项 | 始终：https://liam.page/2016/11/11/ways-to-parse-arguments-in-shell-script/

- 浅谈 shell 遍历数组的几种方法 - 编程宝库：http://www.codebaoku.com/it-shell/it-shell-201789.html

- 使用 vscode 调试 bash 脚本：https://liushiming.cn/article/debug-bash-on-macos.html

- Mac 安装新版本 Bash | Happy Hack Everday：https://blog.happyhack.io/2020/07/16/upgrade-bash-on-macOS/

- Linux：查看占用 cpu/内存 资源最多的进程并杀死：https://blog.csdn.net/qq_41538097/article/details/107539714

- Bash 脚本 set 命令教程：https://www.ruanyifeng.com/blog/2017/11/bash-set.html
