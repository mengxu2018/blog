---
title: powershell module
date: 2020-06-18 20:44:15
tags: powershell
---

## guide
https://www.pstips.net/create-mini-module.html

## 打开 powershell ise

```
function Get-BIOS
{  param($ComputerName, $Credential)
   Get-WmiObject -Class Win32_BIOS @PSBoundParameters
}


$name = 'Get-BIOS'


New-Item -Path $home\Documents\WindowsPowerShell\Modules\$name\$name.psm1 -ItemType File -Force -Value "function $name { $((Get-Item function:\$name).Definition) }" 


打开新窗口

Get-Module  -Name Get-BIOS
Get-BIOS
```

 