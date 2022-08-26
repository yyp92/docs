

# 本地同时链接github和gitee

## 生成 SSH 公钥

打开 `Git bash` 的终端，执行命令:

```bash
# "~/" 代表根路径 C:\Users\Administrator，校验时会默认搜索此路径
# 这里的名字可以根据需要来定义，不一定要是邮箱，
# 可以多个git源使用同一个ssh key，也可以多次执行命令，生成多个key来分别配置使用
ssh-keygen -t rsa -C "xxxxx@xxxxx.com"
```

执行之后按照提示完成三次回车，即可生成 `ssh key`

可以通过查看根路径中`.ssh` 文件夹的`.ssh/id_rsa.pub` 文件内容，或者输入 `cat ~/.ssh/id_rsa.pub`；获取到你的 `public key`。

## 配置 SSH 公钥

- 复制生成后的 `ssh key`，通过 github 和 gitee 中的 ssh 设置将生成的 `public key` 添加到账户中。

- 可以通过在终端输入 `ssh -T git@gitee.com`来验证是否添加成功，这里的`git@gitee.com`根据可以根据 `git` 仓库的 `ssh` 地址域名来输入，首次使用需要确认并添加主机到本机 `SSH` 可信列表，就像下面这样。

```bash
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ECDSA key fingerprint is SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

输入 `yes`，若返回 `Hi XXX! You've successfully authenticated, but Gitee.com does not provide shell access.` 内容，则证明添加成功。

## 配置本地仓库

- 使用 `ssh` 的方式 `clone` 项目
  
  ```bash
   git clone git@github.com:仓库地址
  ```

- 添加 remote 地址
  
  ```bash
  # 添加另一个仓库别名
  git remote add gitee <gitee仓库的git ssh地址>
  
  # 推送代码
  git push
  git push gitee
  ```

通过 `git remote -v` 查看是否添加成功，如果有刚添加的地址也就证明添加成功。

如果第一次推送不上去，则使用 `git push origin master -f` 强推。
