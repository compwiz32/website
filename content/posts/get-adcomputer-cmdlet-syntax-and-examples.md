---
title: 'Get-ADComputer - Cmdlet Syntax and Examples'
date: 2018-04-30
authors: [mike]
draft: false
image: /images/2018/Get-ADComputer/Get-ADComputer.webp
slug: get-adcomputer-cmdlet-syntax-and-examples
tags: ['Active Directory', PowerShell]
description: "Let's check out some common searches you can do with the Get-ADComputer cmdlet, and a few cool tricks too."
---

## SYNOPSIS

Gets one or more Active Directory computers.
<br/>

## DESCRIPTION

The `Get-ADComputer` cmdlet gets a computer or performs a search to retrieve multiple computers.

The `Identity` parameter specifies the Active Directory computer to retrieve. You can identify a computer by its distinguished name (DN), GUID, security identifier (SID) or Security Accounts Manager (SAM) account name. You can also set the parameter to a computer object variable, such as `$<localComputerObject>` or pass a computer object through the pipeline to the Identity parameter.

To search for and retrieve more than one computer, use the `Filter` or `LDAPFilter` parameters. The `Filter` parameter uses the PowerShell Expression Language to write query strings for Active Directory. PowerShell Expression Language syntax provides rich type conversion support for value types received by the Filter parameter.

For more information about the Filter parameter syntax, type `Get-Help about_ActiveDirectory_Filter`. If you have existing LDAP query strings, you can use the `LDAPFilter` parameter.

This cmdlet retrieves a default set of computer object properties. To retrieve additional properties use the Properties parameter. For more information about the how to determine the properties for computer objects, see the Properties parameter description.
<br/>

## SYNTAX

```PowerShell
Get-ADComputer [-AuthType {Negotiate | Basic}] [-Credential <PSCredential>] [-Properties <String[]>] [-ResultPageSize <Int32>]
[-ResultSetSize <Int32>] [-SearchBase <String>] [-SearchScope {Base | OneLevel | Subtree}] [-Server <String>] -Filter <String>
[<CommonParameters>]

Get-ADComputer [-Identity] <ADComputer> [-AuthType {Negotiate | Basic}] [-Credential <PSCredential>]
[-Partition <String>] [-Properties <String[]>] [-Server <String>] [<CommonParameters>]

Get-ADComputer [-AuthType {Negotiate | Basic}] [-Credential <PSCredential>] [-Properties <String[]>] [-ResultPageSize <Int32>]
[-ResultSetSize <Int32>] [-SearchBase <String>] [-SearchScope {Base | OneLevel | Subtree}] [-Server <String>] -LDAPFilter <String>
[<CommonParameters>]
```
<br/>

## EXAMPLES

```PowerShell
Get-ADComputer -Filter 'Name -like "*nsk*"' | select name | ogv
```

- returns all computers that contain NSK in their name
- shows only the name of the computer
- sends output to gridview
<br/>

```PowerShell
Get-ADComputer -Filter 'Name -like "*dc0*"' -prop IPv4Address,operatingsystem,OperatingSystemServicePack,description | select name, `
description,ipv4address,operatingsystem,OperatingSystemServicePack | sort name |  export-csv `
c:\scripts\output\DC.csv -NoTypeInformation
```

- returns all computers that contain DC0 in their name
- creates a table with name, description, IP address, OS name, Service Pack level
- sorts by name
- exports results to CSV file
- gets rid of the "typeInformation" field that is included in CSV exports
<br/>

```PowerShell
get-adcomputer -filter 'Description -like "*WSUS*" -prop * | select name, `
@{Name='ManagedBy';Expression={(Get-ADUser ($_.managedBy)).samaccountname}}, description
```

- Finds computers with a description containing WSUS
- Returns Server name, Server Description and the managed by field
- converts the *Managed By* DN to SAMAccountName
<br/>


```PowerShell
get-adcomputer -filter 'OperatingSystem -like "*server*"' -prop MemberOf, managedby, description |
Where-Object {
    ( $_.MemberOf -notcontains $Group1.DistinguishedName ) -and
    ( $_.MemberOf -notcontains $Group2.DistinguishedName ) -and
    ( $_.MemberOf -notcontains $Group3.DistinguishedName ) -and
    ( $_.MemberOf -notcontains $Group4.DistinguishedName ) -and
    ( $_.MemberOf -notcontains $Group5.DistinguishedName ) -and
    ( $_.MemberOf -notcontains $Group6.DistinguishedName ) -and
    ( $_.MemberOf -notcontains $Group7.DistinguishedName )
} | Select-Object name, @{Name='ManagedBy';Expression={(Get-ADUser ($_.managedBy)).samaccountname}}, description | sort name |  ogv
```

- reads from seven AD groups
- searches for computers with an operating system name that contains ***server***  AND is not a member of the seven groups listed (i.e. good search for trying to find things like "not assigned to a patch group")
<br/>

```PowerShell
Get-ADComputer -Filter 'operatingsystem -like "*server*"' -Properties whenCreated, description | Where-Object `
{ ((Get-Date) - $_.whenCreated).Days -lt 40} | select name, description, whencreated | sort whencreated | `
Export-csv c:\scripts\output\servers-last40-days.csv -NoTypeInformation
```

- searches for computers with "server" in the operatingsystem name field
- filters the list to objected created in the last 40 days
- exports to a CSV