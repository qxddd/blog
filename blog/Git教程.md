# Git的安装 #
------------
## 在Linux上安装Git ##
debain系的系统可以用 `sudo apt install git` 命令来安装Git，Redhat系的可以用 `yum install -y git`来安装。

## 在Windows上安装Git ##
在Windows上使用Git，可以在Git的[官方网站](https://git-scm.com/downloads)上直接下载最新的安装程序，然后按照提示进行安装。

# Git的使用 #
------------
## 创建版本库 ##
版本库也叫仓库，英文叫**repository**，类似一个文件夹，Git可以管理这个文件夹里的文件，记录与跟踪每个文件的修改、删除等操作，可以在任何时候查看历史，或者回退到某个时刻。  

首先，选一个合适的位置，创建一个目录：  
```shell
$ mkdir blog

$ cd blog/

$ pwd
/c/Users/QXD/blog
```
第二步，使用`git init`命令将blog目录变成git可以管理的仓库
```shell
$ git init
Initialized empty Git repository in C:/Users/QXD/blog/.git/
```
Git会在这个目录下创建一个`.git`的隐藏文件夹，是Git用来管理版本库的，一般不要手动改这个文件夹里的内容，要想查看这个文件夹，可以使用`ls -a`命令。
### 添加文件到版本库 ###
首先创建一个文件，例如`test.txt`，随意输入一些内容：
```
This is a test file.
```
然后用命令`git add`将文件添加进仓库
```shell
$ git add test,txt
```
没有消息输出说明添加成功，然后使用`git commit`命令将文件提交到仓库
```shell
$ git commit -m "test file"
[master (root-commit) 13349a0] test file
 1 file changed, 1 insertion(+)
 create mode 100644 test,txt
```
上面的`-m`选项的意思是对本次提交的说明，建议加上，这样以后查看的时候知道提交了什么内容；下面的提示信息是告诉你有一个文件更改了（我们新建的文件），插入了一行内容（文件里只有一行内容）。你可以用`git add`一次添加多个文件或者分多次添加文件，然后一起使用`git commit`添加到仓库中。
### 查看要提交的文件与版本库的区别 ###

使用`git status`命令可以查看当前仓库的状态
```shell
$ git status
On branch master
nothing to commit, working tree clean
```
从上面的输出中可以看到我们现在没有需要提交的修改，我们改一下测试文件，添加一行：
```shell
add a new line.
```
现在运行`git status`
```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   test,txt

no changes added to commit (use "git add" and/or "git commit -a")
```
可以看到`test.txt`文件被修改了，但是还没有被提交，此时可以用`git diff tes.txt`此时的文件与仓库中的文件的区别
```shell
$ git diff test,txt
diff --git a/test,txt b/test,txt
index 6de7b8c..795ab58 100644
--- a/test,txt
+++ b/test,txt
@@ -1 +1,2 @@
 This is a test file.
+add a new line.
```
可以看到我们新添加的行前面有一个加号`+`，按照前面添加文件的方法继续将文件添加进仓库，然后查看状态
```shell
$ git status
On branch master
nothing to commit, working tree clean
```

## 版本回退 ##
到目前为止`test.txt`一共提交了两次，在仓库里有两个版本，可以用`git log`查看
```shell
QXD@DESKTOP-O2DCQJ1 MINGW64 ~/blog (master)
$ git log
commit b038cb4fc55908b9821a02cb25a9dd348e6cab14 (HEAD -> master)
Author: qxd <1591775626@qq.com>
Date:   Fri Mar 8 20:49:36 2019 +0800

    add a new line

commit 13349a0009058a01a1545aef996db88d3f8702f3
Author: qxd <1591775626@qq.com>
Date:   Fri Mar 8 20:14:23 2019 +0800

    test file
```
该命令显示我们从最开始到最近的提交，使用`--pretty=oneline`可以在一行里显示
```shell
$ git log --pretty=oneline
b038cb4fc55908b9821a02cb25a9dd348e6cab14 (HEAD -> master) add a new line
13349a0009058a01a1545aef996db88d3f8702f3 test file
```
当我们现在要回退版本时，必须要知道当前是那个版本，在Git中，用`HEAD`表示当前版本（我们这个例子中是`b038cb...`)，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^`，我们现在回退到上一版本，使用`git reset`命令
```shell
$ git reset --hard HEAD^
HEAD is now at 13349a0 test file
```
现在我们看一下测试文件的内容
```shell
$ cat test,txt
This is a test file.
```
文件被还原了。现在看看版本库的状态
```shell
$ git log
commit 13349a0009058a01a1545aef996db88d3f8702f3 (HEAD -> master)
Author: qxd <1591775626@qq.com>
Date:   Fri Mar 8 20:14:23 2019 +0800

    test file
