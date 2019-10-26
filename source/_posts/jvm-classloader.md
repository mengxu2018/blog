---
title: jvm classloader
date: 2019-10-26 11:33:41
tags: jvm
---
https://tomcat.apache.org/tomcat-9.0-doc/class-loader-howto.html

关于Sharedclassloader 有个没说清楚， webappclassloader会先从自己的repository寻找，找不到才会从Sharedclassloader寻找

log4j:ERROR A "org.apache.log4j.DailyRollingFileAppender" object is not assignable to a "org.apache.log4j.Appender" variable.
log4j:ERROR The class "org.apache.log4j.Appender" was loaded by 
log4j:ERROR [java.net.URLClassLoader@2f0a098b] whereas object of type 
log4j:ERROR "org.apache.log4j.DailyRollingFileAppender" was loaded by [WebappClassLoader
 context: /cdt4middleware
 delegate: false
 repositories:
 /WEB-INF/classes/
 - - - - → Parent Classloader:
java.net.URLClassLoader@2f0a098b
].
log4j:ERROR Could not instantiate appender named "RollingAppender".


这个错误是因为webapp下面有个log4j，而且sharedclassloader下面也有个log4j
因为osa框架里面的类是由sharedclassloader加载的，他如果事先加载了org.apache.log4j.Appender，而后如果DailyRollingFileAppender被webappclassloader加载的话就会报这个错误了

