`Memcached` `flush_all` 命令用于清空缓存。

也就是删除缓存中的所有 **key=>value(键=>值)** 对

## 语法 ##

```
flush_all [time] [noreply]
```

 *  **time** 参数是可选的，单位秒。 如果设置了值，则表示 `Memcached` 将在这个时间后才执行清空操作
 *  **noreply** 参数是可选的，如果设置了值，则表示 `Memcached` 服务不用返回信息

> flush\_all noreply 命令没有任何效果的,因为它本来就没数据返回

## 范例 ##

#### 1 . 不带任何参数 ####

```
set site 0 1000 11
www.ycbbs.vip
STORED
get site
VALUE site 0 11
www.ycbbs.vip
END
flush_all
OK
get site
END
```

#### 2. `10s` 后清空缓存 ####

```
set site 0 1000 11
www.ycbbs.vip
STORED
get site
VALUE site 0 11
www.ycbbs.vip
END
flush_all 10  # 设置 10秒后清空缓存
OK
get site   # 立刻获取有返回
VALUE site 0 11
www.ycbbs.vip
END
get site  # 等待10s后获取则没有返回
END
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
