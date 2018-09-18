## 启动代码

启动代码作用：

- 初始化堆和栈
- 向量表定义
- 地址重映射和中断向量表转移
- 设置系统时钟频率
- 初始化中断寄存器
- 进入 C 程序

#### 启动方式

![启动方式](https://upload-images.jianshu.io/upload_images/3284659-780ece00ceb23756.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/586)

- `reset`后会从`0x00000000`和`0x00000004`处分别去除`sp`和`pc`，通过`BOOT0`和`BOOT1`把`sp`和`pc`重映射到不同区域启动。

1. 内部FLASH启动方式

当芯片上电后采样到BOOT0引脚为低电平时，0x00000000和0x00000004地址被映射到内部FLASH的首地址0x08000000和0x08000004。因此，内核离开复位状态后，读取内部FLASH的0x08000000地址空间存储的内容，赋值给栈指针SP，作为栈顶地址，再读取内部FLASH的0x08000004地址空间存储的内容，赋值给程序指针PC，作为将要执行的第一条指令所在的地址。具备这两个条件后，内核就可以开始从PC指向的地址中读取指令执行了。

2. 内部SRAM启动方式

类似地，当芯片上电后采样到BOOT0和BOOT1引脚均为高电平时，0x00000000和0x00000004地址被映射到内部SRAM的首地址0x20000000和0x20000004，内核从SRAM空间获取内容进行自举。其实自举就是设置运行环境并执行主体程序，只是听起来高大上罢了。

3. 系统存储器启动方式

当芯片上电后采样到BOOT0引脚为高电平，BOOT1为低电平时，内核将从系统存储器的0x1FFF0000及0x1FFF0004获取MSP及PC值进行自举。系统存储器是一段特殊的空间，用户不能访问，ST公司在芯片出厂前就在系统存储器中固化了一段代码。因而使用系统存储器启动方式时，内核会执行该代码，该代码运行时，会为ISP提供支持(In System Program)，如检测USART1/3、CAN2及USB通讯接口传输过来的信息，并根据这些信息更新自己内部FLASH的内容，达到升级产品应用程序的目的，因此这种启动方式也称为ISP启动方式。



### KEIL的启动文件

#### 启动代码分析

#####  堆和栈的初始化

###### 栈初始化

```assembly
AREA    STACK,  NOINIT,  READWRITE,  ALIGN=3
Stack_Size      EQU     0x00000400
Stack_Mem     SPACE   Stack_Size 
__initial_sp 
```



分析：

```assembly
Stack_Size      EQU     0x00000400
```

这段代码很简单，就是给Stack_Size赋一个值而已，用来定义栈区大小。

栈区（stack）:  由编译器自动分配释放 ，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。

```assembly
AREA    STACK,  NOINIT,  READWRITE,  ALIGN=3
```
AREA 伪指令用于定义一个段，如代码段、数据段或者堆栈段。（注意：段是汇编语言中非常重要的一个概念，可以去详细了解一下） ；STACK表示我们定义的是栈，其实这里仅仅是一个便于人理解的一个单词啦；NOINIT指定此数据段仅仅保留了内存单元，而没有将各初始值写入内存单元，或者将各个内存单元值初始化为0；READWRITE属性，指定本段为可读可写；ALIGN属性，用来指定数据对齐的方式，为2的ALIGN次方，这是ALIGN=3，也就是按照字节对齐。

```assembly
Stack_Mem     SPACE   Stack_Size 
```

SPACE用来分配一片连续的存储区域并初始化为0。这里也就是分配一篇大小为0x400的连续存储区域，并初始化为0，并且该区域的起始地址为Stack_Mem。
```assembly
__initial_sp 
```
是汇编代码地址标号，在这里我们用来表示栈空间顶地址。  



######　堆初始化

```assembly
Heap_Size      EQU    0x00000200 
AREA    HEAP,  NOINIT,  READWRITE,  ALIGN=3
Heap_Mem        SPACE  Heap_Size
__heap_limit  
```

##### 中断向量表



```assembly
 PRESERVE8
 THUMB
```

PRESERVE8指定当前文件要求堆栈8位对齐。

THUMB指定所用的指令集为thumb。

其实这两条代码我也不是很懂，希望懂得朋友可以指点一下。

这里我们需要注意一下这条注释：

> ; Vector Table Mapped to Address 0 at Reset

它指的Address 0并不是真正意义上的0x00000000。在我们之前的假设下（假设STM32从FLASH启动）则此中断向量表起始地址为0x8000000，实际上是在CODE区。
```assembly
 AREA    RESET,  DATA,  READONLY 
```

AREA定义一块数据段，只可读，段名字是RESET；DATA属性：用于定义数据段，默认为READWRITE。指定本段为可读可写。

```assembly
 EXPORT  __Vectors     
```

EXPORT伪指令用于在程序中声明一个全局的标号，该标号可在其他的文件中引用。EXPORT可用GLOBAL代替。标号在程序中区分大小写。在程序中声明一个全局的标号__Vectors，该标号可在其他的文件中引用

```assembly
EXPORT  __Vectors_End 
```

在程序中声明一个全局的标号__Vectors_End

```assembly
EXPORT  __Vectors_Size 
```

在程序中声明一个全局的标号__Vectors_Size

```assembly
__Vectors       DCD     __initial_sp              
```

DCD  用于分配一片连续的**字存储单元** 并用指定的数据初始化。向量表第一个表项是栈顶地址，该处物理地址值即为 __Vetors 标号所表示的值，该地址中存储__initial_sp所表示的地址值，大小为一个字（32bit）。

```assembly
DCD     Reset_Handler          
```

向量表第二个表项是复位中断服务函数Reset Handler入口地址

```assembly
DCD     NMI_Handler
 ...
 ...
 ...
DCD     OTG_FS_IRQHandler        
```

这些都是在中断向量表中注册函数入口地址，就不在这里挨个解释了。

```assembly
__Vectors_End  
```

表示中断向量表结束
```assembly
 __Vectors_Size   EQU   __Vectors_End  -  __Vectors  
```
得到向量表的大小



#### 地址重映射及中断向量表的转移

> AREA    |.text|,  CODE,  READONLY   

定义一个代码段，可读，段名字是.text  段名若以数字或者标点开头，则该段名需用"|"括起来，如|1_test|。

> Reset_Handler    PROC 

标记一个函数的开始。利用PROC、ENDP这一对伪指令把程序段分为若干个过程，使程序的结构加清晰。

> EXPORT  Reset_Handler             [WEAK]  

EXPORT伪指令用于在程序中声明一个全局的标号，[WEAK]选项声明其他的同名标号优先于该标号被引用。在外部没有定义该符号时导出该符号Reset_Handler。

> IMPORT  SystemInit
>
> IMPORT  __main 

 IMPORT伪指令用于通知编译器要使用的标号在其他的源文件中定义

> LDR     R0, =SystemInit
>
> BLX     R0

把SystemInit函数的地址装载到R0，BLX为跳转指令，跳转到R0中的地址处，换句话说，就是执行SystemInit（）这个函数啦。函数SystemInit（）主要作用是设置系统时钟频率和中断寄存器的初始化。

> LDR     R0, =__main 
>
> BX      R0  

把main（）函数的地址装载到R0，BLX为跳转指令，跳转到R0中的地址处，换句话说，就是执行main（）这个函数啦，进入C的世界。

> ENDP

函数结束

然后由很长的代码都是用来定义相关函数的，这里就不解释了。

> B

B本来也是跳转指令。它的作用相当于 MOV PC， 目标地址。但是这里B后面没有接上目标地址，我也不知道为什么要用上这么一句。

> ENDP
>
> ALIGN

ENDP不多说。ALIGN属性：使用方式为ALIGN 表达式。在默认时，ELF（可执行连接文件）的代码段和数据段是按字对齐的，表达式的取值范围为0～31，相应的对齐方式为2表达式次方。

#### 4）堆和栈的初始化

> IF      :DEF:__MICROLIB  

判断是否使用DEF:__MICROLIB（micro lib）

> EXPORT  __initial_sp     
>
> EXPORT  __heap_base  ;使外部程序可以使用
>
> EXPORT  __heap_limit

将__initial_sp、__heap_base、__heap_limit赋予全局属性、

> ELSE   
>
> IMPORT  __use_two_region_memory  

定义全局标号 __use_two_region_memory

> EXPORT  __user_initial_stackheap   

声明全局标号__user_initial_stackheap，这样外程序也可调用此标号。允许进行栈和堆的赋值，在__main函数执行过程中调用。

> __user_initial_stackheap   

标号__user_initial_stackheap，表示用户堆栈初始化程序入口

> LDR     R0, =  Heap_Mem    

保存堆始地址

> LDR     R1, =(Stack_Mem + Stack_Size) 

保存栈的大小

> LDR     R2, = (Heap_Mem +  Heap_Size) 

保存堆的大小

> LDR     R3, = Stack_Mem  

保存栈顶指针

> BX      LR

相当于MOV  PC, LR

LR就是连接寄存器（Link Register，LR）在ARM体系结构中LR的特殊用途有两种：一是用来保存子程序返回地址；二是当异常发生时，LR中保存的值等于异常发生时PC的值减4（或者减2），因此在各种异常模式下可以根据LR的值返回到异常发生前的相应位置继续执行。在这里是第一种功能，那么结合BX的用法，就是回到之前保存的返回地址处。

> ALIGN

同上一个ALIGN

> ENDIF
>
> END







### IAR的启动文件

```assembly
 MODULE  ?cstartup  
      
    ;; Forward declaration of sections.  
    SECTION CSTACK:DATA:NOROOT(3)  
  
    SECTION .intvec:CODE:NOROOT(2)  
  
    EXTERN  __iar_program_start  
    EXTERN  SystemInit          
    PUBLIC  __vector_table
```



【1】MODULE 控制指令是用来标记 modules 源码的开始和结束，后边的 ?cstartup 是模块的名字，此文档的最后的 END 表明模块的结束

【2】SECTION 指令是声明段，一个段不能同时包含 public symbol 和 pubweak symbol ，模块只有在相同的名字的模块没有被链接进来的时候才会被链接进来。

​    语法格式：**SECTION section:type [flag] [(align)]**

​        align，是用于指定地址对齐到 2^align，他的取值是 0 到 30

​        flag，取值NOROOT、ROOT、REORDER、NOREORDER，默认是ROOT，NOROOT表示如果这个段中的符号没有被引用，将会被连接器舍弃，即可被优化。ROOT表示不可被优化。REORDER表示开始一个新的名字是 section 的段（section），NOREORDER表示开始一个新的名字为 section 的片段（fragment），多个片段组成一个段（section）

​        type，memory 的类型，取值是 CODE、CONST、DATA

​        section，段的名字

【3】EXTERN 用导入其他模块的 symbol（符号）

【4】PUBLIC 导出 symbol（符号）



```assembly
;;格式：PROGRAM symbol---其中symbol为分配模块的名字;;开始一个模块；PROGRAM和NAME指令的别名
    MODULE  ?cstartup ;汇编模块开始
;; Forward declaration of sections.前置 段声明
;;格式：SECTION section :type [flag] [(align)]
;;NOROOT表示如果这个段里的标号(如CSTACK/.intvec等)没引用，则被linker舍弃;ROOT则是一定不舍弃。
;;(1)表示字节对齐数，为2的幂，(2)表示4字节对齐，(3)表示8字节对齐
;;定义各种模式STACK，具体大小、位置是由linker文件(IAR中 .icf)决定的,这些STACK都定义在内部RAM中
        SECTION CSTACK:DATA:NOROOT(3) ;CSTACK数据段
;;通常INTVEC段被连接到flash闪存的开始地址，应该在.icf文件中有定义，如stmf10X系列 闪存起始地址：0x08000000
        SECTION .intvec:CODE:NOROOT(2) ;.intvec代码
        EXTERN  __iar_program_start ;;外部变量声明
        EXTERN  SystemInit        ;SystemInit声明，于外部定义
        PUBLIC  __vector_table ;;外部可调用；向其他模块输出符号
        
                DATA
  ;;中断向量表     
__vector_table		;;格式：DCD expr [,expr] …  ;Generates 32-bit constants.生成32bit常数
        DCD     sfe(CSTACK)  ;;格式：SFE (section [{+ | -} offset])  返回section段结束地址，SFB(section)返回section段开始地址;
        DCD     Reset_Handler             ; Reset Handler
        DCD     NMI_Handler               ; NMI Handler
        DCD     HardFault_Handler         ; Hard Fault Handler
        DCD     MemManage_Handler         ; MPU Fault Handler
        DCD     BusFault_Handler          ; Bus Fault Handler
        DCD     UsageFault_Handler        ; Usage Fault Handler
        DCD     0                         ; Reserved
        DCD     0                         ; Reserved
        DCD     0                         ; Reserved
        DCD     0                         ; Reserved
        DCD     SVC_Handler               ; SVCall Handler
        DCD     DebugMon_Handler          ; Debug Monitor Handler
        DCD     0                         ; Reserved
        DCD     PendSV_Handler            ; PendSV Handler
        DCD     SysTick_Handler           ; SysTick Handler
 
         ; External Interrupts
        DCD     WWDG_IRQHandler           ; Window Watchdog
        DCD     PVD_IRQHandler            ; PVD through EXTI Line detect
        DCD     TAMPER_IRQHandler         ; Tamper
        DCD     RTC_IRQHandler            ; RTC
        DCD     FLASH_IRQHandler          ; Flash
        DCD     RCC_IRQHandler            ; RCC
        DCD     EXTI0_IRQHandler          ; EXTI Line 0
        DCD     EXTI1_IRQHandler          ; EXTI Line 1
        DCD     EXTI2_IRQHandler          ; EXTI Line 2
        DCD     EXTI3_IRQHandler          ; EXTI Line 3
        DCD     EXTI4_IRQHandler          ; EXTI Line 4
        DCD     DMA1_Channel1_IRQHandler  ; DMA1 Channel 1
        DCD     DMA1_Channel2_IRQHandler  ; DMA1 Channel 2
        DCD     DMA1_Channel3_IRQHandler  ; DMA1 Channel 3
        DCD     DMA1_Channel4_IRQHandler  ; DMA1 Channel 4
        DCD     DMA1_Channel5_IRQHandler  ; DMA1 Channel 5
        DCD     DMA1_Channel6_IRQHandler  ; DMA1 Channel 6
        DCD     DMA1_Channel7_IRQHandler  ; DMA1 Channel 7
        DCD     ADC1_2_IRQHandler         ; ADC1 & ADC2
        DCD     USB_HP_CAN1_TX_IRQHandler  ; USB High Priority or CAN1 TX
        DCD     USB_LP_CAN1_RX0_IRQHandler ; USB Low  Priority or CAN1 RX0
        DCD     CAN1_RX1_IRQHandler       ; CAN1 RX1
        DCD     CAN1_SCE_IRQHandler       ; CAN1 SCE
        DCD     EXTI9_5_IRQHandler        ; EXTI Line 9..5
        DCD     TIM1_BRK_IRQHandler       ; TIM1 Break
        DCD     TIM1_UP_IRQHandler        ; TIM1 Update
        DCD     TIM1_TRG_COM_IRQHandler   ; TIM1 Trigger and Commutation
        DCD     TIM1_CC_IRQHandler        ; TIM1 Capture Compare
        DCD     TIM2_IRQHandler           ; TIM2
        DCD     TIM3_IRQHandler           ; TIM3
        DCD     TIM4_IRQHandler           ; TIM4
        DCD     I2C1_EV_IRQHandler        ; I2C1 Event
        DCD     I2C1_ER_IRQHandler        ; I2C1 Error
        DCD     I2C2_EV_IRQHandler        ; I2C2 Event
        DCD     I2C2_ER_IRQHandler        ; I2C2 Error
        DCD     SPI1_IRQHandler           ; SPI1
        DCD     SPI2_IRQHandler           ; SPI2
        DCD     USART1_IRQHandler         ; USART1
        DCD     USART2_IRQHandler         ; USART2
        DCD     USART3_IRQHandler         ; USART3
        DCD     EXTI15_10_IRQHandler      ; EXTI Line 15..10
        DCD     RTCAlarm_IRQHandler       ; RTC Alarm through EXTI Line
        DCD     USBWakeUp_IRQHandler      ; USB Wakeup from suspend
        DCD     TIM8_BRK_IRQHandler       ; TIM8 Break
        DCD     TIM8_UP_IRQHandler        ; TIM8 Update
        DCD     TIM8_TRG_COM_IRQHandler   ; TIM8 Trigger and Commutation
        DCD     TIM8_CC_IRQHandler        ; TIM8 Capture Compare
        DCD     ADC3_IRQHandler           ; ADC3
        DCD     FSMC_IRQHandler           ; FSMC
        DCD     SDIO_IRQHandler           ; SDIO
        DCD     TIM5_IRQHandler           ; TIM5
        DCD     SPI3_IRQHandler           ; SPI3
        DCD     UART4_IRQHandler          ; UART4
        DCD     UART5_IRQHandler          ; UART5
        DCD     TIM6_IRQHandler           ; TIM6
        DCD     TIM7_IRQHandler           ; TIM7
        DCD     DMA2_Channel1_IRQHandler  ; DMA2 Channel1
        DCD     DMA2_Channel2_IRQHandler  ; DMA2 Channel2
        DCD     DMA2_Channel3_IRQHandler  ; DMA2 Channel3
        DCD     DMA2_Channel4_5_IRQHandler ; DMA2 Channel4 & Channel5
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;;
;; Default interrupt handlers.
;;

        THUMB 							;;Mode control directives--模式控制指令
 
        PUBWEAK Reset_Handler			;;标识符(这里是函数名) 弱定义
        SECTION .text:CODE:REORDER(2)	;;
Reset_Handler
        LDR     R0, =SystemInit
        BLX     R0
        LDR     R0, =__iar_program_start
        BX      R0
               
        PUBWEAK NMI_Handler
        SECTION .text:CODE:REORDER(1)
NMI_Handler
        B NMI_Handler
 
        PUBWEAK HardFault_Handler
        SECTION .text:CODE:REORDER(1)
HardFault_Handler
        B HardFault_Handler
 
        PUBWEAK MemManage_Handler
        SECTION .text:CODE:REORDER(1)
MemManage_Handler
        B MemManage_Handler
 
        PUBWEAK BusFault_Handler
        SECTION .text:CODE:REORDER(1)
BusFault_Handler
        B BusFault_Handler
 
        PUBWEAK UsageFault_Handler
        SECTION .text:CODE:REORDER(1)
.................................
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
```

