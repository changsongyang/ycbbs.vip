MySQL 中众多的数据类型可供选择，大致可以分为三类：数值、日期/时间和字符串(字符)类型。

MySQL 中定义恰当的数据字段的类型对 `MySQL` 数据库的优化是非常重要的

## 数值类型 ##

MySQL 支持所有标准 SQL 数值数据类型

这些类型包括

1、  严格数值数据类型 (`INTEGER` 、`SMALLINT`、`DECIMAL` 和 `NUMERIC` )
2、  近似数值数据类型 (`FLOAT`、`REAL` 和 `DOUBLE PRECISION` )

关键字 `INT` 是 `INTEGER` 的同义词，关键字 `DEC` 是 `DECIMAL` 的同义词

`BIT` 数据类型保存位字段值，并且支持 `MyISAM` 、`MEMORY` 、`InnoDB` 和 `BDB` 等数据库引擎

作为 `SQL` 标准 的扩展，`MySQL` 也支持整数类型 `TINYINT` 、 `MEDIUMINT` 和 `BIGINT`

下表列出了每种整数类型的存储长度和数值范围

<table> 
 <thead> 
  <tr> 
   <th align="left">类型</th> 
   <th align="left">大小</th> 
   <th align="left">范围（有符号）</th> 
   <th align="left">范围（无符号）</th> 
   <th align="left">用途</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">TINYINT</td> 
   <td align="left">1 字节</td> 
   <td align="left">(-128，127)</td> 
   <td align="left">(0，255)</td> 
   <td align="left">小整数值</td> 
  </tr> 
  <tr> 
   <td align="left">SMALLINT</td> 
   <td align="left">2 字节</td> 
   <td align="left">(-32 768，32 767)</td> 
   <td align="left">(0，65 535)</td> 
   <td align="left">大整数值</td> 
  </tr> 
  <tr> 
   <td align="left">MEDIUMINT</td> 
   <td align="left">3 字节</td> 
   <td align="left">(-8 388 608，8 388 607)</td> 
   <td align="left">(0，16 777 215)</td> 
   <td align="left">大整数值</td> 
  </tr> 
  <tr> 
   <td align="left">INT<br>INTEGER</td> 
   <td align="left">4 字节</td> 
   <td align="left">(-2 147 483 648，2 147 483 647)</td> 
   <td align="left">(0，4 294 967 295)</td> 
   <td align="left">大整数值</td> 
  </tr> 
  <tr> 
   <td align="left">BIGINT</td> 
   <td align="left">8 字节</td> 
   <td align="left">(-9 233 372 036 854 775 808，9 223 372 036 854 775 807)</td> 
   <td align="left">(0，18 446 744 073 709 551 615)</td> 
   <td align="left">极大整数值</td> 
  </tr> 
  <tr> 
   <td align="left">FLOAT</td> 
   <td align="left">4 字节</td> 
   <td align="left">(-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38)</td> 
   <td align="left">0，(1.175 494 351 E-38，3.402 823 466 E+38)</td> 
   <td align="left">单精度浮点数值</td> 
  </tr> 
  <tr> 
   <td align="left">DOUBLE</td> 
   <td align="left">8 字节</td> 
   <td align="left">(-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308)</td> 
   <td align="left">0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308)</td> 
   <td align="left">双精度浮点数值</td> 
  </tr> 
  <tr> 
   <td align="left">DECIMAL</td> 
   <td align="left">DECIMAL(M,D)<br>如果M>D，为M+2<br>否则为D+2</td> 
   <td align="left">依赖于M和D的值</td> 
   <td align="left">依赖于M和D的值</td> 
   <td align="left">小数值</td> 
  </tr> 
 </tbody> 
</table>

## 日期和时间类型 ##

表示日期时间的日期和时间类型有 `DATETIME`、`DATE`、`TIMESTAMP`、`TIME`、`YEAR`

每个时间类型有一个有效值范围和一个 **零** 值 当指定不合法的 MySQL 不能表示的值时使用 **零** 值

> TIMESTAMP类型 可以有的自动更新的特性

