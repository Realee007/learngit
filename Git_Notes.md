[TOC]



# git是什么

## git的诞生

​	linus（linux的开创者）不满于其他企业提供Linux系统的代码管理，于是花了2周的时间使用C写了一个分布式版本控制系统，即Git.

## 集中式vs分布式

​	常见的CVS和SVM都是集中式的版本控制系统，而Git是分布式版本控制系统。

1. 集中式版本控制系统

   版本库是集中存放在中央服务器的，而干活的时候，用的都是自己电脑，所以要先从中央服务器取得最新版本，然后开始干活，干活完了，需要把自己的做的推送给中央服务器。

   缺点：

   ​	必须要联网才能工作，并且如果带宽不够，速度较差时，可能提交小的文件就需要耗费很多时间。

2. 分布式版本控制系统

   分布式版本控制系统就没有“中央服务器”，每个人的电脑上都是一个完整的版本库，当你工作的时候也不必联网，当多人协作时，你在自己的电脑上改了文件A，你的同事也在他的电脑上改了文件A，这时，双方只需把各自的修改推送给对方，就可以互相看到对方的修改了。

   优点：

   ​	与集中式比较，分布式版本控制系统的安全性要高很多，因为每个人电脑都有完整的版本库，某一个人的电脑坏了，随便从其他人的电脑复制就好。而集中式版本控制系统的中央服务器出现问题，所有人都没法工作。

   ​	强大的分支管理功能。

# linux下的git

## 创建仓库

```
$ sudo apt install git
lee@ubuntu:~/learngit$ git init
Initialized empty Git repository in /home/lee/learngit/.git/
```

创建好仓库后，会发现当前目录多了一个`.git`目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然会破坏Git仓库。

如果没有看到`.git`目录，那是因为该目录是隐藏的，用`ls -ah`命令可以看见。

所有的版本控制系统，其实只能跟踪文本文件的改动，比如TXT文件，网页，所有的程序代码等等，而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化。

Microsoft的Word格式是二进制格式，因此，版本控制系统是没法跟踪Word文件的改动的。因为文本是有编码的，比如中文有常用的GBK编码，日文有Shift_JIS编码，如果没有历史遗留问题，**强烈建议使用标准的UTF-8编码**，所有语言使用同一种编码，既没有冲突，又被所有平台所支持。

## git初始化设置

由于git是分布式版本控制系统，所以，每个设备都有自己的ID，在git中由name和email组成。

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

注意`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

## 添加文件

```
$ git add <file>
$ git commit -m <message>
```

## 查看状态

```
$ git status
```

该命令可以让我们时刻掌握仓库当前的状态，比如被修改过，但还没有准备提交的修改。

```
$ git diff
```

该命令可以查看具体修改的内容。`diff`顾名思义就是查看difference，显示的格式正是Unix通用的diff格式。

待修改的没问题，我们需要将file提交修改至仓库。

## 提交修改

提交修改和添加文件一样，需要2步。

## 版本回退

在git中，当我们觉得文件改修至某一程度时，就可以“保存一个快照”，这个快照在Git中称为`commit`。一旦你把文件该乱了，或者误删了文件，就可以从最近的一个`commit`恢复。

查看某文件的`commit`历史记录，使用

````
$ git log
````

该命令显示从最近到最远提交的日志。其中我们会看到一大串类似`1094adb...`的是`commit id`（版本号）,和SVN不一样，Git的`commit id`不是1,2,3..... 递增的数字，而是一个SHA1计算出来的一个非常大的数字，以十六进制表示。

在Git中，用`HEAD`表示当前版本，也就是最新提交的版本，上一个版本为`HEAD^`，上上个版本为`HEAD^^`，当然往上100个版本写100个`^`数不过来，于是可以写成`HEAD~100`。

```
$ git reset --hard HEAD^
```

当然如果恢复上个版本后，再使用`git log`查看，会发现退回后之前的版本看不到了。

这里解决的办法是，只要你开启的命令行窗口还没被关掉，你就可以往上寻找，找到你所需要的`commit id`是`1094adb....`，然后使用

```
$ git reset --hard 1094ad
```

版本号不必写全，前几位就可以，Git会自动去找。

### 退回的内部发生了什么

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是把HEAD从指向`commit id`A改为指向`commit id`B。然后将工作区的文件同时更新。

当然如果你讲命令行窗口关闭后，没有记录下来`commit id`怎么回退呢？

### 通过查找commit命令来回退

```
$ git reflog
```

这样你可以查看你每次commit时的每一条命令，于是找到你所需要的`commit id`然后进行回退就好。

## 工作区和暂存区

1. 工作区(Working Directory)

就是你在电脑里面能看到的目录，比如我的`learngit`文件夹。

2. 版本库(Repository)

工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

Git的版本库存了很多东西，其中最重要的就是称为**stage**（或叫Index）的**暂存区**，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![git-01](F:\onedrive_Hust\OneDrive - hust.edu.cn\文档\DJI\beforeJob\image\git-01.jpg)

把文件往Git版本库里添加的时候，是分两步执行：

1. `git add`,把文件添加进去，实际上就是把文件修改添加到暂存区。

2. `git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

   因为创建Git版本库时，Git会自动为我们创建了唯一一个`master`分支，所以，现在`git commit`就是往`master`分支上提交更改。

当第一次修改 -> `git add` -> 第二次修改 -> `git commit`，这里第二次的修改不会被提交。

## 撤销修改

1. 当没有`git add`前，对文件修改，进行撤销。

   解决办法：`git checkout -- `丢弃工作区的修改

   ```
   $ git checkout -- <file>
   ```

