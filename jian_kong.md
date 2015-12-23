# 监控

### cpu
常用的监视工具有：vmstat, top,dstat和mpstat.


### 内存监控
关注页面调度，或者**页面交换**、加锁、线程迁移中的让步式和抢占式上下文切换。

vmstat的free列，监控剩余内存
vmstat的si和so，监控页面换入和换出的量

随着页面调度的增加，空闲内存几乎不变。

当系统空闲内存很少时，内存页面换人和换出的速度几乎一样快。


### 锁监控
让步式上下文切换
pidstat -w -I -p 9351 5

![三次握手和四次握手](/images/pidstat.png)

隔离竞争锁： Oracle Solaris Studio Performance Analyzer .. 一个很好的隔离和报告JAVA锁竞争的工具