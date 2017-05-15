Git is a distributed version control system.

Git is free software distributed under the GPL.

Git has a mutable index called stage.

Git tracks changes of files.

感谢[stormzhang](http://stormzhang.com/github/2016/06/19/learn-github-from-zero-summary/)和[廖老师](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
# git学习笔记
## 远程库和Github
### 远程库
- 克隆远程仓库到本地：
`git clone git@server-name:path/repo-name.git`
- 如果本地仓库需要关联远程仓库：
`git remote add origin git@server-name:path/repo-name.git`
	- 关联后，第一次推送时，加上`-u`是为了将本地和远程`master`分支关联起来，方便以后的推送和拉取：
	`git push -u origin master`
- 从本地推送到远程（`origin`）分支：
`git push origin <branch_name>`
- 从远程拉取到本地，一般先pull再push：
`git pull origin <branch_name>`
- 查看所有远程仓库：
`git remote`
	- 显示详细信息：
	`git remote -v`
- 将远程dev分支迁到本地dev分支：
`git checkout -b dev origin/dev`
### Github
- Watch:Watch 了某个项目，那么以后只要这个项目有任何更新，你都会第一时间收到关于这个项目的通知提醒
- Fork(衍生):希望在别人的开源项目上做改进，可以 Fork 到自己的仓库
- Pull request:希望自己的改进能被原作者加入他的仓库，可以发起 Pull request，等待原作者的接受
## 基本
- 初始化一个仓库：
`git init`
- 将a文件添加到暂存区（stage），可以一次add多个文件，文件间用空格隔开：
```
	git add a
	git add b c.md Util.java main.c
```
- 如果需要add被[`.gitignore`](#忽略特殊文件)忽略的文件，加上：`-f`
- 将之前add的所有文件提交到仓库，`-m<message>`表示提交的说明：
`git commit -m "I have a dream..."`
- 查看当前仓库状态：
`git status`
- 查看某个文件的修改情况：
`git diff a.txt`
	- 查看工作区和当前版本（HEAD）某个文件的区别
	`git diff HEAD -- a.txt`
- 查看历史提交信息：
`git log`
	- 简化一行显示，加上：
	`--pretty=oneline`
	- 缩写`commit_id`，加上：
	`--abbrev-commit`
	- 查看分支图，加上：
	`--graph`
	- 查看最后一次提交，加上：
	`-1`
- 查看之前操作的所有命令：
`git reflog`
## 版本穿梭和分支
### 版本穿梭
- `HEAD`表示当前分支版本，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上n个版本可以写成`HEAD~100`
	- 回退/前进到某个版本：
	`git reset --hard <commit_id>`
- 不添加到暂存区的修改是不会提交的
- 丢弃工作区的修改。如果文件已添加到暂存区后又进行修改，那么就回到添加到暂存区的状态；如果文件还未放到暂存区，就回到当前版本：
`git checkout -- a.txt`
- 丢弃暂存区的修改（unstage）：
`git reset HEAD a.txt`
- 删除文件（需要提交到版本库）：
`git rm a.txt`
- 储藏工作区未添加到暂存区/提交的文件，以便之后恢复：
`git stash`
- 查看储藏列表：
`git stash list`
- 恢复最近一次储藏的内容，但恢复后不会删除：
`git stash apply`
	- 恢复某一次储藏内容：
	`git stash apply stash_id`
- 删除储藏内容（删除某一次同上）：
`git stash drop`
- 恢复并删除最近一次储藏内容：
`git stash pop`
### 分支
- 创建并切换到dev分支：
`git checkout -b dev`

	等价于

	```
	git branch dev
	git checkout dev
	```
- 查看本地所有分支，当前分支前会标`*`号：
`git branch`
- 查看远程所有分支：
`git branch -r`
- 合并dev到当前分支（一般是fast forward合并）：
`git merge dev`
	- 普通合并会生成一个提交，从分支图上可以看出合并信息：
	`git merge --no-ff -m "merge using no-ff" dev`
- 删除**合并过的**dev分支：
`git branch -d dev`
- 删除**未合并过的**分支：
`git branch -D <name>`
## 标签
- [标签是一个有意义的名字，与某个版本对应，也方便查找](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013762144381812a168659b3dd4610b4229d81de5056cc000)
- 查看所有标签：
`git tag`
	- 给**最新提交**打标签：
	`git tag <tag_name>`
	- 给**指定提交**打标签：
	`git tag <tag_name> <commit_id>`
	- 创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：
	`git tag -a <tag_name> -m "tag message" [commit_id]`
- 查看指定标签信息：
`git show <tag_name>`
- 删除本地标签：
`git tag -d <tag_name>`
- 推送某个标签到远程：
`git push origin <tag_name>`
	- 推送所有未推送的标签到远程：
	`git push origin --tags`
- 删除远程标签：
	1. 删除本地标签
	2. 删除远程标签：`git push origin :refs/tags/<tag_name>`
## 忽略特殊文件
- [在工作区的根目录下创建一个`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013758404317281e54b6f5375640abbb11e67be4cd49e0000)。
忽略文件的原则是：
	1. 忽略操作系统自动生成的文件，比如缩略图等；
	2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
	3. 忽略带有敏感信息的配置文件，比如存放口令的配置文件。
- [GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了](https://github.com/github/gitignore)
- 配置完成后将`.gitignore`加入版本库
- 检查某个文件是否被忽略：`git check-ignore -v <file>`
## 配置别名
- 用`st`表示`status`：`git config --global alias.st status`
	- `--global`表示全局参数，也就是这些命令应用于这台电脑的所有Git仓库，对应的配置文件为用户主目录下的`.gitconfig`；而每个仓库的配置文件都放在`.git/config`中
	- 用`last`表示`log -1`：`git config --global alias.last 'log -1'`
	- 为了更清楚地展现每次的提交和分支情况，可以：`alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"`

	