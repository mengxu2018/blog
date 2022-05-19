---
title: java fileinputstream block 
date: 2022-05-07 20:44:15
tags: java
---

https://stackoverflow.com/questions/15218750/when-does-fileinputstream-read-block
普通文件是不会block的，只有named pipe才会block
所以普通文件内容空的时候直接返回负1

关于io block，网络上的字节如果没过来是会block的
