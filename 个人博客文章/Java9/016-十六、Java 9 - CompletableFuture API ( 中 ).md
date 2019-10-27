## 引言

上一章节中我们提到，`Java 9` 添加了 `CompletableFuture` 类的子类化的支持。其实呢 ？ `Java 9` 对 `CompletableFuture` 类的子类化的支持也是新增了两个方法。

一个是 `defaultExecutor()` 方法，返回默认的执行器 ( `Executor` )，一个是 `newIncompleteFuture()` 返回一个 `CompletableFuture` 的新实例。下面，我们就一一介绍这两个方法吧。

> 注意： 这个两个方法都是由 `CompletableFuture` 类提供的。

## defaultExecutor() 方法 ##

`defaultExecutor()` 方法的原型如下

```
public Executor defaultExecutor()
```

这个方法没有任何参数，但可以返回一个默认的执行器 ( `Executor` )，这个执行器可以作为那些没有指定执行器的异步方法的执行器

简单的说，就是为异步方法提供一个执行器

子类中可以重写此方法，以返回一个最小化的独立线程作为执行器

## newIncompleteFuture() 方法 ##

`newIncompleteFuture()` 方法的原型如下

```
public <U> CompletableFuture<U> newIncompleteFuture()
```

返回 `CompletionStage`方法返回的的新的不完整 `CompletableFuture`，默认实现是返回 `CompletableFuture` 类的实例

`CompletableFuture` 类的子类应覆盖此方法，以返回与此 `CompletableFuture` 相同的类的实例


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")