>作者：田小波 [原文连接](http://www.tianxiaobo.com "原文连接")

## 1.简介
从本篇文章开始，我将会对 `Spring` `AOP` 部分的源码进行分析。本文是 `Spring` `AOP` 源码分析系列文章的第二篇，本文主要分析 `Spring` `AOP` 是如何为目标 `bean` 筛选出合适的通知器（`Advisor`）。在上一篇 [AOP 源码分析导读](https://www.ycbbs.vip/?p=1183 "AOP 源码分析导读")一文中，我简单介绍了 `AOP` 中的一些术语及其对应的源码，部分术语和源码将会在本篇文章中出现。如果大家不熟悉这些术语和源码，不妨去看看。
关于 `Spring` `AOP`，我个人在日常开发中用过一些，也参照过 `tiny-spring` 过写过一个玩具版的 `AOP` 框架，并写成了文章。正因为前面做了一些准备工作，最近再看 `Spring` `AOP` 源码时，觉得也没那么难了。

所以如果大家打算看 `AOP` 源码的话，这里建议大家多做一些准备工作。比如熟悉 `AOP` 的中的术语，亦或是实现一个简单的 `IOC` 和 `AOP`，并将两者整合在一起。经过如此准备，相信大家会对 `AOP` 会有更多的认识。

好了，其他的就不多说了，下面进入源码分析阶段。

## 2.源码分析
### 2.1 `AOP` 入口分析
在导读一文中，我已经说过 `Spring` `AOP` 是在何处向目标 `bean` 中织入通知（`Advice`）的。也说过 `Spring` 是如何将 `AOP` 和 `IOC` 模块整合到一起的，即通过拓展点 `BeanPostProcessor` 接口。

`Spring` `AOP` 抽象代理创建器实现了 `BeanPostProcessor` 接口，并在 `bean` 初始化后置处理过程中向 `bean` 中织入通知。下面我们就来看看相关源码，如下：

```java
public abstract class AbstractAutoProxyCreator extends ProxyProcessorSupport
        implements SmartInstantiationAwareBeanPostProcessor, BeanFactoryAware {
    
    @Override
    /** bean 初始化后置处理方法 */
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        if (bean != null) {
            Object cacheKey = getCacheKey(bean.getClass(), beanName);
            if (!this.earlyProxyReferences.contains(cacheKey)) {
                // 如果需要，为 bean 生成代理对象
                return wrapIfNecessary(bean, beanName, cacheKey);
            }
        }
        return bean;
    }
    
    protected Object wrapIfNecessary(Object bean, String beanName, Object cacheKey) {
        if (beanName != null && this.targetSourcedBeans.contains(beanName)) {
            return bean;
        }
        if (Boolean.FALSE.equals(this.advisedBeans.get(cacheKey))) {
            return bean;
        }

        /*
         * 如果是基础设施类（Pointcut、Advice、Advisor 等接口的实现类），或是应该跳过的类，
         * 则不应该生成代理，此时直接返回 bean
         */ 
        if (isInfrastructureClass(bean.getClass()) || shouldSkip(bean.getClass(), beanName)) {
            // 将 <cacheKey, FALSE> 键值对放入缓存中，供上面的 if 分支使用
            this.advisedBeans.put(cacheKey, Boolean.FALSE);
            return bean;
        }

        // 为目标 bean 查找合适的通知器
        Object[] specificInterceptors = getAdvicesAndAdvisorsForBean(bean.getClass(), beanName, null);
        /*
         * 若 specificInterceptors != null，即 specificInterceptors != DO_NOT_PROXY，
         * 则为 bean 生成代理对象，否则直接返回 bean
         */ 
        if (specificInterceptors != DO_NOT_PROXY) {
            this.advisedBeans.put(cacheKey, Boolean.TRUE);
            // 创建代理
            Object proxy = createProxy(
                    bean.getClass(), beanName, specificInterceptors, new SingletonTargetSource(bean));
            this.proxyTypes.put(cacheKey, proxy.getClass());
            /*
             * 返回代理对象，此时 IOC 容器输入 bean，得到 proxy。此时，
             * beanName 对应的 bean 是代理对象，而非原始的 bean
             */ 
            return proxy;
        }

        this.advisedBeans.put(cacheKey, Boolean.FALSE);
        // specificInterceptors = null，直接返回 bean
        return bean;
    }
}
```
以上就是 `Spring` `AOP` 创建代理对象的入口方法分析，过程比较简单，这里简单总结一下：

- 若 `bean` 是 `AOP` 基础设施类型，则直接返回
- 为 `bean` 查找合适的通知器
- 如果通知器数组不为空，则为 `bean` 生成代理对象，并返回该对象
- 若数组为空，则返回原始 `bean`
上面的流程看起来并不复杂，不过不要被表象所迷糊，以上流程不过是冰山一角。

在本文，以及后续的文章中，我将会对步骤2和步骤3对应的源码进行分析。在本篇文章先来分析步骤2对应的源码。

### 2.2 筛选合适的通知器
在向目标 1 中织入通知之前，我们先要为 1 筛选出合适的通知器（通知器持有通知）。如何筛选呢？方式由很多，比如我们可以通过正则表达式匹配方法名，当然更多的时候用的是 `AspectJ` 表达式进行匹配。那下面我们就来看一下使用 `AspectJ` 表达式筛选通知器的过程，如下：

```java
protected Object[] getAdvicesAndAdvisorsForBean(Class<?> beanClass, String beanName, TargetSource targetSource) {
    // 查找合适的通知器
    List<Advisor> advisors = findEligibleAdvisors(beanClass, beanName);
    if (advisors.isEmpty()) {
        return DO_NOT_PROXY;
    }
    return advisors.toArray();
}

protected List<Advisor> findEligibleAdvisors(Class<?> beanClass, String beanName) {
    // 查找所有的通知器
    List<Advisor> candidateAdvisors = findCandidateAdvisors();
    /*
     * 筛选可应用在 beanClass 上的 Advisor，通过 ClassFilter 和 MethodMatcher
     * 对目标类和方法进行匹配
     */
    List<Advisor> eligibleAdvisors = findAdvisorsThatCanApply(candidateAdvisors, beanClass, beanName);
    // 拓展操作
    extendAdvisors(eligibleAdvisors);
    if (!eligibleAdvisors.isEmpty()) {
        eligibleAdvisors = sortAdvisors(eligibleAdvisors);
    }
    return eligibleAdvisors;
}
```
如上，`Spring` 先查询出所有的通知器，然后再调用 `findAdvisorsThatCanApply` 对通知器进行筛选。在下面几节中，我将分别对 `findCandidateAdvisors` 和 `findAdvisorsThatCanApply` 两个方法进行分析，继续往下看吧。

#### 2.2.1 查找通知器
`Spring` 提供了两种配置 `AOP` 的方式，一种是通过 `XML` 进行配置，另一种是注解。对于两种配置方式，`Spring` 的处理逻辑是不同的。对于 `XML` 类型的配置，比如下面的配置：

```html
<!-- 目标 bean -->
<bean id="hello" class="xyz.coolblog.aop.Hello"/>

<aop:aspectj-autoproxy/>
    
<!-- 普通 bean，包含 AOP 切面逻辑 -->
<bean id="aopCode" class="xyz.coolblog.aop.AopCode"/>
<!-- 由 @Aspect 注解修饰的切面类 -->
<bean id="annotationAopCode" class="xyz.coolblog.aop.AnnotationAopCode"/>

<aop:config>
    <aop:aspect ref="aopCode">
        <aop:pointcut id="helloPointcut" expression="execution(* xyz.coolblog.aop.*.hello*(..))" />
        <aop:before method="before" pointcut-ref="helloPointcut"/>
        <aop:after method="after" pointcut-ref="helloPointcut"/>
    </aop:aspect>
</aop:config>
```
`Spring` 会将上的配置解析为下面的结果：
![](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Spring%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/images/09-1.jpg)

如上图所示，红框中对应的是普通的 `bean` 定义，比如 <bean id="hello" .../>、<bean id="annotationAopCode" .../>、<bean id="appCode" .../> 等配置。黄色框中的则是切点的定义，类型为 `AspectJExpressionPointcut`，对应 `<aop:pointcut id="helloPointcut" .../>` 配置。

那绿色框中的结果对应的是什么配置呢？目前仅剩下两个配置没说，所以对应 `<aop:before .../>` 和 `<aop:after .../>` 配置，类型为 `AspectJPointcutAdvisor`。

这里请大家注意，由 `@Aspect` 注解修饰的 `AnnotationAopCode` 也是普通类型的 `bean`，该 `bean` 会在查找通知器的过程中被解析，并被构建为一个或多个 `Advisor`。

上面讲解了 `Spring` `AOP` 两种配置的处理方式，算是为下面的源码分析做铺垫。现在铺垫完毕，我们就来分析一下源码吧。如下：

```java
public class AnnotationAwareAspectJAutoProxyCreator extends AspectJAwareAdvisorAutoProxyCreator {

    //...

    @Override
    protected List<Advisor> findCandidateAdvisors() {
        // 调用父类方法从容器中查找所有的通知器
        List<Advisor> advisors = super.findCandidateAdvisors();
        // 解析 @Aspect 注解，并构建通知器
        advisors.addAll(this.aspectJAdvisorsBuilder.buildAspectJAdvisors());
        return advisors;
    }

    //...
}
```
`AnnotationAwareAspectJAutoProxyCreator` 覆写了父类的方法 `findCandidateAdvisors`，并增加了一步操作，即解析 `@Aspect` 注解，并构建成通知器。下面我先来分析一下父类中的 `findCandidateAdvisors` 方法的逻辑，然后再来分析 `buildAspectJAdvisors` 方法逻的辑。

#### 2.2.1.1 findCandidateAdvisors 方法分析
我们先来看一下 `AbstractAdvisorAutoProxyCreator` 中 `findCandidateAdvisors` 方法的定义，如下：

```java
public abstract class AbstractAdvisorAutoProxyCreator extends AbstractAutoProxyCreator {

    private BeanFactoryAdvisorRetrievalHelper advisorRetrievalHelper;
    
    //...

    protected List<Advisor> findCandidateAdvisors() {
        return this.advisorRetrievalHelper.findAdvisorBeans();
    }

    //...
}
```
从上面的源码中可以看出，`AbstractAdvisorAutoProxyCreator` 中的 `findCandidateAdvisors` 是个空壳方法，所有逻辑封装在了一个 `BeanFactoryAdvisorRetrievalHelper` 的 `findAdvisorBeans` 方法中。

这里大家可以仔细看一下类名 `BeanFactoryAdvisorRetrievalHelper` 和方法 `findAdvisorBeans`，两个名字其实已经描述出他们的职责了。`BeanFactoryAdvisorRetrievalHelper` 可以理解为从 `bean` 容器中获取 `Advisor` 的帮助类，`findAdvisorBeans` 则可理解为查找 `Advisor` 类型的 `bean`。

所以即使不看 `findAdvisorBeans` 方法的源码，我们也可从方法名上推断出它要做什么，即从 `bean` 容器中将 `Advisor` 类型的 `bean` 查找出来。下面我来分析一下这个方法的源码，如下：

```java
public List<Advisor> findAdvisorBeans() {
    String[] advisorNames = null;
    synchronized (this) {
        // cachedAdvisorBeanNames 是 advisor 名称的缓存
        advisorNames = this.cachedAdvisorBeanNames;
        /*
         * 如果 cachedAdvisorBeanNames 为空，这里到容器中查找，
         * 并设置缓存，后续直接使用缓存即可
         */ 
        if (advisorNames == null) {
            // 从容器中查找 Advisor 类型 bean 的名称
            advisorNames = BeanFactoryUtils.beanNamesForTypeIncludingAncestors(
                    this.beanFactory, Advisor.class, true, false);
            // 设置缓存
            this.cachedAdvisorBeanNames = advisorNames;
        }
    }
    if (advisorNames.length == 0) {
        return new LinkedList<Advisor>();
    }

    List<Advisor> advisors = new LinkedList<Advisor>();
    // 遍历 advisorNames
    for (String name : advisorNames) {
        if (isEligibleBean(name)) {
            // 忽略正在创建中的 advisor bean
            if (this.beanFactory.isCurrentlyInCreation(name)) {
                if (logger.isDebugEnabled()) {
                    logger.debug("Skipping currently created advisor '" + name + "'");
                }
            }
            else {
                try {
                    /*
                     * 调用 getBean 方法从容器中获取名称为 name 的 bean，
                     * 并将 bean 添加到 advisors 中
                     */ 
                    advisors.add(this.beanFactory.getBean(name, Advisor.class));
                }
                catch (BeanCreationException ex) {
                    Throwable rootCause = ex.getMostSpecificCause();
                    if (rootCause instanceof BeanCurrentlyInCreationException) {
                        BeanCreationException bce = (BeanCreationException) rootCause;
                        if (this.beanFactory.isCurrentlyInCreation(bce.getBeanName())) {
                            if (logger.isDebugEnabled()) {
                                logger.debug("Skipping advisor '" + name +
                                        "' with dependency on currently created bean: " + ex.getMessage());
                            }
                            continue;
                        }
                    }
                    throw ex;
                }
            }
        }
    }

    return advisors;
}
```
以上就是从容器中查找 `Advisor` 类型的 `bean` 所有的逻辑，代码虽然有点长，但并不复杂。主要做了两件事情：

- 从容器中查找所有类型为 `Advisor` 的 `bean` 对应的名称
- 遍历 `advisorNames`，并从容器中获取对应的 `bean`

看完上面的分析，我们继续来分析一下 `@Aspect` 注解的解析过程。

 #### 2.2.1.2 buildAspectJAdvisors 方法分析
与上一节的内容相比，解析 `@Aspect` 注解的过程还是比较复杂的，需要一些耐心去看。下面我们开始分析 `buildAspectJAdvisors` 方法的源码，如下：

```java
public List<Advisor> buildAspectJAdvisors() {
    List<String> aspectNames = this.aspectBeanNames;

    if (aspectNames == null) {
        synchronized (this) {
            aspectNames = this.aspectBeanNames;
            if (aspectNames == null) {
                List<Advisor> advisors = new LinkedList<Advisor>();
                aspectNames = new LinkedList<String>();
                // 从容器中获取所有 bean 的名称
                String[] beanNames = BeanFactoryUtils.beanNamesForTypeIncludingAncestors(
                        this.beanFactory, Object.class, true, false);
                // 遍历 beanNames
                for (String beanName : beanNames) {
                    if (!isEligibleBean(beanName)) {
                        continue;
                    }
                    
                    // 根据 beanName 获取 bean 的类型
                    Class<?> beanType = this.beanFactory.getType(beanName);
                    if (beanType == null) {
                        continue;
                    }

                    // 检测 beanType 是否包含 Aspect 注解
                    if (this.advisorFactory.isAspect(beanType)) {
                        aspectNames.add(beanName);
                        AspectMetadata amd = new AspectMetadata(beanType, beanName);
                        if (amd.getAjType().getPerClause().getKind() == PerClauseKind.SINGLETON) {
                            MetadataAwareAspectInstanceFactory factory =
                                    new BeanFactoryAspectInstanceFactory(this.beanFactory, beanName);

                            // 获取通知器
                            List<Advisor> classAdvisors = this.advisorFactory.getAdvisors(factory);
                            if (this.beanFactory.isSingleton(beanName)) {
                                this.advisorsCache.put(beanName, classAdvisors);
                            }
                            else {
                                this.aspectFactoryCache.put(beanName, factory);
                            }
                            advisors.addAll(classAdvisors);
                        }
                        else {
                            if (this.beanFactory.isSingleton(beanName)) {
                                throw new IllegalArgumentException("Bean with name '" + beanName +
                                        "' is a singleton, but aspect instantiation model is not singleton");
                            }
                            MetadataAwareAspectInstanceFactory factory =
                                    new PrototypeAspectInstanceFactory(this.beanFactory, beanName);
                            this.aspectFactoryCache.put(beanName, factory);
                            advisors.addAll(this.advisorFactory.getAdvisors(factory));
                        }
                    }
                }
                this.aspectBeanNames = aspectNames;
                return advisors;
            }
        }
    }

    if (aspectNames.isEmpty()) {
        return Collections.emptyList();
    }
    List<Advisor> advisors = new LinkedList<Advisor>();
    for (String aspectName : aspectNames) {
        List<Advisor> cachedAdvisors = this.advisorsCache.get(aspectName);
        if (cachedAdvisors != null) {
            advisors.addAll(cachedAdvisors);
        }
        else {
            MetadataAwareAspectInstanceFactory factory = this.aspectFactoryCache.get(aspectName);
            advisors.addAll(this.advisorFactory.getAdvisors(factory));
        }
    }
    return advisors;
}
```
上面就是 `buildAspectJAdvisors` 的代码，看起来比较长。代码比较多，我们关注重点的方法调用即可。在进行后续的分析前，这里先对 `buildAspectJAdvisors` 方法的执行流程做个总结。

如下：

- 获取容器中所有 `bean` 的名称（`beanName`）
- 遍历上一步获取到的 `bean` 名称数组，并获取当前 `beanName` 对应的 `bean` 类型（`beanType`）
- 根据 `beanType` 判断当前 `bean` 是否是一个的 `Aspect` 注解类，若不是则不做任何处理
- 调用 `advisorFactory.getAdvisors` 获取通知器

下面我们来重点分析 `advisorFactory.getAdvisors(factory)`这个调用，如下：

```java
public List<Advisor> getAdvisors(MetadataAwareAspectInstanceFactory aspectInstanceFactory) {
    // 获取 aspectClass 和 aspectName
    Class<?> aspectClass = aspectInstanceFactory.getAspectMetadata().getAspectClass();
    String aspectName = aspectInstanceFactory.getAspectMetadata().getAspectName();
    validate(aspectClass);

    MetadataAwareAspectInstanceFactory lazySingletonAspectInstanceFactory =
            new LazySingletonAspectInstanceFactoryDecorator(aspectInstanceFactory);

    List<Advisor> advisors = new LinkedList<Advisor>();

    // getAdvisorMethods 用于返回不包含 @Pointcut 注解的方法
    for (Method method : getAdvisorMethods(aspectClass)) {
        // 为每个方法分别调用 getAdvisor 方法
        Advisor advisor = getAdvisor(method, lazySingletonAspectInstanceFactory, advisors.size(), aspectName);
        if (advisor != null) {
            advisors.add(advisor);
        }
    }

    // If it's a per target aspect, emit the dummy instantiating aspect.
    if (!advisors.isEmpty() && lazySingletonAspectInstanceFactory.getAspectMetadata().isLazilyInstantiated()) {
        Advisor instantiationAdvisor = new SyntheticInstantiationAdvisor(lazySingletonAspectInstanceFactory);
        advisors.add(0, instantiationAdvisor);
    }

    // Find introduction fields.
    for (Field field : aspectClass.getDeclaredFields()) {
        Advisor advisor = getDeclareParentsAdvisor(field);
        if (advisor != null) {
            advisors.add(advisor);
        }
    }

    return advisors;
}

public Advisor getAdvisor(Method candidateAdviceMethod, MetadataAwareAspectInstanceFactory aspectInstanceFactory,
        int declarationOrderInAspect, String aspectName) {

    validate(aspectInstanceFactory.getAspectMetadata().getAspectClass());

    // 获取切点实现类
    AspectJExpressionPointcut expressionPointcut = getPointcut(
            candidateAdviceMethod, aspectInstanceFactory.getAspectMetadata().getAspectClass());
    if (expressionPointcut == null) {
        return null;
    }

    // 创建 Advisor 实现类
    return new InstantiationModelAwarePointcutAdvisorImpl(expressionPointcut, candidateAdviceMethod,
            this, aspectInstanceFactory, declarationOrderInAspect, aspectName);
}
```
如上，`getAdvisor` 方法包含两个主要步骤，一个是获取 `AspectJ` 表达式切点，另一个是创建 `Advisor` 实现类。在第二个步骤中，包含一个隐藏步骤 – 创建 `Advice`。下面我将按顺序依次分析这两个步骤，先看获取 `AspectJ` 表达式切点的过程，如下：

```java
private AspectJExpressionPointcut getPointcut(Method candidateAdviceMethod, Class<?> candidateAspectClass) {
    // 获取方法上的 AspectJ 相关注解，包括 @Before，@After 等
    AspectJAnnotation<?> aspectJAnnotation =
            AbstractAspectJAdvisorFactory.findAspectJAnnotationOnMethod(candidateAdviceMethod);
    if (aspectJAnnotation == null) {
        return null;
    }

    // 创建一个 AspectJExpressionPointcut 对象
    AspectJExpressionPointcut ajexp =
            new AspectJExpressionPointcut(candidateAspectClass, new String[0], new Class<?>[0]);
    // 设置切点表达式
    ajexp.setExpression(aspectJAnnotation.getPointcutExpression());
    ajexp.setBeanFactory(this.beanFactory);
    return ajexp;
}

protected static AspectJAnnotation<?> findAspectJAnnotationOnMethod(Method method) {
    // classesToLookFor 中的元素是大家熟悉的
    Class<?>[] classesToLookFor = new Class<?>[] {
            Before.class, Around.class, After.class, AfterReturning.class, AfterThrowing.class, Pointcut.class};
    for (Class<?> c : classesToLookFor) {
        // 查找注解
        AspectJAnnotation<?> foundAnnotation = findAnnotation(method, (Class<Annotation>) c);
        if (foundAnnotation != null) {
            return foundAnnotation;
        }
    }
    return null;
}
```
获取切点的过程并不复杂，不过需要注意的是，目前获取到的切点可能还只是个半成品，需要再次处理一下才行。比如下面的代码：

```java
@Aspect
public class AnnotationAopCode {

    @Pointcut("execution(* xyz.coolblog.aop.*.world*(..))")
    public void pointcut() {}

    @Before("pointcut()")
    public void before() {
        System.out.println("AnnotationAopCode`s before");
    }
}
```
`@Before` 注解中的表达式是`pointcut()`，也就是说 `ajexp` 设置的表达式只是一个中间值，不是最终值，即`execution(* xyz.coolblog.aop.*.world*(..))`。

所以后续还需要将 `ajexp` 中的表达式进行转换，关于这个转换的过程，我就不说了。有点复杂，我暂时没怎么看懂。

说完切点的获取过程，下面再来看看 · 实现类的创建过程。如下：

```java
public InstantiationModelAwarePointcutAdvisorImpl(AspectJExpressionPointcut declaredPointcut,
        Method aspectJAdviceMethod, AspectJAdvisorFactory aspectJAdvisorFactory,
        MetadataAwareAspectInstanceFactory aspectInstanceFactory, int declarationOrder, String aspectName) {

    this.declaredPointcut = declaredPointcut;
    this.declaringClass = aspectJAdviceMethod.getDeclaringClass();
    this.methodName = aspectJAdviceMethod.getName();
    this.parameterTypes = aspectJAdviceMethod.getParameterTypes();
    this.aspectJAdviceMethod = aspectJAdviceMethod;
    this.aspectJAdvisorFactory = aspectJAdvisorFactory;
    this.aspectInstanceFactory = aspectInstanceFactory;
    this.declarationOrder = declarationOrder;
    this.aspectName = aspectName;

    if (aspectInstanceFactory.getAspectMetadata().isLazilyInstantiated()) {
        Pointcut preInstantiationPointcut = Pointcuts.union(
                aspectInstanceFactory.getAspectMetadata().getPerClausePointcut(), this.declaredPointcut);

        this.pointcut = new PerTargetInstantiationModelPointcut(
                this.declaredPointcut, preInstantiationPointcut, aspectInstanceFactory);
        this.lazy = true;
    }
    else {
        this.pointcut = this.declaredPointcut;
        this.lazy = false;

        // 按照注解解析 Advice
        this.instantiatedAdvice = instantiateAdvice(this.declaredPointcut);
    }
}
```
上面是 `InstantiationModelAwarePointcutAdvisorImpl` 的构造方法，不过我们无需太关心这个方法中的一些初始化逻辑。我们把目光移到构造方法的最后一行代码中，即 `instantiateAdvice(this.declaredPointcut)`，这个方法用于创建通知 `Advice`。

在上一篇文章中我已经说过，通知器 `Advisor` 是通知 `Advice` 的持有者，所以在 `Advisor` 实现类的构造方法中创建通知也是合适的。那下面我们就来看看构建通知的过程是怎样的，如下：

```java
private Advice instantiateAdvice(AspectJExpressionPointcut pcut) {
    return this.aspectJAdvisorFactory.getAdvice(this.aspectJAdviceMethod, pcut,
            this.aspectInstanceFactory, this.declarationOrder, this.aspectName);
}

