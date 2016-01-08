# jvm

jstack: http://www.cnblogs.com/nexiyi/p/java_thread_jstack.html

[9个java性能监控工具](https://blog.idrsolutions.com/2014/06/java-performance-tuning-tools/)


[JVM监控分析工具之jmap、jhat和jstack](https://github.com/yikebocai/blog/issues/32) 精华帖


[jstack dump日志文件详解](http://gudaoqing.blog.51cto.com/7729345/1332829)


## JVM自带工具


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

![原文的图片](http://7xpmu3.com1.z0.glb.clouddn.com/181847_dAR9_111708.jpg)

jstat -gc 9943 250 4

其中详细解释了内存结构。

[JVM参数大全](http://www.cnblogs.com/edwardlauxh/archive/2010/04/25/1918603.html)


```
java -XX:+PrintFlagsFinal 查看jvm的默认参数
```

###PS:

 echo $((0xac))   16进制转10进制
 
 printf "%x\n" 21742  10进制转16进制
 
 
## JVM GC

[Java 内存区域和GC机制](http://www.cnblogs.com/zhguang/p/3257367.html)

一些要注意的点：
* 在Eden区，HotSpot虚拟机使用了两种技术来加快内存分配。分别是bump-the-pointer和TLAB（Thread-Local Allocation Buffers）
* 年老代的空间一般比年轻代大，能存放更多的对象，在年老代上发生的GC次数也比年轻代少。当年老代内存不足时，将执行Major GC，也叫 `Full GC`。
* GC机制被称为`Minor GC`或叫Young GC。注意，Minor GC并不代表年轻代内存不足，它事实上只表示在Eden区上的GC。
* 如果对象比较大（比如长字符串或大数组），Young空间不足，则大对象会直接分配到老年代上（大对象可能触发提前GC，应少用，更应避免使用短命的大对象）
* GC算法：年轻代使用`停止-复制`算法；老年代使用`标记整理`算法
* Minor GC时，虚拟机会检查每次晋升进入老年代的大小是否大于老年代的剩余空间大小，如果大于，则直接触发一次Full GC
* 垃圾收集器是GC的具体实现，Java虚拟机规范中对于垃圾收集器没有任何规定，所以不同厂商实现的垃圾 收集器各不相同
* 

介绍了GC算法，待研究整理。