---
date: 2018-05-14
title: Get-ADUser - Cmdlet Syntax and Examples
draft: false
authors: [admin]
image: /images/2018/Get-aduser/get-aduser-header.webp
slug: get-aduser-syntax-and-examples
description: Follow this handy guide to learn how to pull the user data you need from Active Directory.
tags: [PowerShell, 'Active Directory']
---


`Get-ADUser` is probably the first cmdlet you will encounter when you use PowerShell to manage Active Directory. It is the most popular cmdlet in the RSAT module for Active Directory and for good reason. One of the most common tasks of Active Directory is managing users and their attributes.

I will walk you through the PowerShell code needed to do some common queries of Active Directory using `Get-ADUser`. Then I will follow that up with some queries that you may wish to do, but the syntax to run those queries could be trickier to master. Follow along and let's review the basics and then we will build on the basics to do more complex searches.


TABLE OF CONTENTS
CMDLET DESCRIPTION
SEARCHING FOR USERS
CMDLET SYNTAX VIA HELP
CODE EXAMPLES
Querying a user account
Adding a property to a query
Get all properties for a user
Specifying property output with Select-Object
Using wild card matching with Select-Object
Querying an employee's direct reports
Querying the Manager property
Querying the password and login info for a user
Getting All users from Active Directory using the -filter property
Using -filter to find account matches
Using -filter to find address matches
Searching users in a specific Active Directory OU
Searching for accounts by account creation date
Exporting results to a file

## CMDLET DESCRIPTION

The Get-ADUser cmdlet gets a user object or performs a search to retrieve multiple user objects.

The Identity parameter specifies the Active Directory user to get. You can identify a user by its distinguished name (DN), GUID, security identifier (SID), Security Accounts Manager (SAM) account name or name. You can also set the parameter to a user object variable, such as `$<localUserObject>` or pass a user object through the pipeline to the Identity parameter.
SEARCHING FOR USERS

To search for and retrieve multiple users, use the Filter or LDAPFilter parameters. The Filter parameter uses the PowerShell Expression Language to write query strings for Active Directory.

Searching in Active Directory can be a simple process but can also be a frustrating operation when searching for multiple parameters. This is because the cmdlets in RSAT module for Active Directory don't always behave as expected. The RSAT module has been around for a long time and is due for a code rewrite, but that will not happen with the prevalence of AzureAD in the Microsoft ecosystem.

We have to work with the cmdlets as they are written today and we should not expect any new PowerShell cmdlets for Active Directory in the foreseeable future.

## CMDLET SYNTAX VIA HELP

All PowerShell cmdlets include generous help files and the RSAT AD cmdlets are no different. Let's start by getting a list of all parameters. The syntax to access help is simply Get-Help followed by the cmdlet you want help for. Also, the alias for Get-Help is just Help.

And if you're wondering, I use the built-in help files multiple times a day to remember syntax usage I don't use all the time.

```PowerShell
Get-Help Get-ADuser

NAME
    Get-ADUser

SYNOPSIS
    Gets one or more Active Directory users.


SYNTAX

Get-ADUser
    [-AuthType {Negotiate | Basic}]
    [-Credential <PSCredential>]
    [-Properties <String[]>]
    [-ResultPageSize <Int32>]
    [-ResultSetSize <Int32>]
    [-SearchBase
    <String>] [-SearchScope {Base | OneLevel | Subtree}]
    [-Server <String>] -Filter <String> [<CommonParameters>]

Get-ADUser
    [-Identity] <ADUser>
    [-AuthType {Negotiate | Basic}]
    [-Credential <PSCredential>]
    [-Partition <String>]
    [-Properties <String[]>]
    [-Server <String>]
    [<CommonParameters>]

Get-ADUser
    [-AuthType {Negotiate | Basic}]
    [-Credential <PSCredential>]
    [-Properties <String[]>]
    [-ResultPageSize <Int32>]
    [-ResultSetSize <Int32>]
    [-SearchBase <String>]
    [-SearchScope {Base | OneLevel | Subtree}]
    [-Server <String>] -LDAPFilter <String>
    [<CommonParameters>]
```

