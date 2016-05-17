# 2012-12-11-a-sql-question

看到一个问题，据说很多大牛都搞不出来，看了看，问题虽然不是很难，但是一句sql确实包含了很多的sql知识的，mark下来，回忆一下丢失已久的sql知识。

<!-- more -->

####问题

```
数据表结构
user_name product_id
1            A
2            B
1            B
3            C
4            C
1            C
需求：哪些用户同时购买了 A，C，D？（或者说，同时购买A，C，D的用户都是那些？）
A,C,D是用户临时输入的，每次都确定、但不固定。
```

####解答

```
SELECT user_name
FROM product_buy
WHERE product_id in( A, C, D )
GROUP BY user_name
HAVING COUNT( DISTINCT product_id ) = 3 ;
```

####参考
* 问题来源：[一个 MYSQL 并集 的问题](http://julying.com/blog/mysql-and-problem-sets/)