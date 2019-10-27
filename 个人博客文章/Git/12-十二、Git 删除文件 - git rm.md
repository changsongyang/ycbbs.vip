如果只是简单地从工作目录中手工删除文件，运行 `git` `status` 时就显示 `Changes` `not` `staged` `for` `commit` 的提示

要从 `Git` 中移除某个文件，就必须要从已跟踪文件清单中移除，然后提交。可以用以下命令完成此项工作

```
git rm <file>
```

如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项`-f`

```
git rm -f <file>
```

如果把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用`--cached`选项即可

```
git rm --cached <file>
```

如我们删除 `hello.php`文件：

```
$ git rm hello.php 
rm 'hello.php'
$ ls
README
```

不从工作区中删除文件：

```
$ git rm --cached README 
rm 'README'
$ ls
README
```

可以递归删除，即如果后面跟的是一个目录做为参数，则会递归删除整个目录中的所有子目录和文件：

```
git rm –r *
```

进入某个目录中，执行此语句，会删除该目录下的所有文件和子目录。

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")