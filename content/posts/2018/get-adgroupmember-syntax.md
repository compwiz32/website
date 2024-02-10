+++
categories = ["PowerShell", "Active-Directory", "Get-ADComputer"]
date = 2018-04-16T00:17:30Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/04/022916_0138_PowerShellP2.png"
slug = "get-adgroupmember-syntax"
summary = "cmdlet syntax and real-world usage examples showing how to use the Get-ADGroupMember cmdlet"
tags = ["PowerShell", "Active-Directory", "Get-ADComputer"]
title = "Get-ADGroupMember - Cmdlet Syntax and Real World Examples"

+++


#### **SYNOPSIS**
Gets the members of an Active Directory group.
<br>   

#### **ALIAS**
None for this cmdlet
<br>

#### **SYNTAX**
```
Get-ADGroupMember [-Identity] <ADGroup> [-AuthType {Negotiate | Basic}] [-Credential <PSCredential>] [-Partition <String>] [-Recursive] [-Server <String>][<CommonParameters>]
````
<br>

#### **DESCRIPTION**
The `Get-ADGroupMember` cmdlet gets the members of an Active Directory group. Members can be users, groups, and computers.

The Identity parameter specifies the Active Directory group to access. You can identify a group by its distinguished name (DN), GUID, security identifier (SID), or
Security Accounts Manager (SAM) account name. You can also specify the group by passing a group object through the pipeline. For example, you can use the `Get-ADGroup` cmdlet to retrieve a group object and then pass the object through the pipeline to the `Get-ADGroupMember` cmdlet.

If the `Recursive` parameter is specified, the cmdlet gets all members in the hierarchy of the group that do not contain child objects. For example, if the group SaraDavisReports contains the user KarenToh and the group JohnSmithReports, and JohnSmithReports contains the user JoshPollock, then the cmdlet returns KarenToh and JoshPollock.

For AD LDS environments, the Partition parameter must be specified except in the following two conditions:
- The cmdlet is run from an Active Directory provider drive.
- A default naming context or partition is defined for the AD LDS environment. To specify a default naming context for an AD LDS environment, set the msDS-defaultNamingContext property of the Active Directory directory service agent (DSA) object (nTDSDSA) for the AD LDS instance.
<br>

#### **REAL-WORLD EXAMPLES**

```
Get-ADGroupMember "group1" | Get-ADUser | Foreach-Object {Add-ADGroupMember -Identity "group2" -Members $_}
```
- gets members from one group and adds (copies) them to another group 
<br>

```
Get-ADGroupMember "domain controllers" | get-adcomputer -prop operatingsystem | Where-Object {$_.operatingsystem -like "*2012*"} | select name, operatingsystem | sort name
```
- gets members of the Domain Controllers group
- gets the operatingsystem info for each computer in the group
- filters the operatingsystem field for only 2012
- outputs and sorts the results
<br>

```
Get-ADGroupMember "CAD_License_Users" | Sort -Property Name | foreach{ get-aduser $_ -Properties EmailAddress} | select Name, Surname, GivenName,
SamAccountname, EmailAddress | ft -AutoSize
```

- Get members of group CAD_Licensed_Users
- Sorts by Name
- Does a loop and grabs user ID info
- dumps the date to a table that is autosized
<br>

```
Get-ADGroupMember CAD_license_users | select name, SAM*, dist* | Export-Csv c:\temp\users.csv -NoTypeInformation
```

- Get members of group CAD_License_Users
- Displays Name, SAM Account Name and Distinguished Name (OU Path)
- Exports to CSV
- No Type Info takes off the first row (which is garbage)
- matches all via wildcard that fit the pattern. It was easier than typing SAMAccountName and DistinguishedName (because I am lazy)

