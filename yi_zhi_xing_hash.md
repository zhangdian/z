# 一致性hash

先列几个通用的一致性HASH算法文章：

http://wiki.jikexueyuan.com/project/redis/cluster-up.html

http://blog.csdn.net/sparkliang/article/details/5279393

上述一致性hash算法，都是将数据看做缓存来处理的，也就是允许在添加机器之后，原来的数据被映射到其他机器上。

我想将redis当作数据库来看做，那么同一个key的数据只能允许映射到一个机器上。并且当机器挂机之后，有其中一台机器作为backup。

所以上述文档，还没有解决我的问题。redis内部自己的集群是可以解决这个问题的，但是是他们自己的算法。