---
title: regular expression
date: 2019-01-21 22:51:52
tags: java
---

## java 
分组捕获
```
   String line = "This order was placed for QT3000! OK?";
        Pattern pattern = Pattern.compile("(.*?)(\\d+)(.*)");
        Matcher matcher = pattern.matcher(line);

        while (matcher.find()) {
            System.out.println("group 1: " + matcher.group(1));
            System.out.println("group 2: " + matcher.group(2));
            System.out.println("group 3: " + matcher.group(3));
        }
```

开头结尾1
```
        String str="hello world,hello java,hello java";

        System.out.println("===========匹配字符串===========");
        //匹配str中所有字符串hello，这时str中3个hello都能匹配上，通过下面打印的匹配上的字符串的位置可以看出
        Pattern p=Pattern.compile("hello");
        Matcher m=p.matcher(str);
        while(m.find()){
            System.out.println(m.group()+"   位置：["+m.start()+","+m.end()+"]");
        }
        System.out.println("===========匹配字符串，并且该字符串是在开头的位置===========");
        // ^表示匹配字符串的开头，但是如何在[]里面则表示非，如[^a-f] 不匹配a-f
        // "hello"和"^hello"的区别就是：前者匹配时不管是不是在开头位置，只要能匹配就行，后者则是不但要能匹配而且还要是在开头的位置。这时str中3个hello只有第1个能匹配上。
        p=Pattern.compile("^hello");
        m=p.matcher(str);
        System.out.println("is:" + m.matches());
        while(m.find()){
            System.out.println(m.group()+"   位置xxx：["+m.start()+","+m.end()+"]");
        }

        System.out.println("===========匹配字符串===========");
        //这时str中两个java都能匹配上
        p=Pattern.compile("java");
        m=p.matcher(str);
        while(m.find()){
            System.out.println(m.group()+"   位置：["+m.start()+","+m.end()+"]");
        }
        System.out.println("===========匹配字符串，并且是该字符串是在末尾的位置===========");
        //这时str中两个java只有第2个才能匹配上
        p=Pattern.compile("java$");
        m=p.matcher(str);
        while(m.find()){
            System.out.println(m.group()+"   位置：["+m.start()+","+m.end()+"]");
        }
```

开头结尾2
```
        // 这是整个匹配，直接是String的方法
        String test = "http://yahoo.com";
        System.out.println(test.matches("^(http|https|ftp)://.*$"));
        
        // 这个是编译过的表达式，也是整个匹配
        Pattern p2 = Pattern.compile("^(http|https|ftp)://.*$");
        Matcher m2 = p2.matcher(test);
        System.out.println("m2:" + m2.matches());
        
        // 这个是捕获group，取第一个group, index从1开始
        Pattern p2 = Pattern.compile("^(http|https|ftp)://.*$");
        Matcher m2 = p2.matcher(test);
        if (m2.find()) {
            System.out.println("g1:" + m2.group(1));
        }
        
        // 这个是group(0)，没有小括号分组，从前到后匹配到什么算什么
        String str="hello world,hello java,hello java";
        p=Pattern.compile("java");
        m=p.matcher(str);
        while(m.find()){
            System.out.println(m.group()+"   位置：["+m.start()+","+m.end()+"]");
        }
```