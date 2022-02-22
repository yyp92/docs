# linux命令

## mkdir（创建文件夹）

```bash
# 创建文件夹
mkdir <folder>

# mkdir的-p选项允许你一次性创建多层次的目录，而不是一次只创建单独的目录
# test/{a,b,c,d}/date --> 创建test文件夹中包含a，b，c，d 四个文件夹
# test/date --> 创建test文件夹包含date文件夹
mkdir -p <test/{a,b,c,d}/date | test/date>


# 创建或打开文件（文件存在就打开，否则创建）
vi <filename>
vim 
```

## touch（创建/修改文件）

touch命令用于修改文件或者目录的时间属性，包括存取时间和更改时间。若文件不存在，系统会建立一个新的文件。

```bash
# 语法
touch [参数] [文件或目录…]

# 参数
# 改变档案的读取时间记录
-a
# 改变档案的修改时间记录
-m
# 假如目的档案不存在，不会建立新的档案。与 --no-create 的效果一样
-c
# 不使用，是为了与其他 unix 系统的相容性而保留
-f
# 使用参考档的时间记录，与 --file 的效果一样
-r
# 设定时间与日期，可以使用各种不同的格式
-d
# 设定档案的时间记录，格式与 date 指令相同
-t
# 不会建立新档案
--no-create
# 列出指令格式
--help
# 列出版本讯息
--version
```

## rm（删除）

```bash
# 删除文件夹和文件 
# -r 就是向下递归，不管有多少级目录，一并删除 
# -f 就是直接强行删除，不作任何提示的意思
rm -rf <filename 文件或文件夹>
```

## ls/ll（列表目录）

```bash
# 仅列出当前目录可见文件
ls

# 列出当前目录可见文件详细信息(ll 是 ls -l 别名)
ls -l

# 列出详细信息并以可读大小显示文件大小
ls -hl

# 列出所有文件（包括隐藏）的详细信息
ls -al

# 按文件大小排序
ls --human-readable --size -1 -S --classify

# 按文件大小排序(同上)
du -sh * | sort -h 
```

## mv（移动）

用来为文件或目录改名、或将文件或目录移入其它位置。

### 语法

```bash
mv [options] source dest
mv [options] source... directory
```

### 参数

```bash
# 当目标文件或目录存在时，在执行覆盖前，会为其创建一个备份。
-b

# 如果指定移动的源目录或文件与目标的目录或文件同名，则会先询问是否覆盖旧文件，
# 输入 y 表示直接覆盖，输入 n 表示取消该操作。 
-i

# 如果指定移动的源目录或文件与目标的目录或文件同名，不会询问，直接覆盖旧文件。 
-f

# 不要覆盖任何已存在的文件或目录。
-n

# 
-u
```

## 使用

```bash
# 将源文件名 source_file 改为目标文件名 dest_file
mv source_file(文件) dest_file(文件)

# 将文件 source_file 移动到目标目录 dest_directory 中
mv source_file(文件) dest_directory(目录)

# 目录名dest_directory已存在，将source_directory移动到目录名dest_directory中；
# 目录名 dest_directory 不存在则 source_directory 改名为目录名dest_directory；
mv source_directory(目录) dest_directory(目录)

# 报错
mv source_directory(目录) dest_file(文件)
```

## cp（复制）

cp（英文全拼：copy file）命令主要用于复制文件或目录。

### 语法

```bash
cp [options] source dest
cp [options] source... directory
```

### 参数

```bash
# 此选项通常在复制目录时使用，它保留链接、文件属性，并复制目录下的所有内容。
# 其作用等于dpR参数组合。
-a

# 复制时保留链接。这里所说的链接相当于 Windows 系统中的快捷方式。
-d

# 覆盖已经存在的目标文件而不给出提示。
-f

# 与 -f 选项相反，在覆盖目标文件之前给出提示，要求用户确认是否覆盖，
# 回答 y 时目标文件将被覆盖。
-i

# 除复制文件的内容外，还把修改时间和访问权限也复制到新文件中。
-p

# 若给出的源文件是一个目录文件，此时将复制该目录下所有的子目录和文件。
-r

# 不复制文件，只是生成链接文件。
-l
```

### 使用

```bash
 # 使用指令 cp 将当前目录 test/ 下的所有文件复制到新目录 newtest 下
 cp –r test/ newtest  
```

## cat（创建、查看、连接、重定向）

命令用于连接文件并打印到标准输出设备上。

