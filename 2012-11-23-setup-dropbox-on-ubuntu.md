# 2012-11-23-setup-dropbox-on-ubuntu


Dropbox是一款资料同步软件，只要把资料存在云端，就可以无处不在的访问到这些资料。今天在Ubuntu12.04下安装了Dropbox，把安装过程和遇到的问题写下来。
<!--more-->
###1. 安装过程

访问[Dropbox for Linux](https://www.dropbox.com/install?os=linux)，可以下载deb文件，也可以下载源码编译安装，也可以从命令行安装。我选择从命令行安装。

根据系统版本的不同，输入不同的命令进行安装就行：

    # 32位
    cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86" | tar xzf - 
    # 64位
    cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -

安装完之后，运行
    
    ~/.dropbox-dist/dropboxd

就可以运行dropbox了。

###2. 自动启动Dropbox

在/etc/init.d/文件夹中添加一个文件auto_dropbox.sh，添加以下代码：
    
    ~/.dropbox-dist/dropboxd &

保存退出到shell，输入以下命令：
   
    sudo chmod +x /etc/init.d/auto_dropbox.sh # 设置script的执行权限
    sudo update-rc.d auto_dropbox.sh defaults # 设置script在系统启动时执行

这样，就可以在系统启动的时候自动启动dropbox，可以使用命令：
  
    dropbox status

来查询dropbox的状态。但是，每次启动都不能自动运行，不知道为什么。还是在启动之后，运行命令在后台运行吧。

###3. 相关链接
* [dropbox for linux download](https://www.dropbox.com/install?os=linux)

