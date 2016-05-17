# 2013-04-05-email-from-name-reputation

下面是对[A New Year’s Resolution: Monitor Your “From Name” (From Address) Reputation](http://www.adstation.com/a-new-years-resolution-monitor-your-from-name-from-address-reputation/)的翻译和总结，是关于“关注From Name信誉度”的一篇文章。

<!-- more -->

在去年12月中旬，发现Yahoo开始将一些在白名单中的Sender发送的邮件，发送到垃圾箱中。通过分析了解到，Yahoo的这个策略几乎会影响使用多个“From Name”发送邮件的发送者。

* 注意这里的“From Name”和“Friendly From Name”的区别：以发送地址“reputation < bd17kaka@gmail.com > ”为例，“Friendly From Name”指的是“reputation”，“From Name”指的是bd17kaka。而Yahoo的这个变化时针对“From Name”的。

#####Yahoo做了什么改变
Yahoo的信誉度规则发生了改变，从去年12月开始，Yahoo使用“From Name”+“Domain Name”+“IP”的组合来确定信誉度：

```
“From Name + Domain Name + IP”
```

同时也确定“Friendly From Name”和信誉度以及邮件是进入收件箱还是垃圾箱没有什么关系。

#####影响了谁，谁需要改变

* 最近改变了From Name，并且没有为新的From Name建立信誉度；
* 使用多个From Name，并且任何一个From Name组合的信誉度都不满足Yahoo的需求；

在以前，如果某一域名下的所有From Name的所有组合的平均信誉度是可以接受的话，邮件就会被发送到收件箱。经过这个改变之后，如果任何一个From Name的组合不是被接受的，那么这个组合发出来的邮件将不会被发送到收件箱。

考虑下面三个From Name：

* fromname_1@bd17kaka.net
* fromname_2@bd17kaka.net
* fromname_3@bd17kaka.net

假设前两个From Name的组合满足Yahoo的要求，但是第三个FromName是不满足的，那么前两个From Name发出来的邮件会发送到收件箱，第三个From Name发出来的邮件会进入垃圾箱。

#####如果From Name的信誉度很差，那么修改From Name会有帮助吗？
直到新的From Name建立一个好的信誉度之前，修改From Name都没有什么帮助。如果你不提升你发送邮件的策略，那么新的FromName也不会有一个好的信誉度。

#####Yahoo为什么做这个改变？
发送者对于不同收件人列表，使用不同的FromName和相同的IP来发送邮件。对于建立了好的信誉度的部分，会给予奖励，相反会给予惩罚。

在以前，忽略FromName不管，如果一个发送者的平均信誉度满足Yahoo的要求，那么它是可以被接受的。可是现在不再是这样的了。

#####原文链接
[Monitor Your “From Name” (From Address) Reputation](http://www.adstation.com/a-new-years-resolution-monitor-your-from-name-from-address-reputation/)
