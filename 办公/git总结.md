---
title: git总结
date: 2020-02-19 07:38:36
categories: 办公
---
### 添加信息

&emsp;&emsp;使用如下命令，在`git`中添加用户名和邮箱：<!--more-->

``` bash
git config --global user.name "Morvan Zhou"
git config --global user.email "mz@email.com"
```

### 创建版本库(init)

&emsp;&emsp;如果要对某个文件夹中的代码进行管理，首先要进入该文件夹，然后输入如下命令即可：

``` bash
git init
```

### 添加文件(add)

&emsp;&emsp;新建一个名为`1.py`的文件，然后使用`status`来查看版本库的状态：

``` bash
$ git status
On branch master  # 在master分支
Untracked files:
    1.py  # 1.py文件没有被加入版本库(unstaged)
```

目前`1.py`并没有被放入版本库中，所以要使用`add`把它添加进版本库：

``` bash
$ git add 1.py
$ git status  # 再次查看状态status
On branch master
Changes to be committed:
    new file: 1.py  # 版本库已识别1.py(staged)
```

&emsp;&emsp;如果想一次性添加文件夹中所有未被添加的文件，可以使用如下命令：

``` bash
git add .
```

### 提交改变(commit)

&emsp;&emsp;已经添加好了`1.py`文件，最后一步就是提交这次的改变，并在`-m`自定义这次改变的信息：

``` bash
$ git commit -m "create 1.py"
[master (root-commit) 6bd231e] create 1.py
1 file changed, 0 insertions(+), 0 deletions(-)
create mode 100644 1.py
```

### 修改记录(log)

&emsp;&emsp;在`git`中，每一次`commit`的修改都会被单独地保存起来。可以使用`git log`来查看所有的`commit`：

``` bash
$ git log
commit 13be9a7bf70c040544c6242a494206f240aac03c
Author: Morvan Zhou <mz@email.com>
Date:   Tue Nov 29 00:06:47 2016 +1100
    create 1.py  # 这是记录的修改信息
```

&emsp;&emsp;如果对`1.py`文件进行一次修改，例如添加如下代码：

``` bash
a = 1
```

然后就能在`status`中看到修改还没被`commit`的信息了：

``` bash
$ git status
On branch master
Changes not staged for commit:
    modified: 1.py  # 这里显示有一个修改还没被提交
```

&emsp;&emsp;先添加这次修改，然后进行`commit`：

``` bash
$ git add 1.py
$ git commit -m "change 1"
[master fb51216] change 1
1 file changed, 1 insertion(+)  # 提示文件有1处添加
```

使用`git log`，现在就能看到`create 1.py`和`change 1`这两条修改信息了：

``` bash
$ git log
commit fb51216b081e00db3996e14edf8ff080fab1980a
Author: Morvan Zhou <mz@email.com>
Date:   Tue Nov 29 00:24:50 2016 +1100
    change 1

commit 13be9a7bf70c040544c6242a494206f240aac03c
Author: Morvan Zhou <mz@email.com>
Date:   Tue Nov 29 00:06:47 2016 +1100
    create 1.py
```

### 查看unstaged

&emsp;&emsp;把`1.py`的内容改为如下：

``` bash
a = 2
b = 1
```

&emsp;&emsp;如果想要查看这次还没`add`的修改部分和上个已经`commit`的内容有何不同，可以使用`diff`命令：

``` bash
$ git diff
diff --git a/1.py b/1.py
index 1337a53..ff7c36c 100644
--- a/1.py
+++ b/1.py
@@ -1 +1,2 @@
-a = 1  # 删除了“a = 1”
+a = 2  # 添加了“a = 2”
+b = 1  # 添加了“b = 1”
```

### 查看staged

&emsp;&emsp;如果已经`add`了这次修改，可以在`diff`中添加参数`--cached`来查看修改：

``` bash
$ git add .  # add全部修改文件
$ git diff --cached
diff --git a/1.py b/1.py
index 1337a53..ff7c36c 100644
--- a/1.py
+++ b/1.py
@@ -1 +1,2 @@
-a = 1
+a = 2
+b = 1
```

### 查看staged和unstaged(HEAD)

&emsp;&emsp;还有一种方法可以查看`add`过和没`add`的修改，即`git diff HEAD`。
&emsp;&emsp;修改一下文件`1.py`，目前`a = 2`和`b = 1`已被`add`，而`c = b`是新的修改，还没被`add`：

