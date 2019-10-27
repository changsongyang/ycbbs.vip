## 引言
`MongoDB` 连接语法使用标准的 `URI` 语法格式

### 启动 MongoDB 服务 ###

经过前面的几章学习，我们知道如何 如何启动 `MongoDB` 服务

```
只需要在 MongoDB 安装目录的 bin 目录下执行 mongod 即可
```

执行启动操作后，`mongodb` 会输出一些必要信息后，然后等待客户端连接的建立，当连接被建立后，就会开始打印日志信息

### MongoDB 客户端 ###

我们可以使用 `MongoDB` 自带的 `shell` 客户端 **mongo** 连接 `MongoDB` 服务

我们也可以使用使用 `PHP` 来连接 `MongoDB`

本教程我们会使用 **mongo** 来连接 `Mongodb` 服务

### 标准 URI 连接语法 ###

```
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]
```

#### 选项说明 ####

<table> 
 <thead> 
  <tr> 
   <th align="left">选项</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">mongodb://</td> 
   <td align="left">这是固定的格式，必须要指定</td> 
  </tr> 
  <tr> 
   <td align="left">username:password@</td> 
   <td align="left">可选项，如果设置，在连接数据库服务器之后，驱动都会尝试登陆这个数据库</td> 
  </tr> 
  <tr> 
   <td align="left">host1</td> 
   <td align="left">必须的指定至少一个host<br>host1 是这个URI唯一要填写的，它指定了要连接服务器的地址<br>如果要连接集群，请指定多个主机地址</td> 
  </tr> 
  <tr> 
   <td align="left">portX</td> 
   <td align="left">可选的指定端口，如果不填，默认为27017</td> 
  </tr> 
  <tr> 
   <td align="left">/database</td> 
   <td align="left">如果指定username:password@，连接并验证登陆指定数据库。若不指定，默认打开 test 数据库</td> 
  </tr> 
  <tr> 
   <td align="left">?options</td> 
   <td align="left">是连接选项。如果不使用/database，则前面需要加上/<br>所有连接选项都是键值对name=value，键值对之间通过&或;（分号）隔开</td> 
  </tr> 
 </tbody> 
</table>

### options 可以包括以下选项 ###

<table> 
 <thead> 
  <tr> 
   <th align="left">选项</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">replicaSet=name</td> 
   <td align="left">验证replica set的名称，Impliesconnect=replicaSet.</td> 
  </tr> 
  <tr> 
   <td align="left">slaveOk=true|false</td> 
   <td align="left">true:在connect=direct模式下，驱动会连接第一台机器，即使这台服务器不是主。在connect=replicaSet模式下，驱动会发送所有的写请求到主并且把读取操作分布在其他从服务器<br><br>false: 在 connect=direct模式下，驱动会自动找寻主服务器. 在connect=replicaSet 模式下，驱动仅仅连接主服务器，并且所有的读写命令都连接到主服务器。</td> 
  </tr> 
  <tr> 
   <td align="left">safe=true|false</td> 
   <td align="left">true: 在执行更新操作之后，驱动都会发送getLastError命令来确保更新成功。(还要参考 wtimeoutMS)<br><br>false: 在每次更新之后，驱动不会发送getLastError来确保更新成功</td> 
  </tr> 
  <tr> 
   <td align="left">w=n</td> 
   <td align="left">驱动添加 </td> 
  </tr> 
  <tr> 
   <td align="left">wtimeoutMS=ms</td> 
   <td align="left">驱动添加 </td> 
  </tr> 
  <tr> 
   <td align="left">fsync=true|false</td> 
   <td align="left">true: 驱动添加 </td> 
  </tr> 
  <tr> 
   <td align="left">journal=true|false</td> 
   <td align="left">如果设置为 true, 同步到 journal (在提交到数据库前写入到实体中). 应用于 safe=true</td> 
  </tr> 
  <tr> 
   <td align="left">connectTimeoutMS=ms</td> 
   <td align="left">可以打开连接的时间</td> 
  </tr> 
  <tr> 
   <td align="left">socketTimeoutMS=ms</td> 
   <td align="left">发送和接受sockets的时间</td> 
  </tr> 
 </tbody> 
