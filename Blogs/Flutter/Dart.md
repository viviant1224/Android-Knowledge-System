# Dart

Dart是类型安全的语言，但是大多数变量不惜要显示地指定类型。
Dart同时支持AOT（Ahead Of Time compiler）与JIT（Just-in-time），开发期间使用的是JIT，因此每次改都不需要再编译成字节码。节省了大量时间。在部署中使用AOT生成高效的ARM代码以保证高效的性能。

## 基本概念
* 所有变量的值都是对象，也就是类的实例。甚至数字、函数和null也都是对象，都继承自Object类。
* 虽然Dart是强类型语言，但是显式变量类型声明是可选的，Dart支持类型推断。如果不想使用类型推断，可以用dynamic类型。
* Dart支持泛型，List<int>表示包含int类型的列表，List<dynamic>则表示包含任意类型的列表。
* Dart支持顶层（top-level）函数和类成员函数，也支持嵌套函数和本地函数。
* Dart支持顶层变量和类成员变量。
* Dart没有public、protected和private这些关键字，使用下划线“_”开头的变量或者函数，表示只在库内可见。参考库和可见性。
  
## 内存分配
创建对象时只需要在现有堆上移动指针，内存增长始终是线形的，省去了查找可用内存段的过程：

Dart的垃圾回收也采用了多生代算法，新生代在回收内存时采用了“半空间”算法，触发垃圾回收时Dart会将当前半空间中的“活跃”对象拷贝到备用空间，然后整体释放当前空间的所有内存：


## 线程模型
Dart中类似线程的概念叫做Isolate，每个Isolate之间是无法共享内存的，所以这种分配策略可以让Dart实现无锁的快速分配。

## 与Java对比的使用区别：

### 1、Mixins（混合，关键字：with）：
extends -> mixins -> implements

### 2、异步使用：
async await Future
Stream 与async*的结合使用
