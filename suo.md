# 锁

[乐观锁和悲观锁](http://www.cnblogs.com/guyufei/archive/2011/01/10/1931632.html)

CAS 乐观锁

Sychorized 悲观锁

Lock 乐观锁 乐观锁实现的机制就是CAS操作（Compare and Swap）

[Optimistic or pessimistic locking - Which one should you pick?](http://blog.couchbase.com/optimistic-or-pessimistic-locking-which-one-should-you-pick)


[mysql事务和锁InnoDB](http://www.cnblogs.com/zhaoyl/p/4121010.html)

InnoDB  共享锁（读）  排它锁（写）


乐观锁适用于写比较少的情况下，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。但如果经常产生冲突，上层应用会不断的进行retry，这样反倒是降低了性能，所以这种情况下用悲观锁就比较合适。


[详细分析Java中断机制](http://www.infoq.com/cn/articles/java-interrupt-mechanism)  赞，好文


## 可重入性

可重入的意思是某一个线程是否可多次获得一个锁，比如`synchronized`就是可重入的，`ReentrantLock`也是可重入的 


##参考资料
* 《Java Concurrency in Practice》
* 《Concurrent Programming in Java Design principles and patterns》

