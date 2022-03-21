# [git基本操作及冲突解决](https://github.com/Twlig/issuesBlog/issues/67)

Git是分布式版本控制系统。能自动记录每次文件的改动，还可以协作编辑。

## 集中式vs分布式

CVS及SVN都是集中式的版本控制系统，而Git是分布式版本控制系统，集中式和分布式版本控制系统有什么区别呢？

集中式版本控制系统，版本库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。

分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，工作的时候，就不需要联网了，因为版本库就在自己的电脑上。

和集中式相比，分布式更加安全。



## 基本概念

 Git 工作区、暂存区、版本库和远程仓库概念：

- **工作区**：就是在电脑里能看到的目录。
- **暂存区**：英文叫 stage 或 index。一般存放在 **.git** 目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
- **版本库**：工作区有一个隐藏目录 **.git**，这个不算工作区，而是 Git 的版本库。
- **远程仓库**：GitHub提供Git仓库托管服务，可以免费获得Git远程仓库。

前三者都是在本地，远程仓库是在其他服务器上。

## 基本操作

![image](https://user-images.githubusercontent.com/22440467/159282835-11401b1f-3429-4cf9-9205-7e74b29f646f.png)

- 生成公私钥对

  ```
  ssh-keygen -t rsa -C "youremail@example.com"
  ```

- 初始化本地git仓库

  ```
  git init
  ```

  在执行完成 **git init** 命令后，Git 仓库会生成一个 .git 目录，该目录包含了资源的所有元数据。

- 添加文件到暂存区

  ```
  git add .      //添加全部文件
  git add readme.txt   //添加特定文件
  ```

  文件加入暂存区，纳入版本控制，让 Git 开始对这些文件进行跟踪

- 提交本地仓库

  ```
  git commit -m "添加readme文件"
  ```

- 新建分支

  ```
  git branch test
  ```

  新建test分支

- 查看分支

  ```
  git branch
  * master
    test
  ```

  有master和test两个分支，当前在master分支

- 切换分支

  ```
  git checkout test
  ```

  切换到test分支

- 新建并切换分支

  ```
  git checkout -b test
  ```

  新建test分支并切换到test分支

- 删除分支

  ```
  git branch -d test
  ```

  删除test分支

- 添加远程仓库

  ```
  git remote add origin git@github.com:Twlig/issuesBlog.git
  ```

  为本地仓库和远程仓库建立联系，**origin** 为远程地址的别名。

- 删除远程仓库

  ```
  git remote rm origin
  ```

  注意删除时只需要写远程仓库的别名

- 查看远程仓库

  ```
  git remote -v
  ```

- 本地仓库推送到远程仓库

  ```
  git push origin master
  ```

  提交当前分支代码到远程仓库的master分支，master为远程仓库origin的master分支。合并分支，有可能产生冲突。

- 从远程仓库拉取到本地仓库

  ```
  git pull origin master
  ```

  拉取远程仓库master分支的代码，并和本地仓库当前分支合并。有可能产生冲突。

- 从远程仓库获取代码库

  ```
  git fetch origin master
  ```

  不会发生合并，只是获取，不会产生冲突。疑问：那fetch的代码存在本地哪里？

- 合并分支

  ```
  git merge origin/master
  ```

  执行git fetch后，采用merge把远程origin的master分支**合并到本地的当前分支**

- 克隆仓库

  ```
  git clone git@github.com:Twlig/issuesBlog.git
  ```



## 冲突解决

冲突有两种一种是本地分支冲突，多人协作冲突。

- #### 本地分支冲突

就是自身新建的两个分支，都对同一个文件做出修改，但是修改的内容不一致。那在这两个分支合并的时候到底该采用哪一个分支的版本就存在争议，git无法判断，因此，我们需要手动解决。

```
git merge dev
```

假设当前分支为master，那么上述指令就是把dev分支合并到master分支。但是由于dev和master存在冲突，因此会合并失败。假设是readme.txt文件存在冲突。

redeme.txt文件如下：

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容

##### 解决冲突步骤：

1. 修改冲突文件后保存

   ```
   Git is a distributed version control system.
   Git is free software distributed under the GPL.
   Git has a mutable index called stage.
   Git tracks changes of files.
   Creating a new branch is quick AND simple.
   ```

2. 再次提交

   ```
   git add readme.txt
   git commit -m "conflict fixed"
   ```

冲突解决完毕。

- #### 多人协作冲突

在多人协作过程很可能遇见，不同的人对相同的文件做出不同修改，此时就会发生冲突。

假设我们远程的dev分支用于开发新模块功能。我们写好了自己的部分，推送远程。

```
git push origin dev

 ! [rejected]        dev -> dev (non-fast-forward)
error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

推送失败，因为其他人的最新提交和我们试图推送的提交有冲突。

##### 解决冲突步骤：

1. 用`git pull`把最新的提交从`origin/dev`抓下来

   ```
   git pull origin dev
   
   Auto-merging env.txt
   CONFLICT (add/add): Merge conflict in env.txt
   Automatic merge failed; fix conflicts and then commit the result.
   ```

   pull成功，但是提示有合并冲突问题

2. 修改冲突文件后保存，并再次提交

   ```
   git add env.txt
   git commit -m "conflict fixed"
   ```

3. 再次push

   ```
   git push origin dev
   ```





