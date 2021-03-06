# BlokingQueue 之 ArrayBlockingQueue


## 添加元素

### boolean add(E e);

在添加元素的时候，若超出了度列的长度会直接抛出异常：

### boolean offer(E e);

offer方法在添加元素时，如果发现队列已满无法添加的话，会直接返回false。

### void put(E e) throws InterruptedException;

向队尾添加元素的时候发现队列已经满了会发生阻塞一直等待空间，以加入元素。

### boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException;

等待指定时间

## 删除元素


### boolean remove(Object o);

若队列为空，抛出NoSuchElementException异常。

### E poll(long timeout, TimeUnit unit) throws InterruptedException;

若队列为空，返回null。

### E take() throws InterruptedException;

若队列为空，发生阻塞，等待有元素。


## 初始化

参数fair，指定在添加元素的时候，是`公平方式`(FIFO)还是`不公平方式`；

fail参数最后传给`ReentrantLock`，它内部根据`fair`参数初始化了`Sync`对象：

```
sync = fair ? new FairSync() : new NonfairSync();
```

```
其中一个构造函数
    public ArrayBlockingQueue(int capacity, boolean fair) {
        if (capacity <= 0)
            throw new IllegalArgumentException();
        this.items = new Object[capacity];
        lock = new ReentrantLock(fair);
        notEmpty = lock.newCondition();
        notFull =  lock.newCondition();
    }
```

内部使用数组存储元素`this.items = new Object[capacity];`


## put 和 offer 对比

```
    public boolean offer(E e) {
        checkNotNull(e);
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            if (count == items.length)
                return false;
            else {
                enqueue(e);
                return true;
            }
        } finally {
            lock.unlock();
        }
    }
    
    public void put(E e) throws InterruptedException {
        checkNotNull(e);
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == items.length)
                notFull.await();
            enqueue(e);
        } finally {
            lock.unlock();
        }
    }
    
    public boolean offer(E e, long timeout, TimeUnit unit)
        throws InterruptedException {

        checkNotNull(e);
        long nanos = unit.toNanos(timeout);
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == items.length) {
                if (nanos <= 0)
                    return false;
                nanos = notFull.awaitNanos(nanos);
            }
            enqueue(e);
            return true;
        } finally {
            lock.unlock();
        }
    }
```

首先，获取锁的方式不一样，`offer`使用的`lock`，`put`使用的`lockInterruptibly`，为什么呢？

因为，put的原语中，如果队列中没有地方了，会进行等待，所以是`可中断的`。

看源代码就可以知道，`lock()`不处理中断，只是设置中断状态，`lockInterruptibly()`会处理中断，抛出中断异常。

两个方法会直接调用下述方法：

```

public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}

```
```
public final void acquireInterruptibly(int arg)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    if (!tryAcquire(arg))
        doAcquireInterruptibly(arg);
}

private void doAcquireInterruptibly(int arg)
    throws InterruptedException {
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return;
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

注意，两个condition的好处是：这里一个读锁，一个写锁。`如果用一个锁，在唤醒的时候，被阻塞的读和写都会收到信号，不知道唤醒的是读，还是写`，如果唤醒的经常错了，又得重新唤醒。

[JAVA 锁，讲的不错的一个个人博客](http://xiaobaoqiu.github.io/blog/2014/11/12/java-lock/)


## 公平锁(FairSync)和非公平锁(NonFairSync)

首先上代码:

```
    public ReentrantLock() {
        sync = new NonfairSync();
    }

    /**
     * Sync object for non-fair locks
     */
    static final class NonfairSync extends Sync {
        private static final long serialVersionUID = 7316153563782823691L;

        /**
         * Performs lock.  Try immediate barge, backing up to normal
         * acquire on failure.
         */
        final void lock() {
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
                acquire(1);
        }

        protected final boolean tryAcquire(int acquires) {
            return nonfairTryAcquire(acquires);
        }
    }
    
    /**
     * Sync object for fair locks
     */
    static final class FairSync extends Sync {
        private static final long serialVersionUID = -3000897897090466540L;

        final void lock() {
            acquire(1);
        }

        /**
         * Fair version of tryAcquire.  Don't grant access unless
         * recursive call or no waiters or is first.
         */
        protected final boolean tryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                if (!hasQueuedPredecessors() &&
                    compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0)
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
    }
```
ReentrantLock默认是是用`非公平锁`的。

`公平锁`通过调用`acquire(1)`来获取锁，具体机制后面讲；

`非公平锁`直接尝试设置state获取锁，如果失败，走和`公平锁`一样的逻辑。


`compareAndSetState`方法说明：

```
    /**
     * Atomically sets synchronization state to the given updated
     * value if the current state value equals the expected value.
     * This operation has memory semantics of a {@code volatile} read
     * and write.
     */
```

非公平锁，直接尝试获取锁。下面着重看`acquire(1)`

```
    /**
     * Acquires in exclusive mode, ignoring interrupts.  Implemented
     * by invoking at least once {@link #tryAcquire},
     * returning on success.  Otherwise the thread is queued, possibly
     * repeatedly blocking and unblocking, invoking {@link
     * #tryAcquire} until success.  This method can be used
     * to implement method {@link Lock#lock}.
     */
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
```

独占的方式获取，忽略中断。

这里的`tryAcquire()`在两种同步机制的实现是不一样的：

```
    公平：
    /**
     * Fair version of tryAcquire.  Don't grant access unless
     * recursive call or no waiters or is first.
     */
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
    
    不公平：
    protected final boolean tryAcquire(int acquires) {
        return nonfairTryAcquire(acquires);
    }
    final boolean nonfairTryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            if (compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0) // overflow
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
```

首先获取锁状态`state`：

* 如果==0，那么尝试设置；
* 如果当前线程就是自己，直接获取；

不同之处在于，`公平锁`会调用`hasQueuedPredecessors()`，来判断，是否有其他线程等待了更长的时间，程序内部是是用`链表`的形式来保存这种关系的。


如果`tryAcquire()`失败，就会执行`acquireQueued(addWaiter(Node.EXCLUSIVE), arg))`进行等待，中断自己。

`addWaiter`方法，初始化等待队列的节点Node的信息，`acquireQueued`代码如下：

```
    final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            boolean interrupted = false;
            for (;;) {
                final Node p = node.predecessor();
                if (p == head && tryAcquire(arg)) {
                    setHead(node);
                    p.next = null; // help GC
                    failed = false;
                    return interrupted;
                }
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    interrupted = true;
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
```
首先获取节点之前的节点，如果preNode是队首，尝试获取锁。获取成功就返回。

如果失败，如果需要等待，就等待否则循环尝试。





