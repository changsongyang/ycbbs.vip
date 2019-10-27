前面章节我们已经把 `README` 和 `main.c` 文件从工作区添加到了暂存区

假设我们已经完成了开发任务，需要把暂存区的文件提交到版本库

使用 `git commit` 命令可以把当前暂存区的文件提交到版本库

### 语法 ###

git commit 命令语法格式如下

```
$ git commit -m "<本次提交说明信息>"
```

### 范例 ###

使用以下的命令可以把 `README` 和 `main.c` 文件提交到版本库

```
$ git commit -m "初始化项目"
```

输出结果如下

```
$ git commit -m "初始化项目" 
[master (root-commit) b8af03d] 初始化项目
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 README
 create mode 100644 main.c
```

使用 `git status` 查看当前项目状态，显示如下

```
$ git status
On branch master
nothing to commit, working tree clean
```

使用 `git log --pretty=oneline` 可以查看我们刚刚的提交

```
$ git log --pretty=oneline
b8af03dfcae9d4c69c6395cfc3abb0c2d92a4b38 (HEAD -> master) 初始化项目
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")