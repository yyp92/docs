1. 如果mac笔记本电脑，GitHub不显示图片

2. 在笔记本终端输入以下命令
   
   ```bash
   # 打开host文件
   sudo vi /etc/hosts
   
   # 在host文件末尾加入以下内容
   199.232.68.133 raw.githubusercontent.com
   ```
