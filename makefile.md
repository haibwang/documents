#### makefile学习笔记

    makefile文件是用来告诉make工具如何编译和链接一个程序

makefile的规则：

```makefile
target ... : prerequisities ...
	comand
	...
	...
```

- target可以为可执行文件、中间文件的名字，也可以为命令
- prerequisities是创建target的输入文件，**即target的依赖**
- comand为make的执行的动作，**每个命令独占一行，每个命令前必须是一个TAB字符**
- 采用\\换行

