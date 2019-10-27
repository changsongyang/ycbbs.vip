## 1. 简介
本篇文章是“`Spring` `IOC` 容器源码分析”系列文章的最后一篇文章，本篇文章所分析的对象是 `initializeBean` 方法，该方法用于对已完成属性填充的 `bean` 做最后的初始化工作。

相较于之前几篇文章所分析的源码，`initializeBean` 的源码相对比较简单，大家可以愉快的阅读。好了，其他的不多说了，我们直入主题吧。

## 2. 源码分析
本章我们来分析一下 `initializeBean` 方法的源码。在完成分析后，还是像往常一样，把方法的执行流程列出来。好了，看源码吧：
```java
protected Object initializeBean(final String beanName, final Object bean, RootBeanDefinition mbd) {
    if (System.getSecurityManager() != null) {
        AccessController.doPrivileged(new PrivilegedAction<Object>() {
            @Override
            public Object run() {
                invokeAwareMethods(beanName, bean);
                return null;
            }
        }, getAccessControlContext());
    }
    else {
        // 若 bean 实现了 BeanNameAware、BeanFactoryAware、BeanClassLoaderAware 等接口，则向 bean 中注入相关对象
        invokeAwareMethods(beanName, bean);
    }

    Object wrappedBean = bean;
    if (mbd == null || !mbd.isSynthetic()) {
        // 执行 bean 初始化前置操作
        wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
    }

    try {
        /*
         * 调用初始化方法：
         * 1. 若 bean 实现了 InitializingBean 接口，则调用 afterPropertiesSet 方法
         * 2. 若用户配置了 bean 的 init-method 属性，则调用用户在配置中指定的方法
         */
        invokeInitMethods(beanName, wrappedBean, mbd);
    }
    catch (Throwable ex) {
        throw new BeanCreationException(
                (mbd != null ? mbd.getResourceDescription() : null),
                beanName, "Invocation of init method failed", ex);
    }
    if (mbd == null || !mbd.isSynthetic()) {
        // 执行 bean 初始化后置操作，AOP 会在此处向目标对象中织入切面逻辑
        wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
    }
    return wrappedBean;
}
```
以上就是 `initializeBean` 方法的逻辑，很简单是不是。该方法做了如下几件事情：

- 检测 `bean` 是否实现了 *`Aware` 类型接口，若实现，则向 `bean` 中注入相应的对象
- 执行 `bean` 初始化前置操作
- 执行初始化操作
- 执行 `bean` 初始化后置操作

在上面的流程中，我们又发现了后置处理器的踪影。如果大家阅读过 `Spring` 的源码，会发现后置处理器在 `Spring` 源码中多次出现过。后置处理器是 `Spring` 拓展点之一，通过实现后置处理器 `BeanPostProcessor` 接口，我们就可以插手 `bean` 的初始化过程。

比如大家所熟悉的 `AOP` 就是在后置处理 `postProcessAfterInitialization` 方法中向目标对象中织如切面逻辑的。关于“前置处理”和“后置处理”相关的源码，这里就不分析了，大家有兴趣自己去看一下。接下来分析一下 `invokeAwareMethods` 和 `invokeInitMethods` 方法，如下：

```java
private void invokeAwareMethods(final String beanName, final Object bean) {
    if (bean instanceof Aware) {
        if (bean instanceof BeanNameAware) {
            // 注入 beanName 字符串
            ((BeanNameAware) bean).setBeanName(beanName);
        }
        if (bean instanceof BeanClassLoaderAware) {
            // 注入 ClassLoader 对象
            ((BeanClassLoaderAware) bean).setBeanClassLoader(getBeanClassLoader());
        }
        if (bean instanceof BeanFactoryAware) {
            // 注入 BeanFactory 对象
            ((BeanFactoryAware) bean).setBeanFactory(AbstractAutowireCapableBeanFactory.this);
        }
    }
}
```
`invokeAwareMethods` 方法的逻辑很简单，一句话总结：根据 `bean` 所实现的 `Aware` 的类型，向 `bean` 中注入不同类型的对象。

```java
protected void invokeInitMethods(String beanName, final Object bean, RootBeanDefinition mbd)
        throws Throwable {

    // 检测 bean 是否是 InitializingBean 类型的
    boolean isInitializingBean = (bean instanceof InitializingBean);
    if (isInitializingBean && (mbd == null || !mbd.isExternallyManagedInitMethod("afterPropertiesSet"))) {
        if (logger.isDebugEnabled()) {
            logger.debug("Invoking afterPropertiesSet() on bean with name '" + beanName + "'");
        }
        if (System.getSecurityManager() != null) {
            try {
                AccessController.doPrivileged(new PrivilegedExceptionAction<Object>() {
                    @Override
                    public Object run() throws Exception {
                        ((InitializingBean) bean).afterPropertiesSet();
                        return null;
                    }
                }, getAccessControlContext());
            }
            catch (PrivilegedActionException pae) {
                throw pae.getException();
            }
        }
        else {
            // 如果 bean 实现了 InitializingBean，则调用 afterPropertiesSet 方法执行初始化逻辑
            ((InitializingBean) bean).afterPropertiesSet();
        }
    }

    if (mbd != null) {
        String initMethodName = mbd.getInitMethodName();
        if (initMethodName != null && !(isInitializingBean && "afterPropertiesSet".equals(initMethodName)) &&
                !mbd.isExternallyManagedInitMethod(initMethodName)) {
            // 调用用户自定义的初始化方法
            invokeCustomInitMethod(beanName, bean, mbd);
        }
    }
}
```
`invokeInitMethods` 方法用于执行初始化方法，也不复杂，就不多说了。

## 3. 总结
本篇文章到这里差不多就分析完了，总的来说本文的内容比较简单，很容易看懂。正如简介一章中所说，本篇文章是我的“Spring IOC 容器源码分析”系列文章的最后一篇文章。

写完这本篇文章，有种如释重负的感觉。我在5月15号写完 `Java` `CAS` 原理分析 文章后，次日开始阅读 `Spring` `IOC` 部分的源码，阅读该部分源码花了大概两周的时间。

然后在5月30号发布了“Spring IOC 容器源码分析”系列文章的第一篇文章 [Spring IOC 容器源码分析系列文章导读](https://www.ycbbs.vip/?p=1153 "Spring IOC 容器源码分析系列文章导读")。

在写完第一篇文章后，就开启了快速更新模式，以平均2天一篇的速度进行更新。终于在今天，也就是6月11号写完了最后一篇。这一段时间写文章写的很累，经常熬夜。

主要的原因在于，在自己看懂源码的同时，通过写文章的方式尽量保证别人也能看懂的话，这个就比较难了。比如我在阅读源码的时候，在源码上面写了一些简单的注释。

这些注释我可以看懂，但如果想写成文章，则需要把注释写的尽量详细，必要的背景知识也要介绍一下。总的来说，认真写一篇技术文章还是不容易的。写文章尚如此，那写书呢，想必更加辛苦了。

我在阅读源码和写文章的过程中，也参考了一些资料（相关资料在“导读”一文中指明了出处，本文就不再次说明）。在这里，向这些资料的作者致以崇高的敬意，感谢！

好了，本篇文章就到这里了，感谢大家的阅读。

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")