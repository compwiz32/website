+++
categories = ["PowerShell", "Get-PhysicalDisk"]
date = 2017-06-19T19:00:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/04/powershell_2018-04-15_19-52-07.png"
slug = "get-physicaldisk-options"
summary = "Powershell cmdlet options for querying Physical Disks on computers "
tags = ["PowerShell", "Get-PhysicalDisk"]
title = "Get-PhysicalDisk options"

+++


reposted from [Richard Siddaway's Blog ](https://richardspowershellblog.wordpress.com/2017/05/31/get-physicaldisk-options/) - originally published May 31, 2017

These are the `Get-PhysicalDisk` options for identifying the disk you want

```powersehll
-UniqueId <string>
-ObjectId <string>
-FriendlyName <string>
-InputObject <CimInstance#MSFT_PhysicalDisk>
-StorageSubsystem <CimInstance#MSFT_StorageSubsystem>
-StorageEnclosure <CimInstance#MSFT_StorageEnclosure>
-StorageNode <CimInstance#MSFT_StorageNode>
-StoragePool <CimInstance#MSFT_StoragePool>
-VirtualDisk <CimInstance#MSFT_VirtualDisk>
```
When dealing with disks installed in the machine then the friendly names is the easiest to use

```powershell
Get-PhysicalDisk | Format-List UniqueId, ObjectId, FriendlyName

UniqueId     : 60022480233DF060FE631B8A4EDD93A0  
ObjectId     : {1}\\W510W16\root/Microsoft/Windows/Storage/Providers_v2\SPACES_PhysicalDisk.ObjectId="{1dab9cf6-a1b4-11e6-a890-806e6f6e6963}:PD:{12e941a8-6125-c008-8806-8868642331ef}"  
FriendlyName : Msft Virtual Disk

UniqueId     : {d8e80f34-22bc-0a36-b302-d96abe30a6cc}  
ObjectId     : {1}\\W510W16\root/Microsoft/Windows/Storage/Providers_v2\SPACES_PhysicalDisk.ObjectId="{1dab9cf6-a1b4-11e6-a890-806e6f6e6963}:PD:{d8e80f34-22bc-0a36-b302-d96abe30a6cc}"  
FriendlyName : Samsung SSD 840 PRO Series  
```

