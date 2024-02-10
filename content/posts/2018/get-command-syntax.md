+++
categories = ["PowerShell", "Get-Command"]
date = 2018-04-06T09:02:57Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/04/2018-04-15-000190.png"
slug = "get-command-syntax"
summary = "cmdlet syntax and real-world usage examples of how to use the Get-Command cmdlet"
tags = ["PowerShell", "Get-Command"]
title = "Get-Command - Cmdlet Syntax and Real World Examples"

+++


#### **SYNOPSIS**
Gets all commands.
<br>

#### **CMDLET ALIASES**
GCM 
<br>

#### **DESCRIPTION**

The `Get-Command` cmdlet gets all commands that are installed on the computer, including cmdlets, aliases, functions, workflows, filters, scripts, and applications.
 
 `Get-Command` gets the commands from Windows PowerShell modules and snap-ins and commands that were imported from other sessions. To get only commands that have been imported into the current session, use the `ListImported` parameter.

Without parameters, a `Get-Command` command gets all of the cmdlets, functions, workflows and aliases installed on the computer. `Get-Command *` gets all types of commands, including all of the non-Windows PowerShell files in the Path environment variable ($env:path), which it lists in the Application command type.

`Get-Command` without wildcard characters, automatically imports the module that contains the command so that you can use the command immediately. To enable, disable, and configure automatic importing of modules, use the $PSModuleAutoLoadingPreference preference variable. For more information, see about_Preference_Variables (http://go.microsoft.com/fwlink/?LinkID=113248) in the Microsoft TechNet library. `Get-Command` gets its data directly from the command code, unlike `Get-Help`, which gets its information from help topics.

In **Windows PowerShell 2.0**, Get-Command gets only commands in current session. It does not get commands from modules that are installed, but not imported. To limit `Get-Command` in Windows PowerShell 3.0 and later versions to commands in the current session, use the ListImported parameter.

Starting in **Windows PowerShell 5.0**, results of the Get-Command cmdlet display a Version column by default. A new Version property has been added to the CommandInfo class.
<br>

#### **SYNTAX**

```
Get-Command [[-Name] <String[]>] [[-ArgumentList] <Object[]>] [-All] [-CommandType {Alias | Function | Filter | Cmdlet | ExternalScript | Application | Script 
 | Workflow | Configuration | All}]  [-FullyQualifiedModule <ModuleSpecification[]>] [-ListImported] [-Module <String[]>] [-ParameterName <String[]>]
 [-ParameterType <PSTypeName[]>] [-ShowCommandInfo] [-Syntax] [-TotalCount <Int32>][<CommonParameters>]

Get-Command [[-ArgumentList] <Object[]>] [-All] [-FullyQualifiedModule <ModuleSpecification[]>] [-ListImported] [-Module <String[]>] [-Noun <String[]>]
[-ParameterName <String[]>] [-ParameterType <PSTypeName[]>] [-ShowCommandInfo] [-Syntax] [-TotalCount <Int32>] [-Verb <String[]>] [<CommonParameters>]
```
<br>



#### **REAL WORLD EXAMPLES**

```
Gcm *child*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-ChildItem                                      3.1.0.0    Microsoft.PowerShell.Management
```

- GCM is an alias for get-Command
- Returns all cmdlets that contain **child** in the name
<br>


```
Get-Command -Module ActiveDirectory

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Add-ADCentralAccessPolicyMember                    1.0.0.0    ActiveDirectory
Cmdlet          Add-ADComputerServiceAccount                       1.0.0.0    ActiveDirectory
Cmdlet          Add-ADDomainControllerPasswordReplicationPolicy    1.0.0.0    ActiveDirectory
Cmdlet          Add-ADFineGrainedPasswordPolicySubject             1.0.0.0    ActiveDirectory
Cmdlet          Add-ADGroupMember                                  1.0.0.0    ActiveDirectory
Cmdlet          Add-ADPrincipalGroupMembership                     1.0.0.0    ActiveDirectory
Cmdlet          Add-ADResourcePropertyListMember                   1.0.0.0    ActiveDirectory
Cmdlet          Clear-ADAccountExpiration                          1.0.0.0    ActiveDirectory
Cmdlet          Clear-ADClaimTransformLink                         1.0.0.0    ActiveDirectory
Cmdlet          Disable-ADAccount                                  1.0.0.0    ActiveDirectory
Cmdlet          Disable-ADOptionalFeature                          1.0.0.0    ActiveDirectory
Cmdlet          Enable-ADAccount                                   1.0.0.0    ActiveDirectory
Cmdlet          Enable-ADOptionalFeature                           1.0.0.0    ActiveDirectory
Cmdlet          Get-ADAccountAuthorizationGroup                    1.0.0.0    ActiveDirectory
Cmdlet          Get-ADAccountResultantPasswordReplicationPolicy    1.0.0.0    ActiveDirectory
Cmdlet          Get-ADAuthenticationPolicy                         1.0.0.0    ActiveDirectory

```

- returns a list of cmdlets available for the **Active Directory** module
- the list displayed has been abbreviated to save space

