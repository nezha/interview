## 1.你用过哪些集合类？

> [大公司最喜欢问的Java集合类面试题](http://www.codeceo.com/article/java-collection-interview.html)
> [40个Java集合面试问题和答案](http://www.importnew.com/15980.html)
> java.util.Collections 是一个包装类。它包含有各种有关集合操作的静态多态方法。
> java.util.Collection 是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。


```
Collection
├List
│├LinkedList
│├ArrayList
│└Vector
│　└Stack
└Set
Map
├Hashtable
├HashMap
└WeakHashMap
```

---

> ArrayList、HashMap、TreeMap和HashTable类提供对元素的随机访问。

**线程安全**

> Vector
> HashTable(不允许插空值)

**非线程安全**

> ArrayList
> LinkedList
> HashMap(允许插入空值)
> HashSet
> TreeSet
> TreeMap(基于红黑树的Map实现)

## 2.你说说 arraylist 和 linkedlist 的区别？

> ArrayList和LinkedList两者都实现了List接口，但是它们之间有些不同。
> （1）ArrayList是由Array所支持的基于一个索引的数据结构，所以它提供对元素的随机访问
> （2）与ArrayList相比，在LinkedList中插入、添加和删除一个元素会更快
> （3）LinkedList比ArrayList消耗更多的内存，因为LinkedList中的每个节点存储了前后节点的引用

## 3.HashMap 底层是怎么实现的？还有什么处理哈希冲突的方法？

**处理哈希冲突的方法**:

> 解决HashMap一般没有什么特别好的方式，要不扩容重新hash要不优化冲突的链表结构

1.开放定地址法-线性探测法
2.开放定地址法-平方探查法
3.链表解决-可以用红黑树提高查找效率

![](http://tech.meituan.com/img/java-hashmap/java.util.map%E7%B1%BB%E5%9B%BE.png)

> HashMap简介 
> HashMap 是一个散列表，它存储的内容是键值对(key-value)映射。 
> HashMap 继承于AbstractMap，实现了Map、Cloneable、java.io.Serializable接口。 
> HashMap 的实现不是同步的，这意味着它不是线程安全的,但可以用 Collections的synchronizedMap方法使HashMap具有线程安全的能力。**它的key、value都可以为null**。此外，HashMap中的映射不是有序的。 
> HashMap 的实例有两个参数影响其性能：“初始容量” 和 “加载因子”。**初始容量默认是16。默认加载因子是 0.75,** 这是在时间和空间成本上寻求一种折衷。加载因子过高虽然减少了空间开销，但同时也增加了查询成本.
> HashMap是数组+链表+红黑树（JDK1.8增加了红黑树部分）实现的,当链表长度太长（默认超过8）时，链表就转换为红黑树.

![](http://tech.meituan.com/img/java-hashmap/hashMap%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%E5%9B%BE.png)

> [**<font color="red">Java8系列之重新认识HashMap</font>**](http://www.importnew.com/20386.html)
> **功能实现-方法**
> 1. 确定哈希桶数组索引位置 :这里的Hash算法本质上就是三步：取key的hashCode值、高位运算、取模运算。

```java
//方法一：
static final int hash(Object key) {   //jdk1.8 & jdk1.7
     int h;
     // h = key.hashCode() 为第一步 取hashCode值
     // h ^ (h >>> 16)  为第二步 高位参与运算
     return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
//方法二：
static int indexFor(int h, int length) {  //jdk1.7的源码，jdk1.8没有这个方法，但是实现原理一样的
     return h & (length-1);  //第三步 取模运算
}
```

> 2. 分析HashMap的put方法

![](http://tech.meituan.com/img/java-hashmap/hashMap%20put%E6%96%B9%E6%B3%95%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

> 3. **扩容机制：原来的两倍**

## 4.熟悉什么算法，还有说说他们的时间复杂度？

> [**经典排序算法总结与实现**](http://wuchong.me/blog/2014/02/09/algorithm-sort-summary/)

![](http://ww1.sinaimg.cn/large/81b78497jw1emncvtdf1qj20u10afn0r.jpg)


### 5.ArrayList和Vector的底层代码和他们的增长策略,它们是如何进行扩容的？

> ArrayList 默认数组大小是10，其中ensureCapacity扩容，trimToSize容量调整到适中，扩展后数组大小为（(原数组长度*1.5）与传递参数中较大者.
> Vector的扩容，是可以指定扩容因子，同时Vector扩容策略是：1.原来容量的2倍,2.原来容量+扩容参数值。

### 6.jvm 原理。程序运行区域划分

> 问：Java运行时数据区域？ 
> 回答：包括程序计数器、JVM栈、本地方法栈、方法区、堆
> 问：方法区里存放什么？
> 本地方法栈：和jvm栈所发挥的作用类似，区别是jvm栈为jvm执行java方法（字节码）服务，而本地方法栈为jvm使用的native方法服务。
>  JVM栈：局部变量表、操作数栈、动态链接、方法出口。
>  方法区：用于存储已被虚拟机加载的类信息，常量、静态变量、即时编译器编译后的代码等。
>  堆：存放对象实例。

### 7.minor GC 与 Full GC 。分别采用哪种垃圾回收算法？简单介绍算法

> GC（或Minor GC）：收集 生命周期短的区域(Young area)。
> Full GC （或Major GC）：收集生命周期短的区域(Young area)和生命周期比较长的区域(Old area)对整个堆进行垃圾收集。
> `新生代`通常存活时间较短基于Copying算法进行回收,将可用内存分为大小相等的两块，每次只使用其中一块；当这一块用完了，就将还活着的对象复制到另一块上，然后把已使用过的内存清理掉。在HotSpot里，考虑到大部分对象存活时间很短将内存分为Eden和两块Survivor，默认比例为8:1:1。代价是存在部分内存空间浪费，适合在新生代使用；
> `老年代`与`新生代`不同，老年代对象存活的时间比较长、比较稳定，因此采用标记(Mark)算法来进行回收,所谓标记就是扫描出存活的对象，然后再进行回收未被标记的对象，回收后对用空出的空间要么进行合并、要么标记出来便于下次进行分配，总之目的就是要减少内存碎片带来的效率损耗。
在执行机制上JVM提供了串行GC(Serial MSC)、并行GC(Parallel MSC)和并发GC(CMS)。

### 8.HashMap 实现原理

> 在java编程语言中，最基本的结构就是两种，一个是数组，另外一个是模拟指针（引用），所有的数据结构都可以用这两个基本结构来构造的，HashMap也不例外。HashMap实际上是一个“链表散列”的数据结构，即数组和链表的结合体。

### 9.java.util.concurrent 包下使用过哪些

> 1.阻塞队列 BlockingQueue( `ArrayBlockingQueue`, `DelayQueue`, `LinkedBlockingQueue`, `SynchronousQueue`,`LinkedTransferQueue`,`LinkedBlockingDeque`)
> 2.`ConcurrentHashMap`
> 3.`Semaphore`--信号量
> 4.`CountDownLatch`--闭锁
> 5.`CyclicBarrier`--栅栏
> 6.`Exchanger`--交换机
> 7.`Executor`->`ThreadPoolExecutor`,`ScheduledThreadPoolExecutor`

```java
Semaphore semaphore = new Semaphore(1);  
//critical section  
semaphore.acquire();  
...  
semaphore.release();
```