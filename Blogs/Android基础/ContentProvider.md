# ContentProvider

## ContentProvider
ContentProvider作为四大组件之一，其主要负责存储和共享数据。与文件存储、SharedPreferences存储、SQLite数据库存储这几种数据存储方法不同的是，后者保存下的数据只能被该应用程序使用，而前者可以让不同应用程序之间进行数据共享，它还可以选择只对哪一部分数据进行共享，从而保证程序中的隐私数据不会有泄漏风险。

## ContentProvider的权限管理

* 读写分离
* 权限控制-精确到表级
* URL控制

## ContentProvider、ContentResolver、ContentObserver 之间的关系

### ContentProvider
管理数据，提供数据的增删改查操作，数据源可以是数据库、文件、XML、网络等，ContentProvider为这些数据的访问提供了统一的接口，可以用来做进程间数据共享。

### ContentResolver
ContentResolver可以为不同URI操作不同的ContentProvider中的数据，外部进程可以通过ContentResolver与ContentProvider进行交互。

### ContentObserver
观察ContentProvider中的数据变化，并将变化通知给外界。

## 问题

### Android 为什么要设计 ContentProvider 这个组件
* 封装。对数据进行封装，提供统一的接口，使用者完全不必关心这些数据是在 DB ，XML 、Preferences 或者网络请求来的。当项目需求要改变数据来源时，使用我们的地方完全不需要修改。  
* 提供一种跨进程数据共享的方式。  
* 应用程序间的数据共享还有另外的一个重要话题，就是数据更新通知机制了。因为数据是在多个应用程序中共享的，当其中一个应用程序改变了这些共享数据的时候，它有责任通知其它应用程序，让它们知道共享数据被修改了，这样它们就可以作相应的处理。

### 如何访问自定义 ContentProvider
* ContentResolver 接口的 notifyChange 函数来通知那些注册了监控特定 URI的ContentObserver 对象，使得它们可以相应地执行一些处理。  
* ContentObserver 可以通过 registerContentObserver 进行注册。  
* 通过 ContentProvider 的 Uri 访问开放的数据。  
* ContenResolver 对象通过 Context 提供的方法 getContenResolver() 来获得。  
* ContenResolver 提供了以下方法来操作：insert delete update query 这些方法分别会调用 ContenProvider 中与之对应的方法并得到返回的结果。

### ContentProvider 是如何实现数据共享
* 在 Android 中如果想将自己应用的数据 ( 一般多为数据库中的数据 ) 提供给第三发应用, 那么我们只能通过 ContentProvider 来实现了。 ContentProvider 是应用程序之间共享数据的接口。
* 使用的时候首先自定义 一个类继承 ContentProvider , 然后覆写 query 、insert 、update 、delete 等 方法。
* 因为其是四大组件之一因此必须在 AndroidManifest 文件中进行注册。
* 把自己的数据通过 uri 的形式共享出去 android 系统下 不同程序 数据默认是不能共享访问 需要去实现一个类去继承 ContentProvider。

### 为什么要用 ContentProvider ?它和 sql 的实现上有什么差别?
* ContentProvider 屏蔽了数据存储的细节 , 内部实现对用户完全透明 , 用户只需要关心操作数据的 uri 就可以了, ContentProvider 可以实现不同 app之间 共享。
* Sql 也有增删改查的方法, 但是 sql 只能查询本应用下的数据库。
* ContentProvider 还可以去增删改查本地文件. xml 文件的读取等。

### 多个进程同时调用一个 ContentProvider 的 query 获取数据，ContentPrvoider 是如何反应的呢
* 一个 ContentProvider 可以接受来自另外一个进程的数据请求。
* 尽管 ContentResolver 与 ContentProvider 类隐藏了实现细节，但是 ContentProvider 所提供的 query()，insert()，delete()，update() 都是在 ContentProvider 进程的线程池中被调用执行的，而不是进程的主线程中。
* 这个线程池是有 Binder 创建和维护的，其实使用的就是每个应用进程中的 Binder 线程池。

### Android 设计 ContentProvider 的目的是什么呢
* 隐藏数据的实现方式，对外提供统一的数据访问接口；
* 更好的数据访问权限管理。ContentProvider 可以对开发的数据进行权限设置，不同的 URI 可以对应不同的权限，只有符合权限要求的组件才能访问到 ContentProvider 的具体操作。
* ContentProvider 封装了跨进程共享的逻辑，我们只需要 Uri 即可访问数据。由系统来管理 ContentProvider 的创建、生命周期及访问的线程分配，简化我们在应用间共享数据（ 进程间通信 ）的方式。我们只管通过 ContentResolver 访问 ContentProvider 所提示的数据接口，而不需要担心它所在进程是启动还是未启动。

### 运行在主线程的 ContentProvider 为什么不会影响主线程的UI操作?
ContentProvider 的 onCreate() 是运行在 UI 线程的，而 query() ，insert() ，delete() ，update() 是运行在线程池中的工作线程的
所以调用这向个方法并不会阻塞 ContentProvider 所在进程的主线程，但可能会阻塞调用者所在的进程的 UI 线程！
所以，调用 ContentProvider 的操作仍然要放在子线程中去做。
虽然直接的 CRUD 的操作是在工作线程的，但系统会让你的调用线程等待这个异步的操作完成，你才可以继续线程之前的工作。

### 外提供数据共享，那么如何限制对方的使用呢？
* android:exported 属性非常重要。这个属性用于指示该服务是否能够被其他应用程序组件调用或跟它交互。
* 如果设置为 true，则能够被调用或交互，否则不能。
* 设置为 false 时，只有同一个应用程序的组件或带有相同用户 ID 的应用程序才能启动或绑定该服务。

* 对于需要开放的组件应设置合理的权限，如果只需要对同一个签名的其它应用开放 ContentProvider ，则可以设置 signature 级别的权限。可以参考一下系统自带应用的代码，自定义了 signature 级别的 permission

### ContentProvider 运行在哪个进程呢
* ContentProvider 可以在 AndroidManifest.xml 中配置一个叫做 android:multiprocess 的属性，默认值是 false ，表示 ContentProvider 是单例的
* 无论哪个客户端应用的访问都将是同一个 ContentProvider 对象，如果设为 true ，系统会为每一个访问该 ContentProvider 的进程创建一个实例。

### ContentProvider 是如何在不同应用程序之间传输数据的？
* 一个应用进程有 16 个 Binder 线程去和远程服务进行交互，而每个线程可占用的缓存空间是 128KB 这样，超过会报异常。
* ContentResolver 虽然是通过 Binder 进程间通信机制打通了应用程序之间共享数据的通道，但 ContentProvider 组件在不同应用程序之间传输数据是基于匿名共享内存机制来实现的

## 匿名共享内存机制
[Android系统匿名共享内存Ashmem（Anonymous Shared Memory）简要介绍和学习计划](https://blog.csdn.net/luoshengyang/article/details/6651971)