```bash
# 语法
cat [选项] fileName

# 参数
# 由1开始对所有输出的行数编号
-n
# 只不过对于空白行不编号
-b
# 当遇到有连续两行以上的空白行，就替换为一行的空白行
-s
# 使用^和M-符号，除了LFD和TAB以外
-v
# 在每行结束处显示$
-E
# 将TAB字符显示为^
-T
# 等价于-vET
-A
# 等价于"-vE"选项
-e
# 等价于"-vT"选项
-t

# 例子
# 查看文件内容
cat test.txt
```

## tar（压缩/解压）

 打包：指将多个文件（或目录）合并成一个文件，方便在不同节点之间传递或在服务器集群上部署。

 压缩或打包文件常见扩展名：  *.tar, *.tar.gz, *.gz, *.bz2, *.Z。

```bash
# 将文件夹打包，也能将包解开成文件夹。
# 参数
#（create）建立打包文件
-c
# 查看打包文件的内容含有哪些文件
-t
# 解打包或解压缩的功能，可以搭配-C(大写)在特定目录解开
-x
# 通过bzip2的支持进行压缩/解压缩，此时文件最好为*.tar.bz2
-j
# 通过gzip的支持进行压缩/解压缩，此时文件最好为*.tar.gz
-z
# 在压缩/解压缩的过程中，将正在处理的文件名显示出来
-v
# filename：-f 后面跟处理后文件的全名称（路径+文件名+后缀名）
-f 
# 目录：这个选项用在解压缩，若要在特定目录解压缩，可以使用这个选项
-C
# 保留备份数据的原本权限与属性，常用于备份(-c)重要的配置文件
-p
# FILE:在压缩的过程中,不要将那些文件打包（后面可以跟多个，空格隔开）
--exclude=<文件夹或者文件> 

# 压缩命令
tar -czf out/<文件名>.tar.gz  --exclude=app/public

# 解压命令
tar -zxvf <文件名>.tar.gz -C <新的文件夹>
```

## scp（上传/下载）

加密的方式在本地主机和远程主机之间复制文件(上传到远程主机 / 下载到本地)。

```bash
# 前一个文件是源文件 后一个文件是目标文件
scp [可选参数] file_source file_target

# 本地文件传远程的写法
scp 本地路径 远程用户名@远程IP地址:远程文件夹路径
# 🌰
scp /home/test/test.txt  root@192.168.1.200:/home/test/test.txt 

# 从远程复制到本地，就调换源文件和目标文件位置就行了
scp root@192.168.1.200:/home/test/test.txt /home/test/test.txt 

# 安装文件到本地当前目录来
scp root@192.168.1.200:/home/test/test.txt test.txt 
```

### 参数

```bash
# 强制scp命令使用协议ssh1
-1
# 强制scp命令使用协议ssh2
-2
# 强制scp命令只使用IPv4寻址
-4
# 强制scp命令只使用IPv6寻址
-6
# 使用批处理模式（传输过程中不询问传输口令或短语）
-B
# 允许压缩。（将-C标志传递给ssh，从而打开压缩功能）
-C
# 保留原文件的修改时间，访问时间和访问权限
-p
# 不显示传输进度条
-q
# 递归复制整个目录
-r
# 详细方式显示输出。
# scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题
-v
# cipher： 以cipher将数据传输进行加密，这个选项将直接传递给ssh
-c
# ssh_config： 指定一个替代的ssh配置文件，此参数直接传递给ssh
-F
# identity_file： 从指定文件中读取传输时使用的密钥文件，此参数直接传递给ssh
-i
# limit： 限定用户所能使用的带宽，以Kbit/s为单位
-l
# ssh_option： 如果习惯于使用ssh_config(5)中的参数传递方式
-o
# port：注意是大写的P, port是指定数据传输用到的端口号
-P
# program： 指定加密传输时所使用的程序。此程序必须能够理解ssh(1)的选项
-S
```

## ln（创建连接-软链/硬链）

ln命令是一个非常重要而且常用命令，它用于为文件或者目录创建链接。

```bash
# 语法
ln [参数][源文件或目录][目标文件或目录]

# 创建一个文件，用于演示软链接。
# 创建文件
touch test.txt
# 创建软链接
ln -sf test.txt test1.txt
# 检查结果
ll test1.txt

# 创建硬连接
# 创建文件
touch testH.txt
# 创建硬链接，没有使用选项
ln testH.txt testH1.txt
# 检查结果
ll testH1.txt
```

