# 2013-01-07-solve-the-problem-of-bad-file-number-where-ssh-on-port-22
####问题
当Octopress有更新的时候，需要执行以下命令将其更新到线上：

```
rake generate
rake deploy
```

可是，有的时候会报“Bad file number:22”的错误（我是在家的时候），原因是家里的网络出口屏蔽了22端口，所以出不去。
<!-- more -->
在github网站上可以看到，有两种方式clone代码：
```
ssh: git@github.com:zhangdian/zhangdian.github.com.git
https: https://github.com/zhangdian/zhangdian.github.com.git
```

当使用ssh方式的时候，ssh默认使用的是22端口，故而有的时候会出现问题。而使用后面的https协议方式是可以的。

####解决方案
在octopress文件夹中，在执行了rake generate之后，会把生成之后的网站代码保存在_deploy文件夹中，进入_deploy文件夹可以看到，这个文件夹里的文件就是origin master的代码。进到_deploy/.git/config，将url参数的值修改为https地址，即可。

同样，如果要把source分支的代码push带github，可以查看octopress主文件夹中.git/config的内容，可以将ssh地址修改为https地址。
