---
title: java log implemention
date: 2019-03-26 22:51:52
tags:
---

## logback for spring data jpa
```
<configuration>
 <appender name=”STDOUT” class=”ch.qos.logback.core.ConsoleAppender”>
 <encoder class=”ch.qos.logback.classic.encoder.PatternLayoutEncoder”>
 <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M\(%line\) — %msg%n
 </pattern>
 </encoder>
 </appender>

<appender name=”FILE” class=”ch.qos.logback.core.rolling.RollingFileAppender”>
 <file>${catalina.home}/logs/playbook.log</file>
 <rollingPolicy class=”ch.qos.logback.core.rolling.TimeBasedRollingPolicy”>
 <! — daily rollover →
 <fileNamePattern>${catalina.home}/logs/playbook.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
 <timeBasedFileNamingAndTriggeringPolicy
 class=”ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP”>
 <! — or whenever the file size reaches 50MB →
 <maxFileSize>50MB</maxFileSize>
 </timeBasedFileNamingAndTriggeringPolicy>
 <! — keep 30 days’ worth of history →
 <maxHistory>30</maxHistory>
 </rollingPolicy>
 <encoder>
 <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M\(%line\) — %msg%n</pattern>
 </encoder>
 </appender>

<logger name=”ch.qos.logback” level=”WARN” />
 <logger name=”com.demo.test” level=”DEBUG” />
 <logger name=”org.springframework” level=”DEBUG” />
 <logger name=”org.springframework.beans” level=”DEBUG” />
 <logger name=”org.hibernate.SQL” level=”DEBUG” />
 <logger name=”org.hibernate.type.descriptor.sql” level=”TRACE” />
 <root level=”INFO”>
 <appender-ref ref=”STDOUT” />
 <appender-ref ref=”FILE” />
 </root>
</configuration>
```
注意hibernate的log 需要 configure Hibernate to log via SLF4J,
System.setProperty("org.jboss.logging.provider", "slf4j");
同时"hibernate.show_sql"设置为 "false"， 直接用logback,不要这种自带的控制台输出

## tomcat log
所有的控制台输出都会重定向到tomcat/logs/catalina.out，但是maven tomcat7:run启动embended tomcat貌似没有这个文件
tocmat/logs/localhost.out 也会记录用户没有catch到的异常

## reference
https://tomcat.apache.org/tomcat-8.5-doc/logging.html
https://stackoverflow.com/questions/55411338/why-the-hibernate-does-not-work-with-logback