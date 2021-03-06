# Thread


## 查看JNI代码（c、c++代码）

Thread类中的本地方法申明在 `openjdk\jdk\src\share\native\java\lang\Thread.c` 里面

[openJDK1.7源代码](http://7xpmu3.com1.z0.glb.clouddn.com/openjdk-7-fcs-src-b147-27_jun_2011.zip)

这一块太深奥了，要回顾下c++代码

## join

最后的else里面的逻辑，为什么要这么实现? 因为毫秒级别, 怕不精确?

```
    public final synchronized void join(long millis) throws InterruptedException {
        long base = System.currentTimeMillis();
        long now = 0;

        if (millis < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (millis == 0) {
            while (isAlive()) {
                wait(0);
            }
        } else {
            while (isAlive()) {
                long delay = millis - now;
                if (delay <= 0) {
                    break;
                }
                wait(delay);
                now = System.currentTimeMillis() - base;
            }
        }
    }
```

## 同步方法

Thread里面，涉及到线程状态的操作，比如`join start interrupt`等方法，包括线程序号的递增等，也是使用`synchronized`来实现的。


## 参考

[java native](http://stackoverflow.com/questions/6101311/what-is-the-native-keyword-in-java-for)

[How does method yield work?](http://stackoverflow.com/questions/5156985/how-does-method-yield-work)

[Java programming with JNI](http://www.ibm.com/developerworks/java/tutorials/j-jni/j-jni.html)

