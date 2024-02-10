+++
categories = ["PowerShell", "Get-EventLog", "Event Logs"]
date = 2018-05-07T13:38:29Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/2018-05-07-000198.png"
slug = "get-eventlog-syntax-and-examples"
tags = ["PowerShell", "Get-EventLog", "Event Logs"]
title = "Get-Eventlog - Cmdlet Syntax and Examples"

+++


#### **SYNOPSIS**
Gets the events in an event log, or a list of the event logs, on the local or remote computers.
<br>

#### **CMDLET ALIASES**
none
<br>

#### **DESCRIPTION**

The `Get-EventLog` cmdlet gets events and event logs on the local and remote computers.

You can use the parameters of this cmdlet to search for events by using their property values. This cmdlet gets only the events that match all of the specified property values.

The cmdlets that contain the EventLog noun work only on classic event logs. To get events from logs that use the Windows Event Log technology in Windows Vista and later versions of Windows, use `Get-WinEvent`.
<br>
#### **SYNTAX**

```
 Get-EventLog [-LogName] <String> [[-InstanceId] <Int64[]>] [-After <DateTime>] [-AsBaseObject] [-Before <DateTime>] 
 [-ComputerName <String[]>] [-EntryType {Error | Information | FailureAudit | SuccessAudit | Warning}]
 [-Index <Int32[]>] [-Message <String>] [-Newest <Int32>] [-Source <String[]>] [-UserName <String[]>] [<CommonParameters>]

 Get-EventLog [-AsString] [-ComputerName <String[]>] [-List] [<CommonParameters>]

```

<br>

#### **EXAMPLES**

```
get-eventlog -ComputerName SERVER02 -Log System -Newest 50
```

- Gets the last 50 events from the system log on SERVER02

<br>


```
get-eventlog -ComputerName SERVER02 -Index 25733
```

- gets a specific eventlog entry. Useful after doing a list (see above)

<br>


```
Get-EventLog -LogName Security | Group-Object -Property EntryType
```
- create a table of events grouped by event type

