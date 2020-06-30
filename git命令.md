# git 使用手册

### \* 1.创建版本库

第一步 选择合适位置创建一个空目录

```bash
$mkdir learngit
$cd learngit
$pwd  // 显示当前目录
```

第二步，通过 git init 将目录变成 git 可以管理的仓库

```bash
$ git init
```

将文件添加到版本库，第一步 git add 告诉 git 把文件添加到仓库；第二步用命令 git commit 告诉 git 将文件提交到仓库(-m 输入的是本次提交的说明)

```bash
$ git add readme.txt
$ git commit -m "commit first"
```

操作时，可以多次 add，然后 commit 一次提交多个文件

### \* 2.可以穿梭的 git

> 1.使用 git status 查看仓库的当前状态。当然如果时间间隔过长忘记了文件做了哪些修改，可以使用 git diff 查看具体修改内容。在实际工作中可以使用 git log 来查看历史记录

```bash
$ git status
$ git diff
$ git log
```

> 2.版本回退  
> 在 git 中使用 HEAD 表示当前版本，上个版本就是 HEAD^，
> 上上个版本就是 HEAD^^，往上 100 个则写成 HEAD~100。  
> 使用以下代码实现回退到上个版本。

```bash
$ git reset --hard HEAD^
```

> 当然如果此时你后悔了又想要回去，git 也是有办法的。如果之前的命令行窗口还没有关闭，你可以顺着找到你想要回退版本的 commit id，然后 reset，砰~我们又回来了！

```bash
$ git reset --hard 1094a
```

> 如果你回退并关了电脑，第二天后悔了，想恢复到新版本由找不到 commit id 怎么办？贴心的 git 为我们提供了 git reflog，记录了我们每一次命令，找到对应 commit id 再 reset

```bash
$ git reflog
```

> 3.撤销修改

```bash
git checkout -- file  //修改了工作区的内容未add、未commit，此命令直接丢弃工作区的修改恢复到与版本库一模一样
git reset HEAD <file>  // 修改了并add但未commit，此命令将暂存区的修改撤销掉，重新放回工作区，工作区需要再撤销则执行上一步
$ git reset --hard 1094a  //修改了并add、commit，此种情况使用版本回退
```

> 4.删除文件  
> 如果一个文件已经被提交到版本库那么我们不需要担心误删，git 可以恢复文件到最新版，但是会丢失最近一次提交后修改的内容

```bash
$ git rm
```

### \* 3.远程仓库

> 1.添加远程仓库(-u 参数会将本地的 master 分支和远程的 master 分支关联起来)

```bash
$ git remote add origin <项目地址>  // 将本地仓库和远程仓库关联
$ git push -u origin master   //将本地库的内容推送到远程库中
```

> 关联远程仓库然后推送报错 failed to push some refs to git 的解决办法：（出现错误的主要原因是 github 中的 README.md 文件不在本地代码目录中）

```bash
$ git pull --rebase origin master  // pull=fetch+merge
$ git push -u origin master
```

> 2.从远程库克隆

```bash
$ git clone
```

### \* 4.分支管理

> 1.创建和合并分支  
> 合并分支时，加上--no-ff 参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而 fast forward 合并就看不出来曾经做过合并。

```bash
$ git checkout -b dev  // 加上-b参数表示创建并切换
// 等价于
$ git branch dev
$ git checkout dev  // 切换分支
$ git branch // 查看当前分支，当前分支前边会标一个*
$ git merge dev  // 合并dev分支到当前分支
$ git branch -d dev  // 合并后可删除dev分支
$ git merge --no-ff -m "merge with no-ff" dev
git branch -D <name>  // 丢弃一个没有被合并过的分支，可以这样强行删除。
```

> 2.冲突  
> 当 master 分支和 dev 分支各自都有新的提交时合并就会冲突，我们查看冲突文件，Git 用<<<<<<<，=======，>>>>>>>标记出不同分支的内容，将冲突内容修改后保存提交

```bash
git log --graph  //查看分支合并图
```

> 3.bug 分支  
> 使用 git 提供的 stash 功能将当前工作现场储藏起来，等以后恢复现场后继续工作。储藏后创建分支类解决 bug

```bash
$ git stash
$ git stash list  // 查看之前的工作现场
$ git stash apply  // 恢复工作现场且stash内容不删除，需要使用git stash drop来删除
$ git stash pop  // 恢复的同时把stash内容删掉
$ git stash apply <stash@{id}>  // 恢复到指定的stash
```

> 4.多人协作  
> 协作流程：
>
> - 1.首先，可以试图用 git push origin <branch-name>推送自己的修改；
> - 2.如果推送失败，则因为远程分支比你的本地更新，需要先用 git pull 试图合并；
> - 3.如果合并有冲突，则解决冲突，并在本地提交；
> - 4.没有冲突或者解决掉冲突后，再用 git push origin <branch-name>推送就能成功！

> 查看远程库信息，使用 git remote -v；  
> 本地新建的分支如果不推送到远程，对其他人就是不可见的；  
> 从本地推送分支，使用 git push origin branch-name，如果推送失败，先用 git pull 抓取远程的新提交；  
> 在本地创建和远程分支对应的分支，使用 git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；  
> 建立本地分支和远程分支的关联，使用 git branch --set-upstream branch-name origin/branch-name；
> 从远程抓取分支，使用 git pull，如果有冲突，要先处理冲突。

### \* 5.标签管理

```bash
$ git tag v1.0  // 打标签，默认当前标签
$ git tag  // 查看所有标签
$ git tag v0.9 f52c633 // 指定commit id打标签
$ git tag -a v0.1 -m "version 0.1 released" 1094adb   // -a指定标签名，-m指定说明文字
Deleted tag 'v0.1' (was f15b0dd) // 删除标签
$ git push origin <tagname>  // 推送某个标签到远程
$ git push origin --tags  // 一次性推送全部尚未推送到远程的本地标签
// 推送到远程的标签先在本地删除再远程删除
$ git tag -d v0.9
$ git push origin :refs/tags/v0.9
```
