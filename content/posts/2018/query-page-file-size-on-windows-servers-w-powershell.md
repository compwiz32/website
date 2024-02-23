---
date: 2018-08-30
authors: [mike]
description: Learn how to query the pagefile info of a computer via PowerShell along with sizing guidelines for page files on servers
draft: false
image: /images/2018/Query-Pagefile/Balloons.webp
slug: query-page-file-size-on-windows-servers-w-powershell
tags: PowerShell
title: Get-PageFileInfo - Query pagefile size with PowerShell
---

I have recently been dealing with a phenomenon that I personally haven't had to think about for quite some time: **managing the pagefile size of servers**.

Over the years, I have probably read nine or ten different guides on size recommendations for the Windows pagefile. I once read that you don't need a page file (*that's crazy - you do need one*!) and I have also read some posts where the author did some crazy math to determine the optimal size of the file. At the end of the day though, I think most admins have left the pagefile set to "system managed" for the vanilla VM's that they manage, at least I know that's the case at my organization. To be clear, I am talking about the typical VM/server running with 4-16 GB of RAM.

Here's the rub though: As available server memory increases in size, the need for a page file decreases and it's usefulness fades. I personally cut my teeth with virtualization in the ESX3 days running 32-bit servers that had 1-2GB of RAM MAX!!! Sometimes there would be 4GB of RAM but rarely due to the cost of RAM and *even then* you had to monkey around with with /PAE switch (Physical Address Extension) to make it all readable. Back then, the pagefile had a useful purpose as a temporary solution for apps that consumed all the available  RAM in a short period of time. Thankfully, this is no longer a big issue today.

Somewhere along the way, right or wrong, we stopped worrying about the pagefile in our gold image and just left it as the default: system managed. Back in the day, the magic formula was 1.5x the amount of RAM. For example, 4 GB of RAM would equal a pagefile that could grow to about 6GB in size. But that formula is just nuts if your VM (or physical box) has 32 GB or more. The old rule of thumb would create a pagefile of 48 GB (32 GB * 1.5x), and that pagefile would probably rarely ever get used by the O/S due to all the available RAM!!!

I have been seeing more servers with larger RAM sizes: we're talking 32, 64 GB, 96 GB of RAM and upwards of 256 GB in VM's. I know that there are plenty of servers that get built with much more RAM, but I have not had many instances were my everyday VM's need more than 16-32 GB of RAM. Having many VM's running 64, 96, 128 GB of RAM (or higher) with those sizes is still something new for me. As far as pagefile sizes go, I am sure you can imagine that 64 GB with a system managed page file is not a great idea and it makes for some ridiculous page files that are doing nothing but wasting space on your back-end SAN or hyper-converged appliances.

I found a guide on Microsoft's website which is pretty good for sizing. The [blog post](https://blogs.technet.microsoft.com/motiba/2015/10/15/page-file-the-definitive-guide/) goes into pretty deep detail of the how's and why's of pagefile settings. The article doesn't make "an official" recommendation but they do suggest this:

- On System with up to **256GB RAM:  8-12 GB** size for Kernel Memory Dump
- On System with up to **1.5TB RAM: 8-32 GB** size for Kernel Memory Dump

