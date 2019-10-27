`git` `mv` 命令用于移动或重命名一个文件、目录、软连接

### 语法 ###

git mv 命令语法格式如下

```
git mv <old_file> <new_file>
```

### 范例 ###

假如当前项目版本库中有如下文件

```
$ ls 
README  main.c
```

现在我们想把 `README` 文件重命名为 `README.md` 则可以使用下面的命令

```
$ git mv README README.md
$ ls
README.md  main.c
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README -> README.md

(spider) 
```

我们可以看到，重命名后文件会放入暂存区，需要使用 `git` `commit` 命令提交到仓库


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")