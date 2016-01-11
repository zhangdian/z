# 锁

[乐观锁和悲观锁](http://www.cnblogs.com/guyufei/archive/2011/01/10/1931632.html)

CAS 乐观锁

Sychorized 悲观锁

Lock 乐观锁 乐观锁实现的机制就是CAS操作（Compare and Swap）

[Optimistic or pessimistic locking - Which one should you pick?](http://blog.couchbase.com/optimistic-or-pessimistic-locking-which-one-should-you-pick)



## JAVA 锁

http://docs.oracle.com/javaee/6/tutorial/doc/gkjiu.html

![JAVA锁](http://7xpmu3.com1.z0.glb.clouddn.com/java_lock_mode.png)


## MySQL锁

[mysql事务和锁InnoDB](http://www.cnblogs.com/zhaoyl/p/4121010.html)

InnoDB  共享锁（读）  排它锁（写）


乐观锁适用于写比较少的情况下，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。但如果经常产生冲突，上层应用会不断的进行retry，这样反倒是降低了性能，所以这种情况下用悲观锁就比较合适。



## 可重入性

可重入的意思是某一个线程是否可多次获得一个锁，比如`synchronized`就是可重入的，`ReentrantLock`也是可重入的 


## Lock

Lock的默认实现ReentrantLock是非公平锁的，没有提供锁的公平性。

Lock提供了`无条件的、可轮询的、定时的、可中断`的锁获取操作，所有加锁和解锁方法都是显式的。

Lock的实现必须提供具有`与内部加锁相同的内存可见性语义`。但是`加锁的语义，调度算法，顺序保证，性能特性`都可以不同。

在内部锁不能满足使用时，ReentrantLock才被作为更高级的工具，当你需要以下高级特性时，才应使用：`可定时的、可轮询的、可中断的锁获取操作、公平队列、非块结构的锁`。否则，请使用synchronized。

### 公平锁 vs 非公平锁

![非公平锁由于公平锁原因](http://7xpmu3.com1.z0.glb.clouddn.com/%E9%9D%9E%E5%85%AC%E5%B9%B3%E9%94%81%E7%94%B1%E4%BA%8E%E5%85%AC%E5%B9%B3%E9%94%81%E5%8E%9F%E5%9B%A0.png)

##参考资料
* 《Java Concurrency in Practice》
* 《Concurrent Programming in Java Design principles and patterns》
* [详细分析Java中断机制](http://www.infoq.com/cn/articles/java-interrupt-mechanism)  赞，好文
* [FindBugs，有一个未释放锁的侦测器](http://findbugs.sourceforge.net/)
