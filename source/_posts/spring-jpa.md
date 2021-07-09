---
title:  spring jpa
tags: spring
date: 2021-06-26 22:51:52
---

## spring jpa tips
- Jpa entitymanager不是线程安全的 ，但是我们可以在单列的DAO里面注入他是因为注入的实际是代理，这个proxy会为每一个线程创建一个entitymanager

- springboot是默认打开spring.jpa.open-in-view=true

- spring data repository是默认开启事务的， entitymanager并没有, 可以看debug TransactionInterceptor.java

- spring data repository当然也是通过entitymanager 去操作entity

- @Transactional默认只会回滚unchecked/runtime exception

- spring的java配置比xml配置好的地方，比如可以很方便代码里面控制dev/uat/prod使用的数据库，也可以很方便的指定h2数据库的位置，可以很方便的看源码配置bean的参数，不像xml需要查询文档，当然xml和java config是可以配合使用的

- h2是非常好的数据库，可以方便他人迅速部署自己的项目

- hibernate N+1问题通过join fetch来解决是一个可以选择的办法

- 在 JPA 中不建议使用 Entity 实体对象间的一对多、多对多之类的功能，也就是说 Entity 之间尽量独立，要关联查询时，通过 JPQL 或者 SQL 查询或更新就行，避免一些级联关系之间的隐式操作，代码含义更透明，方便应对后续的扩展或针对性的优化改造。

## code example
https://gitprod.company.com/employeeid/spring/tree/master/spring-mvc-jpa-h2