### 参数

```bash
# 必要参数
# 删除，覆盖以前建立的链接
-b 
# 允许超级用户制作目录的硬链接
-d
# 强制执行
-f
# 交互模式，文件存在则提示用户是否覆盖
-i
# 把符号链接视为一般目录
-n
# 软链接(符号链接)
-s
# 显示详细的处理过程
-v

# 选择参数
# 显示帮助信息
--help
# 显示版本信息
--version
```

## pwd（查看当前路径）

pwd 命令将当前目录的全路径名称（从根目录）写入标准输出。全部目录使用 /（斜线）分隔。第一个 / 表示根目录，最后一个目录是当前目录。

```bash
# 语法
pwd [参数]
```

### 参数

```bash
# 如果 PWD 环境变量包含了不包含文件名 
# .（点表示当前目录）或 ..（点点表示父目录）的当前目录的绝对路径名，
# 则显示 PWD 环境变量的值。否则，-L 标志与 -P 标志一样运行。 
-L

# 显示当前目录的绝对路径名。
# 与-P标志一起显示的绝对路径不包含在路径名的绝对路径中涉及到符号链接类型的文件的名称。
-P
```

## 查看端口占用情况

### lsof

lsof(list open files)是一个列出当前系统打开文件的工具。

#### 语法

```bash
lsof -i:端口号
```

#### 命令

```bash
# 查看8080端口占用
lsof -i:8080

# 显示开启文件abc.txt的进程
lsof abc.txt

# 显示abc进程现在打开的文件
lsof -c abc

# 列出进程号为1234的进程所打开的文件
lsof -c -p 1234

# 显示归属gid的进程情况
lsof -g gid

# 显示目录下被进程开启的文件
lsof +d /usr/local/

# 同上，但是会搜索目录下的目录，时间较长
lsof +D /usr/local/

# 显示使用fd为4的进程
lsof -d 4

# 显示所有打开的端口和UNIX domain文件
lsof -i -U
```

### netstat

netstat -tunlp 用于显示 tcp，udp 的端口和进程等相关情况。

#### 语法

```bash
netstat -tunlp | grep 端口号
```

#### 参数

```bash
# 仅显示tcp相关选项
-t (tcp) 

# 仅显示udp相关选项
-u (udp)

# 拒绝显示别名，能显示数字的全部转化为数字
-n

# 仅列出在Listen(监听)的服务状态 
-l

# 显示建立相关链接的程序名
-p
```

#### 命令

```bash
# 查看当前所有tcp端口
netstat -ntlp

# 查看所有80端口使用情况
netstat -ntulp | grep 80

# 查看所有3306端口使用情况
netstat -ntulp | grep 3306
```

### kill

杀掉对应的进程。

#### 语法

```bash
kill -9 [PID]
```

## tail

tail 命令可用于查看文件的内容，有一个常用的参数 -f 常用于查阅正在改变的日志文件。

### 语法

```bash
tail [参数] [文件]
```

### 参数

```bash
# 循环读取
-f

# 不显示处理信息
-q

# 显示详细的处理信息
-v

# 显示的字节数
-c<数目>

# 显示文件的尾部 n 行内容
-n<行数>

# 与-f合用,表示在进程ID,PID死掉之后结束
--pid=PID

# 从不输出给出文件名的首部
-q, --quiet, --silent

# 与-f合用,表示在每次反复的间隔休眠S秒
-s, --sleep-interval=S 
```

### 命令

```bash
# 默认显示最后 10 行
tail notes.log

# 要跟踪名为 notes.log 的文件的增长情况
tail -f notes.log

# 从第 20 行至文件末尾
tail -n +20 notes.log

# 显示文件 notes.log 的最后 10 个字符
tail -c 10 notes.log
```

## grep

grep 命令用于查找文件里符合条件的字符串。

grep 指令用于查找内容包含指定的范本样式的文件，如果发现某文件的内容符合所指定的范本样式，预设 grep 指令会把含有范本样式的那一列显示出来。若不指定任何文件名称，或是所给予的文件名为 -，则 grep 指令会从标准输入设备读取数据。

### 语法

```bash
grep [-abcEFGhHilLnqrsvVwxy][-A<显示行数>][-B<显示列数>][-C<显示列数>]
[-d<进行动作>][-e<范本样式>][-f<范本文件>][--help][范本样式][文件或目录...]
```

### 参数

