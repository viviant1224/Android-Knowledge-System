# Service

## 如何绑定Service

在 Activity 中可以通过 startService 和 bindService 方法启动 Service。一般情况下如果想获取Service 的服务对象那么肯定需要通过 bindService（）方法，比如音乐播放器，第三方支付等。如果仅仅只是为了开启一个后台任务那么可以使用 startService（）方法

## Service 的生命周期
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

## 其他
ANR：前台service无响应的超时时间为20秒，后台service为200秒
