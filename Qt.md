# Qt

Qapplication对象

使用Qt widgets创建qt程序必须的对象

### qt类

- QApplication Qt标准应用程序类

- QMainWindow 主窗口类
- QWidget 所有可视界面的基类
- QDialog 对话框类

## 第2章 GUI应用程序设计基础

### 2.1 UI文件的设计及运行机制

​    通过可视化ui设计生成.ui文件，然后通过uic编译后会生成对应的ui_xxx.h类，类里面setupUi函数定义了各种组件对象，属性设置，信号与槽的的绑定。

​	在窗体类QWidget定义里面定义了一个继承自ui_xxx.h的类对象，通过该对象调用setupUi创建可视化窗体。

### 2.2 可视化窗体设计

#### 2.2.2 界面组件布局

- 伙伴关系：一个lable和一个组件相关联。
  - 作用：执行lable的快捷键时，焦点跳到指定的伙伴上

#### 2.2.3 信号与槽

- 信号：当有事件发生时，发射一个信号

- 槽：对信号响应的函数。

- 槽函数和信号的关联

  - 使用QObject::connect()实现。

    ```c++
    QObject::connect(sender, SIGNAL(singal()),receiver, SLOT(slot()));
    ```

- 信号与槽使用

  - **一个信号可以关联多个槽**，槽函数按照关联的顺序执行。当信号和槽函数带有参数时，需要指明参数的类型，可以不指明具体的参数名称。
  - **多个信号可以连接到同一个槽上**。
  - **一个信号可以连接另一个信号**，当一个信号发射时会发射另一个信号
  - 在使用信号与槽的类中必须在类的定义中加入**Q_OBJECT**的宏。
  - **只有等和信号关联的所有槽函数都执行完了后才会执行发射信号后面的函数**。

### 2.4 混合UI设计

#### 2.4.1 资源文件的使用

qrc文件打开后，可以添加图片资源到文件中，供使用

#### 2.4.2 Action

Action是QAction类，使用Action可以创建菜单项和工具栏按钮。需要编写Action的trigger()信号的槽函数。在UI可视化编辑界面有Action的编辑菜单，可以添加和修改Action。

#### 2.4.3 菜单栏工具栏

窗口中“在这里输入”就是菜单分组，创建菜单分组后，从Action列表中拖一个在分组下，就可以在改分组下创建一个菜单项。可以创建分割符，然后拖到相应的地方。

#### 2.4.4 状态栏

状态栏上面不能通过可视化界面添加组件，需要通过代码添加

```
ui.statusbar->addwidget(&obName);
```

#### 2.4.5 Action功能的实现

Action是一种可见的界面元素，主要用于工具栏可菜单的按钮设计，Action的主要信号是trigger()。

- Action的实现可以通过编辑信号和槽函数实现。信号为*trigger()*



## 3 Qt类库概述

### 3.1 Qt核心特点

#### 3.1.2 Qt元对象系统

元对象系统组成：

- QObject类是所有元对象的基类
- 在一个类的private部分申明**Q_OBJECT**宏，使得类可以使用元对象特性
- MOC(元对象编译器)为每个子QObject提个必要的代码以实现元对象系统的特性。

元对象提供的功能:

- 信号与槽机制
- QObject::metaObject()返回关联元对象
- MEtaoBject: newInstanceO函数创建类的一个新的实例
- Qobject: inherits(const char* className)函数判断一个对象实例是否是名称为 className的
  类或 Qobject的子类的实例
- QObject:trO和 Qobject:trut8O函数可翻译字符串,用于多语言界面设计
- QObject; setPropertyo和 QObject: property函数用于通过属性名称动态设置和获取属性值
  对于 Qobject及其子类,还可以使用 qobject_cast函数进行动态投射( dynamic cast)。

#### 3.1.3 属性系统

1. 属性的定义

   ```c++
   Q_PROPERTY(type name (READ getFunction [WRITE setFunction] 
    MEMBER memberName [(READ getFunction | WRITE setFunction)])
   [RESET resetFunction]
   [	NOTIEY notifysignal
   [REVISION int]
   SIGNABLE bool]
   [SCRIPTABLE bool
   [STORED bool]
   [USER bool
   [CONSTANTI
   [FINAL])
   ```

   Q PROPERTY宏定义属性的主要关键字的意义如下

   - READ指定一个读取属性值的函数,没有 MEMBER关键字时必须设置READ
   - WRITE指定一个设定属性值的函数,只读属性没有 WRITE设置
   - MEMBER指定一个成员变量与属性关联,成为可读可写的属性,无需再设置READ和 WRITE。
   - RESET是可选的,用于指定一个设置属性缺省值的函数
   - NOTIFY是可选的,用于设置一个信号,当属性值变化时发射此信号
   - DESIGNABLE表示属性是否在 Qt Designer里可见,缺省为true
   - CONSTANT表示属性值是一个常数,对于一个对 象实例,READ指定的函数返回值是常
     数,但是每个实例的返回值可以不一样。具有 CONSTANT关键字的属性不能有 WRITE
     和 NOTIFY关键字
   - FINAL表示所定义的属性不能被子类重载