public Advice getAdvice(Method candidateAdviceMethod, AspectJExpressionPointcut expressionPointcut,
        MetadataAwareAspectInstanceFactory aspectInstanceFactory, int declarationOrder, String aspectName) {

    Class<?> candidateAspectClass = aspectInstanceFactory.getAspectMetadata().getAspectClass();
    validate(candidateAspectClass);

    // 获取 Advice 注解
    AspectJAnnotation<?> aspectJAnnotation =
            AbstractAspectJAdvisorFactory.findAspectJAnnotationOnMethod(candidateAdviceMethod);
    if (aspectJAnnotation == null) {
        return null;
    }

    if (!isAspect(candidateAspectClass)) {
        throw new AopConfigException("Advice must be declared inside an aspect type: " +
                "Offending method '" + candidateAdviceMethod + "' in class [" +
                candidateAspectClass.getName() + "]");
    }

    if (logger.isDebugEnabled()) {
        logger.debug("Found AspectJ method: " + candidateAdviceMethod);
    }

    AbstractAspectJAdvice springAdvice;

    // 按照注解类型生成相应的 Advice 实现类
    switch (aspectJAnnotation.getAnnotationType()) {
        case AtBefore:    // @Before -> AspectJMethodBeforeAdvice
            springAdvice = new AspectJMethodBeforeAdvice(
                    candidateAdviceMethod, expressionPointcut, aspectInstanceFactory);
            break;

        case AtAfter:    // @After -> AspectJAfterAdvice
            springAdvice = new AspectJAfterAdvice(
                    candidateAdviceMethod, expressionPointcut, aspectInstanceFactory);
            break;

        case AtAfterReturning:    // @AfterReturning -> AspectJAfterAdvice
            springAdvice = new AspectJAfterReturningAdvice(
                    candidateAdviceMethod, expressionPointcut, aspectInstanceFactory);
            AfterReturning afterReturningAnnotation = (AfterReturning) aspectJAnnotation.getAnnotation();
            if (StringUtils.hasText(afterReturningAnnotation.returning())) {
                springAdvice.setReturningName(afterReturningAnnotation.returning());
            }
            break;

        case AtAfterThrowing:    // @AfterThrowing -> AspectJAfterThrowingAdvice
            springAdvice = new AspectJAfterThrowingAdvice(
                    candidateAdviceMethod, expressionPointcut, aspectInstanceFactory);
            AfterThrowing afterThrowingAnnotation = (AfterThrowing) aspectJAnnotation.getAnnotation();
            if (StringUtils.hasText(afterThrowingAnnotation.throwing())) {
                springAdvice.setThrowingName(afterThrowingAnnotation.throwing());
            }
            break;

        case AtAround:    // @Around -> AspectJAroundAdvice
            springAdvice = new AspectJAroundAdvice(
                    candidateAdviceMethod, expressionPointcut, aspectInstanceFactory);
            break;

        /*
         * 什么都不做，直接返回 null。从整个方法的调用栈来看，
         * 并不会出现注解类型为 AtPointcut 的情况
         */ 
        case AtPointcut:    
            if (logger.isDebugEnabled()) {
                logger.debug("Processing pointcut '" + candidateAdviceMethod.getName() + "'");
            }
            return null;
            
        default:
            throw new UnsupportedOperationException(
                    "Unsupported advice type on method: " + candidateAdviceMethod);
    }

    springAdvice.setAspectName(aspectName);
    springAdvice.setDeclarationOrder(declarationOrder);
    /*
     * 获取方法的参数列表名称，比如方法 int sum(int numX, int numY), 
     * getParameterNames(sum) 得到 argNames = [numX, numY]
     */
    String[] argNames = this.parameterNameDiscoverer.getParameterNames(candidateAdviceMethod);
    if (argNames != null) {
        // 设置参数名
        springAdvice.setArgumentNamesFromStringArray(argNames);
    }
    springAdvice.calculateArgumentBindings();
    return springAdvice;
}
```
上面的代码逻辑不是很复杂，主要的逻辑就是根据注解类型生成与之对应的通知对象。下面来总结一下获取通知器（`getAdvisors`）整个过程的逻辑，如下：

- 从目标 `bean` 中获取不包含 `Pointcut` 注解的方法列表
- 遍历上一步获取的方法列表，并调用 `getAdvisor` 获取当前方法对应的 Advisor
- 创建 `AspectJExpressionPointcut` 对象，并从方法中的注解中获取表达式，最后设置到切点对象中
- 创建 `Advisor` 实现类对象 `InstantiationModelAwarePointcutAdvisorImpl`
- 调用 `instantiateAdvice` 方法构建通知
- 调用 `getAdvice` 方法，并根据注解类型创建相应的通知

如上所示，上面的步骤做了一定的简化。总的来说，获取通知器的过程还是比较复杂的，并不是很容易看懂。大家在阅读的过程中，还要写一些测试代码进行调试才行。调试的过程中，一些不关心的调用就别跟进去了，不然会陷入很深的调用栈中，影响对源码主流程的理解。

现在，大家知道了通知是怎么创建的。那我们难道不要去看看这些通知的实现源码吗？显然，我们应该看一下。那接下里，我们一起来分析一下 AspectJMethodBeforeAdvice，也就是 `@Before` 注解对应的通知实现类。看看它的逻辑是什么样的。

#### 2.2.1.3 AspectJMethodBeforeAdvice 分析

```java
public class AspectJMethodBeforeAdvice extends AbstractAspectJAdvice implements MethodBeforeAdvice {

