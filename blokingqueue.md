# BlokingQueue


## 添加元素

### boolean add(E e);

在添加元素的时候，若超出了度列的长度会直接抛出异常：

### boolean offer(E e);

### void put(E e) throws InterruptedException;

向队尾添加元素的时候发现队列已经满了会发生阻塞一直等待空间，以加入元素。

### boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException;

## 删除元素

### E take() throws InterruptedException;

### E poll(long timeout, TimeUnit unit) throws InterruptedException;

### boolean remove(Object o);