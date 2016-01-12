# Intel VTnue Amplifier

## Locks and wait

### Summary

#### Thread Concurrency Histogram

应该是代表同时执行的线程的个数，已经对应的执行时间

![](http://7xpmu3.com1.z0.glb.clouddn.com/Thread%20Concurrency%20Histogram.png)

The Thread Concurrency Histogram represents the Elapsed time and concurrency level for the specified number of running threads. Ideally, `the highest bar of your chart should be within the Ok or Ideal utilization range`.

Note the `Target` value. `By default, this number is equal to the number of physical cores`. Consider this number as your optimization goal.

The `Average metric` is calculated as `CPU time / Elapsed time`. Use this number as a baseline for your performance measurements. `The closer this number to the number of cores, the better`.