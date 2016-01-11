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


GC机制的基本算法是：`分代收集`，这个不用赘述。下面阐述每个分代的收集方法。

* 年轻代：

事实上，在上一节，已经介绍了新生代的主要垃圾回收方法，在新生代中，使用`“停止-复制”`算法进行清理，将新生代内存分为2部分，1部分 Eden区较大，1部分Survivor比较小，并被划分为两个等量的部分。每次进行清理时，将Eden区和一个Survivor中仍然存活的对象拷贝到 另一个Survivor中，然后清理掉Eden和刚才的Survivor。

这里也可以发现，停止复制算法中，用来复制的两部分并不总是相等的（传统的停止复制算法两部分内存相等，但新生代中使用1个大的Eden区和2个小的Survivor区来避免这个问题）

由于绝大部分的对象都是短命的，甚至存活不到Survivor中，所以，Eden区与Survivor的比例较大，`HotSpot默认是 8:1，即分别占新生代的80%，10%，10%`。如果一次回收中，Survivor+Eden中存活下来的内存超过了10%，则需要将一部分对象分配到 老年代。用-XX:SurvivorRatio参数来配置Eden区域Survivor区的容量比值，默认是8，代表Eden：Survivor1：Survivor2=8:1:1.

* 老年代：

`老年代存储的对象比年轻代多得多，而且不乏大对象，对老年代进行内存清理时，如果使用停止-复制算法，则相当低效(WHY!!!)`。一般，老年代用的算法`标记-整理算法`，即：标记出仍然存活的对象（存在引用的），将所有存活的对象向一端移动，以保证内存的连续。

在发生Minor GC时，虚拟机会检查每次晋升进入老年代的大小是否大于老年代的剩余空间大小，如果大于，则直接触发一次Full GC，否则，就查看是否设置了-XX:+HandlePromotionFailure（允许担保失败），如果允许，则只会进行MinorGC，此时可以容忍内存分配失败；如果不允许，则仍然进行Full GC（这代表着如果设置-XX:+Handle PromotionFailure，则触发MinorGC就会同时触发Full GC，哪怕老年代还有很多内存，所以，最好不要这样做）。
     
* 方法区（永久代）：

永久代的回收有两种：常量池中的常量，无用的类信息，常量的回收很简单，没有引用了就可以被回收。对于无用的类进行回收，必须保证3点：

    * 类的所有实例都已经被回收
    * 加载类的ClassLoader已经被回收
    * 类对象的Class对象没有被引用（即没有通过反射引用该类的地方）
    
    
永久代的回收并`不是必须的`，可以通过参数来设置是否对类进行回收。HotSpot提供-Xnoclassgc进行控制。使用-verbose，-XX:+TraceClassLoading、-XX:+TraceClassUnLoading可以查看类加载和卸载信息
-verbose、-XX:+TraceClassLoading可以在Product版HotSpot中使用；-XX:+TraceClassUnLoading需要fastdebug版HotSpot支持


一些要注意的点：
* 在Eden区，HotSpot虚拟机使用了两种技术来加快内存分配。分别是bump-the-pointer和TLAB（Thread-Local Allocation Buffers）
* 年老代的空间一般比年轻代大，能存放更多的对象，在年老代上发生的GC次数也比年轻代少。当年老代内存不足时，将执行Major GC，也叫 `Full GC`。
* GC机制被称为`Minor GC`或叫Young GC。注意，Minor GC并不代表年轻代内存不足，它事实上只表示在Eden区上的GC。
* 如果对象比较大（比如长字符串或大数组），Young空间不足，则大对象会直接分配到老年代上（大对象可能触发提前GC，应少用，更应避免使用短命的大对象）
* GC算法：年轻代使用`停止-复制`算法；老年代使用`标记整理`算法
* Minor GC时，虚拟机会检查每次晋升进入老年代的大小是否大于老年代的剩余空间大小，如果大于，则直接触发一次Full GC
* 垃圾收集器是GC的具体实现，Java虚拟机规范中对于垃圾收集器没有任何规定，所以不同厂商实现的垃圾 收集器各不相同

