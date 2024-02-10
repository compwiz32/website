+++
categories = ["PowerShell", "Group-Policy", "Remove-GPO"]
date = 2018-03-05T15:15:25Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/04/370px-Usenet_servers_and_clients.svg-1.png"
slug = "remove-gpo"
tags = ["PowerShell", "Group-Policy", "Remove-GPO"]
title = "Remove-GPO - Overview and Examples"

+++


##### **SYNOPSIS**
Deletes a GPO.
<br>

##### **DESCRIPTION**
The `Remove-GPO` cmdlet deletes GPO's from Active Directory.
<br>

##### **SYNTAX**
```
Remove-GPO [-Domain <String>] [-KeepLinks] [-Server <String>] -Guid <Guid> [-Confirm] [-WhatIf] [<CommonParameters>]

Remove-GPO [-Name] <String> [-Domain <String>] [-KeepLinks] [-Server <String>] [-Confirm] [-WhatIf] [<CommonParameters>]
```
<br>

##### **EXAMPLES**

```
Remove-GPO -Name TestGPO
```
- deletes the GPO named **TestGPO**

