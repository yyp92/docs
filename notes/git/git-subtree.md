# git subtree使用

https://zhuanlan.zhihu.com/p/253148857

前端如何在拆分工程的场景下共用代码。 一般有四个选择：

1. npm包共享
2. dll共享
3. submodule
4. subtree

## 总结

（一）、四个方案个人认为的排序是subtree = submodule > dll > npm，subtree由于git命令长+资料匮乏，一点问题就要看源码，稍微减分，和submodule相同。

（二）、subtree通过commit message来找到split和add的commit，截止遍历提交，因此split和add命令自动生成的提交信息不能随便修改。

（三）、如果split提交前一个提交是merge提交，则这次split无效，可以通过在两个提交中间提交一个空commit规避这个问题。

（四）、subtree所有问题都可以通过删掉subtree重新add解决，实在没办法可以先这样。