2. 属性的使用

   不管是否用READ和WRITE定义过接口，都可以使用**QObject::property()**读取属性值，并通过**QObject::setproperty()**设置属性值。

3. 动态属性

4. 类的附加信息

   可以使用**Q_CLASSINFO**为类提供键值类的附加信息

   > Q_OBJECT
   >
   > Q_CLASSINFO("author", "wang")

   可以通过`QMetaClassInfo QMetaObject::classinfo(int index) const`获取

#### 3.1.4 信号和槽

1. connect两种形式

   - 信号名称是唯一的，可以使用函数指针形式

     ```c++
     //原型
     QMetaObject::Connection QObject::connect(const QObject sender, const QMetaMethod &signal, const QObject receiver,const QMetaMethod &method,Qt::ConnectionType type=Qt::AutoConnection);
     
     //使用
     QObject::connect(lineEdit,&QLineEdit::textChanged,this,&widget::on_textChaned);
     ```

   - 另一种采用信号和槽的宏

     ```c++
     //原型
     QMetaObject::Connection QObject::connect(const QObject sender, const char* signal, const QObject receiver,const char*method,Qt::ConnectionTypetype=Qt::AutoConnection);
     
     //使用
     QObject::connect(lineEdit,SIGNAL(textChanged()),this,SLOT(on_textChaned()));
     ```

   connectType:

   - Qt::DirectConnection

     发送者和接收者在同一个线程，槽函数立即执行

   - Qt::QueuedConnect

     在不同线程，事件循环到接受者后才执行

   - Qt::BlockingQueuedConnect

     与Qt::QueuedConnect不同在于信号线程会阻塞，直到槽函数执行完毕**当信号和槽函数在同一线程时，使用这种连接方式会造成死锁**

   2. 使用sender获得信号发送者

   3. 自定义信号及其使用

      - 在自己的类中可以自定义自己的信号，信号的定义就是声明一个函数，该函数不能有返回值。

      - 函数无需实现，只需发射。

        ```c++
        emit 信号函数
        ```

### 3.2 Qt全局定义

```c++
<QtGlobal>  //头文件包含了Qt使用全局定义，包括基本类型，函数，宏定义
```

- 函数定义

  ```c++
  QT_VERSION //数值形式的版本
  QT_VERSION_CHECK
  QT_VERSION_STR//字符串形式
      
  //系统字节序
   QT_BYTE_ORDER,QT_BIG_ENDIAN.QT_LITTLE_ENDIAN
      
  //动态库导入导出
  QT_DECL_IMPORT, QT_DECL_EXPORT
  
  ```

### 3.3 容器类

#### 3.3.1 容器类

顺序容器

关联容器

#### 3.3.2 顺序容器

- QList  数组形式，支持下标访问
- QLinkedList 链表形式，不支持下标访问。使用迭代器访问。
- QVector 动态数组形式
- QStack 栈形式
- QQueue 队列形式

#### 3.3.3 关联容器

- QMap 基于键值
- QMultiMap 处理多值映射
- QHash
- QMultiHash
- QSet 基于散列表，存储的顺序不一定，查找速度快

### 3.4 容器类的迭代

#### 3.4.1 Java类型的迭代器

#### 3.4.2 STL类型的迭代器

### 3.5 Qt类库模块



## 4 常用界面设计组件

### 4.1 字符串的输入输出

- QString
  - setNum()
  - asprintf()
  - append()和prepend()
  - toUper(),toLower
  - count() size() length()一样
  - trimmed() 去掉首尾的空格 simplified()还会去掉中间连续的空格
  - indexOf()查找子串首次出现位置，lastIndexOf()查找最后出现的位置
  - isNull判断字符串是否为空，'\0' 返回false，isEmputy()返回true
  - left() right() 从字符串指定边取字符

### 4.2 QSpinBox

QSpinBox用于整数的显示和输入，QDoubleSpinBox用于浮点数的显示和输入。可以指定前缀和后缀。

主要属性：

- prefix 数字显示的前缀
- suffix 数字显示的后缀
- minimum 数字的最小值
- maximum数字的最大值
- singlestep单步改变值
- value当前显示值
- decimals小数位

### 4.3 其他数字显示输入组件

1. QSlider滑动条

   QSlider QScrollBar QDial共有属性：

   - minimum,maximum, singlestep

2. QScrollBar滚动条

3. QProgressBar进度条

