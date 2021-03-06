
# JVM

## 内存模型

### 线程/工作内存/主内存 三者的关系
* 每个线程都有一个独立的工作内存，用于存储线程私有的数据
* Java内存模型中规定所有变量都存储在主内存，主内存是共享内存区域，所有线程都可以访问
* 线程对变量的操作(读取赋值等)必须在工作内存中进行。（线程安全问题的根本原因）  
（1）首先要将变量从主内存拷贝的自己的工作内存空间  
（2）然后对变量进行操作，操作完成后再将变量写回主内存  
（3）不能直接操作主内存中的变量，工作内存中存储着主内存中的变量副本拷贝  
（4）因此不同的线程间无法访问对方的工作内存，线程间的通信(传值)必须通过主内存来完成。

### 主内存和工作内存
* 主内存是在运行期间所有变量的存放区域，工作内存是运行期间中某一线程独立私有的内存存放区域
* 线程间无法访问对方的工作内存空间，都是通过主内存交换来实现
* 主内存的变量在工作内存中的值是复制过去的副本，读写完成后刷新主内存，这意味着主内存如果发生了改变，工作内存并无法获得最新的结果
* 多个线程对一个共享变量进行修改时，都是对自己工作内存的副本进行操作，相互不可见。主内存最后得到的结果是不可预知的 
### Java 虚拟机内存管理
![image](https://raw.githubusercontent.com/viviant1224/Android-Knowledge-System/main/images/Java/JVM/jvm1.webp)  
* **程序计数器**
内存空间小，线程私有。字节码解释器工作是就是通过改变这个计数器的值来选取下一条需要执行指令的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖计数器完成
* **虚拟机栈**
线程私有，生命周期和线程一致。描述的是 Java 方法执行的内存模型：每个方法在执行时都会创建一个栈帧(Stack Frame)用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每一个方法从调用直至执行结束，就对应着一个栈帧从虚拟机栈中入栈到出栈的过程。  局部变量表：存放了编译期可知的各种基本类型(boolean、byte、char、short、int、float、long、double)、对象引用(reference 类型)和 returnAddress 类型(指向了一条字节码指令的地址)StackOverflowError：线程请求的栈深度大于虚拟机所允许的深度。OutOfMemoryError：如果虚拟机栈可以动态扩展，而扩展时无法申请到足够的内存。
* **本地方法栈**
区别于 Java 虚拟机栈的是，Java 虚拟机栈为虚拟机执行 Java 方法(也就是字节码)服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。也会有 StackOverflowError 和 OutOfMemoryError 异常。
* **方法区**
属于共享内存区域，存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数
* **堆**
对于绝大多数应用来说，这块区域是 JVM 所管理的内存中最大的一块。线程共享，主要是存放对象实例和数组。内部会划分出多个线程私有的分配缓冲区(Thread Local Allocation Buffer, TLAB)。可以位于物理上不连续的空间，但是逻辑上要连续。  OutOfMemoryError：如果堆中没有内存完成实例分配，并且堆也无法再扩展时，抛出该异常。

* **方法区**
属于共享内存区域，存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

* **运行时常量池**
属于方法区一部分，用于存放编译期生成的各种字面量和符号引用。编译器和运行期(String 的 intern() )都可以将常量放入池中。内存有限，无法申请时抛出 OutOfMemoryError。

* **直接内存**
非虚拟机运行时数据区的部分

## Java垃圾回收
### 垃圾回收算法
* **标记-清除算法**
直接标记清除就可。1)、效率不高空间；2）、会产生大量碎片
* **复制算法**
把空间分成两块，每次只对其中一块进行 GC。当这块内存使用完时，就将还存活的对象复制到另一块上面。解决前一种方法的不足，但是会造成空间利用率低下。因为大多数新生代对象都不会熬过第一次 GC。所以没必要 1 : 1 划分空间。可以分一块较大的 Eden 空间和两块较小的 Survivor 空间，每次使用 Eden 空间和其中一块 Survivor。当回收时，将 Eden 和 Survivor 中还存活的对象一次性复制到另一块 Survivor 上，最后清理 Eden 和 Survivor 空间。大小比例一般是 8 : 1 : 1，每次浪费 10% 的 Survivor 空间。但是这里有一个问题就是如果存活的大于 10% 怎么办？这里采用一种分配担保策略：多出来的对象直接进入老年代。
* **标记-整理算法**
* **分代回收算法**
新生代 每次垃圾回收都有大量对象死去，只有少量存活，选用复制算法比较合理。老年代 老年代中对象存活率较高、没有额外的空间分配对它进行担保。所以必须使用 标记 —— 清除 或者 标记 —— 整理 算法回收。

### 对象引用方式
* **强引用**
一般的，如果一个对象可以通过一系列的强引用引用到，那么就说明它是不会被垃圾回收机制（Garbage Collection）回收的。
* **软引用**
SoftReference 类实现软引用。在系统要发生内存溢出异常之前，将会把这些对象列进回收范围之中进行二次回收。
* **弱引用**
WeakReference 类实现弱引用。对象只能生存到下一次垃圾收集之前。在垃圾收集器工作时，无论内存是否足够都会回收掉只被弱引用关联的对象。
* **虚引用**
PhantomReference 类实现虚引用。无法通过虚引用获取一个对象的实例，为一个对象设置虚引用关联的唯一目的就是能在这个对象被收集器回收时收到一个系统通知。


## Java类加载机制
### 类的生命周期
![image](https://github.com/viviant1224/Android-Knowledge-System/blob/main/images/Java/JVM/jvm2.webp)    


### 双亲委派机制