I think that those guidelines seem pretty much right on the mark and really illustrates the lessening importance of the pagefile. Using those metrics, I figured it was time to write a function that I could use to query the servers in my environment and see which ones had a pagefile that was set abnormally low or high. The function below takes the info received from looking up pagefile info via the CIM instances and renames some of the parameters to more useful, more descriptive names. I have the full function pasted below. However, the latest, greatest version of my code will always be in my [GitHub repo](https://github.com/compwiz32/) and the direct link to the function is [here](https://github.com/compwiz32/PowerShell/blob/master/Inventory-Tools/Get-PageFileInfo.ps1) .

With this code you should be able to quickly scan your environment for servers that are configured with page files that are not optimal. Next week, I'll be posting about how to then set the pagefile info via PowerShell. If you find this article useful, please leave me a comment below. Also, don't forget you can join my mail list for weekly email summaries of my blog posts! My website also supports RSS feeds if that's your thing too.

Stay tuned and thanks for reading!

```PowerShell
Function Get-PageFileInfo {

<#
 .Synopsis
  Returns info about the page file size of a Windows computer. Defaults to local machine.

 .Description
  Returns the pagefile size info in MB. Also returns the PageFilePath, PageFileTotalSize, PagefileCurrentUsage,
  and PageFilePeakusage. Also returns if computer is using a TempPafeFile and if the machine's pagefile is
  managed by O/S (AutoManaged: true) or statically set (AutoManaged: False)



 .Example
  Get-PageFileInfo -computername SRV01
  Returns pagefile info for the computer named SRV01

  Computer             : SRV01
  FilePath             : C:\pagefile.sys
  AutoManagedPageFile  : True
  TotalSize (in MB)    : 8192
  CurrentUsage (in MB) : 60
  PeakUsage (in MB)    : 203
  TempPageFileInUse    : False


 .Example
  Get-PageFileInfo SRV01, SRV02
  Returns pagefile info for two computers named SRV01 & DC02.

  Computer             : SRV01
  FilePath             : C:\pagefile.sys
  AutoManagedPageFile  : True
  TotalSize (in MB)    : 8192
  CurrentUsage (in MB) : 60
  PeakUsage (in MB)    : 203
  TempPageFileInUse    : False

  Computer             : SRV02
  FilePath             : C:\pagefile.sys
  AutoManagedPageFile  : True
  TotalSize (in MB)    : 8192
  CurrentUsage (in MB) : 0
  PeakUsage (in MB)    : 0
  TempPageFileInUse    : False

.Example
  Get-PageFileInfo SRV01, SRV02, SRV03 | Format-Table
  Returns pagefile info for three computers named SRV01, SRV02 & SRV03 in a table format.


  Computer  FilePath        AutoManagedPageFile TotalSize (in MB) CurrentUsage (in MB) PeakUsage (in MB) TempPageFileInUse
  --------  --------        ------------------- ----------------- -------------------- ----------------- -----------------
  SRV01    C:\pagefile.sys                True              8192                   60               203             False
  SRV02    C:\pagefile.sys                True             13312                    0                 0             False
  SRV03    C:\pagefile.sys                True              2432                    0                 0             False


  .Parameter computername
  The name of the computer to query. Required field.

 .Notes
  NAME: Get-PageFileInfo
  AUTHOR: Mike Kanakos
  Version: v1.1
  LASTEDIT: Thursday, August 30, 2018 2:19:18 PM

  .Link


#>

[CmdletBinding()]
Param(
    [Parameter(Mandatory=$True,ValueFromPipeline=$True)]
    [string[]]$ComputerName
)

# Main Part of function


Foreach ($computer in $ComputerName)
{

  $online= Test-Connection -ComputerName $computer -Count 2 -Quiet
    if ($online -eq $true)
     {
      $PageFileResults: Get-CimInstance -Class Win32_PageFileUsage -ComputerName $Computer | Select-Object *
      $CompSysResults: Get-CimInstance win32_computersystem -ComputerName $computer -Namespace 'root\cimv2'

      $PageFileStats: [PSCustomObject]@{
        Computer: $computer
        FilePath: $PageFileResults.Description
        AutoManagedPageFile: $CompSysResults.AutomaticManagedPagefile
        "TotalSize(in MB)": $PageFileResults.AllocatedBaseSize
        "CurrentUsage(in MB)" : $PageFileResults.CurrentUsage
        "PeakUsage(in MB)": $PageFileResults.PeakUsage
        TempPageFileInUse: $PageFileResults.TempPageFile
      } #END PSCUSTOMOBJECT
     } #END IF
    else
     {
        # Computer is not reachable!
        Write-Host "Error: $computer not online" -Foreground white -BackgroundColor Red
     } # END ELSE


  $PageFileStats

} #END FOREACH


} #END FUNCTION
```
