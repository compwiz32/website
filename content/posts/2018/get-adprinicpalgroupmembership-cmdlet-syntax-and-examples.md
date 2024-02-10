+++
categories = ["PowerShell", "Active-Directory", "Get-ADPrinicpalGroupMembership", "Get-ADGroup"]
date = 2018-05-10T23:02:13Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2022/03/Screencap-2022-03-02-0003.png"
slug = "get-adprinicpalgroupmembership-cmdlet-syntax-and-examples"
summary = "cmdlet syntax and real-world usage examples of how to use the Get-ADPrincipalGroupMembersip cmdlet"
tags = ["PowerShell", "Active-Directory", "Get-ADPrinicpalGroupMembership", "Get-ADGroup"]
title = "Get-ADPrinicpalGroupMembership - Cmdlet Syntax and Examples"

+++


#### **SYNOPSIS**
Gets the Active Directory groups that have a specified user, computer, group, or service account.
<br>

#### **CMDLET ALIASES**
<br>

#### **DESCRIPTION**
The `Get-ADPrincipalGroupMembership` cmdlet gets the Active Directory groups that a  user, computer, group, or service account is a member of. 

The `Identity` parameter specifies the user, computer, or group object that you want to determine group membership for. You can identify a user, computer, or group object by its distinguished name, GUID, security identifier, or SAM account name. You can also specify a user, group, or computer object variable, such as `$<localGroupObject>`, or pass an object through the pipeline to the `Identity` parameter.

For example, you can use the `Get-ADGroup` cmdlet to retrieve a group object and then pass the object through the pipeline to the `Get-ADPrincipalGroupMembership` cmdlet. Similarly, you can use `Get-ADUser` or `Get-ADComputer` to get user and computer objects to pass through the pipeline.
    
This cmdlet requires a global catalog to perform the group search. If the forest that contains the user, computer, or group does not have a global catalog, the cmdlet returns a non-terminating error. If you want to search for local groups in another domain, use the ResourceContextServer parameter to specify the alternate server in the other domain.
    
<br>

#### **SYNTAX**
```
Get-ADPrincipalGroupMembership [-Identity] <ADPrincipal> [-AuthType {Negotiate | Basic}] [-Credential <PSCredential>]
[-Partition <String>] [-ResourceContextPartition <String>] [-ResourceContextServer <String>] [-Server <String>] [<CommonParameters>]
```

<br>

#### **EXAMPLES**


```
Get-ADPrincipalGroupMembership Michael_Kanakos | select name
```
- Returns groups michael_kanakos
- selects only the name of the group
<br>

```
Get-ADPrincipalGroupMembership Michael_Kanakos | Get-ADGroup -prop description | select name, description
```
- generates a list of groups for Michael Kanakos
- pipes the output from  `Get-AdPrinicpalGroupmembership` to `Get-ADComputer` because the first cmdlet doesnt have the group description information available to display.
- final output is the groups and the description field for each group 
<br>

```
Get-ADPrincipalGroupMembership Michael_Kanakos | Where-Object {$_.name -like "role*"} | Get-ADGroup -prop description | `
select name, description
```

- generates a filtered list of groups that contain the word **ROLE** in the name
- passes the the filtered list to the `Get-ADComputer` cmdlet to retreive group descriptions
- final output is a list of groups that contain the word **ROLE** and their descriptions
  <br> 

```
Get-ADPrincipalGroupMembership Michael_Kanakos | Where-Object { $_.name -like "sg*" } | select name | ogv
```

- Returns list of groups that Michael is a member of
- filters for only groups that start with SG
- outputs results to gridview.

<br>

```
Get-ADPrincipalGroupMembership Michael_Kanakos | get-adgroup -prop description | select name, description | `
Where-Object { $_.name -like "DL*" } | sort name
```

- Returns name and description of groups that Alan is a member of
- filters for only groups that start with DL
<br>

