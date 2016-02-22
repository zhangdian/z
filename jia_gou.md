# 架构

## nginx haproxy lvs

* nginx：7层，可以根据url，域名配置；只支持http；
* haproxy：4层或7层；
* lvs：4层，链路层转发
* 

虚拟IP是根据ARP实现的。通过改变IP地址的MAC地址。
