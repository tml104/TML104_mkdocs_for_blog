# Git常见操作

## 新建

在当前目录新建git库

```
git init
```



## Add、RM & Commit & 状态检查、比对

工作区推送到暂存区

```
git add a.txt b.txt ...
```

工作区推送到暂存区（删除文件）

*也可使用`git add`

~~~
git rm <file>
~~~

暂存区推送到仓库

```
git commit -m "(description)"
```

检查状态

~~~
git status
~~~

比对不同

~~~
git diff a.txt
~~~

Commit记录

~~~
git log [--pretty=oneline] [--gtaph] [--abbrev-commit]
~~~

Commit操作日志（用于寻找Commit ID）

~~~
git reflog
~~~



## 版本回退、复原

回退至之前的Commit

~~~
git reset --hard <HEAD^|HEAD~1>
~~~

以Commit ID回退

~~~
git reset --hard <1094a...>
~~~



在执行 ` git add ` 之前，工作区!=暂存区（index）==仓库

执行`git add`后，工作区==暂存区!=仓库



从仓库中复原至暂存区（撤销暂存区的修改）

~~~
git reset HEAD <file>
~~~

从暂存区复原至工作区（丢弃工作区修改）

~~~
git checkout -- <file>
~~~





## 分支

新建分支并切换至

~~~
git checkout -b <new branch name>
(or)
git branch <new branch name>
git checkout <new branch name>
(or)
git switch -c <new branch name>
~~~

查看所有分支

~~~
git branch
~~~

切换分支

~~~
git checkout <branch name>
(or)
git switch <branch name>
~~~



将其他分支的内容（最新的Commit）与当前分支的内容（最新的Commit）合并

~~~
（在当前分支下执行）
git merge <other branch name>
~~~

删除某分支

~~~
git branch -d <other branch name>
~~~

强制删除（如果此分支没有被合并过）

~~~
git branch -D <other branch name>
~~~





合并时可能会遇到冲突。若遇到冲突，直接打开文件修改最终合并后的文件版本。之后再提交

~~~
git merge <branch name>
git status
...(遇到冲突后修改文件)
git add <file>
git commit -m <description>
~~~



禁用 fast forward 模式合并。此时 merge 会生成新 Commit 

（采取默认方式的 merge 只会将 HEAD 指针移动到新分支的最新 Commit 上）

```
git merge --no-ff -m "merge with no-fast-forward" <other branch name>
```



## Stash 临时保藏

在切换至其他分支前，如果工作区有未 Commit 的修改，可以先保存

~~~
git stash
~~~

恢复（pop会从stash列表中取出最后一个复原，然后删除之）

~~~
git stash apply
git stash drop
(or)
git stash pop
~~~



## Cherry-pick 复用 Commit

复用某次的 Commit 于当前 Commit，这个时候会新建一个新的 Commit 

~~~
git cherry-pick <commit id>
~~~



## 远程推送、抓取

查看远程仓库（若没有则返回空）

```
git remote
```

添加远程仓库

~~~
git remote add origin git@github.com:tml104/<repository name>
~~~

推送本地 master 分支至远程库

使用`-u`参数来将本地分支与远程分支关联

（分支不存在时会自动新建）

~~~
git push [-u|--set-upstream] origin master
git push origin dev
git push （自动将当前所在本地分支推送至对应远程分支，当且仅当当前所在本地分支已经和远程分支关联时可用）
git push origin <local branch name>:<remote branch name>
~~~



Clone 远程仓库

~~~
git clone git@github.com:<user name>/<repository name>
cd <repository name>
~~~



使得本地分支与远程分支关联

~~~
git branch --set-upstream-to=origin/<branch name> <local branch name>
~~~



拉取远程分支并与本地分支合并（可能会冲突，也可能合并之后没有变化）

~~~
git pull [origin] [<local branch name>:<remote branch name>]
~~~



## 设置ssh key

https://www.liaoxuefeng.com/wiki/896043488029600/896954117292416

https://blog.csdn.net/lqlqlq007/article/details/78983879

### 在设置前设置邮箱和用户名

```
git config --global  user.name "这里换上你的用户名"
git config --global user.email "这里换上你的邮箱"
```



### 本地产生ssh密钥，并将公钥放到服务器上

```
ssh-keygen -t rsa -C "1041159637@qq.com"
接着将"C:\Users\10411\.ssh\id_rsa.pub"中的内容复制到github中new sshkey中
```

