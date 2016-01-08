# ConcurrentHashMap

## hash方法

在对传入的key做hash的时候，是用的以下方法：

```
    int hash = spread(key.hashCode());

    /**
     * Spreads (XORs) higher bits of hash to lower and also forces top
     * bit to 0. Because the table uses power-of-two masking, sets of
     * hashes that vary only in bits above the current mask will
     * always collide. (Among known examples are sets of Float keys
     * holding consecutive whole numbers in small tables.)  So we
     * apply a transform that spreads the impact of higher bits
     * downward. There is a tradeoff between speed, utility, and
     * quality of bit-spreading. Because many common sets of hashes
     * are already reasonably distributed (so don't benefit from
     * spreading), and because we use trees to handle large sets of
     * collisions in bins, we just XOR some shifted bits in the
     * cheapest possible way to reduce systematic lossage, as well as
     * to incorporate impact of the highest bits that would otherwise
     * never be used in index calculations because of table bounds.
     */
    static final int spread(int h) {
        return (h ^ (h >>> 16)) & HASH_BITS;
    }
```

看不懂！！

## 实现原理

ConcurrentHashMap使用`分段锁`技术，将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问，能够实现真正的并发访问。


## HashTable

HashTable容器使用`synchronized`（他的get和put方法的实现代码如下）来保证线程安全


## 其他


多线程相关包，最底层都调用了`sun.misc.Unsafe`包里面的方法


[Java transient关键字](http://www.blogjava.net/fhtdy2004/archive/2009/06/20/286112.html)