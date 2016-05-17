# 2013-06-14-java-hashtable-hashmap


## HashMap、TreeMap、HashTable、LinkedHashMap、ConcurrentHashMap

经常用到HashMap，也经常听到HashTable和HashMap之间的故事，故google了一番，总结一下常用的各种Map。

###概述

HashMap：实现上和HashTable相似，并且keys和values是无序的

TreeMap：基于红黑树结构实现，并且key是排好序的

LinkedHashMap：保持插入时的顺序

HashTable：和HashMap相比，是线程安全的

ConcurrentHashMap：相比HashMap，是线程安全的，但是推荐使用它来替代HashTable，因为性能更高，具体见后面

<!-- more -->

### HashMap

如果HashMap的key是自定义的话，需要重载hashcode和equals方法。因为在HashMap中，不允许有两个完全相同的元素的存在。默认情况下，会使用Object类的hashcode和equals方法。其中，hashcode方法中，不同的对象返回不一样的整数，equals方法只在两个对象引用同一个对象时返回true。

在[参考2](http://www.programcreek.com/2011/07/java-equals-and-hashcode-contract/)中，详细讲解了hashcode和equals的原理：

	在一个HashMap中查找一个元素的时候，
	首先会调用HashCode方法，寻找相应的桶位，
	然后在这个桶对应的array中，线性寻找，调用equals方法进行比较，
	直到遍历完这个array，或者找到待查的元素。
	另外，在该示例中，详细给出了equals和hashcode方法是实现示例。

### HashMap vs HashTable

经常听到关于HashMap和HashTable的言论，Java Doc上说：

	From Java Doc:  The HashMap class is roughly equivalent to Hashtable, 
	except that it is unsynchronized and permits nulls.

也就是说，两者几乎是一样的一样的，只是HashMap不是线程安全的，并且允许在keys和values中出现null值。

而java5中，也用ConcurrentHashMap来替代了HashTable，两者使用的锁机制是不一样的。

Hashtable中采用的锁机制是一次锁住整个hash表，从而同一时刻只能由一个线程对其进行操作；而ConcurrentHashMap中则是一次锁住一个桶。ConcurrentHashMap默认将hash表分为16个桶，诸如get,put,remove等常用操作只锁当前需要用到的桶。这样，原来只能一个线程进入，现在却能同时有16个写线程执行，并发性能的提升是显而易见的。上面说到的16个线程指的是写线程，而读操作大部分时候都不需要用到锁。只有在size等操作时才需要锁住整个hash表。

### TreeMap

TreeMap是根据key来进行排序的，必须实现Comparable接口，实现其中的方法：

	@Override
	public int compareTo(Dog o) {
		return  o.size - this.size;
	}

具体事例见[参考1](http://java.dzone.com/articles/hashmap-vs-treemap-vs)。


### LinkedHashMap

LinkedHashMap是HashMap的子类，意味着它继承了HashMap的属性。除此之外，它保存了元素插入的顺序。

###参考

1. [HashMap vs. TreeMap vs. HashTable vs. LinkedHashMap](http://java.dzone.com/articles/hashmap-vs-treemap-vs)
2. [Java equals() and hashCode() Contract – Code Example](http://www.programcreek.com/2011/07/java-equals-and-hashcode-contract/)
3. [Hashtable和HashMap引发的血案](http://developer.51cto.com/art/201102/246431.htm)
4. [Differences between HashMap and Hashtable?](http://stackoverflow.com/questions/40471/differences-between-hashmap-and-hashtable)
5. [java集合框架【3】 java1.5新特性 ConcurrentHashMap、Collections.synchronizedMap、Hashtable讨论](http://blog.csdn.net/itm_hadf/article/details/7506529)
6. [SynchronizedMap和ConcurrentHashMap的深入分析](http://blog.sina.com.cn/s/blog_5157093c0100hm3y.html)