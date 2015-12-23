# 监控

### cpu
常用的监视工具有：vmstat, top,dstat和mpstat.


### 内存监控
关注页面调度，或者**页面交换**、加锁、线程迁移中的让步式和抢占式上下文切换。

vmstat的free列，监控剩余内存
vmstat的si和so，监控页面换入和换出的量