<table> 
 <thead> 
  <tr> 
   <th>类型</th> 
   <th>大小(字节)</th> 
   <th>范围</th> 
   <th>格式</th> 
   <th>用途</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td>DATE</td> 
   <td>3</td> 
   <td>1000-01-01/9999-12-31</td> 
   <td>YYYY-MM-DD</td> 
   <td>日期值</td> 
  </tr> 
  <tr> 
   <td>TIME</td> 
   <td>3</td> 
   <td>'-838:59:59'/'838:59:59'</td> 
   <td>HH:MM:SS</td> 
   <td>时间值或持续时间</td> 
  </tr> 
  <tr> 
   <td>YEAR</td> 
   <td>1</td> 
   <td>1901/2155</td> 
   <td>YYYY</td> 
   <td>年份值</td> 
  </tr> 
  <tr> 
   <td>DATETIME</td> 
   <td>8</td> 
   <td>1000-01-01 00:00:00/9999-12-31 23:59:59</td> 
   <td>YYYY-MM-DD HH:MM:SS</td> 
   <td>日期时间值</td> 
  </tr> 
  <tr> 
   <td>TIMESTAMP</td> 
   <td>4</td> 
   <td>1970-01-01 00:00:00/2037 年某时</td> 
   <td>YYYYMMDD HHMMSS</td> 
   <td>时间戳</td> 
  </tr> 
 </tbody> 
</table>

## 字符串类型 ##

字符串类型指 `CHAR`、`VARCHAR`、`BINARY`、`VARBINARY`、`BLOB`、`TEXT`、`ENUM` 和 `SET`

下表列出了这些类型可存储字符长度及用途

<table> 
 <thead> 
  <tr> 
   <th align="left">类型</th> 
   <th align="left">大小</th> 
   <th align="left">用途</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td align="left">CHAR</td> 
   <td align="left">0 - 255 字节</td> 
   <td align="left">定长字符串</td> 
  </tr> 
  <tr> 
   <td align="left">VARCHAR</td> 
   <td align="left">0- 65535 字节</td> 
   <td align="left">变长字符串</td> 
  </tr> 
  <tr> 
   <td align="left">TINYBLOB</td> 
   <td align="left">0- 255 字节</td> 
   <td align="left">不超过 255 个字符的二进制字符串</td> 
  </tr> 
  <tr> 
   <td align="left">TINYTEXT</td> 
   <td align="left">0- 255 字节</td> 
   <td align="left">短文本字符串</td> 
  </tr> 
  <tr> 
   <td align="left">BLOB</td> 
   <td align="left">0- 65 535 字节</td> 
   <td align="left">二进制形式的长文本数据</td> 
  </tr> 
  <tr> 
   <td align="left">TEXT</td> 
   <td align="left">0- 65 535 字节</td> 
   <td align="left">长文本数据</td> 
  </tr> 
  <tr> 
   <td align="left">MEDIUMBLOB</td> 
   <td align="left">0 - 16 777 215 字节</td> 
   <td align="left">二进制形式的中等长度文本数据</td> 
  </tr> 
  <tr> 
   <td align="left">MEDIUMTEXT</td> 
   <td align="left">0 - 16 777 215 字节</td> 
   <td align="left">中等长度文本数据</td> 
  </tr> 
  <tr> 
   <td align="left">LONGBLOB</td> 
   <td align="left">0 - 4 294 967 295 字节</td> 
   <td align="left">二进制形式的极大文本数据</td> 
  </tr> 
  <tr> 
   <td align="left">LONGTEXT</td> 
   <td align="left">0 - 4 294 967 295 字节</td> 
   <td align="left">极大文本数据</td> 
  </tr> 
 </tbody> 
</table>

1、  `CHAR` 和 `VARCHAR` 类型类似
    
    但它们保存和检索的方式不同
    
    它们的最大长度和是否尾部空格被保留等方面也不同
    
    在存储或检索过程中不进行大小写转换
2、  `BINARY` 和 `VARBINARY` 类类似于 `CHAR` 和 `VARCHAR`
    
    不同的是它们包含二进制字符串而不要非二进制字符串
    
    也就是说，它们包含字节字符串而不是字符字符串
    
    这说明它们没有字符集，并且排序和比较基于列值字节的数值值
3、  `BLOB` 是一个二进制大对象，可以容纳可变数量的数据
    
    有 4 种 BLOB 类型：`TINYBLOB` 、`BLOB` 、`MEDIUMBLOB` 和 `LONGBLOB`
    
    不同的点只是可容纳值的最大长度不同
4、  有 4 种 `TEXT` 类型：`TINYTEXT`、`TEXT`、`MEDIUMTEXT` 和 `LONGTEXT`
    
    这些对应 4 种 `BLOB` 类型，有相同的最大长度和存储需求

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")