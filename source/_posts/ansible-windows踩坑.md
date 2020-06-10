---
title: ansible windows setup
date: 2020-06-04 20:44:15
tags: automation
---

## 1
/etc/ansible/hosts如下，ansible_winrm_transport是关键，否则会报错    "msg": "plaintext: the specified credentials were rejected by the server",
https://www.jianshu.com/p/0ad50049adb7

[windows]
node3 ansible_user=xuhang ansible_password=xuhang ansible_connection=winrm ansible_winrm_transport=ntlm ansible_port=5985 ansible_winrm_scheme=http


## 2
https://www.cnblogs.com/herui1991/p/12304487.html
重点：千万不要yum安装ansible。选择pip3安装，或者二进制包安装。否则，即便安装了pywinrm插件也无法管理Windows主机。 
```
pip3 install --user ansible
```

## 3
[xuhang@localhost ansible]$ ansible all -m ping
[WARNING]: No python interpreters found for host node3 (tried ['/usr/bin/python', 'python3.7', 'python3.6', 'python3.5', 'python2.7', 'python2.6', '/usr/libexec/platform-python', '/usr/bin/python3',
'python'])

这个是因为远程主机是windows，他当成了linux，linux 远程主机需要安装python，至于他为什么当成linux可能是因为这个用的方式不对，不能直接测试ping，应该用下面的例子

```
---
# The playbook install 7zip
- hosts: windows
  tasks:
   - name: Ensure 7-Zip is installed via Chocolatey
     win_chocolatey:
       name: 7zip
       state: present

```

## wrm服务
这个服务在windows server2016是默认打开的，通过命令winrm quickconfig可以打开，提示默认打开，当然这个只是http的方式，https需要证书打开

## authentication 
https://docs.ansible.com/ansible/latest/user_guide/windows_winrm.html#basic
Basic authentication is one of the simplest authentication options to use, but is also the most insecure. This is because the username and password are simply base64 encoded, and if a secure channel is not in use (eg, HTTPS) then it can be decoded by anyone. Basic authentication can only be used for local accounts (not domain accounts).


https://docs.ansible.com/ansible/latest/user_guide/windows_setup.html#winrm-setup
这一步把basic authentication给打开了，basic需要用户名密码，但是可能需要先执行这个powershell脚本来enable


## 总结下就是google和百度一起搜找问题的答案
