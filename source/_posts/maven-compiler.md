---
title: maven compiler
tags:
---

## default compiler
`<forceJavacCompilerUse>`
compiler can now use javax.tools if available in your current jdk, you can disable this feature using -Dmaven.compiler.forceJavacCompilerUse=true or in the plugin configuration
默认会用当前jdk下面的javax.tools.JavaCompiler来编译，所谓当前的jdk就是运行maven的jdk
比如
```
C:\Users\lenovo>mvn -v
Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-25T02:41:47+08:00)
Maven home: D:\software\apache-maven-3.6.0\bin\..
Java version: 1.8.0_191, vendor: Oracle Corporation, runtime: C:\java\jdk1.8.0_191\jre
```
https://maven.apache.org/plugins/maven-compiler-plugin/compile-mojo.html#forceJavacCompilerUse

## 设置编译级别和运行jvm
```
<properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

## Compiling Sources Using A Different JDK
```
 <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
      <verbose>true</verbose>
      <fork>true</fork>
      <executable><!-- path-to-javac --></executable>
      <compilerVersion>1.3</compilerVersion>
    </configuration>
 </plugin>
```

## 总结
其实默认的javax.tools里面的编译器也够了

## scala compiler
scala的编译器是通过scala.version来指定的，具体可以看.m2\repository\org\scala-lang\scala-compiler\2.11.7
<properties>
    <!--specify scala compiler, 2.12 will fail-->
    <!--.m2\repository\org\scala-lang\scala-compiler\2.11.7-->
    <!--you can find that the jars are named with 2.11 scala, D:\software\spark-2.4.1-bin-hadoop2.7\jars\spark-core_2.11-2.4.1.jar-->
    <scala.version>2.11.7</scala.version>
</properties>

<plugin>
    <groupId>net.alchim31.maven</groupId>
    <artifactId>scala-maven-plugin</artifactId>
    <version>4.0.2</version>
    <executions>
        <execution>
            <goals>
                <goal>compile</goal>
                <goal>testCompile</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <jvmArgs>
            <jvmArg>-Xms64m</jvmArg>
            <jvmArg>-Xmx1024m</jvmArg>
        </jvmArgs>
    </configuration>
</plugin>