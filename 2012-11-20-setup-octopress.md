# 2012-11-20-setup-octopress

1.Octopress安装
================
1.1 环境配置
----------------
我是在windows 7上安装的Octopress，Octopress的安装基本上按照[Octopress安装文档](http://octopress.org/docs/setup/)安装就行。我遇到的问题主要有以下几点：

* ruby的版本问题，Octopress必须要ruby1.9.3，如果版本不对的话，文档中提供了rbenv和rvm两种方法来安装合适的版本。我直接使用[http://www.douban.com/group/topic/29077892/](http://www.douban.com/group/topic/29077892/)中讲到的办法，也就是直接安装Ruby Installer和development kit就行了，在ruby的官网上下载就行
* 另外在执行rake的命令的时候，提示Octopress需要0.9.2.2版本，但是我的版本是10.0.1，最后查到可以在前面加上“bundle exec”，就可以通过执行

<!--more-->
1.2 博客部署
----------------
    rake setup_github_pages
	rake generate
	rake deploy
当然，这里需要再每个rake签名加上bundle exec。

1.3 编写博客
----------------
	rake new_post["title"]
使用上述命令添加一个博客页面，具体的信息见[http://octopress.org/docs/blogging/](http://octopress.org/docs/blogging/)。生成的博客文件都在“/source/_post/”中。
由于编码的原因，我直接在MINGW32里面编写博客文件后，在部署的时候会出问题，所以我使用了[MarkDownPad](http://markdownpad.com/)来编写生成的博客文件，然后再进行部署就不会出编码问题。

1.4 其他
----------------
这里只是Octopress最基本的功能，还有很多功能有待研究。。。

1.5 参考资料
----------------
* [在 Windows7 下从头开始安装部署 Octopress](http://sinosmond.github.com/blog/2012/03/12/install-and-deploy-octopress-to-github-on-windows7-from-scratch/)

2.github博客配置
================
2.1 博客名称
----------------
如果是想使用个人博客的话，可以将reposotory的名称设置为yourname.github.com，这里的yourname必须是你登陆github的用户名，那么最后的博客地址就是yourname.github.com；