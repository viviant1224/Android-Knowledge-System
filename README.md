# Android-Knowledge-System
Android知识体系，包含计算机基础，Java基础，数据结构与算法，设计模式，Android应用层，Android框架层，Flutter，Kotlin等  
![image](https://github.com/viviant1224/Android-Knowledge-System/blob/main/images/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9B%B8%E5%85%B3/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C.png)  

![image](https://github.com/viviant1224/Android-Knowledge-System/blob/main/images/Java/JVM/Java.png)  




## 计算机相关
[网络](https://github.com/viviant1224/Android-Knowledge-System/blob/main/Blogs/%E8%AE%A1%E7%AE%97%E6%9C%BA/%E7%BD%91%E7%BB%9C/%E7%BD%91%E7%BB%9C.md) 

## Java
[JVM](https://github.com/viviant1224/Android-Knowledge-System/blob/main/Blogs/Java/JVM/JVM.md)  
[Java并发](https://github.com/viviant1224/Android-Knowledge-System/blob/main/Blogs/Java/Java%E5%B9%B6%E5%8F%91.md)


## 数据结构与算法
[二叉树](https://github.com/viviant1224/Android-Knowledge-System/blob/main/Blogs/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/%E4%BA%8C%E5%8F%89%E6%A0%91.md)

## 设计模式

## Android应用层

## Android框架层

## Flutter

[Dart语言介绍](https://github.com/viviant1224/Android-Knowledge-System/blob/main/Blogs/Flutter/Dart.md) 

悲观锁：synchronized Lock
乐观锁：AtomicInteger----->CAS   Compare And Swap（比较与交换），是一种无锁算法。在不使用锁（没有线程被阻塞）的情况下实现多线程之间的变量同步

***volatile*** 
1、可见性实现：  
* 修改volatile变量时会强制将修改后的值刷新的主内存中。

*　修改volatile变量后会导致其他线程工作内存中对应的变量值失效。因此，再读取该变量值的时候就需要重新从读取主内存中的值。   
2、有序性实现：   
为了实现volatile可见性和happen-befor的语义。JVM底层是通过一个叫做“内存屏障”的东西来完成。内存屏障，也叫做内存栅栏，是一组处理器指令，用于实现对内存操作的顺序限制。

3、不能保证原子性

***synchronized***

原子性：原子性是指一个操作是不可中断的，要全部执行完成，要不就都不执行。在Java中，为了保证原子性，提供了两个高级的字节码指令monitorenter和monitorexit。前面中，介绍过，这两个字节码指令，在Java中对应的关键字就是synchronized。

通过monitorenter和monitorexit指令，可以保证被synchronized修饰的代码在同一时间只能被一个线程访问，在锁未释放之前，无法被其他线程访问到。因此，在Java中可以使用synchronized来保证方法和代码块内的操作是原子性的。

可见性：被synchronized修饰的代码，在开始执行时会加锁，执行完成后会进行解锁。而为了保证可见性，有一条规则是这样的：对一个变量解锁之前，必须先把此变量同步回主存中。这样解锁后，后续线程就可以访问到被修改后的值。

有序性：synchronized是无法禁止指令重排和处理器优化的。也就是说，synchronized无法避免上述提到的问题。由于synchronized修饰的代码，同一时间只能被同一线程访问。那么也就是单线程执行的。所以，可以保证其有序性。

***ReentrantLock***

使用 synchronize 来做同步处理时，锁的获取和释放都是隐式的，实现的原理是通过编译后加上不同的机器指令来实现。

而 ReentrantLock 就是一个普通的类，它是基于 AQS(AbstractQueuedSynchronizer)来实现的。AQS 是 Java 并发包里实现锁、同步的一个重要的基础框架。

是一个重入锁：一个线程获得了锁之后仍然可以反复的加锁，不会出现自己阻塞自己的情况。  




[flutter底层原理](https://github.com/viviant1224/Android-Knowledge-System/blob/main/Blogs/Flutter/底层原理.md) 

## Kotlin


