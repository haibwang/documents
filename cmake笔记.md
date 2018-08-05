

### 指令大小写无关，参数和变量是大小写相关的。推荐全大写！

### 命令

- `project(<project_nmae> [c][ c++][ java])`

  - 指定工程名

  	 创建两个隐式变量	`PROJECT_BINARY_DIR,PROJECT_SOURCE_DIR`，内部编译，两个变量的值都为当前目录。

    外部编译，在外部目录调用`cmake`执行`CMakeLists.txt`文件，`PROJECT_BINARY_DIR`为外部目录

- `SET(VAR  [VALUE])`显示设置

- `MESSAGE`消息打印

- 工程名和可执行文件名没有关系

- `cmake`处理变量名含空格的手段是，把变量名用引号括起来

- `add_subdirectory(source_dir  [binary_dir] [EXCLUDE FROM ALL])`该指令用于向当前工程添加源文件目录，并指定生成的二进制文件目录`EXCLUDE FROM ALL`在构建过程中排除此目录

- 在哪里`add_executable`或`add_library`,就在哪里设置最终目标文件的生成目录，使用`SET(EXECUTABL_OUTPUT_PATH)`或`SET(LIBRARY_OUTPUT_PATH)`

