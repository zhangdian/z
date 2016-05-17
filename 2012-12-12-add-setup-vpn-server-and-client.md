# 2012-12-12-add-setup-vpn-server-and-client
查找了半天的资料，终于在vps上设置好了vpn，在ubuntu中，ssh到服务器，在本地做一个代理，别的客户端就可以通过设置代理到这台机器翻墙了。

####1. 在vps上设置，以允许vpn
#####1.1 开启TUN-TAP
首先确认你的vps的TUN-TAP是否开启，通过以下命令验证：

```
cat /dev/net/tun
```

如果输出显示如下，那么说明你的是启用的。我的是没有启动的，然后去我的vps后台去enabled就好了。

```
cat: /dev/net/tun: File descriptor in bad state
```

#####1.2 升级系统，安装软件
下一步就是升级系统，安装ppp、iptables和pptp：

```
yum update -y
yum install -y ppp iptables
rpm -ivh http://acelnmp.googlecode.com/files/pptpd-1.3.4-1.rhel5.1.i386.rpm
```

#####1.3 配置pptp
 * 编辑/etc/pptpd.conf文件，把下面字段前面的#去掉即可：

```
localip 192.168.0.1
remoteip 192.168.0.234-238,192.168.0.245
```

* 接下来再编辑/etc/ppp/options.pptpd，去掉ms-dns前面的#，并使用Google的NS，修改成如下字段：

```
ms-dns 8.8.8.8
ms-dns 8.8.4.4
```

* 设置pptp VPN账号密码，需要编辑/etc/ppp/chap-secrets这个文件。我的设置是：

```
vpn pptpd vpn *
```

#####1.4 修改内核设置，使其支持转发
编辑/etc/sysctl.conf文件，将“net.ipv4.ip_forward”的值设为1，变成下面的形式：

```
net.ipv4.ip_forward=1
```

然后保存退出。

```
sysctl -p
```

#####1.5 设置转发规则
经过前面的设置之后，我们vpn已经可以进行拨号了，但是还不能访问任何的网页，这里需要添加转发规则， 在命令行中输入：

```
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
```

注意这里的192.168.0.0/24是根据前面1.3的localip的设置得来的。还有就是这里的eth0，如果你的外网网卡不是eth0的话，要设置为相应的外网网卡。

然后保存iptables：

```
/etc/init.d/iptables save
```

#####1.6 重启iptables和pptp

```
/etc/init.d/iptables restart
/etc/init.d/pptpd restart
```

#####1.7 设置为自启动
将pptp和iptables设置为开机自动运行，这样就不需要每次重启服务器后手动启动服务了：

```
chkconfig pptpd on
chkconfig iptables on
```

####2. 客户端设置
在一个机器上使用如下代码ssh到vps上，然后就可以把他当成一个代理了，然后任何计算机都可以设置代理到这个机器，就可以成功翻墙了。

```
ssh -qTfnN -D :7070 -g user@youdomain.com
```
####3. 错误处理
#####3.1 错误619
出现错误619的话，则输入以下命令

```
mknod /dev/ppp c 108 0
```

#####3.2 错误734
出现“错误734：ppp链接控制协议终止”解决的方法是：

```
vi /etc/ppp/options.pptpd
require-mppe-128 -> # require-mppe-128
拨号连接–>安全–>要求数据加密(没有就断开) 前面的勾取消
```
