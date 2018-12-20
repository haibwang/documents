####TDD测试驱动开发

红灯-绿灯-重构

​        TDD 改善设计，长函数、紧耦合、复杂条件都会导致复杂切难测的代码，很难编写测试代码，说明设计有问题。

​      嵌入式软件采用TDD的好处：

1. 在硬件可用之前或者硬件比较贵或者稀少时，通过独立于硬件的方式检验产品代码
2. 在开发环境中执行并移除bug，从而减少长时间的编译、链接和上传
3. 对于在目标硬件中既难发现有难修改的问题，可以减少调试时间
4. 通过在测试中模型化硬件交互，是硬件/软件的交互彼此独立
5. 通过解开模块之间以及模块与硬件之间的耦合来改进软件的设计，可测试的代码必须是模块化的

#### 2 测试驱动开发的工具和约定

##### 自动化单元测试框架

自动测试框架能力：

- 用于表述测试用例的通用语言
- 用于表述期望结果的通用语言
- 能够使产品代码使用编程语言的功能
- 把所有工程、系统、子系统的单元测试用例收集到一起
- 一个能运行全部或部分测试用例的机制

- 对于测试套件的成功和失败给出明确的报告
- 对于失败的测试给出详细的报告

#### 开始一个C模块

##### 具有可测试性的C模块的元素

单实例模块：只有一套数据需要处理----使用static变量，只能够在同一个编译单元操作，通过开放的接口访问。

多实例模块：为不同的客户管理不同的数据----必须初始化数据结构，并且把指针回传给用户，保证其上下文，在编译单元定义结构，只有数据类型完备的单元才能操作该数据，能够有效的隐藏、封装。

 

​        每个模块都有一个头文件，对于单一实例头文件放置接口，对于抽象数据类型还需要提前声明。源文件对接口进行实现。每个模块都应该有初始化和清理函数，特别是有抽象数据类型的模块。C++内化成了*构造函数* 和*解析函数*  



为驱动程序伪装环境：

- 依赖注入，只在初始化写地址，把地址注入驱动函数，驱动函数重入性就好了。
- 让编码跟在测试之后。
- 先测试接口，在测试内部实现

TDD三原则：

- 除非是让一个失败的单元测试通过，否则不要写产品代码
- 不要写比足以失败更多的单元测试，构建失败也可以
- 不要写足以让单元测试失败跟多的产品代码

四段测试模式：

- 建立
- 执行
- 验证
- 拆除
