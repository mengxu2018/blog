---
title: IO
date: 2019-03-07 22:51:52
tags: java
---

## sample code, charset related

```
import com.google.common.io.Files;

import java.io.*;
import java.nio.charset.Charset;
import java.nio.charset.StandardCharsets;

/**
* https://www.zhihu.com/question/27562173
* https://www.oracle.com/technetwork/articles/javase/supplementary-142654.html
*
* https://www.baeldung.com/java-write-to-file
*/
public class Test {
   public  static void main(String[] s) throws IOException {
       // print os default charset
       Charset.defaultCharset().name();
       System.out.println( Charset.defaultCharset().name());


       // below ascii charset will be garbled code.
       FileOutputStream fos = new FileOutputStream("c:/temp/abc.txt");
       fos.write("我的".getBytes(StandardCharsets.US_ASCII));

       // below will be right
       fos.write("中国".getBytes(StandardCharsets.UTF_8));


       /**
        *
        * use below way to write string, no need to call getBytes
        */
       Writer out = new BufferedWriter(new OutputStreamWriter(
               new FileOutputStream("c:\\temp\\test.txt"), StandardCharsets.UTF_8));

       out.append("Website UTF-8").append("\r\n");
       out.append("你是 UTF-8").append("\r\n");
       out.append("我的 UTF-8").append("\r\n");

       out.flush();
       out.close();


       /**
        * append a String to the existing file:
        * use below way to write string, no need to call getBytes
        * but this way can not specify charset
        */
       Writer out2 = new BufferedWriter(new FileWriter("c:/temp/test2.txt", true));

       out2.append("Website2 UTF-8").append("\r\n");
       out2.append("你是2 UTF-8").append("\r\n");
       out2.append("我的2 UTF-8").append("\r\n");

       out2.flush();
       out2.close();


       // java 7 write string to file
       Files.write("我的", new File("c:/temp/test3.txt"), StandardCharsets.UTF_8);

   }
}
```