```
最新的那个版本已经不见了，但是如果我们弄错了要想重新回到最新的那个版本呢？如果命令行窗口还没关闭，还是有办法的，网上翻一下历史命令，找到最新的那个版本的`commit id`是`b038cb`，可以制定回到未来的某个版本
```shell
$ git reset --hard b038cb
HEAD is now at b038cb4 add a new line
```
我们已经回到了最新的版本了，查看一下文件内容
```shell
$ cat test,txt
This is a test file.
add a new line.
```
文件内容已经回到最新的版本了。如果已经关闭了命令行窗口其实问题也不大，Git记录了你每一次操作，使用`git reflog`命令可以查看
```shell
$ git reflog
b038cb4 (HEAD -> master) HEAD@{0}: reset: moving to b038cb
13349a0 HEAD@{1}: reset: moving to HEAD^
b038cb4 (HEAD -> master) HEAD@{2}: commit: add a new line
13349a0 HEAD@{3}: commit (initial): test file
```

## 工作区与暂存区 ##

* 工作区
当前所在的目录也就是最开始创建的文件夹就是一个工作区。

* 暂存区
工作区中有一个隐藏目录`.git`，这个就是Git的版本库，里面存的有很多东西，其中有一个称为stage（或者叫index）的暂存区，还有Git为我们创建的第一个分支`master`,还有一个指向`master`的指针`HEAD`。  

前面我们提交文件到版本库时，有两个步骤
第一步，执行`git add`将文件添加到暂存区，
第二部，执行`git commit`将暂存区的内容合并到版本库中。

## 撤销修改 ##
当发现对文件的修改有错误时，如果还没有提交，可以手动删除错误的修改，或者使用`git checkout -- file`将文件撤回到上一个版本的状态。命令`git checkout -- file`的意思就是把文件在工作区的修改全部撤销，这里有两种情况，一种是文件修改后没有放到暂存区，撤销修改就是回到版本库相同的状态；另一种是文件已经放到暂存区后，又做了修改，现在撤销就是回到添加到暂存区时的状态  
当已经将文件`add`到暂存区后，在`commit`之前，可以用`git reset HEAD file`命令把暂存区的修改撤销掉，现在的状态
```shell
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   test,txt
```
使用`git reset HEAD test.txt`撤销后
```shell
$ git reset HEAD test,txt
Unstaged changes after reset:
M       test,txt
```
查看一下状态
```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   test,txt

no changes added to commit (use "git add" and/or "git commit -a")
```
暂存区没有内容，工作去有修改没有提交，然后就可以执行上面的撤销工作区的修改了
## 删除文件 ##
添加一个新文件并提交到版本库，然后在本地删除该文件，查看版本库状态，Git会告诉你哪些文件被删除了
```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   test,txt
        deleted:    test2.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
