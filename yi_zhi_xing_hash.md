# 一致性hash（consistent hashing）

先列几个通用的一致性HASH算法文章：

http://wiki.jikexueyuan.com/project/redis/cluster-up.html

http://blog.csdn.net/sparkliang/article/details/5279393

[redis 分片文档](http://redis.io/topics/partitioning)

上述一致性hash算法，都是将数据看做缓存来处理的，也就是允许在添加机器之后，原来的数据被映射到其他机器上。

我想将redis当作数据库来看做，那么同一个key的数据只能允许映射到一个机器上。并且当机器挂机之后，有其中一台机器作为backup。

所以上述文档，还没有解决我的问题。redis内部自己的集群是可以解决这个问题的，但是是他们自己的算法。

附上Redis提到的3中分片方法：客户端直接选择目标机器；通过代理；查询路由（Redis集群使用的方法）

* **Client side partitioning** means that the clients directly select the right node where to write or read a given key. Many Redis clients implement client side partitioning.
* **Proxy assisted partitioning** means that our clients send requests to a proxy that is able to speak the Redis protocol, instead of sending requests directly to the right Redis instance. The proxy will make sure to forward our request to the right Redis instance accordingly to the configured partitioning schema, and will send the replies back to the client. The Redis and Memcached proxy Twemproxy implements proxy assisted partitioning.
* **Query routing** means that you can send your query to a random instance, and the instance will make sure to forward your query to the right node. Redis Cluster implements an hybrid form of query routing, with the help of the client (the request is not directly forwarded from a Redis instance to another, but the client gets redirected to the right node).


redis文档讲到了我之前的那个问题：
####Data store or cache?

Although partitioning in Redis is conceptually the same whether using Redis as a data store or as a cache, there is a significant limitation when using it as a data store. When Redis is used as a data store, a given key must always map to the same Redis instance. When Redis is used as a cache, if a given node is unavailable it is not a big problem if a different node is used, altering the key-instance map as we wish to improve the availability of the system (that is, the ability of the system to reply to our queries).

Consistent hashing implementations are often able to switch to other nodes if the preferred node for a given key is not available. Similarly if you add a new node, part of the new keys will start to be stored on the new node.

The main concept here is the following:

* If Redis is used as a cache scaling up and down using consistent hashing is easy.
* If Redis is used as a store, a fixed keys-to-nodes map is used, so the number of nodes must be fixed and cannot vary. Otherwise, a system is needed that is able to rebalance keys between nodes when nodes are added or removed, and currently only Redis Cluster is able to do this - Redis Cluster is generally available and production-ready as of April 1st, 2015.


