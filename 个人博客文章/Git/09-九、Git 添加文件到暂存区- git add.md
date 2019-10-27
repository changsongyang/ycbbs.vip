前面我们已经初始化了一个 `Git` 仓库

假设我们已经创建了几个文件，如下

```
$ tree .
.
├── README
└── main.c

0 directories, 2 files
```

现在我们想让这几个文件提交到暂存区，则需要使用 `git add` 命令

### 语法 ###

`git` `add` 语法格式如下

```
$ git add <file>
```

### 范例 ###

比如下面的命令就是把当前目录下的 `README` 和 `main.c` 提交到暂存区

```
$ git add *.c
$ git add README
```

我们可以使用 `git status` 查看暂存区的状态

```
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

    new file:   README
    new file:   main.c
```

可以看到两个文件已经添加到了暂存区，但还没有提交到版本库

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")