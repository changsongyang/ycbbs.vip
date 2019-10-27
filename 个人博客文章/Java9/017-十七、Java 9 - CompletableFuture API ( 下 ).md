## 引言
Java 9 同时为 `CompletableFuture` 类添加了一些工厂方法

## `completedFuture(U value)` 工厂方法 ##

`completedFuture(U value)` 工厂方法的原型如下

```
public static <U> CompletableFuture<U> completedFuture(U value)
```

此工厂方法返回一个已完成的、使用给定值的新 `CompletableFuture` 。

## `completedStage(U value)` 工厂方法 ##

`completedStage(U value)` 工厂方法的原型如下

```
public static <U> CompletionStage<U> completedStage(U value)
```

此工厂方法返回一个新的使用给定值 `value` 的已完成的 `CompletionStage`，且仅支持接口 `CompletionStage` 中定义的那些方法

## `failedStage(Throwable ex)` 工厂方法 ##

`failedStage(Throwable ex)` 工厂方法的原型如下

```
public static <U> CompletionStage<U> failedStage(Throwable ex)
```

此工厂方法返回一个新的 `CompletionStage`，使用给定异常的情况下异常完成，且仅支持接口 `CompletionStage` 中存在的那些方法


写完了如果写得有什么问题，希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")