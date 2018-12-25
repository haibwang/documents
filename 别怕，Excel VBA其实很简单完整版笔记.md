VBA（Visual Basic For Application）

**VBA结构**

- 过程：sub过程和function过程

  完成一个任务的所以VBA代码包起来

- 模块

  保存过程的地方，可以包含很多不同类型的过程

- 对象

  代码操作和控制的都叫对象：工作簿，工作单元，单元格，图片，图表，透视表

- 

**VBA编程环境**

- 一个工作簿就是一个VBA工程，工程名为*VBA Project(文件名)*

- 4类对象

  - Excel对象

    sheet

    workbook

  - 窗体：自定义的对话框和页面

  - 模块：用来保存用户编写或录制宏的代码

  - 类模块

模块的添加和删除

过程的添加和删除，代码编写

```VB

'在一个模块里创建一个过程
Sub hello()
MsgBox "Hello VBA"
End Sub
```



----

### 1 VBA基础语法



#### 1.1 数据类型

相比较C语言，多了字**节类型(Byte)**、**货币类型(Currency)**、**日期类型(Date)**、**小数类型(Decimal)**

各数据类型长度：

#### 1.2 常量和变量

- 声明变量

  ```Dim 变量名 As 数据类型```

  String声明时可以使用*指定变量的长度

  ```VB
  Dim name As String*15 '声明一个字符串变量，长度为15
  ```

  使用变量类型符声明

  | 数据类型 | 类型声明符 |
  | -------- | ---------- |
  | Integer  | %          |
  | Long     | &          |
  | Single   | !          |
  | Double   | #          |
  | Currency | @          |
  | String   | $          |

  ```VB
  Dim name$
  ```

  声明多个变量

  ```VB
  Dim name As String, age As Integer
  ```

  **不指明变量类型默认为Variant类型:根据需要存储的数据自动选择与之匹配的类型，占用的存储空间较大**

  强制声明变量，在模块的第一句插入`Option Explicit`

  除了Dim还可以使用Public、Private、Static声明

  ```VB
  Dim name As String '作用域单个过程或模块
  Static name As String'作用域单个过程
  Private name As String'作用域单个模块
  Public name As String'作用于所有模块
  ```

- 变量的作用域

  单个过程：Dim和Static声明的变量

  单个模块：在模块的第一个过程之前使用Dim声明的变量和Private声明的变量

  所有模块 : 在模块的第一个过程之前使用Public声明的变量

- 变量赋值

  普通变量赋值：[LET] 变量= 数据，LET可有可无

  对象变量赋值： **SET **  对象名 = 对象， SET必须有

- 常量

  常量声明的同时必须赋值，后面不能更改改值

  ```const 常量名 As 数据类型 = 值```


####1.3 数组

- 数据的声明

  ```VB
  Dim 数组名(a to b) As 数据类型 '声明数组，数据的元素为a到b,ab都是整数，数据元素个数为b-a+1
  
  Dim arr(49) As String '定义元素格式为49-0+1=50个字符串，下标从0开始
  '在模块第一句输入Option Base 1,数组的下标从1开始
  ```

  多数数组，按货架理解，声明多为数据，元素个数用逗号隔开

  声明动态数组,声明不指定数据的大小，在使用时使用ReDime重新定义数组的大小

  ```VB
  Dim employe_arr() As String '不指定数组大小
  ReDime employe_arr As String '重新指定
  ```

  其他创建数组的方式

  - array创建：返回的数据类型为Variant类型变量

  - split创建:把指定的字符串按照分隔符分隔开，返回一个数组，索引从0开始

  - range对象创建：返回单元格的一片连续区域，（左上角单元格编号：右下角单元格编号）

  - UBound（数组名）：数组最大索引数

  - LBound（）：数组最小索引数

  - Jion（数组名，“分割符”）函数：将一维数组里面的元素用指定的分隔符连接成一个字符串，和Split相反

  - 将一个数组写入单元格区域，该区域必须要在同一行，要写一列，需要用工作表函数Tanspose（arr）,单元格的个数须与数组的个数一样

    ```VB
    
    Public Sub ArrtoRange()
    
    Dim arr As Variant '定义一个变量
    arr = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10) '使用Array函数创建一个数组
    '把数组赋值给一个区域
    Range("A1:A10") = Application.WorksheetFunction.Transpose(arr) 
    Range("A1:J1") = arr '在同一行，不需要转置,单元格区域的大小必须与数组大小一致
    
    End Sub
    ```




#### 1.4 集合对象属性和方法

- 集合：多个同类型的对象

- 对象引用

  ```VB
  Application.Workbooks("Book1").Worksheets("sheet1").Range ("A2")  'boo1工作簿的sheet1的A2单元格
  
  'Application代表Excel程序
  'Workbooks是工作簿集合
  'Worksheets是工作表集合
  '如果当前工作簿是激活状态，上面的可以写作
  Worksheets("sheet1").Range("A2")
  '如果当前工作表是激活的，可以写作为
  Range("A2")
  ```

- 对象方法属性的区分

  图表为绿色的代表是方法，其余的都是属性

#### 1.5 运算符

- 算数运算符：

