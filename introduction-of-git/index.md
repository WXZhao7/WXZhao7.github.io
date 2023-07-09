# git入门



## 1. 下载和安装git

Git 目前支持 Linux/Unix、Solaris、Mac和 Windows 平台上运行

Git 各平台安装包下载地址为：[Git](https://git-scm.com/downloads)

各个平台的安装参考：[Git安装和配置教程-菜鸟教程](https://www.runoob.com/git/git-install-setup.html)

## 2.Git 配置

首先在本地创建`ssh key；`

```
ssh-keygen -t rsa -C "your_email@youremail.com" [-f /path/file]
```

环境变量，决定了 Git 在各个环节的具体工作方式和行为。这些变量可以存放在以下三个不同的地方：

- `/etc/gitconfig` 文件：系统中对所有用户都普遍适用的配置。若使用 `git config` 时用 `--system` 选项，读写的就是这个文件。
- `~/.gitconfig` 文件：用户目录下的配置文件只适用于该用户。若使用 `git config` 时用 `--global` 选项，读写的就是这个文件。
- 当前项目的 Git 目录中的配置文件（也就是工作目录中的 `.git/config` 文件）：这里的配置仅仅针对当前项目有效。每一个级别的配置都会覆盖上层的相同配置，所以 `.git/config` 里的配置会覆盖 `/etc/gitconfig` 中的同名变量。

### 用户信息

配置个人的用户名称和电子邮件地址：

```
git config --global user.name "runoob"
git config --global user.email test@runoob.com
```

如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里。

```
git config --system：使对应配置针对系统内所有的用户有效
git config --global：使对应配置针对当前系统用户的所有仓库生效
git config --local：使对应配置只针对当前仓库有效
local选项设置的优先级最高。
```

### 文本编辑器

设置Git默认使用的文本编辑器, 一般可能会是 Vi 或者 Vim。如果你有其他偏好，比如 Emacs 的话，可以重新设置：:

```
$ git config --global core.editor emacs
```

### 差异分析工具

还有一个比较常用的是，在解决合并冲突时使用哪种差异分析工具。比如要改用 vimdiff 的话：

```
$ git config --global merge.tool vimdiff
```

### 查看配置信息

要检查已有的配置信息，可以使用 git config --list 命令：

```
$ git config --list
http.postbuffer=2M
user.name=runoob
user.email=test@runoob.com
```

### 实用小技巧
```shell
#内建的图形化 git：
gitk
#彩色的 git 输出：
git config color.ui true
#显示历史记录时，每个提交的信息只显示一行：
git config format.pretty oneline
#交互式添加文件到暂存区：
git add -i
```
## 3.git的逻辑

## 4.git的使用



```shell
find .git/objects/
git log --oneline
git status
git cat-file -t 
git cat-file -p
git rev-parse

git diff
比较工作区和暂存区
 
git diff HEAD
比较工作区和当前分支最新的提交，你可以把HEAD换成别的分支的名字，比如test分支，"git diff test"表示比较当前工作区和test分支最新的提交之间的差异，也可以把HEAD替换成任何一个commit的ID，表示比较当前工作区和对应提交之间的差异。
 
git diff --cached
比较暂存区和当前分支最新的提交
 
上述命令都是比较所有文件的差异，如果想要指定文件，可以使用"--"指定文件的路径，文件路径可以有多个，用空格隔开。
 
git diff -- file1
git diff -- ./file1
只比较工作区和暂存区中file1文件的差异
 
git diff -- file1 file2
只比较工作区和暂存区中file1以及file2文件的差异
 
git diff -- dir1/d1/f1
只比较工作区和暂存区中dir1/d1/f1文件的差异
 
git diff -- dir1/
只比较工作区和暂存区中dir1目录中所有文件的差异
 
git diff HEAD -- ./file1
只比较工作区和当前分支最新的提交中file1文件的差异，HEAD可以替换成分支名或者commitID
 
git diff testbranch -- ./file1
只比较工作区和testbranch分支最新的提交中file1文件的差异
 
git diff --cached testbranch
比较暂存区和testbranch分支最新的提交
 
git diff --cached testbranch --./file1
只比较暂存区和testbranch分支最新的提交中file1文件的差异
 
git diff HEAD~ HEAD
比较当前分支中最新的两个提交之间的差异
 
git diff HEAD~ HEAD -- file1
比较当前分支中最新的两个提交中的file1文件的差异
 
git diff commitID1 commitID2
比较两个commit之间的差异
git diff commitID1..commitID2
同上，比较两个commit之间的差异，两个命令等效
 
git diff branch1 branch2
比较两个分支上最新提交之间的差异
git diff branch1..branch2
同上，比较两个分支上最新提交之间的差异，两个命令等效
```


