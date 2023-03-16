# 国内快速访问github

## 方法一、Tiga - Github国内下载和克隆文件加速

值得推荐的谷歌浏览器插件，国内软件开发者的好帮手，解决国内下载和克隆GitHub文件太慢的问题。用上Tiga给GitHub加速，体验下载和克隆飞一般的速度。它的主要功能有第一个，给国内下载或者克隆GitHub的项目文件加速，体验超快的GitHub克隆和下载速度。第二个，可以一键前往cnpmjs.org的GitHub镜像。

### 方法二、修改系统host文件

- 第一步、打开IPAddress网站，查询github.com对应的IP地址（140.82.114.4）

- 第二步、修改本地电脑的host文件
  
  - windows: C:\Windows\System32\drivers\etc
  - linux: /etc/hosts
  - 直接在最后加入以下代码：
    - 140.82.114.4 github.com

- 第三步，刷新电脑dns缓存

- 打开cmd.exe程序，输入“ipconfig/flushdns”，按下键盘回车键
