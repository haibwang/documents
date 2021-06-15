## Git Tutorial

https://zhuanlan.zhihu.com/p/21193604

### git

​    世界上先进的分布式版本控制系统

![img](https://www.runoob.com/wp-content/uploads/2015/02/git-command.jpg)

- workspace:工作区
- staging area：暂存取/缓存区
- local repository：本地仓库
- remote repository：远程仓库

### SVN 与 Git

​     svn式集中式版本控制系统，版本库集中放置在中央服务器，干活时用的是自己的电脑。所以要先从服务器获取最新的版本，本地修改后推送到服务器。集中式版本控制系统必须联网，局域完速度快，互联网慢。  

​      Git是分布式版本控制系统，没有中央服务器，每个人的电脑就是一个完整的厂库，工作时不用联网了

### 安装

### 操作

#### 创建仓库命令

| 命令        | 说明                                   |
| :---------- | :------------------------------------- |
| `git init`  | 初始化仓库                             |
| `git clone` | 拷贝一份远程仓库，也就是下载一个项目。 |

#### 提交与修改

| 命令         | 说明                                     |
| :----------- | :--------------------------------------- |
| `git add`    | 添加文件到仓库                           |
| `git status` | 查看仓库当前的状态，显示有变更的文件。   |
| `git diff`   | 比较文件的不同，即暂存区和工作区的差异。 |
| `git commit` | 提交暂存区到本地仓库。                   |
| `git reset`  | 回退版本。                               |
| `git rm`     | 删除工作区文件。                         |
| `git mv`     | 移动或重命名工作区文件。                 |

#### 提交日志

| 命令               | 说明                                 |
| :----------------- | :----------------------------------- |
| `git log`          | 查看历史提交记录                     |
| `git blame <file>` | 以列表形式查看指定文件的历史修改记录 |

#### 远程操作

| 命令         | 说明               |
| :----------- | :----------------- |
| `git remote` | 远程仓库操作       |
| `git fetch`  | 从远程获取代码库   |
| `git pull`   | 下载远程代码并合并 |
| `git push`   | 上传远程代码并     |

### 分支管理

分支管理可以使你从开发主线上分离出来，然后在不影响主线的的同时继续工作

- 创建分支命令

  ```
  git branch (branchname)
  ```

- 切换分支

  当你切换分支的时候，Git 会用该分支的最后提交的快照替换你的工作目录的内容， 所以多个分支不需要多个目录

  ```
  git checkout (branchname)
  ```

  也可以使用 `git checkout -b (branchname) `命令来创建新分支并立即切换到该分支下，从而在该分支中操作

- 删除分支

  ```
  git branch -d (branchname)
  ```

- 合并分支

  ```
  git merge 
  ```

- 合并冲突

  手动编辑冲突后, 在 Git 中，我们可以用 git add 要告诉 Git 文件冲突已经解决



### 提交历史记录

- git blame

  如果要查看指定文件的修改记录可以使用 git blame 命令，格式如下：

  ```
  git blame <file>
  ```

  

### git标签



























- git init 创建版本管理
- git add 文件   :添加文件到暂存取
- git commit 提交文件到本地仓库
  - git commit --amend	
    -  修改最近一次提交。有时候如果提交注释书写有误或者漏提文件，可以使用此命令。
- git push origin master 推送到远程服务器
- git status 查看提交状态
- git log 查看提交日志
- git rest -hard HEAD^  退回到上一个版本
  - git rest -hard HEAD^^  退回到上两个版本
  - git rest -hard HEAD~100  退回到上100个版本
- 代码差异
  - git diff <file>
    - 比较某文件与最近提交节点的差异。
    - 注意：如果该文件已暂存，那么应该使用git diff –cached<file> 
  -  git diff <hashcode> <hashcode> <file>
    - 比较某文件在提交节点a，节点b的差异。
    - 技巧：如果省略后面一个hashcode，则默认表示与上一提交节点比较。（也可以利用^运算符）
  - git diff HEAD 显示工作目录与git仓库之间的差异，
  - git diff 显示工作目录与索引文件之间的差异
  - git diff –cached显示索引文件与git仓库之间的差异

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

- 从远程库克隆分支时，自动的把本地master和远程master关联起来了。远程库默认的名字就是origin
- 使用git remote查看远程库的信息，加个参数-v查看详细信息

  推送分支

- git push origin master 推送到主分支
- git push origin dev 推送到其他分支dev

抓取分支

- git pull
