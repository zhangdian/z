# jvm

jstack: http://www.cnblogs.com/nexiyi/p/java_thread_jstack.html

[9个java性能监控工具](https://blog.idrsolutions.com/2014/06/java-performance-tuning-tools/)


[JVM监控分析工具之jmap、jhat和jstack](https://github.com/yikebocai/blog/issues/32) 精华帖


[jstack dump日志文件详解](http://gudaoqing.blog.51cto.com/7729345/1332829)



下面对各个命令的解释，都是使用的这个文档：

`[JVM性能调优监控工具jps、jstack、jmap、jhat、jstat、hprof使用详解](http://my.oschina.net/feichexia/blog/196575?fromerr=1uGPAVF3)`

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

jstack里面的示例，很经典，很好的查找CPU利用很高的问题。

1. ps -ef 找到进程号
2. top -Hp pid  找到占用CPU时间最高的线程（TOP的TIME+列）
3. 将10进制的线程号转换为16进制
4. 在jstack栈文件中，搜索相应的16进制进程号，就找到了线程对应的栈信息

### jmap（Memory Map）
一般结合jhat（Java Heap Analysis Tool）查看堆内存使用情况。

####语法
jmap -histo[:live] pid

jmap -heap pid

jmap -permstat pid

一个很常用的情况是：用jmap把进程内存使用情况dump到文件中，再用jhat分析查看。jmap进行dump命令格式如下：

jmap -dump:format=b,file=dumpFileName pid

jhat -port 9998 dumpFileName_path


###jstat

jstat -gc 9943 250 4

其中详细解释了内存结构。

[JVM参数大全](http://www.cnblogs.com/edwardlauxh/archive/2010/04/25/1918603.html)


###PS:

 echo $((0xac))   16进制转10进制
 
 printf "%x\n" 21742  10进制转16进制