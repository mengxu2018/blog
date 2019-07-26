---
title: 随机生成大文件
date: 2019-06-03 22:51:52
tags: java
---

### code
```
import java.io.IOException;
import java.io.RandomAccessFile;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;
import java.util.Random;

public class Test {
    public static void main(String[] args) throws IOException {
        byte[] buffer = "Help I am trapped in a fortune cookie factory\n".getBytes();
        byte[] buffer2 = "good morning boy".getBytes();
        int number_of_lines = 400000*20;

        Random rand = new Random();


        FileChannel rwChannel = new RandomAccessFile("mylarge.txt", "rw").getChannel();
        ByteBuffer wrBuf = rwChannel.map(FileChannel.MapMode.READ_WRITE, 0, buffer.length * number_of_lines);
        for (int i = 1; i < number_of_lines; i++)
        {
            int n = rand.nextInt(50) + 1;
            if (n % i == 0) {
                wrBuf.put(buffer);
            } else {
                wrBuf.put(buffer2);
            }

        }
        rwChannel.close();
    }
}

```
