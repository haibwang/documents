## 使用 C 和 Win32 进行多线程编程

### 多线程

- 多线程实质是程序中的执行路径，也是win32安排的最小的执行单元；
- 线程包括**堆栈** 、**CPU寄存器的状态** 、系统**计划程序执行列表** 中的项；
- 每个线程共享所用进程的资源。



- 进程，包括一个或多个线程和代码、数据和内存中的其他**程序资源** ；
- 典型的程序资源是**打开的文件** 、**信号灯** 、**动态分配的内存**;
- 系统计划程序决定执行那个线程，以及何时执行；低优先级的线程需要等较高优先级的线程执行完。
- 多处理器，计划程序将不同线程移到不同CPU上执行，以平衡CPU负荷。
- 进程中，每个线程都是独立的，对其他线程一无所知。除非是这些线程相互可见。
- 线程共享公共资源，但是必须时使用信号灯或其他线程之间的是通信方式进行协调工作



### 多线程库

- `msvcrt.lib`
- 独立流锁可以锁定流然后直接使用流访问的 [_nolock 函数](ms-xhelp:///?method=page&id=7d651d87-38d2-4303-9897-fdb5f7a3e899&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN)。 这允许锁定用法是关键的外部循环。
- 每线程区域设置减少多线程方案的区域设置访问的开销 (参见 [_configthreadlocale](ms-xhelp:///?method=page&id=10e4050e-b587-4f30-80bc-6c76b35fc770&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN))。
- 区域设置相关的函数名称 (其中以_l结尾) 将区域设置作为一个参数，移除严格的成本 (例如， [printf、_printf_l、wprintf、_wprintf_l](ms-xhelp:///?method=page&id=77a854ae-5b48-4865-89f4-f2dc5cf80f52&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN))。
- 公共代码的优化降低许多表短的操作的开销。
- [_CRT_DISABLE_PERFCRIT_LOCKS](ms-xhelp:///?method=page&id=36cc2d86-cdb1-4b2b-a03c-c0d3818e7c6f&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN) 定义强制所有 I/O 操作均采用单线程的模型使用 I/O 和函数的_nolock 窗体。 这允许高度基于 I/O 的单一线程的应用程序将获得更好的性能。
- CRT 堆句柄的公开用于启用 CRT 堆窗口右下角堆碎片 (LFH)，可以充分地提高在高度缩放方案的性能。

### 线程控制的C运行库函数

- 所有的win32程序都必须有一个线程。

- 任何线程都可以创建附加线程

- 线程可以快速的完成工作，然后终止；也可以在程序的整个生命周期中活动。

- `libcmt`和`msvcrt`提供创建和终止线程的函数：

  - `_benginthread(), _benginthreadex()`
  - `_endthread(), _endthreadex()`

  ##### _beginthread 和 _beginthreadex 函数

  ------

  `_beginthread `和` _beginthreadex` 函数用来创建新线程。 线程与进程中的其他线程共享进程的代码和数据段，但是线程具有自己的唯一寄存器值、堆栈空间和当前指令地址。 系统给予每个线程 CPU 时间，使进程中的所有线程都可以同时执行。

  `_beginthread` 和 `_beginthreadex `与 `Win32 API `中的 [CreateThread](ms-xhelp:///?method=page&id=msdn%7cjpdllpro%7c%7e%5chtml%5c_win32_createthread.htm&vendor=Microsoft&topicVersion=140&topicLocale=ZH-CN) 函数类似，但有如下差异：

  - 它们初始化某些 C 运行库变量。 只有在线程中使用 C 运行库时，这一点才很重要。
  - `CreateThread` 帮助提供对安全特性的控制。 可以使用此函数启动处于挂起状态的线程。

  如果成功的话， `_beginthread` 和 `_beginthreadex` 返回新线程的句柄；如果有错误的话，则返回错误代码。

  #####_endthread 和 _endthreadex 函数

  ------

  `_endthread `函数终止由 `_beginthread `创建的线程（同样， `_endthreadex` 终止由 `_beginthreadex `创建的线程）。 线程会在完成时自动终止。 `_endthread` 和 `_endthreadex `用于从线程内部进行条件终止。 例如，专门用于通信处理的线程若无法获取对通信端口的控制，则会退出