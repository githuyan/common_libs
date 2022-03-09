# Git 分布式版本控制工具

## 常用流程方法

参考：

- [521xueweihan/git-tips: Git的奇技淫巧 (github.com)](https://github.com/521xueweihan/git-tips)

正式版本会打 tag 



## 工作流程

1. 修改完项目后，重新拉去项目，在新项目中修改

2. commit （ 兼容空值错误 ）
3. 控制台先 git pull 拉取最新项目，确认没有修改
4. git log ,查看自己的提交日志
5. git rebase -i 
6. git push ( 查看提示 )
7. 推送项目后，会有gitlib 连接，
8. 进入 gitlib 连接，创建合并请求

gitlib 连接：

- CI/CD  会看到自己的提交
- Merge requests 连接发给涛哥，说明错误格式，请求合并代码



## 流通关系

### 日志

```shell
git reflog 显示当前分支 commit 的所有记录

git log -2 查看最近两次 commit 记录

git log master --not --remotes=*/master 显示本地服务器中的所有提交，但不显示任何远程存储库主分支

git log --all --grep='<given-text>' # 正则匹配 commit 日志
```



### 工作区（未追踪区Untrack)

#### CRUD

**查**

```shell
git status # 查看工作区文件状态
git status -s # 简化信息
```

#### > 暂存区

```shell
git add <.*file> # 添加文件到暂存区
git add -u # 只提交被修改和被删除的文件
```

#### > 本地仓库

#### > 远程仓库

### 暂存区

#### CRUD

**查**

```shell
git ls-files  # 查看暂存区文件

git show 查看最后一次提交详情
git show --stat 查看最后一次提交的简介
```

删

```shell
git reset <.*file>  # 撤销上一次 add 到暂存区的操作
git rm --cached <.*file> # 删除暂存区文件，不影响工作区
git rm -f <.*file>  # 删除文件，且同时删除工作区

git rm .git/index # 清空本区
```

#### > 工作区

> [git_工作区与暂存区的文件修改、还原 ](https://www.cnblogs.com/isXianYue/p/14248015.html)

```shell
git checkout <.*file>  # 覆盖型恢复文件到工作区(支持正则)
git restore new.txt
git reset HEAD  # 撤销上一次add到暂存区的文件
```

#### > 本地仓库

```shell
git commit -m "描述"
git commit --amend  # 进入vim，修改提交的注释
```

#### > 远程仓库

### 本地仓库

#### CRUD

**查**

```shell
git branch  # 列出所有本地分支
```

**增**

```shell
git branch develop # 创建名为 develop 的本地分支 
git checkout -b develop # 创建分支并切换到此分支
```

**改**

```shell
git branch -m old_name new_name # 重命名本地分支
git checkout develop # 切换分支
git checkout  -  # 切换到上一个分支
```

**删**

```shell
git branch -d develop # 删除本地分支，不可删除当前所在分支
git branch -D develop # 强制删除本地分支
```

#### > 工作区

```shell
# 先恢复到暂存区，再从暂存区恢复到工作区
git restore --staged <.*file>
git restore <.*file>

git reset --hard HEAD^ # 直接回退一个版本（覆盖写入）
```

#### > 暂存区

```git
# push 之前
git restore --staged <.*file> # 从本地仓库恢复文件到暂存区

git reset --soft|--mixed|--hard <commit_id> # 这个撤销会将commit记录也撤销钓

# push 之后
git revert <commit_id> # 回退本地代码同时回退线上代码 ？TODO 是只回退了本地仓库，还是所有本地区域都回退了
```

#### > 远程仓库

```git
git remote add origin(自定义仓库名) url # 添加一个远程仓库到本地的映射

git push -u origin master # 推送本地仓库到远程 master 分支，并将这个主机分支设定为默认推送地址，下次可使用 git push 完成推送
git push origin --all # 推送所有本地分支到远程
```

### 远程仓库

#### CRUD

**查**

```shell
git remote -v # 查看远程连接
git remote show 远程仓库 # 显示远程仓库信息

git branch -r # 列出所有远程分支
```

**增**

```git
git remote add origin url # 增加一个远程连接并命名 origin
```

**删**

```shell
git remote rm origin # 断开名为 origin 的远程连接

git branch -dr 远程分支名  # 删除远程分支
```

#### > 工作区

```git
git fetch 远程仓库 # 下载远程仓库的所有变动
```

#### > 暂存区

#### > 本地仓库

```shell
# 此处的合并 指的是追加合并
git pull origin master(R):master(L) # 将远程仓库origin的 master 分支合并到本地 master 分支
git pusll origin master # 将远程仓库origin的 master 分支合并到本地当前分支
```

## 命令

### 查看信息

```shell
git reflog   # 查看所有版本信息，包括回滚和删除过的

git ls-files # 查看暂存区中的内容
git ls-files --others # 展示所有 untracked 的文件
```



**git rebase （合并提交）**

**参考：**

- [git rebase](#tems_id)   - https://www.jianshu.com/p/4a8f4af4e803

向上合并 commit 提交信息

```shell
git rebase -i start,end=Head
pick： 标记其他提交合并到这个 commit
squash：将指定的提交合并到上边的pick请求中，保留提交信息
fixup：与 squash 类似，但不保留提交信息 
```

### 同步分支

**参考：**  [git cherry](https://blog.csdn.net/CSDN297904380/article/details/108872875?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_antiscanv2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_antiscanv2&utm_relevant_index=1)



### **git status** （状态）

```shell
HP@DESKTOP-11VL9CJ MINGW64 /d/桌面/学习笔记/study-notes (master)
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:	
#这里是从工作区add到暂存区的文件，通常是绿色的
  (use "git restore --staged <file>..." to unstage)
        modified:   python_List.md

Changes not staged for commit:
#这里是工作区暂存区都存在的文件，在工作区修改但还没有add到暂存区
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md
        modified:   python_String_variable.md
        modified:   工欲善其事篇/Typora使用.md

Untracked files:
#这里是未纳入版本控制的工作区的文件
  (use "git add <file>..." to include in what will be committed)
        python-Collections.md
```

### **git diff**

```shell
git diff 			显示工作区与暂存区的差异 git diff filename 指定文件
	--stat 只显示摘要
git diff head	显示工作区与版本库的差异
git diff --cached	显示暂存区与版本库的差异

# 显示分支的差异
# 分支的差异显示在提交的不同，而不再与具体的改动，当两个分支有不同的提交记录时，才会检测到差异
https://www.jianshu.com/p/bb97fabb475e
git diff branch1 branch2  # 查看两个分支的详细差异
git diff branch1 branch2 --stats  # 两个分支的简略差异
```



### **git reset** （撤销）

> 这个命令的意义更像是 撤销 ，而不是 删除

**暂存区的撤销**

> 该文件撤销跟踪，暂存区删除记录

```shell
git reset <.*file> # 撤销上一次 add 到暂存区的操作
```

**本地仓库的撤销**

> 这里是在 commit 之后，push 之前，撤回一条 commit
>
> 每一次 commit 都会产生 id，在 reflog 中可以找到所有版本

```shell
git reset --hard 版本id  # 硬重置，所有文件撤销为之前（未提交）状态（覆盖写入），并撤销上一条版本日志

git reset --soft 版本id  # 软重置，重置为之前版本的状态，但是已修改的文件保留修改（追加写入）

git reset --hard HEAD^^  # 撤销，回到上上个版本 （硬重置）

git reset --hard HED~2
```



### **git restore**

> git restore 更像是 reset 的补充

```shell
git restore <.*file>  # 将暂存区，工作区回退到上一次 add 之前的状态

git restore --staged  <.*file>  # 将暂存区回退到上一次 commit 之前的状态[^--staged 后缀代表仅恢复暂存区的状态]
```



### **git branch**

```shell
git branch -vv # 展示本地分支关联远程仓库的情况

git branch -d 'branchname' # shanchu bendi fenzhi 

git push origin --delete <remote-branchname> # 删除远程分支

git branch -m <new-branchname> # 重命名本地分支
```



### **git checkout**

```shell
git checkout newbranch # 切换分支
git checkout -b newbranch # 创建并切换分支
git checkout -r  # 列出所有远程分支
			-a # 列出所有远程和本地分支
```

### **git remote**

```shell
git remote  # 查看所有远程仓库
git remote -v # 列出所有远程分支
git remote show origin # 查看远程分支和本地分支的对应关系
```

### **git stash**

```shell
git stash # 存储当前的修改
git stash -u # 保存当前状态，包括 untracked 的文件
git stash list  # 展示所有的 stash

git stash drop  # 删除 隐藏内容
git stash apply <stach@n>  # 回到某一个 stash 状态
git stash pop # 回到最后一个 stash 状态
```

### **git clone**

```shell
git clone -b <branch-name> --single-branch https://github.com.cnpmjs.org/other.git # clone 指定分支
```



## 技巧

### 标签

```shell
git tag  # 列出现有标签

​		-l 'ab*'  # 列出以 ab 开头的标签名

​		-f 'aa'  # 强制创建标签，即使标签已存在

git tag -a **'** 标签名 **'** -m **'** 标签信息 **'** HEAD  # 为当前 HEAD 指针创建标签

git show 标签名  # 查看标签信息

git tag -d 标签名 # 删除标签
```



### 分支

#### 从 dev 分支创建新分支

参考：

- [ Git从dev分支创建新分支](https://blog.csdn.net/landiyaaa/article/details/91366274)
- [基于 Development 分支创建 Feature 分支](https://www.jianshu.com/p/0a9bcef28a6a)

1. 克隆项目 dev 分支到本地
2. 创建本地新分支，
3. 查看分支状态，检查仓库名称（红色：远程分支，黑色：本地分支，绿色：本地当前分支）
4. 推送本地分支到远程
5. 将当前分支与远程分支关联
6. 查看关联情况
7. 推送本地代码

### 隐藏修改

> 所有对本地的修改都是追加写入
>
> git stash会把所有未提交的修改（包括暂存的和非暂存的）都保存起来，用于后续恢复当前工作目录
>
> git stash 会将在**上一次 commit 之后**的所有修改都保存起来

```shell
git stash # 隐藏当前工作区所有修改

git stash list  # 查看当前的所有隐藏

git stash apply # 恢复隐藏文件到工作区 ，stash 内容并不删除

git stash drop  # 删除 隐藏内容

git stash pop # 弹出隐藏文件到工作区，同时删除隐藏文件
```



### 忽略文件

> 该文件只能作用于 Untracked Files，也就是那些从来没有被 Git 记录过的文件（自添加以后，从未 add 及 commit 过的文件）。

```python
# 忽略指定文件
HelloWrold.class

# 使用 # 注释

# 忽略指定文件夹
bin/
bin/gen/

*.class # 忽略.class的所有文件 （ . 没有特殊含义 ）

*ignore/ # 忽略名称中末尾为ignore的文件（文件，文件夹）

ignore* # 忽略名称中以 ignore 开头的所有文件（文件，文件夹）

*ignore*/ # 忽略名称中间包含ignore的文件夹
```



### git blame 

> 查看某一段代码是谁写的

```shell
git blame <file-name>
```

### 查看一段时间内的改动

```shell
git whatchanged --since='2 weeks ago'  # 查看连个星期内的改动
```

## gitflow 工作流

参考：

- 什么是GitFlow工作流？https://www.jianshu.com/p/bdb05232dbc1

双分支

- master 分支存储正式发布的历史
- develop 分支作为功能的集成分支

每一个新功能位于一个自己的分支，这些分支以 develop 分支作为父分支，功能完成后，合并到 push 到 develop 分支，并不与 master 分支直接交互

[^--staged 后缀代表仅恢复暂存区的状态]: 
