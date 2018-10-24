### 环境搭建

 [Download the MSYS2 installer](https://msys2.github.io/) that matches your platform and follow the installation instructions. 

#### Step 2: Install GTK+3 and its dependencies

Open a MSYS2 shell, and run:
 `pacman -S mingw-w64-x86_64-gtk3` 

#### Step 3 (recommended): Install GTK+ core applications

**Glade** is a GUI designer for GTK+. It lets you design your GUI and export it in XML format. You can then import your GUI from your code using the `GtkBuilder` API. Read the `GtkBuilder` section in the GTK+ manual for more information.

To install Glade:
 `pacman -S mingw-w64-x86_64-glade` 

**Devhelp** is a help browser. It lets you easily  navigate offline in the GTK+, glib and gobject API help relative to the  version of these libraries installed on your system.

To install Devhelp:
 `pacman -S mingw-w64-x86_64-devhelp` 

#### Step 4 (optional): Install the Python bindings

If you want to develop a GTK+3 application in Python, you need to install the Python bindings.

If you develop in Python 3:
 `pacman -S mingw-w64-x86_64-python3-gobject` 

If you develop in Python 2:
 `pacman -S mingw-w64-x86_64-python2-gobject` 

#### Step 5 (optional): Install build tools

If you want to develop a GTK+3 application in other languages like C,  C++, Fortran, etc, you'll need a compiler like gcc and other  development tools:
 `pacman -S mingw-w64-x86_64-toolchain base-devel` 



- 导出所有依赖头文件

  ```
  pkg-config --cflags gtk+-3.0
  ```

- 导出所有依赖库

  ```
  pkg-config --libs gtk+-3.0
  ```


### 基础

- 例子0

```c
#include <gtk/gtk.h>


/**
* 在activate()函数中，使用gtk_application_window_new()函数构建了一个GTKwindow，并且把值保存在window*  * 指针中，这个window有一个框架，一个标题条，窗口的控制取决于平台 
*/

static void
activate (GtkApplication* app,
          gpointer        user_data)
{
  GtkWidget *window;

  window = gtk_application_window_new (app);
  gtk_window_set_title (GTK_WINDOW (window), "Window");//使用宏定义把GtkWidget*强转为GtkWindow*
  gtk_window_set_default_size (GTK_WINDOW (window), 200, 200);
  gtk_widget_show_all (window);
}


/*
*  main函数用于创建GtkApplication对象,并且运行它。
*/


int
main (int    argc,
      char **argv)
{
  GtkApplication *app;/**>GtkApplication 指针，使用get_appliction-new初始化*/
  int status;
  
  /*
  * 需要指定一个应用识别名字作为参数传进去
  * 如果应用有特别需求，可以传一个GtkApplicationFlag进去，定义应用的行为
  */
  app = gtk_application_new ("org.gtk.example", G_APPLICATION_FLAGS_NONE);
  
 /*
  * 激活信号连接到activate()函数，然后用g_application_run运行
  *  g_application_run使用命令行参数控制gtk的行为，能识别的参数从array中删除，不能识别的留下，代自己的   *  应用程序解析
  */
  g_signal_connect (app, "activate", G_CALLBACK (activate), NULL);
  status = g_application_run (G_APPLICATION (app), argc, argv);//运行程序，结束时返回值给status
  g_object_unref (app);//从内存释放Application对象

  return status;//最后返回status，退出
}
```

### 2 事件响应

- 例子1


- 相比于例子0，增加了一个`Hello World `按钮。
  - 声明了两个新的`GtkWidget `类型的指针变量，`button`,`button_box`
  - GTK使用`GtkButtonBox`控制按钮的大小和布局

```c
#include <gtk/gtk.h>

static void
print_hello (GtkWidget *widget,
             gpointer   data)
{
  g_print ("Hello World\n");
}

static void
activate (GtkApplication *app,
          gpointer        user_data)
{
  GtkWidget *window;
  GtkWidget *button;
  GtkWidget *button_box;

  window = gtk_application_window_new (app);
  gtk_window_set_title (GTK_WINDOW (window), "Window");
  gtk_window_set_default_size (GTK_WINDOW (window), 200, 200);

  /* 使用gtk_button_box_new()创建button_box ,使用GtkOrientation枚举变量作为参数*/
  button_box = gtk_button_box_new (GTK_ORIENTATION_HORIZONTAL);
  gtk_container_add (GTK_CONTAINER (window), button_box);//把按钮箱添加进widget窗口

  /* 使用和创建button_box一样的方法创建按钮 */
  button = gtk_button_new_with_label ("Hello World");
  
  /* 使用g_singal_connect()给按钮连接一个回调函数，会调函数的参数设置为NULL */
  g_signal_connect (button, "clicked", G_CALLBACK (print_hello), NULL);
  
  /* 
  * 使用g_signal_connect_swapped连接按钮的状态，和g_signal_connect()相似，只是回调函数的处理不一样
  * g_signal_connect_swapped可以指定回调函数带的参数，下面指定会调函数为销毁，参数为整个窗口，而         * g_signal_connect()只能销毁自己按钮本身
  */
  g_signal_connect_swapped (button, "clicked", G_CALLBACK (gtk_widget_destroy), window);
  gtk_container_add (GTK_CONTAINER (button_box), button);

  gtk_widget_show_all (window);
}



int
main (int    argc,
      char **argv)
{
  GtkApplication *app;
  int status;

  app = gtk_application_new ("org.gtk.example", G_APPLICATION_FLAGS_NONE);
  g_signal_connect (app, "activate", G_CALLBACK (activate), NULL);
  status = g_application_run (G_APPLICATION (app), argc, argv);
  g_object_unref (app);

  return status;
}
```

