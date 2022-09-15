# git命令设置别名

在开发过程中，会经常使用git命令取进行一些操作。有些git命令比较长，容易记错。这时候我们可以为自己常用的git命令设置一个语义化的短别名。

## 添加别名设置

使用vim打开根目录下的 .gitconfig 文件，按 ‘i’ 进入编辑模式，在文件[alias]下添加自己常用的命令,.如果没有安装git 就 git安装
如果本地根目录下没有.gitconfig文件的话，可以自己新建一个。

![](../../\imgs\git-alias-1.png)

命令格式为 alias = git command。第一行的[alias]需要加上的

```git
[alias]
    co = checkout        //输入git co => git checkout
    st = status            //输入git st => git status
    cm = commit -m        //输入git cm => git commit -m
    br = branch            //输入git br => git branch 
    dif = diff            //输入git dif => git diff 
    pl = pull            //输入git pl => git pull
    ps = push            //输入git ps => git push
```

![](C:\Users\Administrator\Desktop\docs\imgs\git-alias-2.png)

## 保存设置并愉快的使用

除了上面的常用设置，也可以追加别的命令，格式都是一样的，添加完之后按esc退出vim编辑模式再 按 :wq 表退出并保存

保存完之后就可以愉快的使用上面设置好的命令了。比如：

![](C:\Users\Administrator\Desktop\docs\imgs\git-alias-3.gif)
