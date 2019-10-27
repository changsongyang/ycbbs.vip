## 引言
`MongoDB` **ensureIndex** 方法可以用来创建集合的索引

索引通常可以提高查询的效率，如果没有索引，`MongoDB` 在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录

这种扫描全集合的查询效率是非常低的，特别在处理大量的数据时，查询可以要花费几十秒甚至几分钟，这对网站的性能是非常致命的

索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库表中一列或多列的值进行排序的一种结构

## ensureIndex() 方法 ##

`MongoDB` **ensureIndex()** 方法可以用来来创建索引

### 语法 ###

`ensureIndex()` 方法语法格式如下

```
> db.COLLECTION_NAME.ensureIndex({KEY:1})
```

 *  **Key ：**  要创建的索引字段
    
     *  **1** 按升序创建索引
     *  **-1** 按降序来创建索引

### 范例 ###

下面的代码在 `lession` 集合上创建了 **title** 字段升序的索引

```
> db.lession.ensureIndex({"title":1})
```

### 多字段索引 ( 复合索引 ) ###

`MongoDB ensureIndex()` 方法可以用来创建多个字段创建索引 ( 复合索引 )

```
>db.lession.ensureIndex({"title":1,"description":-1})
>
```

### ensureIndex() 方法可以接收可选参数 ###

<table> 
 <thead> 
  <tr> 
   <th align="left">参数名</th> 
   <th align="left">类型</th> 
   <th align="left">描述</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">background</td> 
   <td align="left">Boolean</td> 
   <td align="left">建索引过程会阻塞其它数据库操作，background可指定以后台方式创建索引，即增加 "background" <br> 可选参数。 "background" 默认值为false</td> 
  </tr> 
  <tr> 
   <td align="left">unique</td> 
   <td align="left">Boolean</td> 
   <td align="left">建立的索引是否唯一。指定为true创建唯一索引。默认值为 false</td> 
  </tr> 
  <tr> 
   <td align="left">name</td> 
   <td align="left">string</td> 
   <td align="left">索引的名称。如果未指定，MongoDB的通过连接索引的字段名和排序顺序生成一个索引名称</td> 
  </tr> 
  <tr> 
   <td align="left">dropDups</td> 
   <td align="left">Boolean</td> 
   <td align="left">在建立唯一索引时是否删除重复记录,指定 true 创建唯一索引。默认值为false</td> 
  </tr> 
  <tr> 
   <td align="left">sparse</td> 
   <td align="left">Boolean</td> 
   <td align="left">对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为true的话，在索引字段中不会查询出不包含对应字段的文档.。默认值为 false</td> 
  </tr> 
  <tr> 
   <td align="left">expireAfterSeconds</td> 
   <td align="left">integer</td> 
   <td align="left">指定一个以秒为单位的数值，完成 TTL设定，设定集合的生存时间。</td> 
  </tr> 
  <tr> 
   <td align="left">v</td> 
   <td align="left">index version</td> 
   <td align="left">索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本。</td> 
  </tr> 
  <tr> 
   <td align="left">weights</td> 
   <td align="left">document</td> 
   <td align="left">索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重</td> 
  </tr> 
  <tr> 
   <td align="left">default_language</td> 
   <td align="left">string</td> 
   <td align="left">对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语</td> 
  </tr> 
  <tr> 
   <td align="left">language_override</td> 
   <td align="left">string</td> 
   <td align="left">对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的 language，默认值为 language</td> 
  </tr> 
 </tbody> 
</table>

### 范例 ###

在后台创建索引

```
db.lesssion.ensureIndex({title: 1, by_user: 1}, {background: true})
```

通过在创建索引时加 **background:true** 的选项，让创建工作在后台执行


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")