# 2012-11-27-git-config

使用git进行版本管理，经常需要敲各种命令，比如
```  
git checkout
git status
git commit
git branch
```
等等，可以在git中进行全局配置，让这些命令更加短一些：
```
git config --global alias.co chekcout
git config --global alias.st status
git config --global alias.ci commit
git config --global alias.br branch
```
这样，以后就可以敲比较短的命令了。