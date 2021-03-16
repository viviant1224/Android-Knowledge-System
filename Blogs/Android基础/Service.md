# Service
## 介绍
与Activity相对应的就是Service（服务）了，Activity在明，Service在暗。
Service 是一个可以在后台执行长时间运行的服务，对用户不可见。服务可由其他应用组件启动，而且即使用户切换到其他应用，服务仍将在后台继续运行（注意：对于国内手机厂商来说，有可能切换到后台后立即就被kill,特别被压倒后台并且手机熄屏后）。 此外，组件可以绑定到服务，以与之进行交互，甚至是执行进程间通信 (IPC)。 例如，服务可以处理网络事务、播放音乐，执行文件 I/O 或与内容提供程序交互，而所有这一切均可在后台进行

## 如何绑定Service

在 Activity 中可以通过 startService 和 bindService 方法启动 Service。一般情况下如果想获取Service 的服务对象那么肯定需要通过 bindService（）方法，比如音乐播放器，第三方支付等。如果仅仅只是为了开启一个后台任务那么可以使用 startService（）方法

## Service 的生命周期

![image](https://user-images.githubusercontent.com/7577770/111306100-c138bb80-8692-11eb-85c2-164402aa0b77.png)

Service 有绑定模式和非绑定模式，以及这两种模式的混合使用方式。不同的使用方法生命周期方法也不同。
### 非绑定模式：
当第一次调用 startService 的时候执行的方法依次为 onCreate() 、onStartCommand()，当Service 关闭的时候调用 onDestory 方法。
### 绑定模式：
第一次 bindService（）的时候，执行的方法为 onCreate()、onBind(）解除绑定的时候会执行onUnbind()、onDestory()。

一个 Service 可以被多个客户进行绑定，只有所有的绑定对象都执行了 onUnBind（）方法后该Service 才会销毁，不过如果有一个客户执行了 onStart()方法，那么这个时候如果所有的 bind 客户都执行了 unBind()该 Service 也不会销毁。、

1． 通过 startService
Service 会经历 onCreate 到 onStart，然后处于运行状态，stopService的时候调用 onDestroy方法。 如果是调用者自己直接退出而没有调用 stopService 的话，Service 会一直在后台运行。

2． 通过 bindService
Service 会运行 onCreate，然后是调用 onBind， 这个时候调用者和 Service绑定在一起。调用者退出了，Srevice 就会调用 onUnbind->onDestroyed 方法。
所谓绑定在一起就共存亡了。调用者也可以通过调用 unbindService 方法来停止服务，这时候Srevice 就会调用onUnbind->onDestroyed 方法。


## IntentService
### 先看 Service 本身存在两个问题：

Service 不会专门启动一条单独的进程，Service 与它所在应用位于同一个进程中；
Service 也不是专门一条新线程，因此不应该在 Service 中直接处理耗时的任务；

### IntentService 特征

会创建独立的 worker 线程来处理所有的 Intent 请求；
会创建独立的 worker 线程来处理 onHandleIntent()方法实现的代码，无需处理多线程问题；
所有请求处理完成后，IntentService 会自动停止，无需调用 stopSelf()方法停止 Service；
为Service 的 onBind()提供默认实现，返回 null；
为Service 的 onStartCommand 提供默认实现，将请求 Intent 添加到队列中；

## Android中跨进程的通信方式


## 其他
ANR：前台service无响应的超时时间为20秒，后台service为200秒

默认情况,如果没有显示的指 service 所运行的进程, Service 和 activity 是运 行在当前 app 所在进程的 main thread(UI 主线程)里面。
service 里面不能执行耗时的操作(网络请求,拷贝数据库,大文件 )
特殊情况 ,可以在清单文件配置 service 执行所在的进程 ,让 service 在另 外的进程中执行

```
<service
        android:name=".aidl.AidlService"
        android:enabled="true"
        android:process=":AidlService"
        android:exported="true">
```

android:enabled service能否被系统实例化，默认为true
android:exported service能否被隐式调用 默认为true
android:process 是否在单独的进程中，
如果没有设置值，则表明此service与UI线程在同一个进程中，并且是在主线程中。
如果设置值以冒号“：”开头，则表明此service是App私有的，不能被共享；
如果设置的值以小写字母开头，则表明此service是可以被系统共享的。


Activity 调用 Service 中的方法都有哪些方式?
源码：AndroidIPC

Binder：
通过 Binder 接口的形式实现,当 Activity 绑定 Service 成功的时候 Activity 会在 ServiceConnection 的类 的 onServiceConnected()回调方法中获取到 Service 的 onBind()方法 return 过来的 Binder 的子类，然后通过对象调用方法。
Aidl:
aidl 比较适合当客户端和服务端不在同一个应用下的场景。
Messenger：
它引用了一个Handler对象，以便others能够向它发送消息(使用mMessenger.send(Message msg)方法)。该类允许跨进程间基于Message的通信(即两个进程间可以通过Message进行通信)，在服务端使用Handler创建一个Messenger，客户端持有这个Messenger就可以与服务端通信了。一个Messeger不能同时双向发送，两个就就能双向发送了

## Binder机制

Binder是进程间通信的具体实现，实现了IBinder接口，在BindService中接收Service中onBind(Intent intent)返回的Binder对象，通过这个Binder对象就可以对Service进行操作。这个Binder对象可以是继承自Binder类型的new出来的实例，也可以是aidl生成的AIDL.Stub对象

![image](https://user-images.githubusercontent.com/7577770/111307114-fa256000-8693-11eb-823e-c52b7374dfe5.png)

### 进程空间划分
一个进程空间分为 用户空间 & 内核空间（Kernel），即把进程内 用户 & 内核 隔离开来
二者区别：
进程间，用户空间的数据不可共享，所以用户空间 = 不可共享空间
进程间，内核空间的数据可共享，所以内核空间 = 可共享空间
所有进程共用1个内核空间

进程内 用户空间 & 内核空间 进行交互 需通过 系统调用，主要通过函数：
copy_from_user（）：将用户空间的数据拷贝到内核空间
copy_to_user（）：将内核空间的数据拷贝到用户空间
![image](https://user-images.githubusercontent.com/7577770/111307244-217c2d00-8694-11eb-8a76-6d69a9aa0eec.png)

### 进程隔离 & 跨进程通信（ IPC ）

* 进程隔离
为了保证 安全性 & 独立性，一个进程 不能直接操作或者访问另一个进程，即Android的进程是相互独立、隔离的

* 跨进程通信（ IPC ）
即进程间需进行数据交互、通信

* 跨进程通信的基本原理
![image](https://user-images.githubusercontent.com/7577770/111307366-4a042700-8694-11eb-835c-c226467d15fe.png)
a. 而Binder的作用则是：连接 两个进程，实现了mmap()系统调用，主要负责 创建数据接收的缓存空间 & 管理数据接收缓存
b. 注：传统的跨进程通信需拷贝数据2次，但Binder机制只需1次，主要是使用到了***内存映射***

### Binder 跨进程通信机制 模型
模型原理图：   
![image](https://user-images.githubusercontent.com/7577770/111308808-1b874b80-8696-11eb-8feb-78c190731149.png)   
模型组成角色说明：
![image](https://user-images.githubusercontent.com/7577770/111308871-2c37c180-8696-11eb-9819-f473206dd659.png)

Binder驱动的作用 & 原理：
![image](https://user-images.githubusercontent.com/7577770/111308944-44a7dc00-8696-11eb-9a9f-1709c54bf58f.png)
跨进程通信的核心原理：   
![image](https://user-images.githubusercontent.com/7577770/111309278-acf6bd80-8696-11eb-8534-00fcc8751fbc.png)
模型原理步骤说明：
![image](https://user-images.githubusercontent.com/7577770/111309458-e6c7c400-8696-11eb-9bba-3686e2cd0708.png) 

说明1：Client进程、Server进程 & Service Manager 进程之间的交互 都必须通过Binder驱动（使用 open 和 ioctl文件操作函数），而非直接交互

### Binder请求的线程管理：

* Server进程会创建很多线程来处理Binder请求
* Binder模型的线程管理 采用Binder驱动的线程池，并由Binder驱动自身进行管理而不是由Server进程来管理的
*一个进程的Binder线程数默认最大是16，超过的请求会被阻塞等待空闲的Binder线程。所以，在进程间通信时处理并发问题时，如使用ContentProvider时，它的CRUD（创建、检索、更新和删除）方法只能同时有16个线程同时工作   
### Binder机制 在Android中的具体实现原理
Binder机制在 Android中的实现主要依靠 Binder类，其实现了IBinder 接口
下面会详细说明

实例说明：Client进程 需要调用 Server进程的加法函数（将整数a和b相加）
即：

Client进程 需要传两个整数给 Server进程
Server进程 需要把相加后的结果 返回给Client进程





## 跨进程通信的基本原理

