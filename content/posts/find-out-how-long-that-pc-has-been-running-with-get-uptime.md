---
title: "Monitor Multiple PCs' Uptime with Ease Using Get-PCUptime in PowerShell"
tags: [PowerShell]
date: 2020-07-15
authors: [mike]
draft: false
image: "/images/2020/Get-PCUptime/clocks.jpg"
slug: "find-out-how-long-that-pc-has-been-running-with-get-uptime"
description: "Get-PCUptime is a PowerShell tool built to get the up-time from multiple computers quickly and display the results."
---

**TABLE OF CONTENTS**

- [Uptime and PowerShell7](#uptime-and-powershell7)
- [Finding the relevant Information](#finding-the-relevant-information)
- [Storing the date/time information](#storing-the-datetime-information)
- [Formatting the Output](#formatting-the-output)
- [Bringing it all together](#bringing-it-all-together)


One thing that frustrates me is how hard it can be to find out how long a computer is powered on. Yes, there are [many ways](https://stackoverflow.com/questions/11606774/how-to-get-the-system-uptime-in-windows) to find out the up-time of a computer, but most are not "PowerShell friendly". If I want to use any of the methods listed in the link above in a script, then I have to manipulate string data or other options which are even worse. I built myself a simple utility to solve this dilemma. Let me show you how I did it.

## Uptime and PowerShell7

Microsoft has made it easy to find the uptime of a computer. In PowerShell7, Microsoft has included a function named `Uptime`. Let's review what this tool produces and if it fulfills my need.

```PowerShell
PS C:\Users\mkana> uptime

Days              : 17
Hours             : 3
Minutes           : 26
Seconds           : 53
Milliseconds      : 0
Ticks             : 14812130000000
TotalDays         : 17.1436689814815
TotalHours        : 411.448055555556
TotalMinutes      : 24686.8833333333
TotalSeconds      : 1481213
TotalMilliseconds : 1481213000


PS C:\Users\mkana> uptime -since

Saturday, June 27, 2020 7:29:36 PM
```

The command displays the time since last boot and gives that time in multiple counters: `TotalDays`, `TotalMinutes`, `TotalSeconds`, and finally `TotalMilliSeconds`. It also has a parameter named `-since` that returns the full date.

The output is nice, but it misses the mark for me for a few reasons. First is that I can see that this computer has been up for 17 days, but the numbers are not whole numbers. The decimals carry out over 10 places sometimes. That's too much precision and unnecessary.

A `Days` counter is helpful, but it still leaves me having to figure out the calendar date and the day of week unless I use the `-since` parameter. The full date display is a string. If I want the day of the week, I need to parse that out of the string, which isn't optimal.

Another thing that doesn't work for me is the `Uptime` cmdlet is PowerShell7 only. If I run this against a PS5 session, it will fail. I can make it work through some voodoo magic, but I don't want to deal with all that nonsense. Obviously this problem goes away as time progresses and PS7 becomes the standard on more machines. But I work almost only with servers, and I still support a decent amount of non PS7; the cmdlet wont be available on most machines.

Since the `Uptime` cmdlet doesn't give me exactly what I want, I can build a tool that can fill the gaps and it will give me the output I want without compromises. I need this tool to return a few bits of information. It should be quick and easy to make; so let's get started!

## Finding the relevant Information

The date information is easy to work with once you know where to get it from. It's available via WMI/CIM in the `Win32_OperatingSystem` class. That class contains the `LastBootUpTime` property, which contains all the date/time information we need.

```PowerShell
PS C:\Scripts\GitRepos> Get-CimInstance Win32_OperatingSystem | Get-Member | where name -like 'last*'


   TypeName: Microsoft.Management.Infrastructure.CimInstance#root/cimv2/Win32_OperatingSystem

Name           MemberType Definition
----           ---------- ----------
LastBootUpTime Property   CimInstance#DateTime LastBootUpTime {get;}
```

A peek at the members for `LastBootupTime` reveals all the date/time parameters.

```PowerShell
PS C:\Scripts\GitRepos> (Get-CimInstance Win32_OperatingSystem).lastbootuptime | Get-Member | where membertype -eq Property

   TypeName: System.DateTime

Name        MemberType Definition
----        ---------- ----------
Date        Property   datetime Date {get;}
Day         Property   int Day {get;}
DayOfWeek   Property   System.DayOfWeek DayOfWeek {get;}
DayOfYear   Property   int DayOfYear {get;}
Hour        Property   int Hour {get;}
Kind        Property   System.DateTimeKind Kind {get;}
Millisecond Property   int Millisecond {get;}
Minute      Property   int Minute {get;}
Month       Property   int Month {get;}
Second      Property   int Second {get;}
Ticks       Property   long Ticks {get;}
TimeOfDay   Property   timespan TimeOfDay {get;}
Year        Property   int Year {get;}
```

## Storing the date/time information

Now that I know where the data is, I can work with the data and manipulate it to get the desired output. I need to create some variables to hold the information for later use.

```PowerShell
$OSInfo: Get-CimInstance Win32_OperatingSystem
$LastBootTime: $OSInfo.LastBootUpTime
```

Earlier I mentioned that I wanted to include `DayOfWeek` and `LastBootTime`. I also want `Days`, `Hours` and `Minutes` in my tool. It would be useful to have the total amount of hours a computer has been online since last boot time. That value doesn't exist outright, but I can calculate it with a time-span.

```PowerShell
PS C:\Scripts\GitRepos> $Uptime: (New-TimeSpan -start $lastBootTime -end (Get-Date))
PS C:\Scripts\GitRepos> $uptime

Days              : 17
Hours             : 8
Minutes           : 45
Seconds           : 28
Milliseconds      : 485
Ticks             : 15003284858475
TotalDays         : 17.3649130306424
TotalHours        : 416.757912735417
TotalMinutes      : 25005.474764125
TotalSeconds      : 1500328.4858475
TotalMilliseconds : 1500328485.8475
```

## Formatting the Output

Once I have all the values, I need to think about displaying the information in a relevant format. I must tackle a few things like rounding the `TotalHours` and pulling out the buried data for `DayOfWeek` and `LastBootTime` . I have six pieces of information to display, but not all of those items are available to display as I would like. I can use Calculated Properties (also called Expressions) to tweak the properties to display as I would like.

If you are not familiar with Calculated Properties, let me walk you through an example. For a deeper dive, check out this [resource](https://mcpmag.com/articles/2017/01/19/using-powershell-calculated-properties.aspx). A calculated property is a mini hash-table (a key/value pair). I can write the syntax of a calculated property a few ways.

```PowerShell
#displayed as a one-liner

@{ Name: '';  Expression: {}}


#displayed as a multi-line

@{
    Name: ''
    Expression: {}
 }
```

An example of how to use this would be if I needed to change a property to have a unique name or manipulate the value of the property. For my version of the uptime tool, I want to display `TotalHours` but I need to round the value to a whole number. I use a calculated property to take care of the rounding.

```PowerShell
@{
    Name      : 'TotalHours'
    Expression: { [math]::Round($Uptime.TotalHours) }
  }
```

The first part of the calculated property is the display name. `Name` will contain what will be the object name in the output. I can change this to anything I like, but for my uptime tool, I like the name `TotalHours`.

The second part, called `Expression` is the value I want displayed. In that expression, I can use code to change the output to how I like. In this example, I am using the MathRound method to round that number for me to a whole number. I can create expressions for all my displayed output if needed.

The only downside is that they can be hard to read when there are multiple expressions in a `Select-Object` statement. We can solve that by using a variable to hold all the formatting for `Select-Object` and take advantage of PowerShell's allowance of line breaks to help display the code in a more readable format. I can then use that variable in the output statement.

```PowerShell

$SelectProps:
    'Days',
    'Hours',
    'Minutes',
    @{
        Name      : 'TotalHours'
        Expression: { [math]::Round($Uptime.TotalHours) }
    },
    @{
        Name      : 'LastBootTime'
        Expression: { $LastBootTime }
    },
    @{
        Name      : 'DayOfWeek'
        Expression: { $LastBootTime.DayOfWeek }
    }


$Uptime | Select-Object $SelectProps
```

That block of code will produce this output:

```PowerShell
Days         : 17
Hours        : 8
Minutes      : 47
TotalHours   : 417
LastBootTime : 6/27/2020 7:29:36 PM
DayOfWeek    : Saturday
```

## Bringing it all together

I have found the data I want and figured out the formatting. A function pulls it all together into a finished piece of code that is reusable and portable. The completed `Get-PCUptime` function includes help and examples.

```PowerShell
function Get-PCUptime {

    <#
    .SYNOPSIS
        Displays the current uptime and last boot time for one or more computers.

    .DESCRIPTION
        Displays the current uptime and last boot time for one or more computers by querying CIM data. Remote computers
        are queried via WINRM using CIMInstance.

    .PARAMETER ComputerName
        Then name of a computer to query. Valid aliases are Name, Computer, & PC.

    .PARAMETER Credential
        Alternate credentials that can be used to run the function as another user.

    .EXAMPLE

        Get-PCUptime

        Days         : 40
        Hours        : 20
        Minutes      : 59
        TotalHours   : 981
        LastBootTime : 4/7/2020 10:38:44 AM
        DayOfWeek    : Tuesday

        Returns uptime stats for the local computer

    .EXAMPLE

        Get-PCUpTime | Format-Table

        Days Hours Minutes TotalHours LastBootTime          DayOfWeek
        ---- ----- ------- ---------- ------------          ---------
        53      22      17       1294 4/7/2020 10:38:44 AM    Tuesday

        Returns uptime stats for the local computer and display results in  table format

    .EXAMPLE

        $dc: 'DC01','DC02','DC03'

        Get-PCUptime $dc | Format-Table -AutoSize

        Days Hours Minutes TotalHours LastBootTime          DayOfWeek PSComputerName
        ---- ----- ------- ---------- ------------          --------- --------------
        12      0      59        289  5/19/2020 7:16:18 AM  Tuesday   DC01
        105     0      25       2520  2/16/2020 7:49:49 AM  Sunday    DC02
        205     9      57       4930  11/7/2019 10:17:50 PM Thursday  DC03

        Returns uptime stats for three remote computers.

    .INPUTS
        Computername
        Accepts input from pipeline

    .OUTPUTS
        Output (if any)

    .NOTES
       NAME:           Get-PCUptime.ps1
       AUTHOR:         Mike Kanakos
       DATE CREATED:   2020-05-18
    #>

    [CmdletBinding()]
    param (
        [Alias("PC", "Computer", "Name")]
        [Parameter(
            ValueFromPipeline,
            ValueFromPipelineByPropertyName
        )]
        [string[]]
        $ComputerName: $env:COMPUTERNAME,

        [PSCredential]
        $Credential
    )

    begin {

        $PcArray: [System.Collections.Generic.List[string]]::new()

        $code: {
            $OSInfo: Get-CimInstance Win32_OperatingSystem
            $LastBootTime: $OSInfo.LastBootUpTime
            $Uptime: (New-TimeSpan -Start $lastBootTime -End (Get-Date))

            $SelectProps:
            'Days',
            'Hours',
            'Minutes',
            @{
                Name      : 'TotalHours'
                Expression: { [math]::Round($Uptime.TotalHours) }
            },
            @{
                Name      : 'LastBootTime'
                Expression: { $LastBootTime }
            },
            @{
                Name      : 'DayOfWeek'
                Expression: { $LastBootTime.DayOfWeek }
            }


            $Uptime | Select-Object $SelectProps
        } #End $code
    }

    process {
        foreach ($pc in $ComputerName) {
            $PcArray.Add($pc)
        }
    } #End Process

    end {

        Invoke-Command -ComputerName $PcArray -ScriptBlock $code -ErrorAction SilentlyContinue -ErrorVariable NoConnect |
            Select-Object * -ExcludeProperty RunspaceID

        foreach ($fail in $NoConnect) {
            Write-Warning "Failed to run on $($fail.TargetObject)"
        } #end foreach warning loop
    }
} #end function
```

Here's what it looks like in use:

![Get-PCInfo-in-Action](/images/2020/Get-PCUptime/get-pcuptime.webp)

You can find a copy of this code in my [GitHub repo](https://github.com/compwiz32/PowerShell). The repo contains all tools I create and you are welcome to download them and use as you wish. I will include this function in a Tools module that I will blog about and share online via my repo. I'll be blogging about I how built some other tools in the module and also finally how I built the module in upcoming posts.
