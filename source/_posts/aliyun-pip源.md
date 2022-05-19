---
title: aliyun pip仓库
date: 2020-03-21 20:44:15
tags: python
---

### Linux

mkdir ~/.pip

cat > ~/.pip/pip.conf << EOF

[global]
trusted-host=mirrors.aliyun.com
index-url=https://mirrors.aliyun.com/pypi/simple/

EOF


### windows
在windows文件管理器中,输入 %APPDATA%
在该目录下新建pip文件夹，然后到pip文件夹里面去新建个pip.ini文件,输入以下内容：
```
[global] 
index-url = http://mirrors.aliyun.com/pypi/simple/ 
[install] 
trusted-host=mirrors.aliyun.com
```


