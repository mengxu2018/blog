---
title: puppeteer centos7 setup
date: 2019-01-31 22:51:52
tags:
---

## Install node, npm
```bash
// 直接下载编译好的node，编译这个非常费时间
wget https://nodejs.org/dist/v10.15.0/node-v10.15.0-linux-x64.tar.xz
tar xzf node-v10.15.0-linux-x64.tar.xz

// 设置好path
npm install -g yarn

```

## 安装好chrome在linux上需要的依赖
```bash
yum install pango.x86_64 libXcomposite.x86_64 libXcursor.x86_64 libXdamage.x86_64 libXext.x86_64 libXi.x86_64 libXtst.x86_64 cups-libs.x86_64 libXScrnSaver.x86_64 libXrandr.x86_64 GConf2.x86_64 alsa-lib.x86_64 atk.x86_64 gtk3.x86_64 -y
yum install ipa-gothic-fonts xorg-x11-fonts-100dpi xorg-x11-fonts-75dpi xorg-x11-utils xorg-x11-fonts-cyrillic xorg-x11-fonts-Type1 xorg-x11-fonts-misc -y
```

## test
```bash
mkdir puppeteer-test
cd puppeteer-test
yarn add puppeteer // 下载失败可以提前执行 npm config set puppeteer_download_host=https://npm.taobao.org/mirrors

node test.js  //test.js 内容如下

const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({
    args: ['--no-sandbox','--disable-setuid-sandbox'],


    timeout: 15000,
    //如果是访问https页面 此属性会忽略https错误
    ignoreHTTPSErrors: true,
    // 打开开发者工具, 当此值为true时, headless总为false
    devtools: false,
    // 关闭headless模式, 不会打开浏览器
    headless: true
  });
  const page = await browser.newPage();
  await page.goto('https://www.baidu.com');
  await page.screenshot({path: 'example.png'});

  await browser.close();
})();

```

## references
// docker 中使用puppeteer
// docker中已经安装好所有的动态库，同时global的puppeteer也已经安装好
// docker会设置好ENV NODE_PATH="/usr/local/share/.config/yarn/global/node_modules:${NODE_PATH}"
// https://serverfault.com/questions/594281/how-can-i-override-cmd-when-running-a-docker-image
https://github.com/alekzonder/docker-puppeteer

https://github.com/GoogleChrome/puppeteer/issues/1597
https://github.com/Googlechrome/puppeteer/issues/290