4. QDial表盘式数字输入

5. QLCDNumber模仿LCD数字显示组件

### 4.4 时间日期与定时器

时间日期类型

- QTime时间类型
- QDate日期类型
- QDateTime时间日期类型

时间日期编辑显示组件

- QTimeEdit
- QDateEdit
- QDateTimeEdit
- QCalendarWidget

定时器

- QTimer,继承QObject不是界面组件。周期性发射timeout信号。

#### 4.4.1 日期格式转换

#### 4.4.2 日历组件

#### 4.4.3 定时器使用

### 4.5 QComboBox和QPlainTextEdit

QComboBox是下拉列表。每个子项可以关联一个Qvariant变量，用于存储一些不可见的对象。

#### 4.5.2 QComboBox的使用

1. 设计时属性设置

​        可以在界面上双击设置列表项

2. 用代码添加简单项

   使用additem方法。addItems用于QStringList

3. 关联数据

4. 列表的访问

   可以通过索引访问某项

   - int    currentIndex返回当前索引
   - QString    currentText返回当前文字
   - QVarriant   currentData返回当前项关联数据
   - int    count 返回项的个数
   - QString  itemText(int index);返回指定索引号的文字
   - QVarriant  itemData(int index)返回指定索引号的关联数据

5. 信号

   ```c++
   void currentIndexChanged(int index);
   void currentIndexChanged(const QString &text);
   ```

### 4.6 QListWidget和QToolButton

- 用于item的组件有两类
  - Item Viewa
    - QListView
    - QTreeView
    - QTableView
    - QColumnView
  - Item widgets
    - QListWidget
    - QTreeWidget
    - QTableWidget

  Item View s是基于模型视图结构，数据与模型关联实现数据的显示和编辑

  Item widget是直接将数据存储在每个项里面

- toolbox组件里面可以放任何组件

- QTabWidget是一个多页的容器

  - tabposition：标签位置，可以选择东西南北四个方向
  - currentIndex:当前页编号
  - currentTabText:当前页标题
  - currentTabName当前页对象名称
  - currentTabIcon当前页图标

- QSplitter设计分割界面

  - 可以左右上下分割

  - 在窗口构造器里面使用语句可以填充整个工作区

    ```c++
    setCentralWidget(ui->splitter);
    ```

- QListWidget

  - flags属性：是Qt::ItemFlag的枚举值

    - selectable项是否可选择
    - editable是否可编辑
    - dragEnabled是否可被拖动
    - dropEnabled是否可以接收拖放的项
    - userCheckable是否可复选
    - enable是否被使能
    - tristate是否允许第三态

    代码设置flags属性使用`setFlags`方法

    往QListWidget组件添加QListWidgetIteam。每一项是一个QListWidgetIteam对象

  - 常用信号

    - currentRowChanged(int currenRow)传递当前项的行号
    - currentItenChanged(QListWidgetItem *currentItem, QListWidgetItem *previous);

- QToolButton与下拉菜单

  - QToolButton关联Action

    QToolButton函数有个方法`setDefaultAction`设置默认action, 添加私有函数，在ui类构造函数调用

    ```c++
    void QtClass::setActionForToolButton(void)
    {
    	ui.toolButton_listWidgetInit->setDefaultAction(ui.action_init);
    	ui.toolButton_listWidgetClear->setDefaultAction(ui.action_clear);
    }
    ```

  - 为QToolButoon设置下拉菜单

    QToolButoon下拉菜单按钮不点小三角里面的有一个默认action，通过方法`setDefaultAction`设置，三角里面可以设置别的菜单action

    ```c++
    //默认action的信号和槽设置
    QObject::connect(ui.actionactPopMenu, SIGNAL(triggered()), ui.action_init, SLOT(trigger()));//actionactPopMenu与action_init绑定
    
    //设置菜单
    void QtClass:: setToolButoonPopMenu(void)
    {
    	ui.toolButton_popMenu->setPopupMode(QToolButton::MenuButtonPopup);//设置按小三角
    	ui.toolButton_popMenu->setDefaultAction(ui.actionactPopMenu);//设置默认action
    	ui.toolButton_popMenu->setToolButtonStyle(Qt::ToolButtonTextBesideIcon);
        
    	QMenu* menu;
    	menu = new QMenu(this);
    	menu->addAction(ui.action_insert);
    	menu->addAction(ui.action_append);
    	menu->addAction(ui.action_del);
    	ui.toolButton_popMenu->setMenu(menu);//给小三角添加菜单
    }
    ```

    

  

