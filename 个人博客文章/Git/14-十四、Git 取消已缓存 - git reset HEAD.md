`git` `reset` `HEAD` 命令用于取消已缓存的内容

我们先将 `README` 文件内容修改如下

```
研发军团 (www.ycbbs.vip)
研发军团，教程 
```

`hello.php` 文件修改为：

```
<?php
echo '教程 ：www.ycbbs.vip';
echo '教程 ：www.ycbbs.vip';
echo '教程 ：www.ycbbs.vip';
```

然后将两个修改的文件都提交到了缓存区，我们现在要取消其中一个的缓存，操作如下：

```
$ git status -s
 M README
 M hello.php
$ git add .
$ git status -s
M  README
M  hello.php
$ git reset HEAD -- hello.php 
Unstaged changes after reset:
M    hello.php
$ git status -s
M  README
 M hello.php
```

现在我们执行 `git` `commit`，只会将 `README` 文件的改动提交，而 `hello.php` 是没有的

```
$ git commit -m '修改'
[master f50cfda] 修改
 1 file changed, 1 insertion(+)
$ git status -s
 M hello.php
```

可以看到 `hello.php` 文件的修改并为提交。

这时我们可以使用以下命令将 `hello.php` 的修改提交：

```
$ git commit -am '修改 hello.php 文件'
[master 760f74d] 修改 hello.php 文件
 1 file changed, 1 insertion(+)
$ git status
On branch master
nothing to commit, working directory clean
```

简而言之，执行 `git` `reset` `HEAD` 以取消之前 `git` `add` 添加，但不希望包含在下一提交快照中的缓存


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")