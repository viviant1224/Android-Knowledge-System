# Binder

## Binder机制
Binder是进程间通信的具体实现，实现了IBinder接口，在BindService中接收Service中onBind(Intent intent)返回的Binder对象，通过这个Binder对象就可以对Service进行操作。这个Binder对象可以是继承自Binder类型的new出来的实例，也可以是aidl生成的AIDL.Stub对象

![image](https://user-images.githubusercontent.com/7577770/112119620-7ff75d00-8bf8-11eb-9a51-6c6cde2cc033.png)

## 进程空间划分
一个进程空间分为 用户空间 & 内核空间（Kernel），即把进程内 用户 & 内核 隔离开来 二者区别： 进程间，用户空间的数据不可共享，所以用户空间 = 不可共享空间 进程间，内核空间的数据可共享，所以内核空间 = 可共享空间 所有进程共用1个内核空间

进程内 用户空间 & 内核空间 进行交互 需通过 系统调用，主要通过函数： copy_from_user（）：将用户空间的数据拷贝到内核空间 copy_to_user（）：将内核空间的数据拷贝到用户空间

![image](https://user-images.githubusercontent.com/7577770/111307244-217c2d00-8694-11eb-8a76-6d69a9aa0eec.png)

## 进程隔离 & 跨进程通信（ IPC ）
进程隔离 为了保证 安全性 & 独立性，一个进程 不能直接操作或者访问另一个进程，即Android的进程是相互独立、隔离的

跨进程通信（ IPC ） 即进程间需进行数据交互、通信

## 跨进程通信的基本原理 
![image](https://user-images.githubusercontent.com/7577770/111307366-4a042700-8694-11eb-835c-c226467d15fe.png)

a. 而Binder的作用则是：连接 两个进程，实现了mmap()系统调用，主要负责 创建数据接收的缓存空间 & 管理数据接收缓存 b. 注：传统的跨进程通信需拷贝数据2次，但Binder机制只需1次，主要是使用到了内存映射

## Binder 跨进程通信机制 模型
模型原理图：
![image](https://user-images.githubusercontent.com/7577770/111308808-1b874b80-8696-11eb-8feb-78c190731149.png)
## 模型组成角色说明：
![image](https://user-images.githubusercontent.com/7577770/111308871-2c37c180-8696-11eb-9819-f473206dd659.png)

## Binder驱动的作用 & 原理：

![image](https://user-images.githubusercontent.com/7577770/111308944-44a7dc00-8696-11eb-9a9f-1709c54bf58f.png)

## 跨进程通信的核心原理：
![image](https://user-images.githubusercontent.com/7577770/111309278-acf6bd80-8696-11eb-8534-00fcc8751fbc.png)

模型原理步骤说明：
![image](https://user-images.githubusercontent.com/7577770/111309458-e6c7c400-8696-11eb-9bba-3686e2cd0708.png)


说明1：Client进程、Server进程 & Service Manager 进程之间的交互 都必须通过Binder驱动（使用 open 和 ioctl文件操作函数），而非直接交互

Binder请求的线程管理：  

Server进程会创建很多线程来处理Binder请求  
Binder模型的线程管理 采用Binder驱动的线程池，并由Binder驱动自身进行管理而不是由Server进程来管理的   
*一个进程的Binder线程数默认最大是16，超过的请求会被阻塞等待空闲的Binder线程。所以，在进程间通信时处理并发问题时，如使用ContentProvider时，它的CRUD（创建、检索、更新和删除）方法只能同时有16个线程同时工作  
Binder机制 在Android中的具体实现原理    
Binder机制在 Android中的实现主要依靠 Binder类，其实现了IBinder 接口 下面会详细说明  

实例说明：Client进程 需要调用 Server进程的加法函数（将整数a和b相加） 即：  

Client进程 需要传两个整数给 Server进程 Server进程 需要把相加后的结果 返回给Client进程

