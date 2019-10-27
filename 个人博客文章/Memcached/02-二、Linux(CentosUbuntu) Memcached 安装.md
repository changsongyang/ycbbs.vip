`Memcached` 支持许多平台：`Linux、FreeBSD、Centos、Ubuntu 、Solaris、Mac OS`

当然也支持安装在 `Windows` 上

`Linux` 系统安装 `Memcached`，首先要先安装 `libevent` 库

## 安装 libevent ##

#### Ubuntu / Debian 系统 ####

```
sudo apt-get install libevent libevent-devel
```

#### Redhat / Fedora / Centos 系统 ####

```
yum install libevent libevent-devel
```

#### FreeBSD 系统 ####

```
portmaster databases/libevent databases/libevent-devel
```

#### MacOS 系统 ####

```
brew install libevent
```

## 安装 Memcached ##

### 使用软件管理器自动安装 ###

#### Ubuntu / Debian 自动安装 ####

```
sudo apt-get install memcached
```

#### Redhat / Fedora / Centos 自动安装 ####

```
yum install memcached
```

#### FreeBSD 自动安装 ####

```
portmaster databases/memcached
```

#### MacOS 使用 brew 安装 ####

```
brew install libmemcached memcached
```

### Linux 源代码安装 ###

从 [ 官方网站 (http://memcached.org)][_ _http_memcached.org] 下载 Memcached 最新版本

> 截止今日，最新版本地址为 http://memcached.org/files/memcached-1.5.1.tar.gz

以下命令要一条一条执行，忽略 \# 开头的注释

```
# 下载最新版本
wget http://memcached.org/files/memcached-1.5.1.tar.gz

# 解压缩
tar -zxvf memcached-1.5.1.tar.gz 

# 进入目录
cd memcached-1.5.1

# 配置编译文件
./configure --prefix=/usr/local/memcached

# 编译
make && make test

# 安装
sudo make install
```

--------------------

## 运行 Memcached 服务 ##

#### 用 which 命令查看 `memcached` 位置 ####

```
$ which memcached
```

输出结果

```
/usr/local/bin/memcached
```

`Memcached` 命令的运行：

#### 查看 Memcached 启动选项 ####

```
$ /usr/local/bin/memcached -h 
```

**启动选项：** 概览

 *  \-d 是启动一个守护进程
 *  \-m 是分配给Memcache使用的内存数量，单位是MB
 *  \-u 是运行Memcache的用户
 *  \-l 是监听的服务器IP地址，可以有多个地址
 *  \-p 是设置Memcache监听的端口，默认是 11211 ，最好是1024以上的端口
 *  \-c 是最大运行的并发连接数，默认是 1024
 *  \-P 是设置保存Memcache的pid文件

### Memcached 作为前台程序运行： ###

从终端输入以下命令，启动 memcached:

```
/usr/local/bin/memcached -p 11211 -m 128m
```

当然你可以使用 `-vv` 来显示调试信息， `v` 越多显示的调试信息就越多

```
/usr/local/bin/memcached -p 11211 -m 128m -vv
```

输出

```
slab class   1: chunk size        96 perslab   10922
slab class   2: chunk size       120 perslab    8738
slab class   3: chunk size       152 perslab    6898
slab class   4: chunk size       192 perslab    5461
slab class   5: chunk size       240 perslab    4369
slab class   6: chunk size       304 perslab    3449
slab class   7: chunk size       384 perslab    2730
slab class   8: chunk size       480 perslab    2184
slab class   9: chunk size       600 perslab    1747
slab class  10: chunk size       752 perslab    1394
slab class  11: chunk size       944 perslab    1110
slab class  12: chunk size      1184 perslab     885
slab class  13: chunk size      1480 perslab     708
slab class  14: chunk size      1856 perslab     564
slab class  15: chunk size      2320 perslab     451
slab class  16: chunk size      2904 perslab     361
slab class  17: chunk size      3632 perslab     288
slab class  18: chunk size      4544 perslab     230
slab class  19: chunk size      5680 perslab     184
slab class  20: chunk size      7104 perslab     147
slab class  21: chunk size      8880 perslab     118
slab class  22: chunk size     11104 perslab      94
slab class  23: chunk size     13880 perslab      75
slab class  24: chunk size     17352 perslab      60
slab class  25: chunk size     21696 perslab      48
slab class  26: chunk size     27120 perslab      38
slab class  27: chunk size     33904 perslab      30
slab class  28: chunk size     42384 perslab      24
slab class  29: chunk size     52984 perslab      19
slab class  30: chunk size     66232 perslab      15
slab class  31: chunk size     82792 perslab      12
slab class  32: chunk size    103496 perslab      10
slab class  33: chunk size    129376 perslab       8
slab class  34: chunk size    161720 perslab       6
slab class  35: chunk size    202152 perslab       5
slab class  36: chunk size    252696 perslab       4
slab class  37: chunk size    315872 perslab       3
slab class  38: chunk size    394840 perslab       2
slab class  39: chunk size    493552 perslab       2
slab class  40: chunk size    616944 perslab       1
slab class  41: chunk size    771184 perslab       1
slab class  42: chunk size   1048576 perslab       1
<17 server listening (auto-negotiate)
<18 server listening (auto-negotiate)
<19 send buffer was 9216, now 3728270
<19 server listening (udp)
<20 server listening (udp)
<23 send buffer was 9216, now 3728270
<22 server listening (udp)
<21 server listening (udp)
<25 server listening (udp)
<23 server listening (udp)
<24 server listening (udp)
<26 server listening (udp)
```

我们看到 128m 的内存被分成了 42 个 slab

`-vv` 是显示了调试信息，调试信息的内容大部分是关于存储的信息

### Memcached 作为后台服务程序运行： ###

```
$ /usr/local/bin/memcached -p 11211 -m 128m -d
```

或者

```
/usr/local/bin/memcached -d -m 64M -u nobody -l 192.168.0.8 -p 11211 -c 256 -P /tmp/memcached.pid
```

> 建议不要使用 -u root

## Memcached 更多启动参数 ##

<table> 
 <thead> 
  <tr> 
   <th align="left">命令</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">-p <num></td> 
   <td align="left">监听的TCP端口(默认: 11211)</td> 
  </tr> 
  <tr> 
   <td align="left">-U <num></td> 
   <td align="left">监听的UDP端口(默认: 11211, 0表示不监听)</td> 
  </tr> 
  <tr> 
   <td align="left">-s <file></td> 
   <td align="left">用于监听的UNIX套接字路径（禁用网络支持）</td> 
  </tr> 
  <tr> 
   <td align="left">-a <mask></td> 
   <td align="left">UNIX套接字访问掩码，八进制数字（默认：0700）</td> 
  </tr> 
  <tr> 
   <td align="left">-l <ip_addr></td> 
   <td align="left">监听的IP地址。（默认：INADDR_ANY，所有地址）</td> 
  </tr> 
  <tr> 
   <td align="left">-d</td> 
   <td align="left">作为守护进程来运行</td> 
  </tr> 
  <tr> 
   <td align="left">-r</td> 
   <td align="left">最大核心文件限制</td> 
  </tr> 
  <tr> 
   <td align="left">-u <username></td> 
   <td align="left">设定进程所属用户。（只有root用户可以使用这个参数）</td> 
  </tr> 
  <tr> 
   <td align="left">-m <num></td> 
   <td align="left">所有slab class可用内存的上限，以MB为单位。（默认：64MB）<br>（译者注：也就是分配给该memcached实例的内存大小。）</td> 
  </tr> 
  <tr> 
   <td align="left">-M</td> 
   <td align="left">内存用光时报错。（不会删除数据）</td> 
  </tr> 
  <tr> 
   <td align="left">-c <num></td> 
   <td align="left">最大并发连接数。（默认：1024）</td> 
  </tr> 
  <tr> 
   <td align="left">-k</td> 
   <td align="left">锁定所有内存页。注意你可以锁定的内存上限。<br>试图分配更多内存会失败的，所以留意启动守护进程时所用的用户可分配的内存上限。<br>不是前面的 -u <username> 参数；在sh下，使用命令"ulimit -S -l NUM_KB"来设置</td> 
  </tr> 
  <tr> 
   <td align="left">-v</td> 
   <td align="left">提示信息（在事件循环中打印错误/警告信息。）</td> 
  </tr> 
  <tr> 
   <td align="left">-vv</td> 
   <td align="left">详细信息（还打印客户端命令/响应）</td> 
  </tr> 
  <tr> 
   <td align="left">-vvv</td> 
   <td align="left">超详细信息（还打印内部状态的变化）</td> 
  </tr> 
  <tr> 
   <td align="left">-h</td> 
   <td align="left">打印这个帮助信息并退出</td> 
  </tr> 
  <tr> 
   <td align="left">-i</td> 
   <td align="left">打印memcached和libevent的许可</td> 
  </tr> 
  <tr> 
   <td align="left">-P <file></td> 
   <td align="left">保存进程ID到指定文件，只有在使用 -d 选项的时候才有意义</td> 
  </tr> 
  <tr> 
   <td align="left">-f <factor></td> 
   <td align="left">不同slab class里面的chunk大小的增长倍率。（默认：1.25）<br>译者注：每个slab class里面有相同数量个slab page，每个slab page里面有chunk，且在当前slab class内的chunk大小固定。<br>而不同slab class里的chunk大小不一致，具体差异就是根据这个参数的倍率在增长，直到分配的内存用尽</td> 
  </tr> 
  <tr> 
   <td align="left">-n <bytes></td> 
   <td align="left">chunk的最小空间（默认：48）<br>chunk数据结构本身需要消耗48个字节，所以一个chunk实际消耗的内存是n+48</td> 
  </tr> 
  <tr> 
   <td align="left">-L</td> 
   <td align="left">尝试使用大内存页（如果可用的话）<br>提高内存页尺寸可以减少"页表缓冲（TLB）"丢失次数，提高运行效率<br>为了从操作系统获得大内存页，memcached会把全部数据项分配到一个大区块</td> 
  </tr> 
  <tr> 
   <td align="left">-D <char></td> 
   <td align="left">使用 <char> 作为前缀和ID的分隔符<br>这个用于按前缀获得状态报告。默认是":"（冒号）<br>如果指定了这个参数，则状态收集会自动开启；如果没指定，则需要用命令"stats detail on"来开启。</td> 
  </tr> 
  <tr> 
   <td align="left">-t <num></td> 
   <td align="left">使用的线程数（默认：4）</td> 
  </tr> 
  <tr> 
   <td align="left">-R</td> 
   <td align="left">每个连接可处理的最大请求数</td> 
  </tr> 
  <tr> 
   <td align="left">-C</td> 
   <td align="left">禁用CAS</td> 
  </tr> 
  <tr> 
   <td align="left">-b</td> 
   <td align="left">设置后台日志队列的长度（默认：1024）</td> 
  </tr> 
  <tr> 
   <td align="left">-B</td> 
   <td align="left">绑定协议 - 可能值：ascii,binary,auto（默认）</td> 
  </tr> 
  <tr> 
   <td align="left">-I</td> 
   <td align="left">重写每个数据页尺寸。调整数据项最大尺寸</td> 
  </tr> 
 </tbody> 
</table>


[_ _http_memcached.org]: http://memcached.org/downloads


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")