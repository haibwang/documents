#### Git Tutorial
https://zhuanlan.zhihu.com/p/21193604

##### git

​    世界上先进的分布式版本控制系统

#####  SVN 与 Git

​     svn式集中式版本控制系统，版本库集中放置在中央服务器，干活时用的是自己的电脑。所以要先从服务器获取最新的版本，本地修改后推送到服务器。集中式版本控制系统必须联网，局域完速度快，互联网慢。  

​      Git是分布式版本控制系统，没有中央服务器，每个人的电脑就是一个完整的厂库，工作时不用联网了

##### 安装

##### 操作

- git init 创建版本管理
- git add 文件   :添加文件
- git commit 提交文件
- git push origin master 推送到远程服务器
- git status 查看提交状态
- git log 查看提交日志
- git rest -hard HEAD^  退回到上一个版本
  - git rest -hard HEAD^^  退回到上两个版本
  - git rest -hard HEAD~100  退回到上100个版本



##### 工作区与暂存区

- 本地电脑上的目录
- 版本库
  - 本地文件有一个`.git`，不属于工作区，版本库里面存了暂存区stage，git创建的第一个分子master，以及指向master的一个指针HEAD
- git add 只是把文件添加到暂存区
- git commit 只是把暂存区的文件提交到当前分支

##### 撤销和删除文件

- git checkout --file  撤销本地修改
- 直接删除，之后提交彻底从版本库删除

##### 远程仓库

- git push origin master

##### 克隆

- git clone url



##### 创建与合并分支





##### 多人协作

- 从远程库克隆分支时，自动的把本地maste和远程master关联起来了。远程库默认的名字就是origin
- 使用git remite查看远程库的信息，加个参数-v查看详细信息

  推送分支

- git push origin master 推送到主分支
- git push origin dev 推送到其他分支dev

抓取分支

- git pull
