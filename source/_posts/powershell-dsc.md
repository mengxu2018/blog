---
title: powershell dsc get started
date: 2020-06-03 20:44:15
tags: powershell
---

## guide
https://docs.microsoft.com/zh-cn/powershell/scripting/dsc/configurations/write-compile-apply-configuration?view=powershell-7

## hello world
注意最后一行的HelloWorld一定要加，否则编译不会生成mof文件
```
Configuration HelloWorld {

    # Import the module that contains the File resource.
    Import-DscResource -ModuleName PsDesiredStateConfiguration

    # The Node statement specifies which targets to compile MOF files for, when this configuration is executed.
    Node 'localhost' {

        # The File resource can ensure the state of files, or copy them from a source to a destination with persistent updates.
        File HelloWorld {
            DestinationPath = "d:\work\HelloWorld.txt"
            Ensure = "Present"
            Contents   = "Hello World from DSC!"
        }
    }
}

HelloWorld
```

## 编译运行
. ./HelloWorld.ps1
Start-DscConfiguration -Path .\HelloWorld -Verbose -Wait   //会发现d:\work\HelloWorld.txt


## troubleshooting
powershell ise需要admin打开
wrm服务要打开
