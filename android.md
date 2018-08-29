### Android应用基础知识

#### 应用组件

四种应用组件的类型：

- **Activity **  *Activity* 表示具有用户界面的单一屏幕

- **服务 ** *服务*是一种在后台运行的组件，用于执行长时间运行的操作或为远程进程执行作业。
  服务不提供用户界面

- **内容提供程序** *内容提供程序*管理一组共享的应用数据。您可以将数据存储在文件系统、SQLite 数据库、网络上或您的应用可以访问的任何其他永久性存储位置。

  其他应用可以通过内容提供程序查询数据，甚至修改数据（如果内容提供程序允许）。

- **广播接收器** *广播接收器*是一种用于响应系统范围广播通知的组件。

#### 启动组件

​    Activity、服务、广播通过Intent（从其他组件请求操作的信使）异步消息启动，







#### UI 约束 

APP主题设置为Base.Theme...才能看到设计界面

- constrainLayout （约束布局）是为了解决复杂界面布局嵌套过多的问题。
- 只是有一个水平和垂直约束，约束对象可以是另一个视图、父布局或者是不可见的引导线





#### Activity

Activity概要

##### 配置manifest文件

- 声明activities

  添加一个activity标签

  ```xml
  <manifest ... >
    <application ... >
        <activity android:name=".ExampleActivity" />
        ...
    </application ... >
    ...
  </manifest >
  ```

- 声明intent过滤器

  intent是安卓平台非常强大的特性，能够**显示**和**隐式** 的而启动activity，显示启动直接指定activity类名，隐式启动通过设定`category`、`action`、`data`就能够指定一个activity，当有多个时，系统让你选择启动哪一个。

  ```xml
  <activity android:name=".ExampleActivity" android:icon="@drawable/app_icon">
      <intent-filter>
          <action android:name="android.intent.action.SEND" />
          <category android:name="android.intent.category.DEFAULT" />
          <data android:mimeType="text/plain" />
      </intent-filter>
  </activity>
  ```
  调用方法：
  ```java
  // Create the text message with a string
  Intent sendIntent = new Intent();
  sendIntent.setAction(Intent.ACTION_SEND);
  sendIntent.setType("text/plain");
  sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
  // Start the activity
  startActivity(sendIntent);
  ```

- 声明权限

  父activity不能够启动子activity，除非在manifest里有一样的权限。如果一个activity定义了权限，者调用该activity的activity就要声明一样的权限。

  被调用者定义了该权限：

  ```xml
  <manifest>
  <activity android:name="...."
     android:permission=”com.google.socialapp.permission.SHARE_POST”

  />
  ```
  调用者声明该权限：

  ```xml
  <manifest>
      <uses-permission android:name="com.google.socialapp.permission.SHARE_POST" />
  </manifest>
  ```

##### 管理Activity的生命周期

| 回调方法        | 解释                                       |
| :---------- | ---------------------------------------- |
| onCreat()   | 必须实现该回调方法，在系统创建activity时发生，用于初始化基本的组件    |
| onStart()   | 退出onCreat后进入，activity变得可视，为变得可交互做出最后的准备  |
| onResume()  | activity到栈顶，可交互。实现一些activity的核心。结束后始终是调用onPause() |
| onPause()   | activity失去焦点调用。此时用可能继续更新UI。不应该在此状态保存数据，网络调用，操作数据库。下一个状态进入onStop()或onResume() |
| onStop()    | 不在可视，下一个状态onRestart()或onDestory()        |
| onRestart() | 修复stop后的状态                               |
| onDestory() | 资源释放                                     |

![Activity的生命周期](https://developer.android.google.cn/guide/components/images/activity_lifecycle.png)

