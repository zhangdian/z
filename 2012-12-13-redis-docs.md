# 2012-12-13-redis-docs
###1. Redis介绍
####1.1 什么是redis
REmote DIctionary Server(Redis)是一个由Salvatore Sanfilippo写的key-value存储系统。Redis提供了一些丰富的数据结构，包括lists，sets，ordered sets以及hashes，当然还有和Memcached一样的strings结构。Redis当然还包括了对这些数据结构的丰富操作。

####1.2 redis的优点
* 性能极高：Redis能支持超过 100K+ 每秒的读写频率；
* 丰富的数据类型：Redis支持二进制安全的Strings，Lists，Hashes，Sets及Ordered Sets数据类型操作；
* __原子性：Redis的所有操作都是原子性的，同时Redis还支持对几个操作全并后的原子性执行；__
* 丰富的特性：Redis还支持 publish/subscribe，通知，key过期等等特性。

<!-- more -->

###2. Redis的数据类型
####2.0 key&value
#####2.0.1 key
redis使用的是key作为存取对象的唯一标识，对“key”的通俗理解就是“字符串”。__Redis中的key值是二进制安全的，这意味着可以用任何二进制序列作为key值，从形如“kaka”的简单字符串到一个JPEG文件的内容都可以。空字符串也是有效key值。__

关于key有几条比较好的规则：

* 不要使用太长的key，比如1024个字节的key，因为这样不仅会浪费内存，而且会影响查询的效率；
* 也不要使用太短的key，比如使用“u:1000:pwd”来代替“user:1000:password”，这本身没有什么问题，但是后者更容易理解，并且增加的空间消耗相对于key object和value object本身来说是很小的；
* 最好使用一种固定的模式。

####2.1 String
#####2.1.0 简介
这是最简单Redis类型。如果你只用这种类型，Redis就像一个可以持久化的memcached服务器，因为memcache的数据时保存在内存的，系统重启后就没了。

redis字符串是二进制安全的，也就是说redis字符串可以包含任意类型的数据，比如一个JPEG图片，或者一个ruby object。一个字符串的最大长度是512M。

#####2.1.1 String的结构
redis string的实现包含在sds.c文件中，redis string的定义在sdshdr.h文件中：

```
struct sdshdr {
    long len;
    long free;
    char buf[];
};
// len代表buf中实际保存的字符串的长度，那么就可以再O(1)的时间内获取字符串的长度
// free代表buf中还剩余的空间长度
// 保存实际的字符串
// len + free 等于buf数组的长度
```

#####2.1.2 String的创建
在sds.h中定义了一个新的类型，实际上类型sds就是字符串指针的一个别名而已。

```
typedef char *sds;
```

在sds.c中定义的sdsnewlen函数创建一个新的字符串：

```
sds sdsnewlen(const void *init, size_t initlen) {
    struct sdshdr *sh;

    sh = zmalloc(sizeof(struct sdshdr)+initlen+1);
#ifdef SDS_ABORT_ON_OOM
    if (sh == NULL) sdsOomAbort();
#else
    if (sh == NULL) return NULL;
#endif
    sh->len = initlen;
    sh->free = 0;
    if (initlen) {
        if (init) memcpy(sh->buf, init, initlen);
        else memset(sh->buf,0,initlen);
    }
    sh->buf[initlen] = '\0';
    return (char*)sh->buf;
}
```

可以看到，函数介绍两个参数，一个是字符串指针，一个是字符串长度。然后分配一个地址空间，包括三个部分：struct sdshdr长度、字符串长度以及字符串最后的结尾符。最后对struct sdshdr的各个元素赋值，返回其buf部分，也就是实际的字符串。

假设使用如下代码初始化它，那么内存结构大概是下面这个样子的：

```
// 初始化
sdsnewlen("bd17kaka", 8);

// 结果
-----------
|8|0|bd17kaka|
-----------
^   ^
sh  sh->buf

```

#####2.1.3 通过buf指针获取sh指针
从上面的内存结构图可以看到，buf的内存地址实际上只比sh的内存地址多了len和free两个变量的长度，也就是两个long的长度，实际上就是struct sdshdr的长度，那么可以通过以下方法获取sh的指针：

```
struct sdshdr *sh = (void*) (s-(sizeof(struct sdshdr)));

// 获取字符串的长度
size_t sdslen(const sds s) {
    struct sdshdr *sh = (void*) (s-(sizeof(struct sdshdr)));
    return sh->len;
}
```

在redis源代码其他地方，可以经常看到这个技巧的使用。

#####2.1.4 redis使用
redis可以进行很多操作，下面列出几种比较常用的，所有的操作见[字符串操作](http://redis.io/commands/#string)。

* 使用诸如INCR, DECR, INCRBY的原子操作；
* 使用APPEND进行字符串append操作；
* 随机访问：SETRANGE, GETRANGE；
* 在一个极小空间内编码很多数据；
* 访问一个字符串的某一位的数据：GETBIT, SETBIT；


#####2.1.5 INCR
INCR将字符串的值解析为一个整数，然后+1，然后将这个字符串的值设置为新获得的整数值，类似的命令有DECR, INCR等等。如果这个字符串无法解析为一个整数的话，会报一个错：

```
ERR value is not an integer or out of range
```

#####2.1.6 INCR的原子性
INCR的原子性也就说，假设多个客户端同时对一个key进行诸如INCR, DECR等的操作，也不会出现冲突的情况。比如key=‘2’，此时两个客户端同时发出“INCR ke”y的操作，那么最后key的值一定是4，而不会是3。在一个“read-increment-set”过程执行的时候，其他客户端不会同时执行一个命令。

####2.2 List
#####2.2.0 简介
列表有两种，分别是linked list和array list。前者使用链表实现，后者实际上是一个数组，两个的优缺点很清楚。

Redis的list使用linked list实现的，那么意味着在头部和尾部添加一个元素的时间复杂度是常数级别的，不管现有的list的规模多大，时间复杂度都一样。其相对于arraylist的缺点就是访问速度了，arraylist通过index访问速度很快。

Redis Lists用linked list实现的原因是：对于数据库系统来说，至关重要的特性是：__能非常快的在很大的列表上添加元素__。另一个重要因素是，正如你将要看到的：__Redis lists能在常数时间取得常数长度__。





####2.3 set（集合）


####2.4 sorted set（有序集合）


####2.5 hash


###3. Publish/Subscribe


###4. 数据过期


###5. 事务性


###6. 持久化


###7. 管理


### 参考链接
* [A fifteen minute introduction to Redis data types](http://redis.io/topics/data-types-intro)
* [Data types](http://redis.io/topics/data-types)
* [redis官网](http://redis.io/)
* [String 操作](http://redis.io/commands/#string)
