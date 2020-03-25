---
title: 廖雪峰git学习记录
date: 2019-07-07 16:05:51
categories: 工具
tags: git
---

这篇博客，主要是记录自己通过[廖雪峰老师网站](https://www.liaoxuefeng.com/wiki/896043488029600)学习Git的笔记。

## Git是什么

Git是目前世界上最先进的分布式版本控制系统（没有之一）

## 集中式和分布式

### 集中式(CVS SVN)

版本库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。

集中式版本控制系统最大的毛病就是必须联网才能工作。

### 分布式(Git)

分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库。

分布式版本控制系统通常也有一台充当“中央服务器”的电脑，但这个服务器的作用仅仅是用来方便“交换”大家的修改，没有它大家也一样干活，只是交换修改不方便而已。

## 安装Git

- linux

  ```shell
  sudo yum install git
  ```

- windows

  git官网[git下载](<https://git-scm.com/downloads>)

安装完成后配置名字和Email地址

```shell
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

## 创建版本库

版本库又名仓库，英文名**repository**，你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

1. 创建空目录
2. 使用git init将目录变为Git可以管理的仓库。初始化完成为生成`.git`目录

**将文件添加到版本库**

1. 用`git add`命令告诉Git，把文件添加到仓库。

   ```shell
   git add [filename]
   ```

2. 用`git commit`命令，将文件提交到仓库。

   ```shell
   git commit -m [message]
   ```

## 时光机穿梭

Git中通过每次commit保存不同的版本，通过每一个commit版本进行恢复。

### 查看历史记录

```shell
git log
```

现在有test01，test02，test03三个版本。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161104.png"/>

只显示一行:

```shell
git log --pretty=oneline
```

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161126.png"/>

- 那一串黄色的字符代表的是 `commit id`，是一个SHA1计算出来的一个非常大的数字，用十六进制表示。
- 每提交一个新版本，实际上Git就会把它们自动串成一条时间线。
- 在Git中用`HEAD`表示当前版本，也就是最新的提交`commit id`
  上一个版本`HEAD^` 上上个版本`HEAD^^`, 前100个版本`HEAD~100`

### 版本回退

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是把HEAD从指向`test03`改为指向`test02`：

```
┌────┐
│HEAD│
└────┘
   │
   └──> ○ test03
        │
        ○ test02
        │
        ○ test01
```

```ascii
┌────┐
│HEAD│
└────┘
   │
   │    ○ test03
   │    │
   └──> ○ test02
        │
        ○ test01
```

```shell
git reset --hard <commit id>
```

从当前版本test03回退到test02

```shell
$ git reset --hard HEAD^
HEAD is now at 6eeb0dc test02
```

查看当前readme内容。可以看到确实回退到了test02

```shell
$ cat readme.txt
test01
test02
```

通过`commit id`及SHA1那一串数字穿梭任意版本。重新回到test03.
commit id不用写完整，能唯一标识就行，Git会自动查找。

```shell
$ git reset --hard 9a9f
HEAD is now at 9a9ff40 test03
```

### 查看命令历史

```shell
git reflog
```

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161151.png"/>

可以查看命令历史以及对应的commit id，然后通过commit id进行版本回退。

## 工作区和暂存区

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161209.png"/>

### 工作区

就是你在电脑里能看到的目录。

### 版本库（Repository）

工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

我们把文件往Git版本库里添加，分两步。

1. `git add`把文件修改添加到暂存区。
2. `git commit`提交更改，把暂存区所有内容提交到当前分支。

需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

修改readme.txt中的内容。创建一个新文件LICENSE使用git status。	`readme.txt`被修改，`LICENSE`从来没有被添加过，状态时`Untracked`。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161228.png"/>

使用`git add`将两个文件都添加到暂存区。再使用`git status`查看

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161246.png"/>

现在暂存区的状态

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161300.png"/>

`git add`命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行`git commit`就可以一次性把暂存区的所有修改提交到分支。

```shell
$ git commit -m "修改readme 添加LICENSE"
[master fc4287e] 修改readme 添加LICENSE
 2 files changed, 2 insertions(+), 1 deletion(-)
 create mode 100644 LICENSE
```

使用`git staus查看`没有对工作区做任何修改，那么工作区就是“干净”的：

```shell
$ git status
On branch master
nothing to commit, working tree clean
```

最后暂存区的状态

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161317.png"/>

### 管理修改

Git跟踪并管理的是修改，而非文件。

第一次修改 -> `git add` -> 第二次修改 -> `git commit`

Git管理的是修改，当你用`git add`命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，`git commit`只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161330.png"/>

要想提交第二次修改，继续`git add`然后`git commit`。可以不急着`add`第一次修改，等两次修改后在一起`git commit`: 第一次修改->`git add` -> 第二次修改 -> `git add` -> `git commit`。

每次修改，如果不用`git add`到暂存区，那就不会加入到`commit`中。

#### 查看修改

可以通过使用git diff。

- git diff 比较的是工作区文件与暂存区文件的区别（上次git add 的内容）
- git diff --cached 比较的是暂存区的文件与仓库分支里（上次git commit 后的内容）的区别 

### 撤销修改

#### checkout

当修改了文件，但是还没有`git add`到暂存区，使用`git status`查看，会提示使用`git checkout -- <file>`进行撤销修改。

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

在Git中可以使用`git checkout -- file`可以丢弃工作区的修改：

```shell
git checkout -- readme.txt
```

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

1. `readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
2. `readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

#### reset

当修改已经`git add`到暂存区。但还没有提交时，会提示使用`git reset HEAD <file>...` 进行撤销。

```shell
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   readme.txt
```

`git reset`命令既可以回退版本，也可以把*暂存区的修改回退到工作区。*当我们用`HEAD`时，表示最新的版本。

```shell
$ git reset HEAD readme.txt
Unstaged changes after reset:
M       readme.txt
```

然后再使用`checkout`进行撤销文件修改。

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，使用`git reset --hard <commit id>`不过前提是没有推送到远程库。

### 删除文件

删除也是一个修改操作，当使用`rm <file>...`删除一个文件，使用`git staus`查看。

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

Git会提示做了修改，现在又两种情况进行选择。

1. 确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`。

   ```shell
   $ git rm test.txt
   rm 'test.txt'
   
   $ git commit -m "删除test.txt"
   [master c61c0eb] 删除test.txt
    1 file changed, 1 deletion(-)
    delete mode 100644 test.txt
   ```

2. 删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本。

   ```shell
   $ git checkout -- test.txt
   ```

## 远程仓库

- 创建SSH Key，输入下面命令，一路回车。

  ```shell
  $ ssh-keygen -t rsa -C "youremail@example.com"
  ```

在~/.ssh下会生成`id_rsa`(私钥)和`id_rsa.pub`(公钥)这两个文件。

- 在GitHub上添加SSH Key。将公钥中的内容添加。

### 添加远程仓库

先有本地库，后有远程库的时候，如何关联远程库。

在Github上创建一个仓库，名字叫test。

- 在本地仓库添加远程仓库

  ```shell
  $ git remote add origin git@github.com:Qin-K/test.git
  ```

- 将本地仓库的内容推送到远程仓库

  ```shell
  $ git push -u origin master
  ```

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

推送成功后，可以立刻在GitHub页面中看到远程库的内容已经和本地一模一样。在以后的推送中只需要使用:

```shell
$ git push origin master
```

### 从远程仓库克隆

从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。

在远程仓库新建仓库，勾选`Initialize this repository with a README`，这样GitHub会自动为我们创建一个`README.md`文件。

用`git clone`命令从远程仓库克隆。git clone '仓库地址'。

```shell
$ git clone git@github.com:Qin-K/test.git
```

Git支持多种协议，默认的`git://`使用ssh，但也可以使用`https`等其他协议。使用`https`除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令

- ssh  `git@github.com:Qin-K/test.git`
- https: `https://github.com/Qin-K/test.git`

## 分支管理

### 创建与合并分支

每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即`master`分支。

`HEAD`严格来说不是指向提交，而是指向`master`，`master`才是指向提交的，所以，`HEAD`指向的就是当前分支。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161356.png"/>

每次提交，`master`分支都会向前移动一步，这样，随着你不断提交，`master`分支的线也越来越长。

当我们创建新的分支，例如`dev`时，Git新建了一个指针叫`dev`，指向`master`相同的提交，再把`HEAD`指向`dev`，就表示当前分支在`dev`上：

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161413.png"/>

创建一个`dev`分支，然后切换到`dev`分支

```shell
$ git checkout -b dev
Switched to a new branch 'dev'
```

`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

```shell
$ git branch dev # 创建分支
$ git checkout dev # 切换分支
Switched to branch 'dev'
```

用`git branch`命令查看当前分支：

```shell
$ git branch
* dev
  master
```

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

在当前`dev` 分支对readme.txt进行修改并且提交，然后切换到master分支。重新查看readme.txt中的内容，文件没有改变。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161425.png"/>

把`dev`分支的工作成果合并到`master`分支上：

```shell
$ git merge dev
```

`git merge`用于合并指定分支到当前分支。合并后，再查看`readme.txt`的内容，就可以看到，和`dev`分支的最新提交是完全一样的。

删除分支`dev`分支。

```shell
$ git branch -d dev
```

因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。

### 解决冲突

新建一个feature1分支，修改readme.txt最后一行，并添加提交。

切换到master分支，也修改readme.txt最后一行，添加并提交。
在master分支上试图合并会有冲突。

现在`master`和`feature1`各分别有新的提交，变成这样:

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161441.png"/>

这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突

```shell
$ git merge feature1
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```

而且有方显示分支名为 ：(master|MERGING)

Git告诉我们，`readme.txt`文件存在冲突，必须手动解决冲突后再提交。`git status`也可以告诉我们冲突的文件：

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161457.png"/>

查看readme.txt文件中的内容:

```txt
test01
test02
test0333344
<<<<<<< HEAD
创建一个新的分支 test
=======
创建一个新的分支test
>>>>>>> feature1
```

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容。然后根据具体情况修改readme.txt中的内容，然后重新添加提交解决冲突。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161511.png"/>

现在`master`分支和`feature1`分支变成了这样:

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161530.png"/>

查看分支合并情况:

```shell
$ git log --graph --pretty=oneline --abbrev-commit
```

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161544.png"/>

### 分支管理策略

#### 不使用Fast forward

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

创建一个新分支`dev`在dev中修改readme.txt并且添加和提交，然后切换到master分支进行强制禁用`Fast forward`模式合并。

```shell
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 readme.txt | 1 -
 1 file changed, 1 deletion(-)
```

使用`git  log`查看分支历史。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161559.png"/>

不是用`Fast forward`模式，merge之后:

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161611.png"/>

#### 分支策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；

你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161621.png"/>



### Bug分支

软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支`issue-101`来修复它，但是，等等，当前正在`dev`上进行的工作还没有提交。工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该bug，怎么办？

Git还提供了一个`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

```shell
$ git stash
```

用`git status`查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支：

```shell
$ git checkout master

$ git checkout -b issue-101
```

修复完成后，切换到`master`分支，并完成合并，最后删除`issue-101`分支：

```shell
$ git checkout master
$ git merge --no-ff -m "merged bug fix 101" issue-101
```

Bug修复后切换到 `dev`分支干活。

```shell
$ git checkout dev
Switched to branch 'dev'

$ git status
On branch dev
nothing to commit, working tree clean
```

工作区是干净的，刚才的工作现场存到哪去了？用`git stash list`命令看看：

```shell
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
```

工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；

另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

```shell
$ git stash pop
```

再用`git stash list`查看，就看不到任何stash内容了。

你可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

```shell
$ git stash apply stash@{0}
```

### Feature分支

软件开发中，总有无穷无尽的新的功能要不断添加进来。

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

### 多人协作

当你从远程仓库克隆时，实际上Git自动把本地的`master`分支和远程的`master`分支对应起来了，并且，远程仓库的默认名称是`origin`。

要查看远程库的信息，用`git remote`，用`git remote -v`显示更详细的信息。显示了可以抓取和推送的`origin`的地址。如果没有推送权限，就看不到push的地址。

```shell
$ git remote -v
origin  git@github.com:Qin-K/learn-git.git (fetch)
origin  git@github.com:Qin-K/learn-git.git (push)
```

#### 推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。

```shell
$ git push origin master
```

推送到`dev`分支

```shell
$ git push origin dev
```

但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

- `master`分支是主分支，因此要时刻与远程同步；
- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
- feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

#### 抓取分支

多人协作时，大家都会往`master`和`dev`分支上推送各自的修改。

当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的`master`分支。

现在，你的小伙伴要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，于是他用这个命令创建本地`dev`分支：

```shell
$ git checkout -b dev origin/dev
```

他就可以在`dev`上继续修改，然后，时不时地把`dev`分支`push`到远程。

你的小伙伴已经向`origin/dev`分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送。推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送。

`git pull`也失败了，原因是没有指定本地`dev`分支与远程`origin/dev`分支的链接，根据提示，设置`dev`和`origin/dev`的链接：

```shell
$ git branch --set-upstream-to=origin/dev dev
```

因此，多人协作的工作模式通常是这样：

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

- 查看远程库信息，使用`git remote -v`；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
- 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

### Rebase

多人在同一个分支上协作时，很容易出现冲突。即使没有冲突，后push的童鞋不得不先pull，在本地合并，然后才能push成功。每次合并再push后，分支变成了这样：

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161638.png"/>

在本地创建修改rebase.txt，然后push时远程已经修改，必须使用`git pull`拉取远程的修改。最后使用`git log `,显然提交历史分叉了。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161648.png"/>  

使用`git rebase`

```shell
$ git rebase
First, rewinding head to replay your work on top of it...
Applying: 测试rebase
Applying: 第一次修改rebase
Applying: 第二次修改rebase
```

再使用`git log`查看原本分叉的提交现在变成一条直线了。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161701.png"/>

通过push操作把本地分支推送到远程后再查看`git log`。远程提交的git 历史也变成了一条直线。

<img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161716.png"/>

这就是rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。

- rebase操作可以把本地未push的分叉提交历史整理成直线；
- rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

## 标签管理

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。

### 创建标签

首先，切换到需要打标签的分支上：

```shell
$ git checkout master
```

- 使用`git tag`命令创建一个标签。

```shell
$ git tag v1.0
```

默认标签是打在最新提交的commit上的。对以前提交的commit打tag：方法是找到历史提交的commit id。

```shell
$ git log --pretty=oneline --abbrev-commit
45835f8 (HEAD -> master, tag: v1.0, origin/master) 第二次修改rebase
8714bad 第一次修改rebase
73c5b50 测试rebase
...
```

```shell
$ git tag test-rebase 73c5b50
```

- 查看所有标签

```shell
$ git tag
test-rebase
v1.0
```

标签不是按时间顺序列出，而是按字母排序的。

- 查看标签信息 git show \<tagname>

  <img src="https://raw.githubusercontent.com/Qin-K/picGo/master/img/20190904161740.png"/>

- 创建带有说明的标签git tag -a -m

  ```shell
  $ git tag -a v0.1 -m "修改readme" 35463c
  ```

  使用`git show <tagname>`可以看到备注信息。 

  ```shell
  $ git show v0.1
  tag v0.1
  Tagger: Qin-K <1547127079@qq.com>
  Date:   Sun Jul 7 19:22:45 2019 +0800
  
  修改readme
  
  ...
  ```

- 命令`git tag <tagname>`用于新建一个标签，默认为`HEAD`，也可以指定一个commit id；

- 命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息；

- 命令`git tag`可以查看所有标签。

### 操作标签

- 命令`git push origin <tagname>`可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d <tagname>`可以删除一个本地标签；
- 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

## 自定义git

### 忽略特殊文件

在Git工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

不需要从头写`.gitignore`文件，GitHub已经为我们准备了各种配置文件，组合一下就可以使用了。所有配置文件可以直接在线浏览：<https://github.com/github/gitignore>

忽略文件的原则是：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

```
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa

```

被`.gitignore`忽略的文件，可以用`-f`强制添加到Git：

```shell
$ git add -f App.class
```

找出来到底哪个规则写错了，可以用`git check-ignore`命令检查：

```shell\
$ git check-ignore -v App.class
.gitignore:3:*.class	App.class
```

