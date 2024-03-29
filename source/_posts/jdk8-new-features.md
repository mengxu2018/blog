---
title: jdk8 new features
date: 2019-08-11 13:27:07
tags: java
---

### 为什么jdk8的interface要引入default method?
To give you an example take the case of the Collection.forEach method, which is designed to take an instance of the Consumer functional interface and has a default implementation in the Collection interface:

```
default void forEach(Consumer<? super T> action) {
    Objects.requireNonNull(action);
    for (T t : this) {
        action.accept(t);
    }
}
```
If the JDK designers didn't introduce the concept of default methods then all the implementing classes of the Collection interface would have to implement the forEach method so it would be problematic to switch to Java - 8 without breaking your code.

So to facilitate the adoption of lambdas and the use of the new functional interfaces like Consumer, Supplier, Predicate, etc. the JDK designers introduced the concept of default methods to provide backward compatibility and it is now easier to switch to Java - 8 without making any changes.

If you don't like the default implementation in the interface you can override it and supply your own.

## java线程池
https://www.baeldung.com/java-runnable-callable
Runnable和Callable区别


## 时间日期问题
https://gitee.com/mengxu2018/jdk-new-feature/tree/master/jdk8-datetime

如果用jpa可以看下面文章，老的jdk一般用java.sql.Timestamp,可以看到这个是sql这个package下面的
https://www.baeldung.com/jpa-java-time