## CODE EXAMPLES

You can also see some of the included examples of PowerShell syntax by typing Help Get-ADuser -examples

I am going to skip the built-in examples that are included and jump into some of my own examples to share with you that I think cover all the major points.
Querying a user account

Let's start by getting the data for a user account in my test domain. When you query a user account, there are default properties and extended properties. What this means is that Active Directory will return the default set of properties for an account unless you tell it to do otherwise. The default parameters are 10 fields from a longer list of close to 100 properties for a user account.

```PowerShell
get-aduser bgoodman

DistinguishedName : CN=BGoodman,OU=TestUserAccounts,DC=mk,DC=lab
Enabled           : True
GivenName         : Bailey
Name              : BGoodman
ObjectClass       : user
ObjectGUID        : b46efb36-dafa-40a2-8f80-2ef4cb3872d0
SamAccountName    : BGoodman
SID               : S-1-5-21-354139404-3389107512-194839510-1191
Surname           : Goodman
UserPrincipalName : bgoodman@mk.lab
```

### Adding a property to a query

The list above is the 10 default properties returned for an account. If I want to add a property, I would use the `-properties` parameter. Let's add the `officePhone` property.

```PowerShell
get-aduser bgoodman -prop officephone

DistinguishedName : CN=BGoodman,OU=TestUserAccounts,DC=mk,DC=lab
Enabled           : True
GivenName         : Bailey
Name              : BGoodman
ObjectClass       : user
ObjectGUID        : b46efb36-dafa-40a2-8f80-2ef4cb3872d0
OfficePhone       : 777-777-7777
SamAccountName    : BGoodman
SID               : S-1-5-21-354139404-3389107512-194839510-1191
Surname           : Goodman
UserPrincipalName : bgoodman@mk.lab
```

You can see that we added the phone property and now we have 11 fields. If we wanted to see all the properties for an account, then we could use `*` for a value in the `properties` field.
Get all properties for a user

```PowerShell
Get-ADUser -Identity BGoodmand -Properties *
```

This command syntax will list ALL properties for the account BGoodman and the list will scroll all 100+ properties that are available until it reaches the end of the list. I did not display the output since it is over 100 lines long.

