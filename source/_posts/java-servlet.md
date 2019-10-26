---
title: java servlet
date: 2019-10-26 11:42:16
tags: java
---

这个文章主要说明servlet2.5, 3.0, 3.1的差异

## servlet2.5
![tomcat-nio](/images/tomcat-servlet-version.png)
tomcat本身在servlet2.5也就是tomcat6的时候就已经实现了nio，但是仅仅在建立连接和解析request header方面nio，这个在上图可以看的很清楚

## servlet3.0
在servlet3.0就是tomcat7以后，实现了异步的servlet，但是这个异步仅仅是对request的处理是异步的，就是将这次请求走到servlet的时候放到一个单独的线程池，不要占用container本身处理请求的线程池。这个感觉提升并不大，因为对request stream io本身还是普通的io, Servlet 3.0 allowed asynchronous request processing but only traditional I/O was permitted. In other words, with Servlet 3.0, only the request processing part became async, but not the I/O for serving the requests and responses. If enough threads block, this results in thread starvation and affects performance. 3.0只能叫异步，3.1才是nio
```
import javax.servlet.AsyncContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(value = "/simpleAsync", asyncSupported = true)
public class SimpleAsyncHelloServlet extends HttpServlet {

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        AsyncContext asyncContext = request.startAsync();

        asyncContext.start(() -> {
            new LongRunningProcess().run();
            try {
                asyncContext.getResponse().getWriter().write("Hello World!");
            } catch (IOException e) {
                e.printStackTrace();
            }
            asyncContext.complete();
        });

    }

}
```
此时，我们先通过request.startAsync()获取到该请求对应的AsyncContext，然后调用AsyncContext的start()方法进行异步处理，处理完毕后需要调用complete()方法告知Servlet容器。start()方法会向Servlet容器另外申请一个新的线程（可以是从Servlet容器中已有的主线程池获取，也可以另外维护一个线程池，不同容器实现可能不一样），然后在这个新的线程中继续处理请求，而原先的线程将被回收到主线程池中。事实上，这种方式对性能的改进不大，因为如果新的线程和初始线程共享同一个线程池的话，相当于闲置下了一个线程，但同时又占用了另一个线程。

## servlet3.1
在servlet3.1后，就是tomcat8以后实现了non blocking io, With Servlet 3.1 NIO, this problem is solved by ReadListener and WriteListener interfaces. These are registered in ServletInputStream and ServletOutputStream. The listeners have callback methods that are invoked when the content is available to be read or can be written without the servlet container blocking on the I/O threads. So these I/O threads are freed up and can now serve other request increasing performance.
参考例子 https://github.com/mengxu2018/java-code-sample/tree/master/servlet3.1



## ref
https://www.cnblogs.com/davenkin/p/async-servlet.html
![servlet-spec](/images/servlet-spec.png)


