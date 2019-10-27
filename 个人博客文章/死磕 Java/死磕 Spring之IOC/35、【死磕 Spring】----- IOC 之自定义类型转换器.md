# 35、【死磕 Spring】----- IOC 之自定义类型转换器

在上篇文章中小编分析了 Spring ConversionService 类型转换体系，相信各位都对其有了一个清晰的认识，这篇博客将利用 ConversionService 体系来实现自己的类型转换器。

ConversionService 是 Spring 类型转换器体系中的核心接口，它定义了是否可以完成转换（`canConvert()`） 与 类型转换（`convert()`）两类接口。ConversionService 有三个子类，每个子类针对不同的类型转换：

- Converter<S,T>:将 S 类型对象转为 T 类型对象。
- GenericConverter:会根据源类对象及目标类对象所在的宿主类中的上下文信息进行类型转换。
- ConverterFactory:将相同系列多个 “同质” Converter 封装在一起。如果希望将一种类型的对象转换为另一种类型及其子类的对象(例如将 String 转换为 Number 及 Number 子类(Integer、Long、Double 等)对象)可使用该转换器工厂类。

如何自定义类型转换器？分两步走：

1. 实现 Converter / GenericConverter / ConverterFactory 接口
2. 将该类注册到 ConversionServiceFactoryBean 中。

ConversionServiceFactoryBean 实现了 InitializingBean 接口实现 `afterPropertiesSet()` ，我们知道在 Bean 实例化 bean 阶段，Spring 容器会检查当前 bean 是否实现了 InitializingBean 接口，如果是则执行相应的初始化方法。（关于 InitializingBean 详情请参考：[【死磕 Spring】----- IOC 之 深入分析 InitializingBean 和 init-method]()）。`afterPropertiesSet()` 源码如下：

```java
public void afterPropertiesSet() {
 this.conversionService = createConversionService();
 ConversionServiceFactory.registerConverters(this.converters, this.conversionService);
}
```

首先调用 `createConversionService()` 初始化 conversionService，然后调用 `ConversionServiceFactory.registerConverters()` 将定义的 converters 注入到类型转换体系中。`createConversionService()` 其实就是创建一个 DefaultConversionService 实例对象，对于 DefaultConversionService 小编在上篇博客已经分析了，如有不了解的请移步上篇博文。这里直接分析 `ConversionServiceFactory.registerConverters()`，该方法是将定义的 converter 注册到目标 ConverterRegistry 中，我们知道 ConverterRegistry 是一个 Converter 注册器，他定义了一系列注册方法。

```java
 public static void registerConverters(@Nullable Set<?> converters, ConverterRegistry registry) {
  if (converters != null) {
   for (Object converter : converters) {
    if (converter instanceof GenericConverter) {
     registry.addConverter((GenericConverter) converter);
    }
    else if (converter instanceof Converter<?, ?>) {
     registry.addConverter((Converter<?, ?>) converter);
    }
    else if (converter instanceof ConverterFactory<?, ?>) {
     registry.addConverterFactory((ConverterFactory<?, ?>) converter);
    }
    else {
     throw new IllegalArgumentException("Each converter object must implement one of the " +
       "Converter, ConverterFactory, or GenericConverter interfaces");
    }
   }
  }
 }
```

调用 ConverterRegistry 的 `addConverter()` 方法将转换器注册到容器中。所以在我们使用 Spring 容器的时候，Spring 将会自动识别出 IOC 容器中注册的 ConversionService 并且在 bean 属性注入阶段使用自定义的转换器完成属性的转换了。

## 实例

定义 StudentConversionService 转换器：

```java
public class StudentConversionService implements Converter<String,StudentService>{

    @Override
    public StudentService convert(String source) {
        if(StringUtils.hasLength(source)){
            String[] sources = source.split("#");

            StudentService studentService = new StudentService();
            studentService.setAge(Integer.parseInt(sources[0]));
            studentService.setName(sources[1]);

            return studentService;
        }
        return null;
    }
}
```

配置：

```xml
<bean id="conversionService"
          class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <set>
            <ref bean="studentConversionService"/>
        </set>
     </property>
 </bean>

<bean id="studentConversionService" class="org.springframework.core.conversion.StudentConversionService"/>

<bean id="student" class="org.springframework.core.conversion.Student">
    <property name="studentService" value="18#chenssy"/>
</bean>
```
