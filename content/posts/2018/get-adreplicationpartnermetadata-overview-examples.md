+++
categories = ["PowerShell", "Active-Directory", "Get-ADReplicationPartnerMetadata"]
date = 2018-03-05T18:43:17Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/04/pexels-photo-276452-1.jpeg"
slug = "get-adreplicationpartnermetadata-overview-examples"
summary = "Quick overview of Get-ADReplicationPartnerMetadata command along with useful real-world examples "
tags = ["PowerShell", "Active-Directory", "Get-ADReplicationPartnerMetadata"]
title = "Get-ADReplicationPartnerMetadata - Cmdlet Syntax and Real World Examples"

+++


#### **SYNOPSIS**
Returns the replication metadata for a set of one or more replication partners
<br>

#### **DESCRIPTION**
The `Get-ADReplicationPartnerMetadata` cmdlet returns information related to an Active Directory replication partner and its replication partners. This includes attributes such as `LastReplicationSuccess` or `LastReplicationAttempt` and other data specific to each pairing of replication partners.

If the results are too verbose for your needs, you can use the Partition parameter to specify a partition to narrow down the results. Optionally, you can use the Filter parameter to narrow down results as well. If no partition or filter are specified for the results, the default naming context is used and metadata for all replication partners is returned.
<br>

#### **SYNTAX**

```
 Get-ADReplicationPartnerMetadata [-Target] <Object[]> [[-Partition] <String[]>] [[-PartnerType] {Both | Inbound | Outbound}] [-AuthType {Negotiate | Basic}] [-Credential
 <PSCredential>] [-EnumerationServer <String>] [-Filter <String>] [<CommonParameters>]

 Get-ADReplicationPartnerMetadata [[-Target] <Object[]>] [-Scope] {Domain | Forest | Server | Site} [[-Partition] <String[]>] [[-PartnerType] {Both | Inbound | Outbound}]
 [-AuthType {Negotiate | Basic}] [-Credential <PSCredential>] [-EnumerationServer <String>] [-Filter <String>] [<CommonParameters>]
```
<br>


#### **REAL WORLD EXAMPLES**

```
Get-ADReplicationPartnerMetadata -Target dc01 | select-object Server, LastReplicationAttempt, LastReplicationSuccess, partner | out-gridview
```
- displays the replication info for a target server and the partners replicating with it.
- sends output to a gridview window
<br>



```
Get-ADReplicationPartnerMetadata -Target dc01,dc02 | select-object Server, LastReplicationAttempt, LastReplicationSuccess, partner | out-gridview
```

- displays the replication info for two target servers and the partners replicating with it.
- sends output to a gridview window
<br>

```
Get-ADReplicationPartnerMetadata -Target NorthAmerica -Scope Site -Partition *
```

- Displays replication partner metadata for all domain controllers in a site

