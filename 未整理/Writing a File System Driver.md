p390

DriverEntry
文件系统驱动程序通常在其驱动程序入口例程中执行下面列出的步骤。

1. 为全局数据结构分配内存并初始化这些数据结构。
2. 如果需要，请读取注册表信息。虽然大多数文件系统实现不会提供许多可配置的参数，但是重定向器和服务器(例如局域网管理器软件)确实允许用户指定许多可配置参数的值。
3. 创建一个设备对象，可以满足针对FSD本身的请求(与针对由FSD管理的逻辑卷的请求相对)。
- FILE_DEVICE_DISK_FILE_SYSTEM
	这由基于磁盘的FSD实现使用，例如NTFS(设备对象名为\Ntfs)和FAT(设备对象名为\Fai)。
- FILE_DEVICE_NETWORK
- FILE_DEVICE_TAPE_FILE_SYSTEM
- FILE_DEVICE_NETWORK_FILE_SYSTEM

4. 初始化不同IRP请求的调度例程函数指针
5. 初始化fast IO和用于跨模块同步的回调函数
6. 初始化您的FSD可能需要的任何计时器对象和相关联的DPC对象。一些FSD实现使用定时器中断来执行异步处理。这需要使用定时器对象。
7. 如果需要，启动异步初始化。例如，如果您的驱动程序需要创建异步执行某些初始化的工作线程，这些线程可以在DriverEntry()例程的上下文中创建，也可以作为异步操作创建。
8. 基于物理媒体的文件系统驱动程序将调用loRegisterFile-System()来注册驱动程序的当前加载实例。请注意，NT输入/输出管理器支持的基于物理介质的前端设备支持基于磁盘、虚拟磁盘、光盘和磁带的前端设备。通过使用输入/输出管理器对文件系统进行注册，您的文件系统将确保它在输入/输出管理器要求检查的文件系统驱动程序列表中，并在一个或多个周期内首次访问的介质上有效地执行装载操作
9. 支持通用命名约定(UNC)名称的网络文件系统实现将调用FsRtlRegisterUncProvider()向MUP组件注册自己。
10. 网络重定向器和服务器还将使用IoRegisterShutdownNotification()例程注册关闭通知功能 确保FSD有机会在系统停机前刷新修改后的数据，并执行任何其他必要的处理。
