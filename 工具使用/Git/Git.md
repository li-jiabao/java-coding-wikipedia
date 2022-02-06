# Git

## 1 简介

### 1.1 什么是版本控制

版本控制就是在开发过程中用于管理我们对文件、目录或工程等内容的修改历史，方便查看更改的历史记录，备份以便恢复以前版本的一种软件工程技术

- 实现多人协同开发
- 追踪和记录一个或者多个文件而历史记录
- 组织和保护你的源代码和文档
- 统计工作量
- 并行开发，提高工作效率
- 跟踪记录软件的整个开发过程
- 减轻开发人员的负担，节省时间，同时降低人为的错误

用于管理多人协同开发的一种技术

### 1.2 分布式版本控制

**分布式版本控制系统**（ Distributed Version Control System，简称 DVCS ）。

在这类系统中，像 **Git**，Mercurial，Bazaar 以及 Darcs 等，客户端并不只提取最新版本的文件快照，而是把原始的代码仓库完整地镜像下来。这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜 像出来的本地仓库恢复。因为每一次的提取操作，实际上都是一次对代码仓库的完整备份。

## 2 原理

### 2.1 几个区域分类

- 工作区，执行文件添加修改的区域，就是看得到的克隆下来的仓库目录文件，工作区添加到暂存区的命令，`git add filename/directory`反过来的一个命令：`git checkout`
- 暂存区：代码提交的暂时存放区域，暂存区到本地仓库的命令，`git commit -m "message"`，本地仓库到暂存区的命令`git reset`。暂存区内的文件是可以删除修改的，且不会影响到工作区的内容
- 本地仓库：存放需要和远程仓库进行交互的文件的地方，就是文件中的`.git`文件夹，本地仓库中有stage暂存区，还有git自动创建的第一个分支master，以及只想master的HEAD指针。
  本地仓库到远程仓库的命令`git push origin master`，远程仓库到本地仓库的命令`git clone/fetch remoterep`
- 远程仓库：存放文件与别人共享的地方，也就是git服务器上对应的仓库，远程仓库到工作区`git pull`