``` bash
a = 2
b = 1
c = b
```

&emsp;&emsp;使用`git diff HEAD`来查看修改：

``` bash
$ git diff HEAD  # staged & unstaged
@@ -1 +1,3 @@
-a = 1  # 已staged
+a = 2  # 已staged
+b = 1  # 已staged
+c = b  # 还没add(unstaged状态)
```

&emsp;&emsp;然后使用`add`命令变成`staged`状态，并进行`commit`：

``` bash
$ git add .
$ git commit -m "change 2"
[master 6cc6579] change 2
1 file changed, 3 insertions(+), 1 deletion(-)
```

### 修改已commit的版本

&emsp;&emsp;如果已经提交了`commit`，却发现在这次`commit`中忘了附上另一个文件。可以使用`--amend`将这次改变合并到之前的`commit`中。

``` bash
$ git add 2.py
$ git commit --amend --no-edit  # “--no-edit”：不编辑commit，直接合并到上一个commit
$ git log --oneline  # “--oneline”：每个commit内容显示在一行
904e1ba change 2  # 合并过的“change 2”
c6762a1 change 1
13be9a7 create 1.py
```

### reset回到commit之前

&emsp;&emsp;`git`累加更改的方式如下所示：

1. 每个`commit`都有自己的`id`数字号。
2. `HEAD`是一个指针，指引当前的状态是在哪个`commit`。
3. 最近的一次`commit`在最右边。

<img src="./git总结/2.png" width="50%">

&emsp;&emsp;如果要回到过去的`commit`，就是让`HEAD`回到过去：

``` bash
$ git log --oneline  # 看看所有的log
904e1ba change 2
c6762a1 change 1
13be9a7 create 1.py
# -----------------------
# 回到c10ea64，即“change 1”
# “--hard”删除代码的改动，而“--soft”则不删除改动
$ git reset --hard HEAD^  # 方式1：“HEAD^”
$ git reset --hard c6762a1  # 方式2：“commit id”
# -----------------------
$ git log --oneline  # 看看现在的log
c6762a1 change 1
13be9a7 create 1.py
```

怎么`change 2`消失了？还有办法挽救消失的`change 2`吗？
&emsp;&emsp;可以使用`git reflog`查看最近做的所有`HEAD`的改动，并选择想要挽救的`commit id`：

``` bash
$ git reflog
c6762a1 HEAD@{0}: reset: moving to c6762a1
904e1ba HEAD@{1}: commit (amend): change 2
0107760 HEAD@{2}: commit: change 2
c6762a1 HEAD@{3}: commit: change 1
13be9a7 HEAD@{4}: commit (initial): create 1.py
```

再次`reset`就能回到`commit (amend): change 2`：

``` bash
$ git reset --hard 904e1ba
$ git log --oneline
904e1ba change 2
c6762a1 change 1
13be9a7 create 1.py
```

### 分支(branch)

&emsp;&emsp;之前编辑的所有改变都是在主分支`master`上进行的，通常会把`master`当作最终的版本；而开发新版本的时候，在另外一个分支上进行。
&emsp;&emsp;可以使用`--graph`来观看分支情况：

``` bash
$ git log --oneline --graph
* 47f167e back to change 1 and add comment for 1.py
* 904e1ba change 2
* c6762a1 change 1
* 13be9a7 create 1.py
```

&emsp;&emsp;接着建立另一个分支`dev`，并查看当前管理的所有分支：

``` bash
$ git branch dev  # 建立dev分支
$ git branch  # 查看当前分支
  dev
* master  # “*”代表了当前HEAD所在的分支
```

&emsp;&emsp;当需要把`HEAD`切换到`dev`分支时，可以使用`checkout`命令：

``` bash
$ git checkout dev
Switched to branch 'dev'
# --------------------------
$ git branch
* dev  # 这时HEAD已经被切换至dev分支
  master
```

&emsp;&emsp;目前`dev`分支中的`1.py`与`2.py`和`master`中的文件是一模一样的，因为当前的指针`HEAD`在`dev`分支上，所以现在对文件夹中的文件进行修改将不会影响到`master`分支。
&emsp;&emsp;在`1.py`上加入`# I was changed in dev branch`，然后再`commit`：

``` bash
git commit -am "change 3 in dev"  # “-am”：add所有改变，并直接commit
```

