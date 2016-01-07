# jvm

jstack: http://www.cnblogs.com/nexiyi/p/java_thread_jstack.html

[9个java性能监控工具](https://blog.idrsolutions.com/2014/06/java-performance-tuning-tools/)


[JVM监控分析工具之jmap、jhat和jstack](https://github.com/yikebocai/blog/issues/32) 精华帖


[jstack dump日志文件详解](http://gudaoqing.blog.51cto.com/7729345/1332829)

[JVM性能调优监控工具jps、jstack、jmap、jhat、jstat、hprof使用详解](http://my.oschina.net/feichexia/blog/196575?fromerr=1uGPAVF3)

###jps

jps -v 输出传入JVM的参数


###jstack

#### 语法
jstack [option] pid

jstack [option] executable core

jstack [option] [server-id@]remote-hostname-or-ip

#### 参数
-l long listings，会打印出额外的锁信息，在发生死锁时可以用jstack -l pid来观察锁持有情况

-m mixed mode，不仅会输出Java堆栈信息，还会输出C/C++堆栈信息（比如Native方法）