![image-20210924214954912](https://gitee.com/Jia_bao_Li/img/raw/master/img/git%E5%8E%9F%E7%90%86%E5%9B%BE.png)

**HEAD指针**：指向的是当前分支的最新提交。指向的是当前的提交版本位置。版本的回退就是移动这一个指针

### 2.2 branch分支介绍

前面提到的master就是GitHub的主分支，也是git为我们创的第一个分支，其他分支开发完成后都会合并到这个主分支，合并分支到当前分支的命令`git merge  [branch_name]`

分支的好处：

- 多个功能的并行开发，提高开发效率
- 各个分支在开发过程中，如果某个分支开发失败，不会影响其他的分支，将失败分支删除重新开始即可
- 每个分支可以只关注自己需要的功能去实现即可

分支冲突的时候怎么办：

`git status`先查看当前处于什么状况，可以找出冲突文件

冲突文件可以直接打开，然后在文件内部你可以看到两个文件相互冲突的东西，然后你可以根据需要再去保留你所需要的内容，删除不需要的内容。文件修改好了之后可以add、commit再提交

`git add 文件名`

`git commit -m "分支合并，冲突文件的修改保留"`不需要指定文件名，指定文件名会出错

### 2.3 tag标签介绍

标签是用来标记特定的点或提交的历史，通常会用来标记特定的点或者提交的历史，一般用来标记发布版本的名称或者版本号，标签是固定的不能随意改动

### 2.4 工作流程

1. 克隆git资源作为本地工作目录
2. 在克隆的资源上添加或者修改文件
3. 如果其他人修改了，你可以更新资源
4. 在提交前查看修改
5. 提交修改

### 2.5 本地仓库新建

需要执行本地新建的操作的花，需要首先使用ssh key，这个需要你先生成你的sshkey密钥。

1. `ssh-keygen -t rsa -C "注册邮箱"`其中-t选项可选的密钥形式有多种，可以在官网查看，执行命令后一路回车即可
2. 复制生成的密钥文件的公钥内容，然后粘贴到官网进行设置
3. 验证是否成功`ssh -T git@github.com`


```
mkdir test-repository
cd test-repository
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add rep_name https://gitee.com/Jia_bao_Li/repository_name.git
git push -u origin_name master 
```

## 3 常用命令

### 3.1 配置命令

```
git config --list   # 列出所有的配置

git config --global/system/local --list    # 列出所有的全局/系统/本地配置

git config --global user.name "name"  
# 添加用户名配置,添加配置就是类似的操作,只是对应的值和键名不同
```

### 3.2 和仓库对接的命令

`git add file/当前路径`添加文件到缓存区，选项有`-i（交互式命令子系统） -u（交互式命令系统的update模式） -p（交互式命令子系统的patch模式）`

文件的增删改查：在Linux命令的增删改查前面加上git，就可免去`git add`操作

`git commit -m "提交原因"`提交添加的文件到版本库中。-m提示信息最好可以代表你此次提交所操作的东西以及主要内容，便于后续再去版本回溯便于区分这一次提交到底是修改了什么
`git commint --amend -m "提交原因"`提交最新一条记录的更新原因
`git commit -C HEAD`将当前文件改动提交到head或者当前分支的历史id

`git push rep_name [branch_name]` 推送到远程仓库

`git clone/fetch remote-rep-addr` 获取git服务器的远程仓库到本地

`git pull`将远程仓库拉回到本地工作区

`git checkout`恢复正在工作的工作区tree file

`git status`查看文件变动状态选项`-s（简短记录查看） --ignored（包括被忽略的文件）`

`git log`查看本地的提交记录

`git tag`为项目标记里程碑


### 3.3 分支命令

`git branch -a`查看本地和远程的分支列表
`git branch -r`查看远程的分支列表，加-d参数可以删除远程版本库的分支

`git branch [branch_name]`新建分支
`git branch -m origin_name new_name`用于更改分支名字
`git branch`查看当前分支
`git branch -d [branch_name]`删除分支
`git branch -D`分支未提交到本地版本库前强行删除分支
`git branch -vv`查看详细信息

#### **合并分支**

这个命令是用来合并分支到当前分支
默认的分支合并命令执行快进式合并，直接将master分支指向devel分支

`git merge --no-ff`
使用了这个参数之后，在master分支上会生成一个新节点，保证版本演进更加清晰

`git merge --no-edit`
在没有冲突的情况下合并，不想手动编辑提交原因，自动生成提交原因

#### **切换分支**

`git checkout [branch_name`用来切换到分支

`git checkout -b [branch_name]`创建分支并切换到这个分支

`git checkout HEAD demo.html`
从本地版本库中的HEAD历史找出文件并覆盖当前工作的文件，没有HEAD就从暂存区寻找

`git checkout --orphan new_branch`
创建出一个全新的完全没有历史记录的分支，但当前源分支上的最新文件都在，但是这个分支需要commit之后才正式成为分支

`git checkout -p other_branch`
用来查看两个分支之间的差异


### 3.4 栈命令

在git的栈中保存当前修改或者删除的工作进度，当你在一个分支里面做某项功能开发时，接到通知把昨天测试完没问题的代码提交发布到线上，但这时候，你已经在这个分支里面加入了其他未提交 的代码，这个时候，就可以把这些未提交的代码存到栈里面

`git stash`将未提交的文件提交到栈中
`git stash list`查看栈中保存的内容
`git stash show stash@{0}`显示栈中的一条记录
`git stash drop stash@{0}`移除一条记录
`git stash pop`检出最新的一条记录，并移除
`git stash apply stash@{0}`检出一条记录不删除
`git stash branch new_branch `当前栈中最新一条记录检出，并创建一个新分支
`git stash clear`清空
`git stash create`创建一个自定义的栈，并且返回一个ID，此时并未真正存储到栈中
`git stash store ID`真正的创建一条记录

### 3.5 操作历史

显示历史提交记录

`git log -p`显示提交历史差异对比的记录

`git log file`查看文件的历史记录

`git log --since="2 weeks ago"`2周前到现在的历史记录

`git log --before="2 weeks ago"`截止到2周前的历史记录

`git log -10`查看10条记录

`git log --pretty=oneline`一行中输出简短历史记录

`git log --pretty=format:"%h"`指出自定义格式

### 3.6 查看所有记录

`git reflog`查看所有分支的所有操作记录

`git blame <file>`查看指定文件的历史修改记录

### 3.7 HEAD指针相关

#### 重置分支

将当前分支重设到指定的commit或者HEAD

#### 撤销

HEAD代表的是所处的当前版本的指针，便于区分当前所处的版本

撤销某次操作，此次操作前后的记录都会保留，并且把这次撤销作为一次最新的提交

`git revert -n HEAD`撤销前一次的提交操作，n可以指定多次记录。返回来重做特定的某个版本，且该版本之后的修改我仍然想要保留，可以使用这个命令。这种操作之后可能会出现文件冲突，需要手动修改冲突文件

`git reset --hard HEAD@{1}`撤回回到head1提交操作所在的版本。使用这个命令之后，这个版本之后的版本都会被丢弃，不再能够找回撤销的那些版本。适合：`如果想恢复到之前提交的某个版本，且该版本之后提交的版本都不需要了，可以使用这种方案` 

### 3.8 diff命令

查看工作区，暂存区，本地库和远程库之间的文件差异

`git diff`
--stat参数可以用来查看变更统计数据


### 3.9 远程版本库连接

如果在创建远程版本库之前，文件已经存在本地文件库中，可以使用`git init`本地初始化本地版本库，之后再将其与远程版本库连接
具体流程：

```
# 已有本地文件的初始化和远程库连接
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add rep_name https://gitee.com/Jia_bao_Li/repository_name.git
git push -u origin_name master 
```

`git init`在本地目录内部生成.git文件夹
`git remote -v`列出远程分支
`git remote add origin repo-addr`添加一个新的远程仓库，指定名字为origin，以便引用后面的url远程仓库
`git fetch origin branch_name`指定分支名时只取出这个分支的更新到本地，不指定分支，默认拉取所有分支更新到本地版本库

### 3.10 问题排查

`git blame`查看文件每行代码块的历史信息

`git bisect`二分查找历史记录，排查bug
后面加的参数：start开始查找，good没问题的点，bad有问题的点，reset回到原分支


### 3.11 其他操作

`git submodule`
git子模块跟踪外部版本库，允许在一个版本库中存储例外的版本库，并且可以保持两个版本库完全独立，参考Youcompleteme的安装时，他的版本库

`git gc` 运行git的垃圾回收机制，清理冗余的历史快照

`git archive`将加了某个tag的某个版本进行打包
`git archive -v --fromat=zip v0.1 > v0.1.zip`

## 4 Git最佳项目应用

### 4.1 gitignore文件

`.gitignore配置文件`设置哪些文件不需要推送到服务器，这个文件一般在新建Git仓库之后都需要设置好需要忽略的一些目录和文件等等。

```
# 配置文件示例
# 每一行防止需要忽略传送到服务器的文件,支持通配符
/**/*.class  忽略所有子文件夹下面的所有class文件
CNAME

*.java
```





## 8 Git常见问题和解决记录

### 证书错误

对于克隆文件时显示的证书错误，这可能是由于需要证书验证而造成的。解决方案：

- 禁用证书验证：`git config –system http.sslverify false`这个解决方案生效
- 指定证书目录：`git config --system http.sslcainfo "curl-ca-bundle.crt's directory"`指定你的git安装目录下的bin目录的证书路径。好像不太对这个解决方案

### 重置登录信息

`git config --system --unset credential.helper`

### 解决本地文件修改之后pull出现冲突的解决

`git status`查看状态

`git add -A`：把所有未追踪的文件先暂存起来

`git stash`用这个命令先把代码缓存起来

`git pull`拉去远端代码

`git stash pop/drop`缓存文件弹出来，强行merge回来

`git status`再查看冲突状况，可以酌情修改再重新修改文件，完毕之后再重新上传



## 9 Git Token

github的token值`6eeb31390b3b0e5359022de4bddcbc418314399b`

sm.ms的token值：`LVl8GWH95m59DdU5VnGqAxlv3wCPQ07W`