    public AspectJMethodBeforeAdvice(
            Method aspectJBeforeAdviceMethod, AspectJExpressionPointcut pointcut, AspectInstanceFactory aif) {

        super(aspectJBeforeAdviceMethod, pointcut, aif);
    }


    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        // 调用通知方法
        invokeAdviceMethod(getJoinPointMatch(), null, null);
    }

    @Override
    public boolean isBeforeAdvice() {
        return true;
    }

    @Override
    public boolean isAfterAdvice() {
        return false;
    }

}

protected Object invokeAdviceMethod(JoinPointMatch jpMatch, Object returnValue, Throwable ex) throws Throwable {
    // 调用通知方法，并向其传递参数
    return invokeAdviceMethodWithGivenArgs(argBinding(getJoinPoint(), jpMatch, returnValue, ex));
}

protected Object invokeAdviceMethodWithGivenArgs(Object[] args) throws Throwable {
    Object[] actualArgs = args;
    if (this.aspectJAdviceMethod.getParameterTypes().length == 0) {
        actualArgs = null;
    }
    try {
        ReflectionUtils.makeAccessible(this.aspectJAdviceMethod);
        // 通过反射调用通知方法
        return this.aspectJAdviceMethod.invoke(this.aspectInstanceFactory.getAspectInstance(), actualArgs);
    }
    catch (IllegalArgumentException ex) {
        throw new AopInvocationException("Mismatch on arguments to advice method [" +
                this.aspectJAdviceMethod + "]; pointcut expression [" +
                this.pointcut.getPointcutExpression() + "]", ex);
    }
    catch (InvocationTargetException ex) {
        throw ex.getTargetException();
    }
}
```
如上，`AspectJMethodBeforeAdvice` 的源码比较简单，这里我们仅关注 `before` 方法。这个方法调用了父类中的 `invokeAdviceMethod`，然后 `invokeAdviceMethod` 在调用 `invokeAdviceMethodWithGivenArgs`，最后在 `invokeAdviceMethodWithGivenArgs` 通过反射执行通知方法。是不是很简单？

关于 `AspectJMethodBeforeAdvice` 就简单介绍到这里吧，至于剩下的几种实现，大家可以自己去看看。好了，关于 `AspectJMethodBeforeAdvice` 的源码分析，就分析到这里了。我们继续往下看吧。

#### 2.2.2 筛选合适的通知器
查找出所有的通知器，整个流程还没算完，接下来我们还要对这些通知器进行筛选。适合应用在当前 `bean` 上的通知器留下，不适合的就让它自生自灭吧。那下面我们来分析一下通知器筛选的过程，如下：

```java
protected List<Advisor> findAdvisorsThatCanApply(
        List<Advisor> candidateAdvisors, Class<?> beanClass, String beanName) {

    ProxyCreationContext.setCurrentProxiedBeanName(beanName);
    try {
        // 调用重载方法
        return AopUtils.findAdvisorsThatCanApply(candidateAdvisors, beanClass);
    }
    finally {
        ProxyCreationContext.setCurrentProxiedBeanName(null);
    }
}

