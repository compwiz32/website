+++
date = 2018-09-25T15:42:18Z
description = ""
draft = true
slug = "resize-vm-disks"
title = "Resize VM Disks"

+++


```PowerShell
# Unsupported Method 
# ResizeGuestPartition has been depracted and no longer supported (but still works)

Get-HardDisk -VM "SRVWIN0923 - ININ 4.0 CIC DEV Server" | where {$_.Name -eq "Hard Disk 1"} | Set-HardDisk -CapacityGB 100 -Confirm:$false -ResizeGuestPartition
```

```PS
# Supported Method
# Script increasing VM disk size to 100GB & expands OS disk to maximum available size using Invoke-Command

$vm = IISDEVVM01
Get-HardDisk -vm $vm | Where {$_.Name -eq "Hard disk 1"} | Set-HardDisk -CapacityGB 100 -Confirm:$false
 
Invoke-Command -ComputerName $vm -ScriptBlock {
$size = Get-PartitionSupportedSize -DriveLetter C 
Resize-Partition -DriveLetter C -Size $size.SizeMax
```



