# Activity

## 启动模式

### Standard
这是 Activity 的默认启动模式，每次激活 Activity 的时候都会创建一个新的 Activity 实例，并放入任务栈中。使用场景：基本绝大多数地方都可以用。
### singleTop
如果在任务的栈顶正好存有该 Activity 的实例，则会通过调用 onNewIntent() 方法进行重用，否则就会同 standard 模式一样，创建新的实例并放入栈顶。即便栈中已经存在了该 Activity 的实例，也会创建新的实例，即：A -> B ->A，此时栈内为 A -> B -> A，但 A -> B ->B ，此时栈内为 A -> B。一句话概述就是：当且仅当启动的 Activity 和上一个 Activity 一致的时候才会通过调用 onNewIntent() 方法重用 Activity 。
### singleTask
这个 launchMode专门用于解决上面 singleTop 的另外一种情况，只要栈中已经存在了该 Activity 的实例，就会直接调用 onNewIntent() 方法来实现重用实例。重用时，直接让该 Activity 的实例回到栈顶，并且移除之前它上面的所有 Activity 实例。如果栈中不存在这样的实例，则和 standard 模式相同。即： A ->B -> C -> D -> B，此时栈内变成了  A -> B。而 A -> B -> C，栈内还是 A -> B -> C。
### singleInstance
在一个新栈中创建该 Activity 的实例，并让多个应用共享该栈中的该 Activity 实例。一旦该模式的 Activity 实例已经存在于某个栈中，任何应用再激活该 Activity 时都会重用该栈中的实例，是的，依然是调用 onNewIntent() 方法。其效果相当于多个应用共享一个应用，不管是谁激活，该 Activity 都会进入同一个应用中。但值得引起注意的是：singleInstance 不要用于中间页面，如果用户中间页面，跳转会出现很难受的问题。 这个在实际开发中我暂未遇到过，不过 Android 系统的来电页面，多次来电均是使用的同一个 Activity 。

### Intent 标签
***FLAG_ACTIVITY_NEW_TASK***
这个标识会使新启动的 Activity 独立创建一个 Task。

***FLAG_ACTIVITY_CLEAR_TOP***
这个标识会使新启动的 Activity 检查是否存在于 Task 中，如果存在则清除其之上的 Activity，使它获得焦点，并不重新实例化一个 Activity，一般结合 FLAG_ACTIVITY_NEW_TASK 一起使用。

***FLAG_ACTIVITY_SINGLE_TOP***
等同于在 launcherMode 属性设置为 singleTop。

## 生命周期
![image](https://user-images.githubusercontent.com/7577770/111623290-23c1c100-8825-11eb-9023-d6d8f83bfeb9.png)

（1）onCreate:create表示创建，这是Activity生命周期的第一个方法，也是我们在android开发中接触的最多的生命周期方法。它本身的作用是进行Activity的一些初始化工作，比如使用setContentView加载布局，对一些控件和变量进行初始化等。但也有很多人将很多与初始化无关的代码放在这，其实这是不规范的。此时Activity还在后台，不可见。所以动画不应该在这里初始化，因为看不到……

（2）onStart:start表示启动，这是Activity生命周期的第二个方法。此时Activity已经可见了，但是还没出现在前台，我们还看不到，无法与Activity交互。其实将Activity的初始化工作放在这也没有什么问题，放在onCreate中是由于官方推荐的以及我们开发的习惯。

（3）onResume:resume表示继续、重新开始，这名字和它的职责也相同。此时Activity经过前两个阶段的初始化已经蓄势待发。Activity在这个阶段已经出现在前台并且可见了。这个阶段可以打开独占设备

（4）onPause:pause表示暂停，当Activity要跳到另一个Activity或应用正常退出时都会执行这个方法。此时Activity在前台并可见，我们可以进行一些轻量级的存储数据和去初始化的工作，不能太耗时，因为在跳转Activity时只有当一个Activity执行完了onPause方法后另一个Activity才会启动，而且android中指定如果onPause在500ms即0.5秒内没有执行完毕的话就会强制关闭Activity。从生命周期图中发现可以在这快速重启，但这种情况其实很罕见，比如用户切到下一个Activity的途中按back键快速得切回来。

（5）onStop：stop表示停止，此时Activity已经不可见了，但是Activity对象还在内存中，没有被销毁。这个阶段的主要工作也是做一些资源的回收工作。

（6）onDestroy：destroy表示毁灭，这个阶段Activity被销毁，不可见，我们可以将还没释放的资源释放，以及进行一些回收工作。

（7）onRestart：restart表示重新开始，Activity在这时可见，当用户按Home键切换到桌面后又切回来或者从后一个Activity切回前一个Activity就会触发这个方法。这里一般不做什么操作。

onCreate和onStart之间有什么区别？
（1）可见与不可见的区别。前者不可见，后者可见。
（2）执行次数的区别。onCreate方法只在Activity创建时执行一次，而onStart方法在Activity的切换以及按Home键返回桌面再切回应用的过程中被多次调用。因此Bundle数据的恢复在onStart中进行比onCreate中执行更合适。
（3）onCreate能做的事onStart其实都能做，但是onstart能做的事onCreate却未必适合做。如前文所说的，setContentView和资源初始化在两者都能做，然而想动画的初始化在onStart中做比较好。

3.onStart方法和onResume方法有什么区别？
（1）是否在前台。onStart方法中Activity可见但不在前台，不可交互，而在onResume中在前台。
（2）职责不同，onStart方法中主要还是进行初始化工作，而onResume方法，根据官方的建议，可以做开启动画和独占设备的操作。

4.onPause方法和onStop方法有什么区别？
（1）是否可见。onPause时Activity可见，onStop时Activity不可见，但Activity对象还在内存中。
（2）在系统内存不足的时候可能不会执行onStop方法，因此程序状态的保存、独占设备和动画的关闭、以及一些数据的保存最好在onPause中进行，但要注意不能太耗时。

5.onStop方法和onDestroy方法有什么区别？
onStop阶段Activity还没有被销毁，对象还在内存中，此时可以通过切换Activity再次回到该Activity，而onDestroy阶段Acivity被销毁

6.为什么切换Activity时各方法的执行次序是(A)onPause→(B)onCreate→(B)onStart→(B)onResume→(A)onStop而不是(A)onPause→(A)onStop→(B)onCreate→(B)onStart→(B)onResume
（1）一个Activity或多或少会占有系统资源，而在官方的建议中，onPause方法将会释放掉很多系统资源，为切换Activity提供流畅性的保障，而不需要再等多两个阶段，这样做切换更快。
（2）按照生命周期图的表示，如果用户在切换Activity的过程中再次切回原Activity，是在onPause方法后直接调用onResume方法的，这样比onPause→onStop→onRestart→onStart→onResume要快得多。

7.与生命周期密切相关的onSaveInstanceState方法和onRestoreInstanceState方法在什么时候执行？
通过阅读源码会发现，当targetSdkVersion小于3时onSaveInstanceState是在onPause方法中调用的，而大于3时是在onStop方法中调用的。
而onRestoreInstanceState是在onStart之后、onResume之前调用的

https://www.jianshu.com/p/50c20ecefad3
[横竖屏切换生命周期](https://blog.csdn.net/qq_36713816/article/details/80538467)
