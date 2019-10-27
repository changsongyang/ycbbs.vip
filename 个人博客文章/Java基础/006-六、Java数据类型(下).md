## Char类型
`Char` 是字符类型（单个字符）占`2`个字节` 16`位。

单引号用来表示字符常量。例如‘`A`’是一个字符，它与“`A`”是不同的，“`A`”表示含有一个字符的字符串。

`char` 类型用来表示在 `Unicode` 编码表中的字符。

`Unicode` 编码被设计用来处理各种语言的所有文字，它占`2`个字节，可允许有`65536`个字符；`ASCII`码占`1`个字节，可允许有`128`个字符，是`Unicode`编码表中前`128`个字符。
```java
  char eChar = 'a';
                char cChar ='中';
```

`Unicode`具有从`0`到`65535`之间的编码,他们通常用从’`\u0000`’到’`\uFFFF`’之间的十六进制值来表示（前缀为`u`表示`Unicode`）
```java
  char c = ‘\u0061’;
```

`Java` 语言中还允许使用转义字符 ‘`\`’ 来将其后的字符转变为其它的含义，
```java
   char c2 = '\n';  //代表换行符
```
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/6-1.jpg)


## char运算
`Char` 类型是可以运算的因为 `char` 在 `ASCII` 等字符编码表中有对应的数值。

在 `JAVA` 中，对 `char` 类型字符运行时，直接当做 `ASCII` 表对应的整数来对待
```java
	char a = 'a';
	int b = 2;
	int c = a+b;
	System.out.println(c);//答案是99
```
如图比对一下：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/3/6-2.png)
```java
char m='a';　　——a。
char m='a'+'b';　　——Ã。  //char类型相加，提升为int类型，输出对应的字符。注，在CMD.exe用输出结果是问题?，不同的编码输出显示不一样。Eclipse中须改成UTF-8。
int m='a'+'b';　　 ——195。//195没有超出int范围，直接输出195。
char m='a'+b;　　——报错。//因为b是一个赋值的变量。
char m=197;　　——Ã。 //输出字符编码表中对应的字符。
char m='197;　　——报错。//因为有单引号，表示是字符，只允许放单个字符。
char m='a'+1;　　——b。//提升为int，计算结果98对应的字符是b。
char m='中'+'国';　　——42282。
char m='中'+'国'+'国'+'国';　　——报错。int转char有损失。因为结果已经超出char类型的范围。
int m='中'+'国'+'国'+'国';　　——86820
char m='中'+1;　　——丮。//1是int，结果提升为int，输出对应的字符。
char m='中'+"国";　　——报错。String无法转换为char。
```
## 总结：

```java
用单引号''标识，只能放单个字符。
char+char，char+int——类型均提升为int，附值char变量后，输出字符编码表中对应的字符。
boolean类型一位，不是一个字节
boolean类型有两个值，true和false,不可以 0 或非 0 的整数替代 true 和 false ，这点和C语言不同。
boolean bool = true;//或者boolean bool = false;
boolean 类型用来判断逻辑条件，一般用于if、while、do while。
boolean sex = false;

案例一
if(sex==true){
System.out.println("你是男的");
}else{
System.out.println("你是女的");
}
案例二
while(sex){
  ...
}
案例三
do{

}while(sex)
```
写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击此处扫下面二维码关注微信公众号