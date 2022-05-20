---
title: java threadpool
date: 2022-05-20 20:44:15
tags: java
---

## Waiting for Threads to Finish（shutdown, awaitTermination)
Basically on an ExecutorService you call shutdown() and then awaitTermination():
```
ExecutorService taskExecutor = Executors.newFixedThreadPool(4);
while(...) {
  taskExecutor.execute(new MyTask());
}
taskExecutor.shutdown();
try {
  taskExecutor.awaitTermination(Long.MAX_VALUE, TimeUnit.NANOSECONDS);
} catch (InterruptedException e) {
  ...
}
```
 关于shutdown，This method does not wait for previously submitted tasks to complete execution. Use awaitTermination to do that.
 awaitTermination，Blocks until all tasks have completed execution after a shutdown request, or the timeout occurs, or the current thread is interrupted, whichever happens first.
 这两个方法配合使用可以关闭线程池，然后等待所有任务结束

 https://stackoverflow.com/questions/1250643/how-to-wait-for-all-threads-to-finish-using-executorservice


## Waiting for Threads to Finish(invokeAll)
https://gitee.com/mengxu2018/threadlearning/blob/master/src/com/company/InvokeAllTest.java
invokeAll会等待的，Executes the given tasks, returning a list of Futures holding their status and results when all complete


## reference
https://www.baeldung.com/java-executor-wait-for-threads 