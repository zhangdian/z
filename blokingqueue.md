# BlokingQueue

##BlockingQueue队列的几个操作原语的含义

### boolean add(E e);

在添加元素的时候，若超出了度列的长度会直接抛出异常：

### boolean offer(E e);

### void put(E e) throws InterruptedException;

### boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException;

### E take() throws InterruptedException;

### E poll(long timeout, TimeUnit unit) throws InterruptedException;

### boolean remove(Object o);