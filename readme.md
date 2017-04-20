Git is a distributed version control system.

Git is free software distributed under the GPL.

Git has a mutable index called stage.

Git tracks changes of files.

Create and switch to a branch.

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
	
