`Git` 除了可以使用 `git init` 初始化一个仓库外，还可以使用 `git clone` 从现有 `Git` 仓库中拷贝项目

### 语法 ###

`git` `clone` 命令克隆仓库的语法格式如下

```
git clone <repo>
```

如果我们需要克隆到指定的目录，可以使用以下命令格式：

```
git clone <repo> <directory>
```

### 参数说明 ###

<table> 
 <thead> 
  <tr> 
   <th align="left">参数</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">repo</td> 
   <td align="left">Git 仓库</td> 
  </tr> 
  <tr> 
   <td align="left">directory</td> 
   <td align="left">本地目录</td> 
  </tr> 
 </tbody> 
</table>

### 范例 ###

比如，要克隆 `Ruby` 语言的 `Git` 代码仓库 `Grit`，可以用下面的命令：

```
$ git clone git://github.com/schacon/grit.git
```

执行该命令后，会在当前目录下创建一个名为 `grit` 的目录，其中包含一个 `.git` 的目录，用于保存下载下来的所有版本记录

如果要自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：

```
$ git clone git://github.com/schacon/grit.git mygrit
```

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")