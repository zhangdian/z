# 2013-06-14-java-schedule-task

## 定时任务

###关于定时任务

定时任务有两种：

* 固定延时的定时任务，即一个任务结束等待固定时间后再执行下一个；
* 固定频率的定时任务，即任务在固定时常后开始执行。

前者的意思是，任务完成之后，等待固定的诗句再执行下一个；而后者的意思是在固定的时间执行任务，如果前一次任务的结束时间已经超过了下一次任务开始的时间，那么就会立即执行。

<!-- more -->

###定时任务实现方案

Timer和ScheduledThreadPoolExecutor都可以完成定时任务的工作，也都支持上面的两种方式。

下面分别给出两个类的示例：


    package com.concurrent.basic;
    import java.util.Timer;
    import java.util.TimerTask;
    public class TimerTest {
	    private Timer timer = new Timer();
 
        // 启动计时器
        public void lanuchTimer() {
        	timer.schedule(new TimerTask() {
            	public void run() {
            	    // do sth...
           	 	}
        	}, 1000 * 3, 500);
    	}
	 
    	public static void main(String[] args) throws Exception {
	        TimerTest test = new TimerTest();
	        test.lanuchTimer();
	    }
	}

这个例子是固定延时的任务，详细文档见[Timer](http://docs.oracle.com/javase/7/docs/api/)，文档中，也有scheduleAtFixedRate方法来调用固定频率的任务。


	package com.concurrent.basic;
	 
	import java.util.concurrent.Executors;
	import java.util.concurrent.ScheduledExecutorService;
	import java.util.concurrent.TimeUnit;
	 
	public class ScheduledExecutorTest {
	
	    public ScheduledExecutorService scheduExec = Executors
	            .newScheduledThreadPool(1);
	 
	    // 启动计时器
	    public void lanuchTimer() {
	        Runnable task = new Runnable() {
	            public void run() {
	                // do sth...
	            }
	        };
	        scheduExec.scheduleWithFixedDelay(task, 1000 * 5, 1000 * 10,
	                TimeUnit.MILLISECONDS);
	    }
	 
	    public static void main(String[] args) throws Exception {
	        ScheduledExecutorTest test = new ScheduledExecutorTest();
	        test.lanuchTimer();
	    }
	}

这个例子是固定延时的任务示例，也可以调用scheduleAtFixedRate方法来启用固定频率的任务。


###比较

* Timer对系统时钟是敏感的，而ScheduledThreadPoolExecutor不是；
* Timer只有一个执行线程，所以如果一个线程延时太久了，会影响其他的固定延时任务。而ScheduledThreadPoolExecutor可以配置任意数量的线程，并且你可以完成控制你所创建的这些线程；
* _在Timer中，运行时错误会直接杀死线程，也就会导致线程挂掉，后面计划的任务也不会执行。ScheduledThreadPoolExecutor不仅可以给你catch住运行时异常，还可以对它们进行处理。抛出异常的任务会挂掉，但是其他的定时任务会继续执行。_

###相关链接

* [通过java concurrent实现定时任务](http://marshal.easymorse.com/archives/3136)
* [定时任务:Java中Timer和TimerTask的使用](http://batitan.iteye.com/blog/253483)
* [Java Timer vs ExecutorService?](http://stackoverflow.com/questions/409932/java-timer-vs-executorservice)