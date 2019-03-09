---
title: httpd tomcat
tags:
---

## install apache on VM1
yum install -y httpd

## start apache
sudo systemctl start httpd

## starts at boot
sudo systemctl enable httpd

## install apxs
yum install httpd-devel
- find / -name apxs
- which apxs  (this file path will be used in ./configure below)

## install gcc
yum install -y gcc*
yum install autoconf
yum install libtool

## install mod_jk
wget http://www.eu.apache.org/dist/tomcat/tomcat-connectors/jk/tomcat-connectors-1.2.46-src.tar.gz
tar -xf tomcat-connectors-1.2.46-src.tar.gz
cd tomcat-connectors-1.2.46-src/native
./buildconf.sh
./configure --with-apxs=/usr/bin/apxs
make && make install
(find / -name mod_jk.so
/usr/lib64/httpd/modules/mod_jk.so)

## Configure httpd.conf
Now that you've installed mod_jk, you need to configure it in your httpd.conf file so that you can use it.
You can find httpd.conf in the 'conf' directory of your Apache HTTPD home folder.
If httpd in installed by 'yum install httpd', this file is
`/etc/httpd/conf/httpd.conf`

mod_jk requires two entities:
- mod_jk.xxx - The Apache HTTP Server module, depending on your operating system, it will be mod_jk.so, mod_jk.nlm or MOD_JK.SRVPGM (see the build section).
- workers.properties - A file that describes the host(s) and port(s) used by the workers (Tomcat processes). A sample workers.properties can be found under the conf directory in the source download.

The configuration provided below, based on the Tomcat documentation, is the minimum configuration required for mod_jk to run correctly.

` #Load mod_jk module`
LoadModule    jk_module  /usr/lib64/httpd/modules/mod_jk.so
` # Add the module (activate this lne for Apache 1.3)`
` # AddModule     mod_jk.c`
` # Where to find workers.properties`
JkWorkersFile /etc/httpd/conf/workers.properties
` # Where to put jk shared memory`
JkShmFile     /var/log/httpd/mod_jk.shm
` # Where to put jk logs`
JkLogFile     /var/log/httpd/mod_jk.log
` # Set the jk log level [debug/error/info]`
JkLogLevel    info
` # Send requests for context /examples to worker named worker1`
` # 至于为什么 balancer，https://tomcat.apache.org/connectors-doc/common_howto/loadbalancers.html`
JkMount  /playbook/* balancer

## set up two tomcat on VM2, VM3
wget http://ftp.mirror.tw/pub/apache/tomcat/tomcat-8/v8.5.38/bin/apache-tomcat-8.5.38.tar.gz

##  Configure workers.properties
The workers.properties file defines a list of Tomcat 'workers' to which Apache HTTPD can pass requests.
8009 is the ajp port
```
worker.node1.host=192.168.77.131
worker.node1.port=8009

worker.node2.host=192.168.77.132
worker.node2.port=8009

```

## test
start two tomcat
可以在两台tomcat里面都放一个playbook
第一台里面放一个webapp/playbook/1.txt, 内容是I am node1
第一台里面放一个webapp/playbook/1.txt, 内容是I am node2
systemctl restart httpd
现在访问
`http://192.168.77.130/playbook/1.txt`
会发现I am node1和I am node2交替出现
如果强制停止一台tomcat，整个服务不会中断

## refer
systemctl start httpd
systemctl restart/start/status httpd
https://tomcat.apache.org/connectors-doc/webserver_howto/apache.html
https://www.mulesoft.com/tcat/apache-tomcat-mod-jk-connector-configuration
https://tomcat.apache.org/connectors-doc/common_howto/workers.html
https://tomcat.apache.org/connectors-doc/common_howto/loadbalancers.html


