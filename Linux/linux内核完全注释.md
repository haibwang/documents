### 2 内核体系结构

内核就是与硬件进行交互，对硬件的编程控制盒接口操作，调度硬件的资源访问，并让计算机上的用户程序提供一个高级的执行环境和对硬件的虚拟接口。

#### 2.1内核模式

整体的单内核：内核代码紧凑，执行速度快，层次结构不强

层次的微内核：

![1553818659156](D:\opensource\github\documents\Linux\图片\%5CUsers%5Chew385%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1553818659156.png)

单内核粗略的分为三层，调用服务的主程序层、执行系统调用的服务层，支持系统调用的底层



#### 2.2内核体系结构

![1553818797541](D:\opensource\github\documents\Linux\图片\%5CUsers%5Chew385%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1553818797541.png)

**进程调度**：负责控制进程对CPU资源的使用

**内存管理模块**：确保所有进程能够安全的共享主内存区；虚拟内存，使内存比实际内存大

**文件系统**：对外部设备的驱动和存储

**进程间通信**：负责多进程间的信息交换

**网络接口**：对多种通信标准的访问并支持多网络硬件

#### 2.3 内核代码的目录结构

![1553819604968](D:\opensource\github\documents\Linux\图片\%5CUsers%5Chew385%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1553819604968.png)