```bash
# 不要忽略二进制的数据
-a 或 --text

# 除了显示符合范本样式的那一列之外，并显示该行之后的内容。
-A<显示行数> 或 --after-context=<显示行数>

# 在显示符合样式的那一行之前，标示出该行第一个字符的编号。
-b 或 --byte-offset

# 除了显示符合样式的那一行之外，并显示该行之前的内容。
-B<显示行数> 或 --before-context=<显示行数>

# 计算符合样式的列数。
-c 或 --count

# 除了显示符合样式的那一行之外，并显示该行之前后的内容。
-C<显示行数> 或 --context=<显示行数>或-<显示行数> 

# 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep指令将回报信息并停止动作。
-d <动作> 或 --directories=<动作>

# 指定字符串做为查找文件内容的样式。
-e<范本样式> 或 --regexp=<范本样式>

# 将样式为延伸的正则表达式来使用。
-E 或 --extended-regexp

# 指定规则文件，其内容含有一个或多个规则样式，让grep查找符合规则条件的文件内容，
# 格式为每行一个规则样式。
-f<规则文件> 或 --file=<规则文件>

# 将样式视为固定字符串的列表。
-F 或 --fixed-regexp

# 将样式视为普通的表示法来使用。
-G 或 --basic-regexp

# 在显示符合样式的那一行之前，不标示该行所属的文件名称。
-h 或 --no-filename

# 在显示符合样式的那一行之前，表示该行所属的文件名称。
-H 或 --with-filename

# 忽略字符大小写的差别。
-i 或 --ignore-case

# 列出文件内容符合指定的样式的文件名称。
-l 或 --file-with-matches

# 列出文件内容不符合指定的样式的文件名称。
-L 或 --files-without-match

# 在显示符合样式的那一行之前，标示出该行的列数编号。
-n 或 --line-number

# 只显示匹配PATTERN 部分。
-o 或 --only-matching

# 不显示任何信息。
-q 或 --quiet或--silent

# 此参数的效果和指定"-d recurse"参数相同。
-r 或 --recursive

# 不显示错误信息。
-s 或 --no-messages

# 显示不包含匹配文本的所有行。
-v 或 --invert-match

# 显示版本信息。
-V 或 --version

# 只显示全字符合的列。
-w 或 --word-regexp

# 只显示全列符合的列。
-x --line-regexp

# 此参数的效果和指定"-i"参数相同。
-y 

# 将模式解释为Perl正则表达式
-P
```

## xargs

- xargs（英文全拼： eXtended ARGuments）是给命令传递参数的一个过滤器，也是组合多个命令的一个工具。

- xargs 可以将管道或标准输入（stdin）数据转换成命令行参数，也能够从文件的输出中读取数据。

- xargs 也可以将单行或多行文本输入转换为其他格式，例如多行变单行，单行变多行。

- xargs 默认的命令是 echo，这意味着通过管道传递给 xargs 的输入将会包含换行和空白，不过通过 xargs 的处理，换行和空白将被空格取代。

- xargs 是一个强有力的命令，它能够捕获一个命令的输出，然后传递给另外一个命令。

### 语法

```bash
somecommand | xargs -item  command 
```

### 参数

```bash
# 从文件中读入作为 stdin
-a file

# 注意有的时候可能会是-E，flag必须是一个以空格分隔的标志，
# 当xargs分析到含有flag这个标志的时候就停止。
-e flag 

# 当每次执行一个argument的时候询问一次用户。
-p

# 后面加次数，表示命令在执行的时候一次用的argument的个数，默认是用所有的。
-n num

# 表示先打印命令，然后再执行。
-t

# 或者是-I，这得看linux支持了，将xargs的每项名称，
# 一般是一行一行赋值给 {}，可以用 {} 代替。
-i
-r no-run-if-empty 当xargs的输入为空的时候则停止xargs，不用再去执行了。

# 命令行的最大字符数，指的是 xargs 后面那个命令的最大命令行字符数。
-s num

# 从标准输入一次读取 num 行送给 command 命令。
-L num
-l 同 -L。

# delim 分隔符，默认的xargs分隔符是回车，argument的分隔符是空格，
# 这里修改的是xargs的分隔符。
-d 

# exit的意思，主要是配合-s使用。
-x 

# 修改最大的进程数，默认是1，为0时候为as many as it can ，
# 这个例子我没有想到，应该平时都用不到的吧。
-P 
```

## mac下的命令

```bash
# 打开文件和文件夹
open <file>

# 进入根目录
cd /

# 查看文件夹目录
pwd
```
