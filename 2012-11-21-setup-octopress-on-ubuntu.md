# 2012-11-21-setup-octopress-on-ubuntu


之前安装了在Windows7下面安装了Octopress，现在也把在Ubuntu12.04下安装过程中遇到的问题记录一下。
<!--more-->
#1. 环境搭建

##1.1 安装git

    apt-get install git

##1.2安装ruby

###1.2.2 安装RVM
RVM(Ruby Version Manager)管理ruby环境的安装，而octopress需要ruby1.9.3。使用如下命令就能安装rvm：

    curl -L https://get.rvm.io | bash -s stable --ruby

###1.2.1 安装ruby依赖
直接安装RVM会失败，在此之前要安装ruby的依赖包，使用如下命令安装ruby依赖：
    
    apt-get install build-essential bison openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-0 libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake

如果rvm安装失败的情况下，最后彻底清除rvm环境，然后安装依赖，然后安装rvm，使用如下的代码应该可以彻底清除rvm环境：
    
    sudo apt-get --purge remove ruby-rvm
    sudo rm -rf /usr/share/ruby-rvm /etc/rvmrc /etc/profile.d/rvm.sh

然后使用如下命令查询rvm，确认没有输出，可以尝试打开新的窗户来查询。
 
    env | grep rvm

_注：每次使用rvm之前好像要source一下~/.rvm中的一个script才能正常工作。_

###1.2.3 安装ruby
使用如下命令安装ruby就行：
    rvm install 1.9.3
    rvm use 1.9.3
    rvm rubygems latest

#2. 安装Octopress
使用如下命令就可以把octopress clone下来了：

    git clone git://github.com/imathis/octopress.git octopress

然后安装依赖：

    gem install bundler
    rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command
    bundle install

最后执行命令安装默认主题：

	rake install

#3. 其他
* 其他项目比如博客设置，编写博客和在windows下面是一样的了；
* 在windows下面是使用MarkdownPad来编辑的，在Ubuntu下可以使用retext来编辑；

#4. 参考链接
* [Installed Ruby 1.9.3 with RVM but command line doesn't show ruby -v](http://stackoverflow.com/questions/9056008/installed-ruby-1-9-3-with-rvm-but-command-line-doesnt-show-ruby-v/9056395#9056395)
* [Generating SSH Keys](https://help.github.com/articles/generating-ssh-keys)
* [RVM “ERROR: Loading command: install (LoadError) > no such file to load — zlib”](http://413486774.iteye.com/blog/1166431)
* [Octopress Setup](http://octopress.org/docs/setup/)