### 垃圾收集器，垃圾收集算法

* Serial收集器：`新生代收集器`，使用`停止复制`算法，使用`一个线`程进行GC，串行，其它工作线程暂停。使用-XX:+UseSerialGC可以使用Serial+Serial Old模式运行进行内存回收（这也是虚拟机在Client模式下运行的默认值）
* ParNew收集器：`新生代收集器`，使用`停止复制`算法，Serial收集器的`多线程`版，用多个线程进行GC，并行，其它工作线程暂停，`关注缩短垃圾收集时间`。使用-XX:+UseParNewGC开关来控制使用ParNew+Serial Old收集器组合收集内存；使用-XX:ParallelGCThreads来设置执行内存回收的线程数。
* Parallel Scavenge 收集器：`新生代收集器`，使用`停止复制`算法，`关注CPU吞吐`量，即运行用户代码的时间/总时间，比如：JVM运行100分钟，其中运行用户代码99分钟，垃 圾收集1分钟，则吞吐量是99%，这种收集器能最高效率的利用CPU，适合运行后台运算（关注缩短垃圾收集时间的收集器，如CMS，等待时间很少，所以适 合用户交互，提高用户体验）。使用-XX:+UseParallelGC开关控制使用Parallel Scavenge+Serial Old收集器组合回收垃圾（这也是在Server模式下的默认值）；使用-XX:GCTimeRatio来设置用户执行时间占总时间的比例，默认99，即1%的时间用来进行垃圾回收。使用-XX:MaxGCPauseMillis设置GC的最大停顿时间（这个参数只对Parallel Scavenge有效），用开关参数-XX:+UseAdaptiveSizePolicy可以进行动态控制，如自动调整Eden/Survivor比例，老年代对象年龄，新生代大小等，这个参数在ParNew下没有。
* Serial Old收集器：`老年代收集`器，`单线程`收集器，串行，使用`标记整理`（整理的方法是Sweep（清理）和Compact（压缩），清理是将废弃的对象干掉，只留幸存的对象，压缩是将移动对象，将空间填满保证内存分为2块，一块全是对象，一块空闲）算法，使用单线程进行GC，其它工作线程暂停（注意，在老年代中进行标记整理算法清理，也需要暂停其它线程），在JDK1.5之前，Serial Old收集器与ParallelScavenge搭配使用。
* Parallel Old收集器：`老年代收集器`，`多线程`，并行，多线程机制与Parallel Scavenge差不错，使用`标记整理`（与Serial Old`不同`，这里的整理是Summary（汇总）和Compact（压缩），汇总的意思就是将幸存的对象复制到预先准备好的区域，而不是像Sweep（清理）那样清理废弃的对象）算法，在Parallel Old执行时，仍然需要暂停其它线程。Parallel Old在多核计算中很有用。Parallel Old出现后（JDK 1.6），与Parallel Scavenge配合有很好的效果，充分体现Parallel Scavenge收集器吞吐量优先的效果。使用-XX:+UseParallelOldGC开关控制使用Parallel Scavenge +Parallel Old组合收集器进行收集。
* CMS（Concurrent Mark Sweep）收集器：`老年代收集器`，致力于获取最短回收停顿时间（即缩短垃圾回收的时间），使用`标记清除`算法，多线程，优点是并发收集（用户线程可以和GC线程同时工作），停顿小。使用-XX:+UseConcMarkSweepGC进行ParNew+CMS+Serial Old进行内存回收，优先使用ParNew+CMS（原因见后面），当用户线程内存不足时，采用备用方案Serial Old收集。
* G1收集器：在JDK1.7中正式发布，与现状的新生代、老年代概念有很大不同，目前使用较少，不做介绍。

介绍了GC算法，待研究整理。