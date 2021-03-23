# String

## String、StringBuilder、StringBuffer区别

* String类长度是不可变的，StringBuffer和StringBuilder类长度是可以改变的
* String是不可变的对象，因此每次在对String类进行改变的时候都会生成一个新的string对象，然后将指针指向新的string对象，所以经常要改变字符串长度的话不要使用string，因为每次生成对象都会对系统性能产生影响，特别是当内存中引用的对象多了以后，JVM的GC就会开始工作，性能就会降低
* StringBuffer可以理解为多线程下的StringBuilder

## intern（）

String.intern()是一个Native(本地)方法，它的作用是如果字符串常量池已经包含一个等于此String对象的字符串，则返回字符串常量池中这个字符串的引用, 否则将当前String对象的引用地址（堆中）添加到字符串常量池中并返回

## 源码实现
