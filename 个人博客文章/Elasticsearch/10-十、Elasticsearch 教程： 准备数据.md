上一章节我们体验了下结巴分词器，对中文的分词效果真的好好啊，是不是跃跃欲试，想要赶紧怎么把自己的数据添加到 `Elasticsearch`

别急别急，在添加数据之前我们必须先要对自己的数据进行格式化

本章节我们就来讨论如何格式化数据

对于搜云库技术团队 的搜索功能，我初步的计划是把用户信息放到 `Elasticsearch` 上，于是我对此做了一些简单的分析

## 索引 ##

网站的用户分为两大类，版主以上级别的用户和普通注册的用户

根据前面所学，我们知道 `Elasticsearch` 也有数据库、表结构和行数据的概念，分别是索引、映射和文档

所以我们首先需要创建两个索引 `user_admin` 和 `user`，分别用于索引版主和普通用户

而且它们有功能的类型 `type`，都是 `user`

## 字段 ##

因为具有相同的类型，所以我们的版主和普通用户的信息几乎是一模一样的，都包括以下字段

<table> 
 <thead> 
  <tr> 
   <th align="left">字段</th> 
   <th align="left">类型</th> 
   <th align="left">说明</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">id</td> 
   <td align="left">int</td> 
   <td align="left">ID</td> 
  </tr> 
  <tr> 
   <td align="left">nickname</td> 
   <td align="left">string</td> 
   <td align="left">昵称</td> 
  </tr> 
  <tr> 
   <td align="left">description</td> 
   <td align="left">string</td> 
   <td align="left">用户简介</td> 
  </tr> 
  <tr> 
   <td align="left">street</td> 
   <td align="left">string</td> 
   <td align="left">当前居住街道</td> 
  </tr> 
  <tr> 
   <td align="left">city</td> 
   <td align="left">string</td> 
   <td align="left">当前所在城市</td> 
  </tr> 
  <tr> 
   <td align="left">state</td> 
   <td align="left">string</td> 
   <td align="left">当前所在省</td> 
  </tr> 
  <tr> 
   <td align="left">zip</td> 
   <td align="left">int</td> 
   <td align="left">邮政编码</td> 
  </tr> 
  <tr> 
   <td align="left">location</td> 
   <td align="left">array</td> 
   <td align="left">地理位置，两个元素数组，第一个表示经度，第二个表示纬度</td> 
  </tr> 
  <tr> 
   <td align="left">money</td> 
   <td align="left">int</td> 
   <td align="left">当前站币</td> 
  </tr> 
  <tr> 
   <td align="left">tags</td> 
   <td align="left">array</td> 
   <td align="left">标签</td> 
  </tr> 
  <tr> 
   <td align="left">vitality</td> 
   <td align="left">float</td> 
   <td align="left">活跃度，满分为 10 分</td> 
  </tr> 
 </tbody> 
</table>

## 数据 ##

我们需要把数据库中用户相关的所有数据读出来，然后筛选出上面的字段，拼接成多个 JSON 对象

1、版主 ( user\_admin )
    
```
    [
     {
        "id":1,
        "nickname":"站长",
        "description":"创业是的天赋是天生的，而我偏偏是后生的", "street":"东四十条",
        "city":"Beijing",
        "state":"Beijing",
        "zip":"100007",
        "location":[116.432727,39.937732],
        "money":5201314,
        "tags":["PHP", "Python"],
        "vitality":"9.0"
     },
    
     {
        "id":2,
        "nickname":"雅少",
        "description":"虚怀若谷",
        "street":"四川大学",
        "city":"Chengdu",
        "state":"Sichuan",
        "zip":"610044",
        "location":[104.094537,30.640174],
        "money":68023,
        "tags":["Python", "HTML"],
        "vitality":"7.8"
     },
     {
        "id":3,
        "nickname":"歌者",
        "description":"程序设计也是设计，研发新菜也是研发",
        "street":"五道口",
        "city":"Beijing",
        "state":"Beijing",
        "zip":"100083",
        "location":[116.346346,39.999333],
        "money":71128,
        "tags":["Java", "Scala"],
        "vitality":"6.9"
     }
    ]
```
2、普通用户
    
```
    [
     {
        "id":1,
        "nickname":"question", 
        "description":"问题少年也是少年",
        "street":"张江高科技园区",
        "city":"Shanghai",
        "state":"Shanghai",
        "zip":"201204",
        "location":[121.60632,31.199305],
        "money":13648,
        "tags":["VUE", "HTML"],
        "vitality":"8.8"
     },
    
     {
        "id":2,
        "nickname":"枫晚",
        "description":"停车坐爰枫林晚",
        "street":"苏州大学",
        "city":"Suzhou",
        "state":"Jiangsu",
        "zip":"215006",
        "location":[120.65426,31.30797],
        "money":10235,
        "tags":["Java", "Android"],
        "vitality":"3.5"
     }
    ]
```


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")