`dev`已经更新好了，现在要将`dev`中的修改合并到`master`中。首先切换到`master`，再将`dev`推送过来：

``` bash
$ git checkout master  # 切换至master才能把其他分支合并过来
$ git merge --no-ff -m "keep merge info" dev  # “--no-ff”：保留merge信息
$ git log --oneline --graph
*   c60668f keep merge info
|\
| * f9584f8 change 3 in dev  # 这里就能看出，我们建立过一个分支
|/
* 47f167e back to change 1 and add comment for 1.py
* 904e1ba change 2
* c6762a1 change 1
* 13be9a7 create 1.py
```

### merge分支冲突

&emsp;&emsp;当创建了一个分支后，同时对两个分支进行修改，例如在`master`中的`1.py`加上`# edited in master`，在`dev`中的`1.py`加上`# edited in dev`，下面可以看出`master`和`dev`中不同的`commit`：

``` bash
# 这是master的log
* 3d7796e change 4 in master  # 这一条commit和dev的不一样
* 47f167e back to change 1 and add comment for 1.py
* 904e1ba change 2
* c6762a1 change 1
* 13be9a7 create 1.py
# -----------------------------
# 这是dev的log
* f7d2e3a change 3 in dev  # 这一条commit和master的不一样
* 47f167e back to change 1 and add comment for 1.py
* 904e1ba change 2
* c6762a1 change 1
* 13be9a7 create 1.py
```

当我们想要将`dev`合并到`master`时：

``` bash
$ git branch
  dev
* master
# -------------------------
$ git merge dev
Auto-merging 1.py
CONFLICT (content): Merge conflict in 1.py
Automatic merge failed; fix conflicts and then commit the result.
```

`git`发现`1.py`在`master`和`dev`的版本是不同的，所以提示`merge`有冲突。
&emsp;&emsp;打开`1.py`，就能看到如下内容：

``` bash
a = 1
# I went back to change 1
<<<<<<< HEAD
# edited in master
=======
# edited in dev
>>>>>>> dev
```

只要在`1.py`中手动合并一下两者的不同就可以了：

``` bash
a = 1
# I went back to change 1
# edited in master and dev
```

然后再`commit`现在的文件，冲突就解决了。

### 忽略特定项

&emsp;&emsp;在项目的根目录建立文件`.gitignore`，在该文件中添加要忽略的文件或文件夹。

### git clean

&emsp;&emsp;`git clean`命令用来从工作目录中删除所有没有`tracked`过的文件，例如编译生成的`.o`和`.exe`等文件。

### git fetch

&emsp;&emsp;`git fetch`是将远程主机的最新内容拉到本地，用户在检查了以后，决定是否合并到工作本机分支中。

- `git fetch <远程主机名>`：这个命令将某个远程主机的更新全部取回本地。
- `git fetch <远程主机名> <分支名>`：如果只想取回特定分支的更新，可以指定分支名。

&emsp;&emsp;最常见的命令是取回`origin`主机的`master`分支：

``` bash
git fetch origin master
```

&emsp;&emsp;取回更新后，会返回一个`FETCH_HEAD`，指的是某个`branch`在服务器上的最新状态。

### git pull

&emsp;&emsp;`git pull`是将远程主机的最新内容拉下来后直接合并，其过程可以理解为：

``` bash
git fetch origin master # 从远程主机的master分支拉取最新内容
git merge FETCH_HEAD    # 将拉取下来的最新内容合并到当前所在的分支中
```

&emsp;&emsp;`git pull`命令的完整格式如下：

``` bash
git pull <远程主机名> <远程分支名>:<本地分支名>
```

&emsp;&emsp;如果远程分支是与当前分支合并，则`<本地分支名>`可以省略：

``` bash
git pull origin next
```

### git push

&emsp;&emsp;`git push`命令用于将本地分支的更新推送到远程主机：

``` cpp
git push <远程主机名> <本地分支名>:<远程分支名>
```

&emsp;&emsp;将本地的`master`分支推送到`origin`主机的`master`分支：

``` cpp
git push origin master
```

### 强制覆盖本地代码

&emsp;&emsp;使用如下命令：

``` bash
git fetch --all
git reset --hard origin/master
git pull
```

### 注意点

&emsp;&emsp;1. 如果将文件名的进行大小写转换(例如将`ABC.cpp`重命名为`abc.cpp`)，则`git`无法察觉到这种改变。