</table>

### 范例 ###

1、  连接到本地使用默认端口启动的 `MongoDB` 服务  
      
    
    
```
    mongodb://localhost
```
2、  通过 `shell` 连接 `MongoDB` 服务
    
```
    $ mongo
    MongoDB shell version v3.4.9
    connecting to: mongodb://127.0.0.1:27017
    MongoDB server version: 3.4.9
```
    
    这时候返回查看运行 **./mongod** 命令的窗口 可以看到是从哪里连接到 MongoDB 的服务器，输出如下信息
    
```
    2017-10-23T17:22:27.336+0800 I CONTROL  [initandlisten] allocator: tcmalloc
    2017-10-23T17:22:27.336+0800 I CONTROL  [initandlisten] options: { storage: { dbPath: "/data/db" } }
    2017-10-23T17:22:27.350+0800 I NETWORK  [initandlisten] waiting for connections on port 27017
    2017-10-23T17:22:36.012+0800 I NETWORK  [initandlisten] connection accepted from 127.0.0.1:37310 #1 (1 connection now open)
    
    # 上面该行表明一个来自本机的连接
    
    ……省略信息……
```

## MongoDB 连接语法格式 ##

使用用户名和密码连接到 `MongoDB` 服务器，必须使用以下连接语法

```
username:password@hostname:port/dbname
```

 *  `username` 为用户名
 *  `password` 为密码
 *  `hostname` 为 `IP` 地址
 *  `port` 为端口号，如果省略，默认为 `27017`
 *  `dbname` 为数据库，如果省略，默认连接到 `admin`

### 范例 ###

1、  使用用户名和密码连接登陆到默认数据库  
      
    
    
```
    $ ./mongo
    MongoDB shell version: 3.0.6
    connecting to: test
```
2、  使用用户名 `penglei` 和密码 `123456` 连接到本地的 `MongoDB` 服务上
    
```
    mongodb://admin:123456@localhost/
```
3、  使用用户名和密码连接登陆到指定数据库
    
```
    mongodb://admin:123456@localhost/test
```

### 更多连接范例 ###

1、  连接本地数据库服务器，端口使用默认的
    
```
    mongodb://localhost
```
2、  使用用户名 `penglei`，密码 `123abc321` 登录 `localhost` 的 `admin` 数据库
    
```
    mongodb://penglei:123abc321@localhost
```
3、  使用用户名 `penglei`，密码 `123abc32q` 登录 `localhost` 的 `ycbbs` 数据库
    
```
    mongodb://penglei:123abc321@localhost/souyunku
```
4、  连接到 `MongoDB` 集群, 服务器 1为 `db1.ycbbs.cn` 服务器2 为 `db2.ycbbs.cn`
    
```
    mongodb://db1.ycbbs.cn:27017,db2.ycbbs.cn:27017
```
5、  连接到本地集群，三台服务器端口为 `27017`, `27018`, `27019`
    
```
    mongodb://localhost,localhost:27018,localhost:27019
```
6、  连接集群三台服务器 `host1`, `host2`, `host3` , 写入操作应用在主服务器 并且分布查询到从服务器
    
```
    mongodb://host1,host2,host3/?slaveOk=true
```
7、  直接连接第一个服务器，无论是它是一部分或者主服务器或者从服务器
    
```
    mongodb://host1,host2,host3/?connect=direct;slaveOk=true
```
    
    当连接服务器有优先级，还需要列出所有服务器，可以使用上述连接方式
8、  安全模式连接到 localhost
    
```
    mongodb://localhost/?safe=true
```
9、  以安全模式连接到集群，并且等待至少两个复制服务器成功写入，超时时间设置为 `2` 秒
    
    mongodb://host1,host2,host3/?safe=true;w=2;wtimeoutMS=2000


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")