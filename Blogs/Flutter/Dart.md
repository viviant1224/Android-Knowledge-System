# Dart

Dart是类型安全的语言，但是大多数变量不惜要显示地指定类型。
Dart同时支持AOT（Ahead Of Time compiler）与JIT（Just-in-time），开发期间使用的是JIT，因此每次改都不需要再编译成字节码。节省了大量时间。在部署中使用AOT生成高效的ARM代码以保证高效的性能。

## 与Java对比的使用区别：
### 1、Mixins（混合，关键字：with）：
extends -> mixins -> implements

### 2、异步使用：
async await Future

Stream 与async*的结合使用