public static List<Advisor> findAdvisorsThatCanApply(List<Advisor> candidateAdvisors, Class<?> clazz) {
    if (candidateAdvisors.isEmpty()) {
        return candidateAdvisors;
    }
    List<Advisor> eligibleAdvisors = new LinkedList<Advisor>();
    for (Advisor candidate : candidateAdvisors) {
        // 筛选 IntroductionAdvisor 类型的通知器
        if (candidate instanceof IntroductionAdvisor && canApply(candidate, clazz)) {
            eligibleAdvisors.add(candidate);
        }
    }
    boolean hasIntroductions = !eligibleAdvisors.isEmpty();
    for (Advisor candidate : candidateAdvisors) {
        if (candidate instanceof IntroductionAdvisor) {
            continue;
        }

        // 筛选普通类型的通知器
        if (canApply(candidate, clazz, hasIntroductions)) {
            eligibleAdvisors.add(candidate);
        }
    }
    return eligibleAdvisors;
}

public static boolean canApply(Advisor advisor, Class<?> targetClass, boolean hasIntroductions) {
    if (advisor instanceof IntroductionAdvisor) {
        /*
         * 从通知器中获取类型过滤器 ClassFilter，并调用 matchers 方法进行匹配。
         * ClassFilter 接口的实现类 AspectJExpressionPointcut 为例，该类的
         * 匹配工作由 AspectJ 表达式解析器负责，具体匹配细节这个就没法分析了，我
         * AspectJ 表达式的工作流程不是很熟
         */
        return ((IntroductionAdvisor) advisor).getClassFilter().matches(targetClass);
    }
    else if (advisor instanceof PointcutAdvisor) {
        PointcutAdvisor pca = (PointcutAdvisor) advisor;
        // 对于普通类型的通知器，这里继续调用重载方法进行筛选
        return canApply(pca.getPointcut(), targetClass, hasIntroductions);
    }
    else {
        return true;
    }
}

