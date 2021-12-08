---
title: maven
date: 2019-05-26 22:51:52
tags: maven
---


### 阿里云配置

https://developer.aliyun.com/mvn/guide



put below in pom.xml
```
 <repositories>
        <repository>
            <id>nexus-aliyun</id>
            <name>Nexus aliyun</name>
            <layout>default</layout>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <releases>
                <enabled>true</enabled>
            </releases>
        </repository>

    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>nexus-aliyun</id>
            <name>Nexus aliyun</name>
            <layout>default</layout>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <releases>
                <enabled>true</enabled>
            </releases>
        </pluginRepository>
    </pluginRepositories>
```

如果setting统一配置如下
```
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>*</mirrorOf>
  <name>阿里云公共仓库</name>
  <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

put below in build.gradle

```
buildscript {
    repositories {
        maven {
            url 'http://maven.aliyun.com/nexus/content/groups/public/'
        }
        maven {
            url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        }
    }
}

allprojects {
    repositories {
        maven {
            url 'http://maven.aliyun.com/nexus/content/groups/public/'
        }
        maven {
            url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        }
    }
}
```

### maven搜索顺序
https://cloud.tencent.com/developer/article/1532388



### maven 覆盖springboot Jackson版本
https://stackoverflow.com/questions/46475554/how-to-override-the-version-numbers-from-spring-boot-when-importing-a-bom-pom
<properties>
    <jackson-bom.version>2.12.3</jackson-bom.version>
</properties>

这次花了一个多小时来看这个问题，明明我自己的artifact依赖的Jackson是2.12.5，但是死活最后的dependence tree是2.12.5，最后发现springboot的依赖覆盖了，所以我们自己的项目需要设置系统属性，但是这个property的key是jackson-bom.version