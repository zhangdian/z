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
```

首先，获取锁的方式不一样，`offer`使用的`lock`，`put`使用的`lockInterruptibly`，为什么呢？

因为，put的原语中，如果队列中没有地方了，会进行等待，所以是`可中断的`。
