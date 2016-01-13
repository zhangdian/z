# 监控

## cpu
常用的监视工具有：vmstat, top,dstat和mpstat.


## 内存监控
关注页面调度，或者**页面交换**、加锁、线程迁移中的让步式和抢占式上下文切换。

用vmstat监控页面交换

* si和so，分别表示内存页面换入和换出的量
* free表示可用的空闲内存


随着页面调度的增加，空闲内存几乎不变。

当系统空闲内存很少时，内存页面换人和换出的速度几乎一样快。

观察是否同时出现`空闲内存少`和`页面调度频繁的情形`，说明系统可能在进行页面交换。

## 锁监控
让步式上下文切换
pidstat -w -I -p 9351 5

![](/images/pidstat.png)

隔离竞争锁： `Oracle Solaris Studio Performance Analyzer(Linux)`  一个很好的隔离和报告JAVA锁竞争的工具    
windows上的类似工具是`Intel VTune`和`AMD CodeAnalyst`


## 监控抢占式上下文监控

Linux使用`pidstat -w`监控抢占式上下文监控。

```
cswch/s
     Total  number  of  voluntary  context  switches  the task made per second.  A voluntary context switch occurs when a task blocks
     because it requires a resource that is unavailable.

nvcswch/s
     Total number of non voluntary context switches the task made per second.  A involuntary context switch takes place when  a  task
     executes for the duration of its time slice and then is forced to relinquish the processor.
```