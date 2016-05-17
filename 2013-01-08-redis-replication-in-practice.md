# 2013-01-08-redis-replication-in-practice

在看了redis replication的官方文档之后，有必要亲自实践一下。下面就在同一个机器上开两个实例，两个不同的端口6379和6380，一个master一个slave。
<!-- more -->
首先配置master，bind ip 127.0.0.1，开启master，监听在127.0.0.1:6379，默认master是可读写的。

然后配置slave，需要将以下配置的注释删掉：
```
#slaveof <masterip> <masterport>
```

然后将masterip修改为127.0.0.1，masterport修改为6379。此外还需要将修改以下两行：
```
bind 127.0.0.1
port 6380
```

启动slave，这样slave就监听在6380端口，同时它是127.0.0.1::6379的slave。默认情况下slave是只读不写的，可以写数据进行确认。

注意如果master配置了auth的话，也需要配置slave的auth密码，配置成和master一样。