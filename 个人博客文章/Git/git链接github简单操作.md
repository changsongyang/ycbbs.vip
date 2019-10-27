## git基本操作
1.记住在你要创建项目的文件夹下右键`Git Bash Here`,打开命令行
2.在命令行里创建一个项目：`mkdir` 项目名称
3.进入当前创建的项目文件夹里：`cd` 项目名称
4.初始化项目:`git` `init`,显示样式 `Initialized empty Git repository in D:/jsTest/gittest/WeChat/.git/`，表示初始化成功
- 查看创建成功的隐藏目录：`ls` `-lA`
5.设置签名信息
```shell
git config user.name tom_pro(名称随便取)
git config user.email goodMorning_pro@dcc.com(名称随便取)
```
6.查看签名是否成功
```shell
cat .git/config
```
7.添加系统签名
```shell
git config --global user.name tom_glb(名称随便取)
git config --global user.email goodMorning_glb@dcc.com(名称随便取)
```
8.查看状态
```shell
git status
			命令显示为：
				On branch master

				No commits yet

				Untracked files:
				(use "git add <file>..." to include in what will be committed)

					goods.txt//表示没有提交到暂存区文件

				nothing added to commit but untracked files present (use "git add" to track)
```
9.提交暂存区(添加/更新)
```shell
git add goods.txt(文件名)
```

10.从暂存区删除文件
```shell
git rm --cached goods.txt(文件名)
```

11.提交到本地库
```shell
git commit -m "要提交的信息" goods.txt（文件名）
```

12.查看提交的文件信息
```shell
cat goods.txt(文件名)
```
13.撤销提交
```shell
git checkout --goods.txt(文件名) 
```
14.查看历史记录
```shell
git log --oneline或者 git reflog(推荐)
```
查看下一页直接空格即可

16.选择进入那个时段的版本	
```shell
git reset --hard c2201b0（索引号,使用reflog可以查看到进入的版本号）
```
17.找回删除之后的文件
```shell
rm 文件名
```
删除之后还需要再次提交操作（`git` `add` 文件名，并且 `git commit -m"删除信息" 文件名称`）
然后返回到删除前的版本就找回了删除的文件

18.查看文件差异
```shell
git diff或者git diff HEAD
```
## git 分支管理
1.查看所有分支
```shell
git branch -v
```
2.创建分支hot_fix
```shell
git branch hot_fix
```
修改文件以及提交文件都照常进行

3.切换分支
```shell
git checkout hot_fix
```
**4.将创建的分支合并到主干分支**
4.1必须要切换到主干分支`marster`
```shell
git checkout marster
```
4.2合并分支
```shell
git merge hot_fix
```
表示将`hot_fix`分支合并到`master`分支上
示例
```shell
$ git branch -v
hot_fix 546506d 我是hot_fix第一次提交
* master  546506d 我是hot_fix第一次提交
```

**5.解决分支冲突**
5.1首先修改2个分支的相同的文件，然后将`hot_fix`分支以及`master`分支上的文件都提交到本地库中
```shell
git add 文件名,git commit 文件名
```
5.2然后需要将`hot_fix`分支合并到`master`分支上。
		切换到`master`主干分支上：`git checkout master`
		开始合并:`git merge hot_fix`，这个时候会有冲突，这个时候进入到要合并的文件里面解决冲突。
		解决完成之后就可以`git add` 文件名,随后就`git commit -m"描述"`。
		注意：这个时候`git commit`就不要加文件名了

## git本地库与远程库交互

### 1.如何将本地仓库提交到远程仓库中
1.1，在本地创建一个文件夹，然后初始化成`git`仓库.
		`mkdir` 仓库名称
		进入仓库路径之后`git init`
		然后在仓库中创建文件即可

1.2，在本地仓库设置远程仓库的地址
		`git remote -v`,查看是否设置的远程仓库的地址
```shell
git remote add origin（远程地址的别名） https://github.com/duchaochen/ycbbs-web-view.git(远程仓库的地址)
```
1.3，分支推送到远程库
```shell
git push origin(远程地址的别名) master(要推送的是哪个分支)
```

### 2.协作开发（如果有其他人也需要来一起协作开发）
2.1，将远程的仓库克隆到本地(如果是克隆的情况就不需要做1的所有操作了)
```shell
git clone https://github.com/duchaochen/WeChat.git(远程仓库的地址)
```
2.1，使用`git remote -v`查看是否克隆成功
2.3，然后操作一致，推送，拉取操作都是一样的，不过需要加入团队,需要在`github`上加入成员

### 3.拉取文件
3.1，拉取远程master仓库中的文件
```shell
git fetch origin(远程地址的别名) master(要拉取的分支)
```
3.2，拉取之后需要切换到远程的master仓库中查看
```shell
git checkout origin/master(切换远程仓库)
```
3.3，然后将远程库的文件合并到本地文件
```shell
git merge origin/master,然后修改文件
然后推送：git push origin(远程地址的别名) master(要推送的是哪个分支)
```
3.4，如果觉得远程仓库中没有冲突文件直接使用下面命令即可
```shell
git pull origin master
```



希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")