> 原文出自：[http://cmsblogs.com](http://cmsblogs.com)

----


从 6 月份开始写 Spring IOC 源码解析，一直写到 12 月才结束，半年时间一共写了 42 篇文章，基本上每个周末都总有一天是坐在电脑面前写博客，虽然过程比较苦逼，但收获还是挺多的，比如又复习了设计模式、CGLIB、动态代理等等一系列的知识点，小编非常喜欢下面这句话：

> 知识的广度来自知识的深度

Spring 是一个非常优秀的框架，具有良好的结构设计和接口抽象，它的每一个接口都是其功能具体到各个模块中的高度抽象，实际使用过程中相当于把接口的各个实现类按照接口所提供的组织架构装配起来以提供完整的服务。同时 Spring 对外提供了非常多的扩展点，每一个扩展点都值得我们去认真研究。

【死磕 Spring】之 IOC 系列一共有 42 偏文章，整个 IOC 的核心部分基本上都介绍了一遍，读完该系列，你一定会对 Spring IOC 有一个彻底的了解。整个目录如下：

1. [【死磕 Spring】—– IOC 之深入理解 Spring IoC](http://cmsblogs.com/?p=2652)
2. [【死磕 Spring】—– IOC 之 Spring 统一资源加载策略](http://cmsblogs.com/?p=2656)
3. [【死磕 Spring】—– IOC 之 加载 Bean](http://cmsblogs.com/?p=2658)
4. [【死磕 Spring】—– IOC 之 获取验证模型](http://cmsblogs.com/?p=2688)
5. [【死磕 Spring】—– IOC 之 注册 BeanDefinition](http://cmsblogs.com/?p=2697)
6. [【死磕 Spring】—– IOC 之 获取 Document 对象](http://cmsblogs.com/?p=2695)
7. [【死磕 Spring】—– IOC 之解析Bean：解析 import 标签](http://cmsblogs.com/?p=2724)
8. [【死磕 Spring】—– IOC 之解析 bean 标签：开启解析进程](http://cmsblogs.com/?p=2731)
9. [【死磕 Spring】—– IOC 之解析 bean 标签：BeanDefinition](http://cmsblogs.com/?p=2734)
10. [【死磕 Spring】—– IOC 之解析 bean 标签：meta、lookup-method、replace-method](http://cmsblogs.com/?p=2736)
11. [【死磕 Spring】—– IOC 之解析 bean 标签：constructor-arg、property 子元素](http://cmsblogs.com/?p=2754)
12. [【死磕 Spring】—– IOC 之解析 bean 标签：解析自定义标签](http://cmsblogs.com/?p=2756)
13. [【死磕 Spring】—– IOC 之解析自定义标签](http://cmsblogs.com/?p=2841)
14. [【死磕 Spring】—– IOC 之注册解析的 BeanDefinition](http://cmsblogs.com/?p=2763)
15. [【死磕 Spring】—– IOC 之 IOC 初始化总结](http://cmsblogs.com/?p=2790)
16. [【死磕 Spring】—– IOC 之开启 bean 的加载](http://cmsblogs.com/?p=2806)
17. [【死磕 Spring】—– IOC 之从单例缓存中获取单例 bean](http://cmsblogs.com/?p=2808)
18. [【死磕 Spring】—– IOC 之parentBeanFactory 与依赖处理](http://cmsblogs.com/?p=2810)
19. [【死磕 Spring】—– IOC 之分析各 scope 的 bean 创建](http://cmsblogs.com/?p=2839)
20. [【死磕 Spring】—– IOC 之开启 bean 的实例化进程](http://cmsblogs.com/?p=2846)
21. [【死磕 Spring】—– IOC 之 Factory 实例化 bean](http://cmsblogs.com/?p=2848)
22. [【死磕 Spring】—– IOC 之构造函数实例化 bean](http://cmsblogs.com/?p=2850)
23. [【死磕 Spring】—– IOC 之 属性填充](http://cmsblogs.com/?p=2885)
24. [【死磕 Spring】—– IOC 之循环依赖处理](http://cmsblogs.com/?p=2887)
25. [【死磕 Spring】—– IOC 之 bean 的初始化](http://cmsblogs.com/?p=2890 )
26. [【死磕 Spring】—– IOC 之加载 bean：总结](http://cmsblogs.com/?p=2905)
27. [【死磕 Spring】—– IOC 之 深入分析 Aware 接口](http://cmsblogs.com/?p=3335)
28. [【死磕 Spring】—– IOC 之 深入分析 BeanPostProcessor](http://cmsblogs.com/?p=3338)
29. [【死磕 Spring】—– IOC 之 深入分析 InitializingBean 和 init-method](http://cmsblogs.com/?p=3340)
30. [【死磕 Spring】—– IOC 之 深入分析 BeanFactoryPostProcessor](http://cmsblogs.com/?p=3342)
31. [【死磕 Spring】—– IOC 之 深入分析 PropertyPlaceholderConfigurer](http://cmsblogs.com/?p=3837)
32. [【死磕 Spring】—– IOC 之 PropertyPlaceholderConfigurer 的应用](http://cmsblogs.com/?p=3839)
33. [【死磕 Spring】—– IOC 之深入分析 PropertyOverrideConfigurer](http://cmsblogs.com/?p=3924)
34. [【死磕 Spring】—– IOC 之深入分析 Bean 的类型转换体系](http://cmsblogs.com/?p=3983)
35. [【死磕 Spring】—– IOC 之自定义类型转换器](http://cmsblogs.com/?p=3985)
36. [【死磕 Spring】—– IOC 之分析 BeanWrapper](http://cmsblogs.com/?p=4020)
37. [【死磕 Spring】—– IOC 之 bean 的实例化策略：InstantiationStrategy](http://cmsblogs.com/?p=4022)
38. [【死磕 Spring】—– IOC 之 BeanDefinition 注册机：BeanDefinitionRegistry](http://cmsblogs.com/?p=4026)
39. [【死磕 Spring】—– Spring 的环境&属性：PropertySource、Environment、Profile](http://cmsblogs.com/?p=4032)
40. [【死磕 Spring】—– IOC 之 分析 bean 的生命周期](http://cmsblogs.com/?p=4034)
41. [【死磕 Spring】—– ApplicationContext 相关接口架构分析](http://cmsblogs.com/?p=4036)
42. [【死磕 Spring】—– 深入分析 ApplicationContext 的 refresh()](http://cmsblogs.com/?p=4043)
43. [【死磕 Spring】—– 4 张图带你读懂 Spring IOC 的世界](http://cmsblogs.com/?p=4045)

小编曾经想花三、四年时间写完 Spring，包括 IOC、AOP、MVC、transactional、Spring Boot，可惜计划赶不上变化，19 年工作重点在大数据方向，而小编对大数据那是七窍通了六窍，只有一窍尚未开通，所以 19 年的空闲时间都会交给大数据了，毕竟饭碗为重。

**最后**

目前【死磕 Java】系列一共完成了 4 个：

- [死磕 Java 并发](http://cmsblogs.com/?p=2611)：http://cmsblogs.com/?p=2611
- [死磕 Tomcat](http://cmsblogs.com/?p=2686)：http://cmsblogs.com/?p=2686
- [死磕 Sharding-jdbc](http://cmsblogs.com/?p=2648)：http://cmsblogs.com/?p=2648
- [死磕 Spring](http://cmsblogs.com/?cat=206)：http://cmsblogs.com/?cat=206

【死磕 Java 并发】、【死磕 Spring】为小编原创，【死磕 Sharding-jdbc】为阿飞哥原创，【死磕 Tomcat】为晓峰大神原创。[https://www.jianshu.com/u/6779ec81d3b7](https://www.jianshu.com/u/6779ec81d3b7) 是阿飞哥简书，欢迎各位关注，晓峰大神没有博客，各位就不要关注了。

【死磕 Redis】 还在整理创作中，敬请期待。