public static boolean canApply(Pointcut pc, Class<?> targetClass, boolean hasIntroductions) {
    Assert.notNull(pc, "Pointcut must not be null");
    // 使用 ClassFilter 匹配 class
    if (!pc.getClassFilter().matches(targetClass)) {
        return false;
    }

    MethodMatcher methodMatcher = pc.getMethodMatcher();
    if (methodMatcher == MethodMatcher.TRUE) {
        return true;
    }

    IntroductionAwareMethodMatcher introductionAwareMethodMatcher = null;
    if (methodMatcher instanceof IntroductionAwareMethodMatcher) {
        introductionAwareMethodMatcher = (IntroductionAwareMethodMatcher) methodMatcher;
    }

    /*
     * 查找当前类及其父类（以及父类的父类等等）所实现的接口，由于接口中的方法是 public，
     * 所以当前类可以继承其父类，和父类的父类中所有的接口方法
     */ 
    Set<Class<?>> classes = new LinkedHashSet<Class<?>>(ClassUtils.getAllInterfacesForClassAsSet(targetClass));
    classes.add(targetClass);
    for (Class<?> clazz : classes) {
        // 获取当前类的方法列表，包括从父类中继承的方法
        Method[] methods = ReflectionUtils.getAllDeclaredMethods(clazz);
        for (Method method : methods) {
            // 使用 methodMatcher 匹配方法，匹配成功即可立即返回
            if ((introductionAwareMethodMatcher != null &&
                    introductionAwareMethodMatcher.matches(method, targetClass, hasIntroductions)) ||
                    methodMatcher.matches(method, targetClass)) {
                return true;
            }
        }
    }

    return false;
}
```
以上是通知器筛选的过程，筛选的工作主要由 `ClassFilter` 和 `MethodMatcher` 完成。关于 `ClassFilter` 和 `MethodMatcher` 我在导读一文中已经说过了，这里再说一遍吧。

在 `AOP` 中，切点 `Pointcut` 是用来匹配连接点的，以 `AspectJExpressionPointcut` 类型的切点为例。该类型切点实现了 `ClassFilter` 和 `MethodMatcher` 接口，匹配的工作则是由 `AspectJ` 表达式解析器复杂。

除了使用 `AspectJ` 表达式进行匹配，Spring 还提供了基于正则表达式的切点类，以及更简单的根据方法名进行匹配的切点类。大家有兴趣的话，可以自己去了解一下，这里就不多说了。

在完成通知器的查找和筛选过程后，还需要进行最后一步处理 – 对通知器列表进行拓展。怎么拓展呢？我们一起到下一节中一探究竟吧。

#### 2.2.3 拓展筛选出通知器列表
拓展方法 `extendAdvisors` 做的事情并不多，逻辑也比较简单。我们一起来看一下，如下：

```java
protected void extendAdvisors(List<Advisor> candidateAdvisors) {
    AspectJProxyUtils.makeAdvisorChainAspectJCapableIfNecessary(candidateAdvisors);
}

