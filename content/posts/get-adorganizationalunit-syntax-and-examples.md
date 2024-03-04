---
title: "Get-ADOrganizationalUnit - Cmdlet Syntax and Examples"
tags: ["active directory", PowerShell]
date: 2018-05-05T05:06:15Z
authors: [mike]
draft: false
image: /images/2018/Get-ADOrganizationalUnit/Get-ADOrganizationalUnit-header.webp
slug: get-adorganizationalunit-syntax-and-examples
description: "Learn common searches you can do with the Get-ADOrganizationalUnit cmdlet."

---

## SYNOPSIS

Gets one or more Active Directory organizational units.
<br>

## CMDLET ALIASES

None
<br>

## DESCRIPTION

The `Get-ADOrganizational` unit cmdlet gets an organizational unit object or performs a search to retrieve multiple organizational units.

The `Identity` parameter specifies the Active Directory organizational unit to retrieve. You can identify an organizational unit by its distinguished name (DN) or GUID. You can also set the parameter to an organizational unit object variable, such as $<localOrganizationalunitObject> or pass an organizational unit object through the pipeline to the Identity parameter.

To search for and retrieve more than one organizational unit, use the `Filter` or `LDAPFilter` parameters. The Filter parameter uses the PowerShell Expression Language to write query strings for Active Directory. PowerShell Expression Language syntax provides rich type conversion support for value types received by the Filter parameter. For more information about the Filter parameter syntax, see `about_ActiveDirectory_Filter`. If you have existing LDAP query strings, you can use the LDAPFilter parameter.

This cmdlet retrieves a default set of organizational unit object properties. To retrieve additional properties use the Properties parameter. For more information about the how to determine the properties for computer objects, see the Properties parameter description.
<br>

## SYNTAX

```PowerShell
Get-ADOrganizationalUnit [-Identity] ADOrganizationalUnit
         [-AuthType {Negotiate | Basic}] [-Credential PSCredential]
            [-Partition string] [-Properties string[]]
               [-Server string] [CommonParameters]

      Get-ADOrganizationalUnit -Filter string
         [-ResultPageSize int] [-ResultSetSize Int32] [-SearchBase string]
            [-SearchScope {Base | OneLevel | Subtree}] [-AuthType {Negotiate | Basic}]
               [-Credential PSCredential] [-Partition string] [-Properties string[]]
                  [-Server string] [CommonParameters]

      Get-ADOrganizationalUnit -LDAPFilter string
         [-ResultPageSize int] [-ResultSetSize Int32] [-SearchBase string]
            [-SearchScope {Base | OneLevel | Subtree}] [-AuthType {Negotiate | Basic}]
               [-Credential PSCredential] [-Partition string] [-Properties string[]]
                  [-Server string] [CommonParameters]
```

<br>

## EXAMPLES

```PowerShell
Get-ADOrganizationalUnit -LDAPFilter '(name=*)' -SearchBase 'OU=South_America,DC=BIGFIRM,DC=BIZ' -SearchScope OneLevel
| select distinguishedname
```

- gets all sub OU's one level deep under the SouthAmerica OU
- returns the the DN for all selected OU's
<br>

```PowerShell
C:\PS>Get-ADOrganizationalUnit -Filter 'Name -like "*"' | FT Name, DistinguishedName -A


Name                 DistinguishedName
----                 -----------------
Domain Controllers   OU=Domain Controllers,DC=FABRIKAM,DC=COM
UserAccounts         OU=UserAccounts,DC=FABRIKAM,DC=COM
Sales                OU=Sales,OU=UserAccounts,DC=FABRIKAM,DC=COM
Marketing            OU=Marketing,OU=UserAccounts,DC=FABRIKAM,DC=COM
Production           OU=Production,OU=UserAccounts,DC=FABRIKAM,DC=COM
HumanResources       OU=HumanResources,OU=UserAccounts,DC=FABRIKAM,DC=COM
NorthAmerica         OU=NorthAmerica,OU=Sales,OU=UserAccounts,DC=FABRIKAM,DC=COM
SouthAmerica         OU=SouthAmerica,OU=Sales,OU=UserAccounts,DC=FABRIKAM,DC=COM
Europe               OU=Europe,OU=Sales,OU=UserAccounts,DC=FABRIKAM,DC=COM
AsiaPacific          OU=AsiaPacific,OU=Sales,OU=UserAccounts,DC=FABRIKAM,DC=COM
Finance              OU=Finance,OU=UserAccounts,DC=FABRIKAM,DC=COM
Corporate            OU=Corporate,OU=UserAccounts,DC=FABRIKAM,DC=COM
ApplicationServers   OU=ApplicationServers,DC=FABRIKAM,DC=COM
Groups               OU=Groups,OU=Managed,DC=FABRIKAM,DC=COM
PasswordPolicyGroups OU=PasswordPolicyGroups,OU=Groups,OU=Managed,DC=FABRIKAM,DC=COM
Managed              OU=Managed,DC=FABRIKAM,DC=COM
ServiceAccounts      OU=ServiceAccounts,OU=Managed,DC=FABRIKAM,DC=COM
```

- Gets all the Organizational Units in the domain

<br>

```PowerShell
C:\PS>Get-ADOrganizationalUnit -LDAPFilter '(name=*)' -SearchBase 'OU=Sales,OU=UserAccounts,DC=FABRIKAM,DC=COM' `
-SearchScope OneLevel | ft Name,Country,PostalCode,City,StreetAddress,State


Name                    Country                 PostalCode             City                   StreetAddress          State
----                    -------                 ----------             ----                   -------------          -----
AsiaPacific             AU                      4171                   Balmoral               45 Martens Place       QLD
Europe                  UK                      NG34 0NI               QUARRINGTON            22 Station Rd
NorthAmerica            US                      02142                  Cambridge              1634 Randolph Street   MA
```

- Gets Organizational Units underneath the sales Organizational Unit using an LDAP filter.
