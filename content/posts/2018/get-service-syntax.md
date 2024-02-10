+++
categories = ["PowerShell", "Get-Service"]
date = 2018-04-06T09:48:15Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/04/powershell_2018-04-15_18-47-47.png"
slug = "get-service-syntax"
summary = "cmdlet syntax and real world usage examples of how to use the Get-Service cmdlet"
tags = ["PowerShell", "Get-Service"]
title = "Get-Service - Cmdlet Syntax and Real World Examples"

+++


#### **SYNOPSIS**
Gets the services on a local or remote computer.
<br>

#### **ALIAS**
GSV
<br>

#### **DESCRIPTION**
The `Get-Service` cmdlet gets objects that represent the services on a local computer or on a remote computer, including running and stopped services.

You can direct this cmdlet to get only particular services by specifying the service name or display name of the services, or you can pipe service objects to this cmdlet
<br>

#### **SYNTAX**
```
Get-Service [-ComputerName <String[]>] [-DependentServices] -DisplayName <String[]> [-Exclude <String[]>] [-Include <String[]>] [-RequiredServices] [<CommonParameters>]

Get-Service [-ComputerName <String[]>] [-DependentServices] [-Exclude <String[]>] [-Include <String[]>] [-InputObject <ServiceController[]>] 
[-RequiredServices] [<CommonParameters>]

Get-Service [[-Name] <String[]>] [-ComputerName <String[]>] [-DependentServices] [-Exclude <String[]>] [-Include <String[]>] [-RequiredServices] [<CommonParameters>]
```
<br>

#### **REAL WORLD EXAMPLES**

```
Get-Service -DisplayName *print* -ComputerName srv01, srv02, srv03, srv04 | select-object machinename, status, name, displayname | sort-object machinename

MachineName  Status  Name    DisplayName
-----------  ------  ----    -----------
srv01        Running Spooler Print Spooler
srv02        Running LPDSVC  TCP/IP Print Server
srv03        Running Spooler Print Spooler
srv04        Running Spooler Print Spooler
```

- gets the service status of all services with the word **Print** in the DisplayName
- returns the results but only displays the **MachineName, Status, Name** and **DisplayName** fields
<br>


```
get-service -DisplayName *spool*

Status   Name               DisplayName
------   ----               -----------
Stopped  Spooler            Print Spooler

```

- finds services that contain **spool** in the displayname
- Stops the services that found in the first part of the cmdlet 
<br>

```
get-service -DisplayName *spool* | restart-Service
```

- finds services that contain **spool** in the displayname
- There is no output for this cmdlet. If successful, it just returns to the cmd prompt

