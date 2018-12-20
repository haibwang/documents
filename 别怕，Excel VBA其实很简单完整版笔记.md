## VBA（Visual Basic For Application）

## 数组

- array创建
- split创建
- range对象创建
- UBound（数组名）：数组最大索引数
- LBound（）：数组最小索引数
- Jion（数组名，“分割符”）函数：将一维数组里面的元素用指定的分隔符连接成一个字符串
- 讲一个数组写入单元格，默认识写一行，要写一列，需要用工作表函数Tanspose（arr）,单元格的个数必须与数组的个数一样

## 集合对象属性和方法

- 集合：多个同类型的对象

## 内置函数

vba.  系统会提示



### 流程控制

- if then 没有括号，多行以end if结束

- ```vba
  select case value
         case 表达式
         case else 表达式
  end select
  //没有冒号，不需要break，只执行一个case
  ```

  ​