如果确实要删除版本库里的文件，就用`git rm`删除文件，然后再`commit`;
```shell
$ git rm test2.txt
rm 'test2.txt'

QXD@DESKTOP-O2DCQJ1 MINGW64 ~/blog (master)
$ git commit -m "remove test2.txt"
[master d226982] remove test2.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test2.txt
```
如果是误删，还可以使用`git checkout -- test2.txt`恢复到版本库里的最新版本.
# 远程仓库 #
如果想把本地仓库推到远程仓库，比如著名的[GitHub](https://github.com/)上，需要在该网站上注册一个账号，由于本地仓库与远程仓库传输使用SSH加密的，所以需要设置一下：
第一步：创建SSH Key。在主目录下如果有`.ssh`目录的话，看看里面有没有`id_rsa`和`id_rsa.pub`两个文件，如果有，跳过；如果没有，打开命令行窗口创建，直接使用默认选项回车就行，不需要输入密码；
第二部：将id_rsa.pub上传到GitHub网站。登陆GitHub，打开“settings”，“SSH and GPG keys”页面：然后，点“New SSH key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容。
## 添加远程仓库 ##
登陆GitHub，然后点击右上角的**+**符号，再点击New repository，在Repository name填写blog，其他保持不变，点击Create repository创建仓库，然后根据GitHub提示，在本地的blog的仓库下运行：
```shell
$ git remote add origin git@github.com:qxdty/blog.git
```
将上面的地址换成自己的，在页面上有各自相对应的命令，然后把本地仓库推送到远程仓库
```shell
$ git push -u origin master
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 4 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (10/10), 841 bytes | 280.00 KiB/s, done.
Total 10 (delta 0), reused 0 (delta 0)
To github.com:qxdty/blog.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```
把本地仓库的推送到远程，使用`git push`命令，实际上就是把本地`master`分支推送到远程，因为远程仓库是空的，第一次推送时，加了`-u`参数，Git会把本地的`master`分支推送到远程`master`分支，并把本地的`master`分支与远程`master`分支关联起来，以后推送时可以不带`-u`选项。从现在起，只要本地作了提交，就可以通过命令：`$ git push origin master`，把本地`master`分支的最新修改推送至GitHub。第一次连接GitHub时，会有警告，输入yes即可。  

产看远程仓库的信息，用`git remote`
```shell
$ git remote
origin
```
想显示更加详细的信息，可以使用`-v`选项
```shell
$ git remote -v
origin  git@github.com:qxdty/blog.git (fetch)
origin  git@github.com:qxdty/blog.git (push)
```

## 从远程仓库克隆 ##
当我们需要将远程仓库的内容下载到本地时，可以使用`git clone url`将远程仓库克隆到本地。
# 分支管理 #
## 创建与合并分支 ##
从教程开始到现在，我们每一次提交，Git都会把他们串成一条时间线，这条时间线就是一个分支；目前我们只有一条时间线，就是`master`，前面说到的指向当前提交的指针`HEAD`，严格来讲`HEAD`并不是指向提交，而是指向`master`，`master`才是指向提交，所以`HEAD`指向的时当前分支。  
我们刚开始的时候，`master`分支只有一条时间线，Git用`master`指向最新的提交，再用`HEAD`指向`master`，就可以确定当前分支与所在的提交点。我们每次提交，`master`就向前移动一步，时间线也就越长。当我们创建了一个新分支时，比如`dev`，Git创建了一个新的指针`dev`，指向`master`相同的提交位置，再把`HEAD`指向`dev`，就表示我们当前在`dev`分支上了。  
从现在开始，我们在工作去所做的所有修改和提交都是针对`dev`分支的了，你每次提交，`dev`指针都会向前移动，而`master`不变。当你要把`dev`分支合并到`master`分支的时候，Git就直接把`master`指针指向`dev`当前的提交，合并就完成了，然后就可以把`dev`分支删除，也就是删除掉`dev`指针，这样我们就剩下一条`master`分支了。  
现在我们创建`dev`分支，并切换上去
```shell
$ git checkout -b dev
Switched to a new branch 'dev'
```
`-b`参数表示创建并切换，相当于下面两条命令
```shell
$ git branch dev
$ git checkout dev
Switched to branch 'dev
```
使用`git branch`可以查看所有的分支，当前分支前面会有一个*符号。
```shell
$ git branch
* dev
  master
```
现在可以在当前分支上继续修改提交，不会对`master`分支中的文件造成影响。当我们想把`dev`分支合并到`master`分支上时，
```shell
$ git merge dev
Updating 412229b..8aec2cb
Fast-forward
 test,txt | 1 +
 1 file changed, 1 insertion(+)
```
`git merge`可以合并指定分支到当前分支，合并后，文件当前分支的文件内容就和`dev`分支一样了。上面的`Fast-forward`意思时当前使用的时快速合并模式，就是前面说的直接改指针指向。现在合并完成后就可以删除`dev`分支了。
```shell
$ git branch -d dev
Deleted branch dev (was 8aec2cb).
```
现在就剩下`master`一个分支了。  

如果某一个分支在没有合并之前想删掉的时候，使用`-d`选项删除会报错，如果需要删除需要使用`-D`选项。
## 解决冲突 ##
比如现在我们创建了一个新分支`feature`，修改文件并提交
```shell
$ git checkout -b feature
Switched to a new branch 'feature'

$ cat test,txt
This is a test file.
add a new line.
add other line.
we are at dev.
this is a test line.

$ git add test,txt

$ git commit -m "feature commit"
[feature 6f20394] feature commit
 1 file changed, 1 insertion(+)
```
现在我们回到`master`分支也修改并提交
```shell
$ cat test,txt
This is a test file.
add a new line.
add other line.
we are at dev.
this is one test line.
```
我们在最后一行改一个单词，现在两个分支都有各自的新提交，当合并这两个分支的时候，Git无法使用”快速合并“模式，它会试图合并各自的修改部分，但是这就有可能产生冲突
```shell
$ git merge feature
Auto-merging test,txt
CONFLICT (content): Merge conflict in test,txt
Automatic merge failed; fix conflicts and then commit the result.
```
Git告诉我们文件有冲突，需要解决冲突在提交，我们也可以用`git status`来查看
```shell
$ git status
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

        both modified:   test,txt

no changes added to commit (use "git add" and/or "git commit -a")
```
我们可以直接查看`test.txt`的内容
```shell
This is a test file.
add a new line.
add other line.
we are at dev.
<<<<<<< HEAD
this is one test line.
=======
this is a test line.
>>>>>>> feature
```
Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容，我们修改如下后保存：
```shell
this is an test line.
```
然后再提交
```shell
$ git add test,txt

$ git commit -m "conflicts fix"
[master 9041f55] conflicts fix
```
现在两个分支已经成功合并，我们用`git log`可以查看分支合并情况
```shell
$ git log --pretty=oneline --graph --abbrev-commit
*   9041f55 (HEAD -> master) conflicts fix
|\
| * 6f20394 (feature) feature commit
* | ac630e1 master commit
|/
* 8aec2cb add in dev
* 412229b add other line.
* d226982 (origin/master) remove test2.txt
* 65668ff add a new file
* b038cb4 add a new line
* 13349a0 test file
```
> 1. --graph 显示 ASCII 图形表示的分支合并历史。
> 2. --pretty 使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。
> 3. --abbrev-commit 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。

然后就可以删除分支了
```shell
$ git branch -d feature
Deleted branch feature (was 6f20394).
```
## 分支管理策略 ##
通常，合并分支的时候，如果可以的话，Git会用`Fast-forward`模式，就是直接把`master`指针直接指向当前版本的提交点，这种情况下，如果删除分支后，就会丢失分支的信息，无法查看该分支进行了哪些操作，如果要强制禁止`Fast-forward`模式，Git就会在合并的时候生成一个新的commit，时两个独立的提交，这样，从分支历史上就可以看出分支信息。
例如我们创建并切换`dev`分支
```shell
$ git checkout -b dev
Switched to a new branch 'dev'

$ git add test,txt

$ git commit -m "add merge"
[dev b1da8c2] add merge
 1 file changed, 1 insertion(+)
```
然后回到`master`，准备合并分支，并且使用`--no-ff`参数禁用`Fast-forward`模式
```shell
$ git merge --no-ff -m "merge without ff" dev
Merge made by the 'recursive' strategy.
 test,txt | 1 +
 1 file changed, 1 insertion(+)
```
因为要新生成一个commit，所以要加上`-m`选项给这个提交添加描述信息
```shell
$ git log --pretty=oneline --graph --abbrev-commit
*   0617920 (HEAD -> master) merge without ff
|\
| * b1da8c2 (dev) add merge
|/
*   9041f55 conflicts fix
```
可以看到两个合并后`master`分支的最新提交点与`dev`分支的ID并不相同。
## Bug分支 ##
当你需要临时创建一个分支而当前所在的分支工作还没做完不能提交时，比如修复Bug，可以使用Git的`stash`功能，把当前工作去的内容暂存起来，等Bug修复完成之后在恢复。
```shell
$ git status
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   test,txt

no changes added to commit (use "git add" and/or "git commit -a")
```
Git说test.txt文件有修改但还没有提交，现在我们用stash将工作区暂存起来
```shell
$ git stash
Saved working directory and index state WIP on dev: b1da8c2 add merge

$ git status
On branch dev
nothing to commit, working tree clean
```
我们的工作区已经干净了，现在就可以放心的区创建分支修复Bug了。  

当我们修复工作完成之后，就可以恢复到之前的工作现场，有两种恢复方式，一是用`git stash apply`命令，恢复后之前暂存起来的内容并不会删除，需要使用`git stash drop`来删除，另一种是用`git stash pop`命令，恢复的时候同时也把之前暂存的内容一并删除掉。
```shell
$ git stash list
stash@{0}: WIP on dev: b1da8c2 add merge

$ git stash pop
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   test,txt

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (ff2c669e4acf023c5d658b86dedb5e5def7fdc57)

$ git stash list

```
如果有多次stash，恢复的时候先用`git stash list`查看，再恢复制定的stash
```shell
$ git stash apply stash@{0}
```
# 标签管理 #
当我们发布一个版本的时候，通常会在版本库中打一个标签，用来标记当时的版本，将来取某个标签的版本，就是把那个标签所在的历史版本库取出来。  
标签其实是一个指向相对的某个commit的指针，但是并不会随着commit而移动。Git引入标签的意义在于给commit取一个别名，用来代替那一串常常的commit ID。
## 创建标签 ##
创建标签很简单，首先切换到要打标签的分支，然后使用｀git tag TAG_NAME`命令就可以打一个标签
```shell
$ git branch
  dev
* master

$ git tag v1.0

$ git tag
v1.0
```
标签默认是打在最新的提交上，如果要在过去的某个提交上打标签，需要指定相对应commit的ID号
```shell
$ git log --pretty=oneline --abbrev-commit
0617920 (HEAD -> master, tag: v1.0) merge without ff
b1da8c2 (dev) add merge
9041f55 conflicts fix
ac630e1 master commit
6f20394 feature commit
8aec2cb add in dev
412229b add other line.
d226982 (origin/master) remove test2.txt
65668ff add a new file
b038cb4 add a new line
13349a0 test file

$ git tag v0.8 8aec2cb

$ git tag
v0.8
v1.0
```
使用`git show TAG_NAME`可以查看某个标签的详细信息
```shell
$ git show v0.8
commit 8aec2cb60dd48cb095a5fc5038e8c0758b6c110b (tag: v0.8)
Author: qxd <1591775626@qq.com>
Date:   Sat Mar 9 14:42:43 2019 +0800

    add in dev

diff --git a/test,txt b/test,txt
index 1d99a05..d288e65 100644
--- a/test,txt
+++ b/test,txt
@@ -1,3 +1,4 @@
 This is a test file.
 add a new line.
 add other line.
+we are at dev.
```
创建标签的时候还可以使用`-m`选项给标签加上说明信息
```shell
$ git show v0.5
tag v0.5
Tagger: qxd <1591775626@qq.com>
Date:   Sat Mar 9 17:12:51 2019 +0800

version 0.5

commit 65668ff54f5cddbb7dd85f1492a3a802d00e91c8 (tag: v0.5)
Author: qxd <1591775626@qq.com>
Date:   Fri Mar 8 23:17:18 2019 +0800

    add a new file

diff --git a/test2.txt b/test2.txt
new file mode 100644
index 0000000..f73f309
--- /dev/null
+++ b/test2.txt
@@ -0,0 +1 @@
+file
```

> 注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。

## 操作标签 ##
如果标签打错了，也可以删除
```shell
$ git tag
v0.5
v0.8
v1.0

$ git tag -d v0.5
Deleted tag 'v0.5' (was 171a95c)
```
推送某个标签到远程，可以使用`git push origin TAG_NAME`
```shell
$ git push origin v1.0
Total 0 (delta 0), reused 0 (delta 0)
To github.com:qxdty/blog.git
 * [new tag]         v1.0 -> v1.0
```
或者一次性把所有未推送的标签一起推送到远程
```shell
$ git push origin --tags
Total 0 (delta 0), reused 0 (delta 0)
To github.com:qxdty/blog.git
 * [new tag]         v0.8 -> v0.8
```
如果要删除远程的标签，要先删除本地的标签，在删除远程标签，命令页视`push`
```shell
$ git tag -d v1.0
Deleted tag 'v1.0' (was 0617920)

$ git push origin :refs/tags/v1.0
To github.com:qxdty/blog.git
 - [deleted]         v1.0
```
# 参与开源项目 #
当你发现对某个开源项目有想法的时候，可以访问它的GitHub项目主页，然后点击`Fork`将项目克隆到自己的仓库下，然后就可以在本地同步这个仓库，进而实现自己的想法；一定要从自己的仓库中克隆，这样你所做的修改才有权限推送，如果直接从源仓库克隆，因为没有相应的权限，你并不能提交你自己的修改。如果你希望项目的官方库能接受你的修改，你就可以在GitHub上发起一个pull request。当然，对方是否接受你的pull request就不一定了。