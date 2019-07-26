---
title: sprintboot docker deployment
date: 2019-03-31 22:51:52
tags: spring
---

## build code
```bash
git clone https://github.com/mengxu2018/springboot.git
cd springboot/spring-boot-crud
mvn package # will generate spring-boot-crud.jar under target directory
```

## build docker image
```bash
docker build -t springboot-crud-sample .
docker inspect springboot-crud-sample  #  you will find userful info under node "ContainerConfig"
```

## run docker image
```bash
docker run -d -p 9080:8080 springboot-crud-sample
docker inspect 573fcd31c442  # 573fcd31c442 is the container id, can find "Mounts" here
```

## test the client
visit http://192.168.77.140:9080/