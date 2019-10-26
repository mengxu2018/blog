---
title: FileChannel ByteBuffer
date: 2019-08-11 13:17:41
tags: IO
---

FileChannel示例代码
```
public class FileChannelWriter {
    public static void main(String[] args) {
        String file = "test_write_using_filechannel.txt";
        try (RandomAccessFile writer = new RandomAccessFile(file, "rw");
             FileChannel channel = writer.getChannel()) {
            ByteBuffer buff = ByteBuffer.wrap("Hello".getBytes(StandardCharsets.UTF_8));

            channel.write(buff);
            System.out.println(channel.position());

            System.out.println(buff.position());
//            ByteBuffer buff2 = ByteBuffer.wrap("Hello world".getBytes(StandardCharsets.UTF_8));
            channel.position(3);
            buff.position(4);
            System.out.println(channel.position());
            channel.write(buff);
            System.out.println(channel.position());

            // verify
            RandomAccessFile reader = new RandomAccessFile(file, "r");
            System.out.println(reader.readLine());
            reader.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
发现channel写buffer的时候buffer自己的文件指针也随着变化 ，position如果是11，下次读写从12开始

https://medium.com/@xunnan.xu/its-all-about-buffers-zero-copy-mmap-and-java-nio-50f2a1bfc05c

linux sendfile是用来实现zero-copy的，就是java的FileChannel.transferTo
linux epoll是用来实现nio的selector的
前者是网络传输增强性能避免用户空间的buffer的，后者是io的模型用来减少所需要的线程数量

http://man7.org/linux/man-pages/man7/epoll.7.html
The epoll API performs a similar task to poll(2): monitoring multiple file descriptors to see if I/O is possible on any of them.  The epoll API can be used either as an edge-triggered or a level-triggered interface and scales well to large numbers of watched file descriptors.

A new java.nio.channels.SelectorProvider implementation that is based on the Linux epoll event notification facility is included
If linux kernel >= 2.6 is detected, then the java.nio.channels.spi.SelectorProvider will use epoll.
```
public static Selector open() throws IOException {
    return SelectorProvider.provider().openSelector();
}
```
https://www.baeldung.com/java-nio-selector
https://github.com/mengxu2018/java-code-sample/tree/master/simple-nio/src/com/xu/nio/EchoTest.java