- `+`、`*`、`/`、`\`、`^`、`Mod`

- `-`：相减或取相反数

- 比较运算符：

  <>不等于

  is：比较两个对象的引用变量

  like:字符串是否匹配

  > VBA中的通配符

- 连接运算符:

  +:两边都为字符串是连接，否则进行算数运算

  &：连接两个字符串

- 逻辑运算符：And、Or、Not、Xor

  Eqv：等价

  Imp: 蕴含==》Not 表达式1 Or 表达式2

处理优先级顺序**算数运算->比较运算->逻辑运算**





#### 1.6 内置函数

可以在VBE环境下使用`vba. ` 查询内置函数

#### 1.7 流程控制

- if then 没有括号，多行以end if结束

  ```VB
  IF 条件 THEN 语句
  ''''''''''''''''''''''''''
  IF 条件 THEN 
     语句 
  END IF
      
  '''''''''''''''''''''''''
      IF 条件 THEN
          语句
      ELSE
          语句
      END IF
   ''''''''''''''''''''''''
      IF 条件 THEN 
         语句
      ELSEIF 条件 THEN
          语句
      ELSE
          语句
      END IF
   '''''''''''''''''''''''''
      
      
      
  ```

- select case语句
    ```VB
    select case value
         case 表达式
      		语句
         case 表达式
    			语句
    case Else '相当于C语言的Default
    End select
    '没有冒号，不需要break，只执行一个case
    ```

- For Next语句

    ```VB
    FOR 变量=a to b[STEP]'Step为步长，省略时为1
    	循环体
    	[EXIT FOR] '退出for循环
    	[循环体]
    Next[循环变量]'更改循环变量的值，相当于循环变量+step
    ```

- Do While语句

    ```vb
    
    ```




#### 1.8 基本程序单元，VBA过程

- SUB 过程

  声明过程

  ```VB
  [Public|Private|Static] Sub 过程名（[参数列表]）
      [语句块]
      [EXIT Sub]
      [语句块]
      END Sub
          
  ```

  过程引用，三种方式

  ```VB
  Sub sayHello()
     MsgBox "Hello"
  END Sub
  
  ’第一种方式：过程名[参数1，参数2，参数3]
  Sub Dialog()
      sayHello
  end Sub
  
  '第二种方式：Call 过程名[(参数1，餐参数2，参数3)],参数需要用括号括起来
  Sub Dialog()
      call sayHello
  END sub
  
  '第三种方式：采用Applicatuon对象Run方法，Application.run "过程名"[,参数1，参数2，参数3]
  Sub Dialog
      Application.Run "sayHello"
  End Sub
      
  ```

  过程的作用域

- 函数过程

  把函数计算的结果返回给函数名

  ```VB
  [Public|Private|Static] Function 函数名([参数列表]) [As 数据类型]
      [函数体]
      [函数名 = 过程结果]
      [Exit Function]
      [函数体]
      [函数名 = 过程结果]
  END Function
  ```



### 2 常用对象

#### 2.1对象模型

#### 2.2 常用对象

| 对象        | 对象说明           |
| ----------- | ------------------ |
| Application | Excel本身程序      |
| Workbooks   | 工作簿             |
| Worksheets  | 工作表             |
| Range       | 单元格或单元格区域 |

- Application对象常用属性和方法

  - screenUpdating：程序的计算结果是否显示在屏幕上，默认为TRUE

  - DisplayAlerts：显示警告属性，默认为TRUE

  - EnableEvents：启用或禁止事件

    事件是能够被Excel认识的一个动作，当触发某个事件时，会自动执行指定的代码

  - WorksheetFunction：引用worksheet的函数

  - excel梳妆打扮属性

    - Application.Caption：标题栏属性

  - 引用对象

    | 对象           | 说明                                        |
    | -------------- | ------------------------------------------- |
    | ActiveCell     | 当前活动单元格                              |
    | ActiveChart    | 当前活动工作簿中的活动图表                  |
    | ActiveSheet    | 当前活动工作簿中的活动工作表                |
    | ActiveWindow   | 当前活动窗口                                |
    | ActiveWorkbook | 当前活动工作簿                              |
    | Charts         | 当前活动工作簿中的所有图表                  |
    | selection      | 当前活动工作簿中的所有选中对象              |
    | sheets         | 当前工作簿中所有sheet对象                   |
    | worksheet      | 当前中作簿中所有worksheet对象（普通工作表） |
    | workbooks      | 当前所有打开的工作簿                        |

- Workbook对象

   通过指明工作簿的位置和名称引用单个工作簿

  ```vb
  Workbooks("book1.xlsx")    '引用book1工作簿
  
  
  ThisWorkbook '代码所在的工作簿
  
  Sub wookbook()
  Dim name As String
  Dim path As String
  Dim fullname As String
  
  name = ThisWorkbook.name
  path = ThisWorkbook.path
  fullname = ThisWorkbook.fullname
  
  MsgBox "name=" + name
  MsgBox "path: " + path
  MsgBox "fullname: " + fullname
  
  End Sub
  
  
  
  '创建工作簿
  Workbooks.add 
  '打开工作簿
  Workbooks.open
  '激活工作簿
  Workbooks("工作簿名").Activate
  '保存工作簿
  Thisworkbook.save  '保存代码所在的工作簿
  Thisworkbook.saveAs Filename:="fullname"
  '关闭工作簿
  Workbooks.close
  Workbooks("book1").close
  
  ```

- Worksheets对象

  worksheet的代码名称和标签名称

  ```VB
  Worksheets.add after:=worksheets(1) count:=3   '在第一张工作表之后创建3张工作表
  Worksheets.add befor:=Worksheets(Worksheets.count) count:=2'在最后一张工作表前插入两张表
  Worksheets(1).name = "联系方式" '更改工作表的标签名称
  Worksheets(1).delete '删除工作表
  Worksheets(1).Activate == Worksheets(1).select '激活工作表
  Worksheets("工资表").copy before:=worksheets("出勤表") '拷贝工作表，在同一个工作簿中，自动命名，与原工作表不同
  Worksheets("工资表").COPY '将复制到一个新的工作簿中，名字和原来的一样
      
      '移动工作簿
      '隐藏和显示工作簿
      '获取工作表的数目
      Worksheets.count
      
  ```























