# PUTTY使用

原生的putty不支持标签，不支持保存密码。

支持标签，可以通过SuperPutty这个工具来处理，他是对putty的一个封装。[点此下载](http://7xpmu3.com1.z0.glb.clouddn.com/SuperPuTTY.zip)

支持自动登录有2种解决方式：

1. 右键->属性->目标: "C:/Program Files/PuTTY/putty.exe" -load "colinux" -ssh -l root -pw root
2. 这个也是对putty的一个封装，可以在配置putty的时候填写密码参数: [点此下载](http://7xpmu3.com1.z0.glb.clouddn.com/putty(%E6%94%AF%E6%8C%81%E4%BF%9D%E5%AD%98%E5%AF%86%E7%A0%81))
3. 也是使用SuperPutty，在配置的时候，在“Extra PuTTY Arguments”里面指定-pw参数，如果所使用的PuTTY不支持这个参数，那就只能用PublicKey了。