You can see the full list of default and extended properties on the [Microsoft website](https://social.technet.microsoft.com/wiki/contents/articles/12037.active-directory-get-aduser-default-and-extended-properties.aspx?WT.mc_id=CDM-MVP-5004073&ref=commandline.ninja).

### Specifying property output with Select-Object

When you use `Get-ADUser`, you can add more properties than just the default 10 properties as you saw in the previous example. You can also specify which properties you want to display by using the `Select-Object` cmdlet. Here I am choosing the four fields I would like to see in the output.

```PowerShell
Get-ADUser -Identity BGoodman | Select-Object SamAccountName, GivenName, Surname, Name

SamAccountName GivenName Surname Name
-------------- --------- ------- ----
BGoodman       Bailey    Goodman BGoodman
```

### Using wild card matching with Select-Object

We can take this a step farther by adding all the properties and then using wildcards with `Select-Object`. In the next example, I will display all phone numbers associated with a user account by using a simple wild-card of `*phone*`. This syntax tells AD to return all fields that contain the word phone.

```PowerShell
Get-ADUser -Identity BGoodman -properties * | Select-Object SamAccountName, GivenName, Surname, Name, *phone*

SamAccountName  : BGoodman
GivenName       : Bailey
Surname         : Goodman
Name            : BGoodman
HomePhone       : 919-123-4567
MobilePhone     : 212-867-5309
OfficePhone     : 777-777-7777
telephoneNumber : 777-777-7777
```

Another example would be if we wanted to see logon timestamps. We could add the word logon to Select-Object and grab all those properties.

```PowerShell
 Get-ADUser -Identity BGoodman -properties * | Select-Object SamAccountName, GivenName, Surname, Name, *phone*, *logon*

SamAccountName         : BGoodman
GivenName              : Bailey
Surname                : Goodman
Name                   : BGoodman
HomePhone              : 919-123-4567
MobilePhone            : 212-867-5309
OfficePhone            : 777-777-7777
telephoneNumber        : 777-777-7777
BadLogonCount          : 0
lastLogon              : 132786397616695462
LastLogonDate          : 10/10/2021 9:32:38 PM
lastLogonTimestamp     : 132783895583769159
logonCount             : 6
LogonWorkstations      :
MNSLogonAccount        : False
SmartcardLogonRequired : False
```

### Querying an employee's direct reports

How about all the people who are direct reports of a particular user?
Active Directory keeps a hierarchial relationship between accounts that we can use to generate a simple organization reporting chain.

```PowerShell
get-aduser criley -prop directreports

directreports     : {CN=CCrosby,OU=TestUserAccounts,DC=mk,DC=lab, CN=BGoodman,OU=TestUserAccounts,DC=mk,DC=lab,
                    CN=AWolf,OU=TestUserAccounts,DC=mk,DC=lab, CN=JBlake,OU=TestUserAccounts,DC=mk,DC=lab…}
DistinguishedName : CN=CRiley,OU=TestUserAccounts,DC=mk,DC=lab
Enabled           : True
GivenName         : Catalina
Name              : CRiley
ObjectClass       : user
ObjectGUID        : c69a5675-d1c9-4b8d-8ba3-e6889111f7a7
SamAccountName    : CRiley
SID               : S-1-5-21-354139404-3389107512-194839510-1144
Surname           : Riley
UserPrincipalName :
```

We can make that look a little better by using `-expandproperty` parameter. What AD returns is the distinguishedname of the accounts that are direct reports of CRILEY.

```PowerShell
get-aduser criley -prop directreports | Select-Object -ExpandProperty directreports
CN=CCrosby,OU=TestUserAccounts,DC=mk,DC=lab
CN=BGoodman,OU=TestUserAccounts,DC=mk,DC=lab
CN=AWolf,OU=TestUserAccounts,DC=mk,DC=lab
CN=JBlake,OU=TestUserAccounts,DC=mk,DC=lab
CN=DJacobs,OU=TestUserAccounts,DC=mk,DC=lab
```

We can also take that previous query one step further and get the actual names of three employees with some advanced syntax. In the example below, I am doing one lookup and saving the results to a variable.

Then I loop through each value in the variable and do another AD lookup to get the values I want.

```PowerShell
$Reports = get-aduser criley -prop directreports | select -ExpandProperty directreports

 $reports | foreach-object {Get-Aduser $_ -prop DisplayName, Manager | select SamAccountName, DisplayName, Manager}

SamAccountName DisplayName      Manager
-------------- -----------      -------
CCrosby        Craig Crosby     CN=CRiley,OU=TestUserAccounts,DC=mk,DC=lab
BGoodman       Bailey Goodman   CN=CRiley,OU=TestUserAccounts,DC=mk,DC=lab
AWolf          Aubree Wolf      CN=CRiley,OU=TestUserAccounts,DC=mk,DC=lab
JBlake         Jaydon Blake     CN=CRiley,OU=TestUserAccounts,DC=mk,DC=lab
DJacobs        Dominique Jacobs CN=CRiley,OU=TestUserAccounts,DC=mk,DC=lab
```

### Querying the Manager property

We can also go the other way and resolve a person's manager.

```PowerShell
get-aduser bgoodman -prop manager | select name, manager

name     manager
----     -------
BGoodman CN=CRiley,OU=TestUserAccounts,DC=mk,DC=lab
```

The results returned show the DistinguishedName for the manager. If we want the Display Name of the manager, we can do that with a calculated property also known as an expression.

```PowerShell
get-aduser bgoodman -prop manager | select name, @{
    Name        =   'Manager';
    Expression  =   {
        (Get-ADUser -properties Displayname ($_.manager)).DisplayName
        }
    }

name     Manager
----     -------
BGoodman Catalina Riley
```

That can be a bit confusing, let's walk through that syntax together. First, we did a normal get-aduser query and added the manager property. We send the results down the pipeline to Select-Object and we add a calculated property. The calculated property starts with the name that we want displayed for the property. I chose Manager.

Then I perform another Get-ADUser lookup using the $_ variable which contains the results from the left side of the query (the output of our first `Get-ADUser` lookup) and add a `.` to access just the manager property. When we add the `.` that means we want to call one of the previous properties we already looked up. Here, it's the value for Manager. We then wrap all that output in a set of parentheses and use another . to call the displayname.

So let me just say that the previous query is complex and hard to understand. With practice, you can get a hand on how to handle these complex scenarios. For now, you can refer to this article when you need manager or directreports syntax for queries.
Querying the password and login info for a user

Each user account in Active Directory also contains properties about a user's password expiration dates and login information. You can query the information and keep a tab on when passwords are expiring for an account.

```PowerShell
get-aduser bgoodman -prop * | select *password*, *Logon*

AllowReversiblePasswordEncryption : False
badPasswordTime                   : 132783895365611679
CannotChangePassword              : False
LastBadPasswordAttempt            : 10/10/2021 9:32:16 PM
PasswordExpired                   : False
PasswordLastSet                   : 10/10/2021 9:32:35 PM
PasswordNeverExpires              : False
PasswordNotRequired               : False
BadLogonCount                     : 0
lastLogon                         : 132786397616695462
LastLogonDate                     : 10/10/2021 9:32:38 PM
lastLogonTimestamp                : 132783895583769159
logonCount                        : 6
LogonWorkstations                 :
MNSLogonAccount                   : False
SmartcardLogonRequired            : False
```

That funky value for BadPasswordTime and other properties is expected. The values are displayed in "ticks". You can convert ticks to seconds with some math.
💡
Ticks are a time format in .NET Framework and start at 12:00 AM January 1, 0001. Ticks are equal to one ten-millionth of a second, which means there are 10,000 ticks per millisecond. PowerShell and .NET Framework both can use ticks to represent date-time values. There are math cmdlets that can convert ticks into a standard date format.

In this example, I have used another expression to calculate the value as a datetime value.

```PowerShell
get-aduser bgoodman -prop * |
    select Name, @{ Name = 'BadPasswordTime';  Expression = {Get-date -date $_.badpasswordtime }}

Name     BadPasswordTime
----     ---------------
BGoodman 10/11/2021 1:32:16 AM
```

### Getting All users from Active Directory using the -filter property

Up to now, we have been getting the properties of one user. You can filter for users that match a set criteria. Let's start with the easiest filter: get all users. In this query, I am running only the name property.

 ```PowerShell
 get-aduser –filter * | select name | sort-object –property name | more

name
----
ABarajas
ABowman
ADaniel
ADennis
Administrator
AGallegos
AKirk
ALawson
ALozano
AMorales
```

### Using -filter to find account matches

The filter parameter is powerful and can be used for some very creative searches. Let's look at a few that might interest you.

Here's a search for all users with the last name of Stanley

```PowerShell
 get-aduser -filter {Surname -like "stanley"} -prop DisplayName | select DisplayName, GivenName, Surname, SamAccountName

DisplayName    GivenName Surname SamAccountName
-----------    --------- ------- --------------
Hailie Stanley Hailie    Stanley HStanley
James Stanley  James     Stanley JStanley
Sam Stanley    Sam       Stanley SStanley
```

### Using -filter to find address matches

Here I am searching for employees who have a City of Miami in their profile. Some companies only use address info in Active Directory for office locations, while others will use Active Directory to use personal and work addresses. Each company is different.

```PowerShell
get-aduser -filter {City -like "Miami"} -prop DisplayName, StreetAddress, City, State |
    select DisplayName, GivenName, Surname, SamAccountName, StreetAddress, City, State | Format-Table

DisplayName    GivenName Surname SamAccountName StreetAddress         City  State
-----------    --------- ------- -------------- -------------         ----  -----
Yesenia Montes Yesenia   Montes  YMontes        3 James Street        Miami FL
Mckenzie Pugh  Mckenzie  Pugh    MPugh          14 Salem St           Miami FL
Paulina Hale   Paulina   Hale    PHale          2020 Mockingbird Lane Miami FL
```PowerShell

### Searching users in a specific Active Directory OU

Objects in AD are stored in the "Users" container. It is a common practice to create OU's to organize users and computers in different logical units like city or country and then move objects into the appropriate OU's. In this example, I'm searching for users in the US_Raleigh OU. To do this, I am using the SearchBase parameter to specify an OU in my Active Directory.

```PowerShell
 get-aduser -filter * -searchbase "OU=US-Raleigh,DC=mk,DC=lab" -Properties DisplayName |
    select SamAccountName, DisplayName

SamAccountName DisplayName
-------------- -----------
MParker        Makai Parker
CRivas         Carolyn Rivas
JWolfe         Jan Wolfe
TDuke          Tara Duke
```

### Searching for accounts by account creation date

There are several dates recorded for all accounts in Active Directory. One useful date stamped on every account is the account creation date. You can pull this data with a simple PowerShell query.

```PowerShell
get-aduser bgoodman -Properties * | select name, Created

name     Created
----     -------
BGoodman 8/29/2021 11:08:02 PM

We can also use this date when we want to run reports. One example would find all accounts created in the last XXX days. In the example below, I am finding all accounts created in the last 270 days.

get-aduser -filter * -properties Created |
    Where-Object { $_.created -gt  (get-date).AddDays(-270)} |
        select-object Name, GivenName, Surname, SAMAccountName, Created |
            Sort-Object Created | Format-table

Name                     GivenName   Surname      SAMAccountName Created
----                     ---------   -------      -------------- -------
OWright                  Orlando     Wright       OWright        8/29/2021 11:07:54 PM
JWolfe                   Jan         Wolfe        JWolfe         8/29/2021 11:07:54 PM
JFrederick               Jett        Frederick    JFrederick     8/29/2021 11:07:54 PM
CNorton                  Camilla     Norton       CNorton        8/29/2021 11:07:54 PM
KGaines                  Kasey       Gaines       KGaines        8/29/2021 11:07:54 PM
TDuke                    Tara        Duke         TDuke          8/29/2021 11:07:54 PM
SStephens                Sasha       Stephens     SStephens      8/29/2021 11:07:54 PM
DJacobs                  Dominique   Jacobs       DJacobs        8/29/2021 11:07:55 PM
AGallegos                Aubrey      Gallegos     AGallegos      8/29/2021 11:07:55 PM
WWashington              Walter      Washington   WWashington    8/29/2021 11:07:55 PM
```

Let's break down the syntax into small bite size nuggets that are easy to digest. The first part of the code does a lookup of all users and adds the Created property to the results. After that I am using the `where-object` cmdlet to find all accounts created in the last 270 days from today. I am using `Get-Date` to get today's date and then I instruct PowerShell to subtract 270 days from today. PowerShell then checks the date against all accounts found in the first part of the query and only shows the accounts that are created 270 days ago or less.

The last part of the query is displaying the results. I am using `Select-Object` to pick which properties I want displayed. After that I am using Sort-Object to sort the accounts by account creation date. The very last part of the code uses `Format-Table` to force PowerShell to present the results as a table view.

### Exporting results to a file

We can take this same query we just ran even farther by exporting the results to a CSV file. CSV's are good for importing into Excel and making beautiful tables. To export the data, we add the Export-CSV cmdlet to the end of the syntax. But first we must get rid of the Format-List cmdlet because Format-List tells PowerShell, hey, no more data is sent to the pipeline after this cmdlet. So Export-CSV wouldn't work if we added it to the end of the syntax.

```PowerShell
get-aduser -filter * -properties Created |
    Where-Object { $_.created -gt  (get-date).AddDays(-270)} |
        select-object Name, GivenName, Surname, SAMAccountName, Created |
            Sort-Object Created | Export-CSV "C:\Temp\AccountCreatedReport.csv"
```

I hope all of these examples help you to achieve the results you need. I am always available of you have questions about these examples or one you might know of that is represented here.

Thanks for reading!