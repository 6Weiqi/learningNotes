Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
Create and switch to a branch.
感谢[stormzhang](http://stormzhang.com/github/2016/06/04/learn-github-from-zero4/)和[廖老师](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
# git学习笔记
- 克隆远程仓库到本地：
`git clone git@server-name:path/repo-name.git`
- 如果本地仓库需要关联远程仓库：
`git remote add origin git@server-name:path/repo-name.git`
	- 关联后，第一次推送时，加上`-u`是为了将本地和远程`master`分支关联起来，方便以后的推送和拉取
	`git push -u origin master`
- 从本地推送到远程（`origin`）的`master`分支
`git push origin master`
- 从远程拉取到本地，一般先pull再push
`git pull origin master`
- 初始化一个仓库：
`git init`
- 将a文件添加到暂存区（stage），可以一次add多个文件，文件间用空格隔开
```
git add a
git add b c.md d.java e.c
```
- 将之前add的所有文件提交到仓库，`-m<message>`表示提交的说明
`git commit -m "I have a dream..."`
- 查看当前仓库状态
`git status`
- 查看某个文件的修改情况
`git diff a.txt`