public static boolean makeAdvisorChainAspectJCapableIfNecessary(List<Advisor> advisors) {
    // 如果通知器列表是一个空列表，则啥都不做
    if (!advisors.isEmpty()) {
        boolean foundAspectJAdvice = false;
        /*
         * 下面的 for 循环用于检测 advisors 列表中是否存在 
         * AspectJ 类型的 Advisor 或 Advice
         */
        for (Advisor advisor : advisors) {
            if (isAspectJAdvice(advisor)) {
                foundAspectJAdvice = true;
            }
        }

        /*
         * 向 advisors 列表的首部添加 DefaultPointcutAdvisor，
         * 至于为什么这样做，我会在后续的文章中进行说明
         */
        if (foundAspectJAdvice && !advisors.contains(ExposeInvocationInterceptor.ADVISOR)) {
            advisors.add(0, ExposeInvocationInterceptor.ADVISOR);
            return true;
        }
    }
    return false;
}

private static boolean isAspectJAdvice(Advisor advisor) {
    return (advisor instanceof InstantiationModelAwarePointcutAdvisor ||
            advisor.getAdvice() instanceof AbstractAspectJAdvice ||
            (advisor instanceof PointcutAdvisor &&
                     ((PointcutAdvisor) advisor).getPointcut() instanceof AspectJExpressionPointcut));
}
```
如上，上面的代码比较少，也不复杂。由源码可以看出 `extendAdvisors` 是一个空壳方法，除了调用 `makeAdvisorChainAspectJCapableIfNecessary`，该方法没有其他更多的逻辑了。

至于 `makeAdvisorChainAspectJCapableIfNecessary` 这个方法，该方法主要的目的是向通知器列表首部添加 `DefaultPointcutAdvisor` 类型的通知器，也就是 `ExposeInvocationInterceptor.ADVISOR`。

至于添加此种类型通知器的意图，我会在后面文章里说明，这里不便展开。关于 `extendAdvisors` 这个方法，这里就先说到这了。

## 3.总结
到这里，本篇文章就接近尾声了。这篇文章有点长，大家看下来应该蛮累的吧。由于个人能力问题，暂时未能做到对本篇文章中所贴的源码进行更为细致的分析，有点遗憾。

不过好在目前把主逻辑分析弄清楚了，总的来说还算合格吧，给个及格分。大家在阅读的过程中，如果发现文章中出现错误或不妥之处，这里还请指明，也请多多指教。大家共同学习，一起进步。

好了，本篇文章就到这里了。谢谢大家的阅读。

 参考
《[Spring 源码深度解析](https://book.douban.com/subject/25866350/ "Spring 源码深度解析")》- 郝佳


希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")