- 创建快捷菜单

  每个从widget继承的类都有customContexMenuRequested(const Qpoint &)信号,该信号在鼠标右击是发射。

  以listWidget组件为例添加右键菜单

  ```c++
  //设置组件菜单自定义
  ui.listWidget->setContextMenuPolicy(Qt::CustomContextMenu);//或者在ui设计界面设置该属性
  
  //绑定信号与槽
  QObject::connect(ui.listWidget, SIGNAL(customContextMenuRequested(const QPoint &)),this,
  		SLOT(on_customContextMenuRequested(const QPoint &)));
  //槽函数实现，添加右键菜单
  void QtClass::on_customContextMenuRequested(const QPoint &pos)
  {
  	Q_UNUSED(pos);
  	QMenu *menu = new QMenu(this);
  	menu->addAction(ui.action_clear);
  	menu->addAction(ui.action_del);
  	menu->addAction(ui.action_insert);
  	menu->addAction(ui.action_append);
  	menu->exec(QCursor::pos());
  	delete menu;
  }
  ```

  

#### 4.7 QTreeWidget和QDocWidget



## 5 Model/View（模型/视图）结构

是Qt的组件显示和数据编辑的一种结构。视图是显示和编辑数据的组件界面，模型是视图和原始数据之间的接口。

```

```









## 6 对话框与多窗体设计

### 6.1 标准对话框

#### 6.1.6 消息对话框QMessageBox

由几个静态函数实现消息对话框

- warnning()
- information()
- critical()
- about()
- question()确认对话框，返回按钮类型枚举值



## 7 文件系统和文件的读写

### 7.1 文本文件的读写

两种读写文本文件的方法：

- QFile类的IODevice直接读写
- QFile的QTextStream结合，使用Stream的方法进行读写

#### 7.1.2 QFile读写文件

1. 通过QDir静态方法currentPath()获取文件路径
2. 创建一个QFileDialog,调用QFileDialog的静态方法getOpenFileName()获取文件名。

```c++
QString name = QFileDialog::getOpenFileName(this, "select file", path);
```
 QFileDialog的静态方法
```c++
QString getOpenFileName() //选择打开一个文件
QStringList getOpenFileNames() //选择打开多个文件
QString getSaveFileNmae()//选择保存一个文件
QString getExistingDirectory()//选择一个已有目录
QUrl    getOpenFileUrl()//选择一个网络远程文件打开
```

3. 通过QFile创建一个文件对象file，然后打开文件，读取，关闭文件

#### 7.1.3 QFile和QTextStream结合方式读写文件

创建QTextStream对象，把QFile对象传递给Stream。读写方式采用Stream读写。

```c++
void notepad::on_actOpen_clicked(void)
{
    QString path = QDir::currentPath();
    QString name = QFileDialog::getOpenFileName(this, "打开文件...", path, 
        "程序文件(*.h *.cpp) ;; 文本文件(*.txt) ;; 所有文件(*.*)");
    
    openFile = new QFile(name);
    openFile->open(QIODevice::ReadOnly);
    QTextStream* stream = new QTextStream(openFile);

    //QString str = openFile->readAll();//读取中文会乱码
    stream->setAutoDetectUnicode(true);
    QString str = stream->readAll();
 
    ui.textEdit->setText(str);
    ui.textEdit->setEnabled(true);
    openFile->close();
}
```

### 7.2 二进制文件的读写

qt使用QFile和QDataStream来读取而二进制文件，QFile复制IO，QDataStream负责流

QDataStream保存文件有两种编码方式：

- Qt预定义编码方式（.stm）
  - 会自动添加数据类型的标记字节
- 标准编码方式（.dat）
  - 完全是和文件一样的二进制数据

#### 7.2.1 Qt预定义编码文件的读写

​	和TextStream一样，只是把QTextStream替换为QDataStream，在使用QDataStream对象之前，需要设置QDataStream的版本

```c++
dataStream.setVersion(QDataStream::Qt_5_9);
```

#### 7.2.2 标准编码文件读写

需要制定二进制文件的字节序

```c++
dataStream.setByteOrde(QDataStream::LittleEndian);
```

- 写文件使用`writeRawData`方法
- 使用`writeBytes`方写入，会自动先把字符串的长度写入到文件中（4个字节）

- 读文件使用`readRawData` 方法
- `readByte`会先读取4个字节的长度

### 7.3 文件目录的操作

- QCoreApplication:用于提取应用程序的路径，名字
- QFile: 打开，复制，删除文件
- QFileInfo：用于提取文件的信息，包括路径，文件名，后缀
- QDir:用于提取文件目录，或文件信息，获取一个目下的文件或目录列表，创建和删除目录和文件，文件重命名
- QTemporaryDir和QTemporaryFile:用于创建临时目录和文件
- QFileSystemWatcher文件和目录监听类，监听目录下文件的添加删除等变化，监听文件修改

#### 7.3.3 QCoreApplication类

​    是为无GUI应用提供事件循环的类，是所用应用程序的基类





## 8 绘图







