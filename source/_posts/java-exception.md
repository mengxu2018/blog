---
title: java exception
date: 2019-10-26 11:37:23
tags: java
---

{
status: 1
message: "转账成功"
data: {} //业务成功有时候会数据返回到前台
}
{
status: 1
message: "您的余额不足" //余额不足可以当作正常业务流程提示用户
data: {} //业务成功有时候会数据返回到前台
}
{
status: 1
message: "您的套餐三个月内不能修改" //套餐不能修改可以当作正常业务流程提示用户
data: {} //业务成功有时候会数据返回到前台
}
{
status: 0
errorCode: 1001
errorMessage: "业务处理出现错误，请稍后再尝试" //数据库错误，socket错误等不应该让用户看到
exception: java.xx.socketException,etc
}


但是有的业务是需要按照异常给用户的，比如 EmailNotUniqueException, InvalidUserStateException，需要自定义异常https://stackabuse.com/how-to-make-custom-exceptions-in-java/


http://literatejava.com/exceptions/checked-exceptions-javas-biggest-mistake/
这个文章讲解了checked异常的不好的地方


https://stackoverflow.com/questions/7561550/list-of-spring-runtime-exceptions

spring内部基本都是用的unchecked exception, 因为uncheck exception不需要用户catch，如果有异常就直接输出到日志了，比如说下面的空指针
```
java.lang.NullPointerException: null
 at com.ssc.rest.controller.ManagerControllerMiddleware.delete(ManagerControllerMiddleware.java:61)
 at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
 at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
 at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
 at java.lang.reflect.Method.invoke(Method.java:497)
```
这段话会被输出到日志文件 


一般业务系统的异常都是需要自定义异常，既然自定义异常，那么余额为0或者套餐变更要求不符合还会当作异常被抛出到spring全局处理器然后给js返回错误的数据格式{errormsg:xx, errorCode:xx}？
应该是不建议的，定义这种类别的自定义异常不是好的方法,但是不绝对，
一般自定义的异常，比如用户名不唯一，邮件格式不对等
