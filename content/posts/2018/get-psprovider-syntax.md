+++
categories = ["PowerShell", "Get-PSProvider"]
date = 2018-04-15T21:11:58Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/04/powershell_2018-04-15_17-06-42.png"
slug = "get-psprovider-syntax"
summary = "cmdlet syntax and real world usage examples of how to use the Get-PSPRovider cmdlet"
tags = ["PowerShell", "Get-PSProvider"]
title = "Get-PSProvider - Overview & Examples"

+++


#### **SYNOPSIS**
Gets information about the specified Windows PowerShell provider.
<br>

#### **CMDLET ALIASES**
No aliases exist for this cmdlet
<br>

#### **SYNTAX**

```
Get-PSProvider [[-PSProvider] <String[]>] [<CommonParameters>]
```
<br>

#### **DESCRIPTION**
The `Get-PSProvider` cmdlet gets the Windows PowerShell providers in the current session. You can get a particular drive or all drives in the session.

Windows PowerShell providers let you access a variety of data stores as though they were file system drives. For information about Windows PowerShell providers, see `about_Providers`.
<br>

#### **Real World Examples:**

```
PS C:\WINDOWS\system32> Get-PSProvider
Name                 Capabilities                                                          Drives
----                 ------------                                                          ------
Registry             ShouldProcess, Transactions                                           {HKLM, HKCU}
Alias                ShouldProcess                                                         {Alias}
Environment          ShouldProcess                                                         {Env}
FileSystem           Filter, ShouldProcess, Credentials                                    {C, D, F, G..
Function             ShouldProcess                                                         {Function}
Variable             ShouldProcess                                                         {Variable}

```

