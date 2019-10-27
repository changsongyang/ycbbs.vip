## 引言
`@Deprecated` 注解很早就存在了，如果我记得没错的话，好像是 `Java 5` ( 后来我去查了资料，也的确是 `Java 5` 就引入了 ) 。

一个使用 `@Deprecated` 注解的元素，无论是一个类或是一个方法，可能是由以下原因导致了不应该再使用它

1、  使用它可能会导致错误
2、  在未来的版本中不被兼容
3、  在未来的版本中可能会被删除
4、  存在更好的更有效的替代方法

如果一个程序或代码片段使用了 `@Deprecated` 注解的元素，那么编译器就会生成一个警告信息，表明这个元素是不被推荐使用的。

我们都一直延续了这样的习惯好久，直到 `Java 9` 的发布，我才发现 `@Deprecated` 注解还可以做的更好

`Java 9` 对 `@Deprecated` 注解做了两项重要的增强。

1、  `forRemoval` - 指示在将来的版本中是否要删除带注解的元素。默认值为 `false`
2、  `since` - 返回注解元素刚添加 `@Deprecated` 注解的版本。

一看不知道，看了很吃惊，这两个属性，还是蛮有作用的

## @Deprecated 的 since 属性 ##

对 `since` 的使用，你可以查阅 `Java 9` 的官方文档中 `Boolean` 类型的文档，在该文档中演示了如何在 `@Deprecated` 注解上使用 `since` 属性

文档地址 [https://docs.oracle.com/javase/9/docs/api/java/lang/Boolean.html\#Boolean-boolean-][https_docs.oracle.com_javase_9_docs_api_java_lang_Boolean.html_Boolean-boolean-]

可以看到下图中粉色框框的内容

![img\_1.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Java9/Java9-image/img_11-1.png)

## @Deprecated 的 forRemoval 属性 ##

关于 `@Deprecated` 的 `forRemoval` 属性的使用，可以查看官方提供的文档中的有关 `System` 类的部分，该部分演示了 `@Deprecated` 注解使用 `forRemoval` 属性

文档地址 [https://docs.oracle.com/javase/9/docs/api/java/lang/System.html\#runFinalizersOnExit-boolean-][https_docs.oracle.com_javase_9_docs_api_java_lang_System.html_runFinalizersOnExit-boolean-]

可以看到下图中粉色框框的内容

![img\_2.png](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Java9/Java9-image/img_11-2.png)


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")