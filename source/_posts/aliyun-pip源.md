---
title: aliyun pip仓库
date: 2020-03-21 20:44:15
tags: python
---

mkdir ~/.pip

cat > ~/.pip/pip.conf << EOF

[global]
trusted-host=mirrors.aliyun.com
index-url=https://mirrors.aliyun.com/pypi/simple/

EOF