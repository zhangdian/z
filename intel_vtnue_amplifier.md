# Intel VTnue Amplifier（Windows）

## 安装配置

[安装文件和license](http://7xpmu3.com1.z0.glb.clouddn.com/Vtune.rar)

## 支持JAVA源码

[配置支持JAVA源码！！！](https://software.intel.com/en-us/articles/java-support-is-back-in-vtune-amplifier-xe)

总的来说，思路是：

1. 新建一个`bat`文件，在该文件中，调用`main`函数执行
2. 在Intel VTnue的项目属性中，`Application`一栏选择对应的bat文件，执行即可

![](http://7xpmu3.com1.z0.glb.clouddn.com/java_vtune_amplifier.jpg)

## Locks and wait

安装好之后，在安装目录可以看到`文档`

### Summary

#### Thread Concurrency Histogram

应该是代表同时执行的线程的个数，已经对应的执行时间

![](http://7xpmu3.com1.z0.glb.clouddn.com/Thread%20Concurrency%20Histogram.png)

The Thread Concurrency Histogram represents the Elapsed time and concurrency level for the specified number of running threads. Ideally, `the highest bar of your chart should be within the Ok or Ideal utilization range`.

Note the `Target` value. `By default, this number is equal to the number of physical cores`. Consider this number as your optimization goal.

The `Average metric` is calculated as `CPU time / Elapsed time`. Use this number as a baseline for your performance measurements. `The closer this number to the number of cores, the better`.


大致知道怎么用，但是不熟，要多实践，多试试。