2. 当进行`git add`后，进行撤销

   解决办法：使用`git reset HEAD`把暂存区的修改撤销(unstage),重新放回工作区。

   ```
   $ git reset HEAD <file>
   $ git checkout -- <file>
   ```

3. 当`git commit`后，进行撤销

   参考版本回退。

## 删除文件

1. 误删了文件

   由于版本库还存在，所以使用`git checkout`是用版本库的版本替换工作区的版本。无论工作区是修改还是删除，都可以还原。

   但是要小心，你只能恢复文件到最新版本，你会丢失**最近一次提交后你修改的内容**。

   ```
   $ git checkout -- <file>
   ```

2. 确实要从版本库中删除文件

   ```
   $ git rm <file>
   $ git commit -m "xxx"
   ```

# 远程库

## local=> github

```
$ git remote add origin git@github.com:Realee007/learngit.git
$ git push -u origin master
```

添加后，远程库的名字就是`origin`，这是Git的默认叫法，当然也可以修改成其他。

通过`git push` 命令，将当前分支`master`所有内容推送到远程。

由于远程库是空的，第一次推送`master`分支时，会加上`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会将本地的`master`分支和远程的`master`分支关联起来。后续本地修改进行远程提交只需要:

```
$ git push origin master
```

## github=>local

1. 准备好github上的仓库

2. 用命令`git clone`克隆一个本地库

   使用clone时会发现github会支持多种协议，默认的`ssh`,也可以使用`https等`其他协议。

   使用`https`除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令。

# 分支管理

​	首先，创建`dev`分支，然后切换至`dev`分支

```
$ git checkout -b dev
```

​	`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令

```
$ git branch dev
$ git checkout dev
```

然后使用`git branch`命令查看当前分支

```
$ git branch
* dev
 master
```

其中当前分支前面会标一个`*`号。于是现在我们就是在`dev`分支上进行操作。

从`dev`切换成`master`分支： `git checkout master`，切换到`master`分支后，在`dev`分支上进行的修改就不见了，如果要将`dev`分支的工作成果合并到`master`分支上：

```
$ git merge dev
Updating a05d9c1..6668d44
Fast-forward
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

`git merge` 用于合并指定分支到当前分支，合并后，可以发现`dev`分支的内容将`master`覆盖了。

合并有多种“模式”，比如`Fast-forward`，为快进模式，也就是直接把`master`指向`dev`。

合并完成后，就可以放心的删除`dev`分支。

```
$ git branch -d dev
Deleted branch dev (was 6668d44).
```

现在再查看分支，就只剩下`master`分支

```
$ git branch
* master
```



## 命令

查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

## 解决冲突

当我们在合并不同分支，并且合并存在冲突时，git会告诉我们冲突的存在，此时必须手动解决冲突后再提交。

`git status`可以告诉我们冲突的文件，Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容。

用带参数的`git log`也可以看到分支的合并情况（使用`q`进行退出）。

合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

准备合并`dev`分支，请注意`--no-ff`参数，表示禁用`Fast forward`：

```
$ git merge --no-ff -m "merge with no-ff" dev
```



## stash功能（bug分支）

常见场景：A为一游戏软件

1. master 上面发布的是A的1.0版本
2. dev 上开发的是A的2.0版本
3. 这时，用户反映 1.0版本存在漏洞，有人利用这个漏洞开外挂
4. 需要从dev切换到master去填这个漏洞，正常必须先提交dev目前的工作，才能切换。
5. 而dev的工作还未完成，不想提交，所以先把dev的工作stash一下。然后切换到master
6. 在master建立分支issue101并切换.
7. 在issue101上修复漏洞。
8. 修复后，在master上合并issue101
9. 切回dev，恢复原本工作，继续工作。

```
$ git stash
保存并隐藏当前分支工作现场。
$ git stash pop 
返回工作现场
```

## feature分支(新功能分支)

1.  在已有分支上上创建feature-xx分支

2. 然后add,commit

3. 切换到对应分支，进行合并

   1. 确定合并，进行`merge`

   2. 取消合并，进行旧分支的删除,对于未合并的分支，如果删除就会被丢失，此时只能进行`-D`删除

      ```
      $ git branch -D <name>
      ```

## 远程协作

通常我们的仓库至少会放2个分支：1.主分支 master,2. 开发分支dev

多人协作时，会往`master`和`dev`分支上推送各自的修改。

1. 首先，试图用`git push origin <branch-name>`推送自己的修改；

2. 如果推送失败，则表明远程分支比本地更新，需要先用`git pull`试图合并；

3. 如果合并有冲突，则解决冲突，并在本地提交；

4. 没有冲突或者解决掉冲突后，再用`git push origin<branch-name>`推送成功

   如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`

## 标签

由于commit id 的复杂性，我们可以使用tag来代替commit id。

### 添加标签

敲命令`git tag <name>`就可以打一个新标签，默认为`HEAD`，可以用命令`git tag`查看所有标签。

对之前的`commit id`补上标签，则是

```
$ git tag <name> <commit id>
```

标签不是按时间顺序列出，而是按字母排序的。使用`git show <tagname>`可以查看具体标签信息。

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

```
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
```



### 删除标签

1. 仅存在本地的标签。

```
$ git tag -d <tagname>
```

创建的标签都只存在本地，不会自动推送到远程，若要推送到远程则使用：

```
$ git push origin <tagname>
```

或者一次性推送本地尚未推送的标签：

```
$ git push origin --tag
```

2. 若标签存在远程
   1. 先删除本地;
   2. 再远程push删除.

```
$ git tag -d <tagname>
$ git push origin :refs/tags/<tagname>
```



