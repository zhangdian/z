# 2012-12-11-java-spring-some-best-practices

一些没写过java，现在一直主要写java，刚才向公司的java大牛请教了一些关于java spring的一些观点，现在总结如下，作为自己的一些best practice。

<!-- more -->

####1. 异常处理
#####1.1 什么是异常
异常分为两种，一种是checked异常，比如IOException、SQLException，一种是unchecked异常。这两种Exception的区别主要是CheckedException需要用try...catch...显示的捕获，而UncheckedException不需要捕获。通常UncheckedException又叫做RuntimeException。 

* 我们常见的RuntimeExcepiton有IllegalArgumentException、IllegalStateException、NullPointerException、IndexOutOfBoundsException等等。
* 对于那些CheckedException就不胜枚举了，我们在编写程序过程中try...catch...捕捉的异常都是CheckedException。io包中的IOException及其子类，这些都是CheckedException。


#####1.2 异常best practice
* 如果逻辑比较核心的话，那么就创建一个专门的异常，继承runtimeexception，在这个异常中需要记录能够标示这个异常的一些必要的信息，比如用户id以及基本信息等，来替代日志；
* 如果逻辑不是太核心的话，创建通用的异常类，同样继承runtimeexception，在这个异常里记录一些常用的信息；

总之，我们自己创建的异常都是继承自runtimeexception的。定义特殊的类是为了记录特殊的信息，而如果没有特殊的信息，那么就使用通用的异常类就行。

####2. spring框架内service和dao调用
* service和dao之间，service和service之间的调用其实没有什么限制，都是可以调用的；
* 一般在一个service函数中，通常是调用dao来完成这个函数的功能；
* 如果另外一个service中的一个函数，完成了我们现在要完成的一个业务，也可以直接去调用哪个函数即可，而不是去调用dao，然后把那个函数的逻辑重新走一遍，这样做的目的就是省事方便。
