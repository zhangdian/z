# shell

几乎很少在windows里面长期连接linux服务器，一般都使用git自带的bash连接，但是在工作中，很难大规模的用起来，难以维护。

[Win免费SSH客户端工具](http://server.zol.com.cn/522/5222509_all.html) 这里介绍了很多shell连接linux服务器的工具，都蛮好的。经过对比，我使用的是SmarTTY，支持多连接，每个连接支持多回话，还是很给力的。初步看上去，有点像tmux。用着看看。

使用了一段时间SmarTTY，还是不方便，还是回归PuTTY。


### PUTTY使用

原生的putty不支持标签，不支持保存密码。

支持标签，可以通过SuperPutty这个工具来处理，他是对putty的一个封装。[点此下载](http://7xpmu3.com1.z0.glb.clouddn.com/SuperPuTTY.zip)

支持自动登录有2种解决方式：

1. 右键->属性->目标: "C:/Program Files/PuTTY/putty.exe" -load "colinux" -ssh -l root -pw root
2. 这个也是对putty的一个封装，可以在配置putty的时候填写密码参数: [点此下载](http://7xpmu3.com1.z0.glb.clouddn.com/putty(%E6%94%AF%E6%8C%81%E4%BF%9D%E5%AD%98%E5%AF%86%E7%A0%81))
3. 也是使用SuperPutty，在配置的时候，在“Extra PuTTY Arguments”里面指定-pw参数，如果所使用的PuTTY不支持这个参数，那就只能用PublicKey了。