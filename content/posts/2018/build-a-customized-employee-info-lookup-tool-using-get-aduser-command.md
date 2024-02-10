+++
date = 2018-04-15T04:07:33Z
description = ""
draft = true
slug = "build-a-customized-employee-info-lookup-tool-using-get-aduser-command"
title = "Build a customized employee info lookup tool using Get-ADUser command"

+++


Users calling in for assistance often are asking for help with their accounts (account lockouts, password expirations, new user info, etc.). My goal is to build a tool that displays those fields and any others that a support team member would need to ascertain the status of an employee's account in one easy to read display. 

Today I will show you how to build a custom employee information lookup tool that you can tailor specifically to your needs. In my example, we're going to create a tool that displays the most common fields needed to help employees who are calling into the helpdesk for assistance. I am going to walk you through the building of this script piece by piece and then present the final solution at the end of this article.

I want an easy way to display the Active Directory fields related to an employee's account status, account expiration, password status as well as when their password was last set and when it it will expire. I would also like to know if the acocunt is locked out. Last but not least, I aslo need to display the common employee info fields (name, address, department, manager, title, etc.). The tool also needs to be simple to use for my helpdesk staff since they sometimes deal with a large volume of calls. I can't expect them to be typing in super long commands to get the info they need. 

We can build this tool using the `Get-ADUser` powershell cmdlet. We can lookup all the fields available and we can easily build a super long one liner that looks like this: 

```
get-aduser username -Properties *, "msDS-UserPasswordExpiryTimeComputed" | Select-Object GivenName, Surname, SamAccountName, Manager, DisplayName, `
City, EmailAddress, EmployeeID, Enabled, Department, OfficePhone, MobilePhone, LockedOut, LockOutTime, AccountExpirationDate, `
PasswordExpired, PasswordLastSet, Title

```

The block of code above produces the following output:

```
GivenName             : Joe
Surname               : Smith
SamAccountName        : Joe_Smith
Manager               : CN=Jones\, Tom,OU=Users,DC=BIGFIRM,DC=BIZ
DisplayName           : Smith, Joe
City                  : New York
EmailAddress          : Joe_Smith@bigfirm.biz
EmployeeID            : 123456789
Enabled               : True
Department            : marketing
OfficePhone           : +1 631-555-1212
MobilePhone           : +1 631-333-4444
LockedOut             : False
LockOutTime           : 0
AccountExpirationDate :
PasswordExpired       : False
PasswordLastSet       : 3/26/2018 9:29:02 AM
Title                 : Marketing Analyst
```

This syntax works absolutely fine and gets us most of what we need without a ton of work. Overall, that's pretty close to what I am looking to acheive. We could stop right here and call it a day and for many people this would a useable solution. However, it isn't the most elegant solution and more importantly, the output above is missing the **password expiration** date and the **manager** field is not easily readable. I think we can do better. 

I certainly wouldn't expect someone to type in all that data over and over. We're close but we have to make it simple to type. Also, when I look at these fields, I see two sets of data: employee information and account status information but the fields are not organized in any particular way and is just a long table. 

Let's address the two problem fields first. The manager field is not formatted as I would prefer: I would like to to see first name and last name. That can be fixed by doing a lookup on the manager's name and returning the manager's **SAMAccountName**. I used SAMAccountName because the Displayname is listed as Last name, First name which is not what I wanted but you may like that field better for your version of this script.  

I am using a **hashtable**, also called a dictionary or associative array to lookup the manager's AD info. The hashtable is an array that consists of key-value pairs. In this case, the key is the **Name** of the property I want to create, and the **value**, represented as **Expression**, is the content I want assigned to that property. 

```
@{Name = 'Manager'; Expression = {(Get-ADUser ($_.manager)).samaccountname}}
````
This hashtable looks up the value **CN=Jones\, Tom,OU=Users,DC=BIGFIRM,DC=BIZ** in Active Directory and returns the SAMAccountName **Tom_Jones**. 

The password expiration is tricky because it's a special field that isn't easy to find if you don't know what your looking for and the value is not in a human readable form by default. I'll need to make two changes to get the desired result. The first thing I need to do is add the field **"msDS-UserPasswordExpiryTimeComputed"** to my Get-ADuser cmdlet and then I need to convert the data returned to a format that we can make sense of. Again, I'll use a hashtable to do the calculation for me.

```
@{Name = "PasswordExpiry"; Expression = {[datetime]::FromFileTime($_."msDS-UserPasswordExpiryTimeComputed")}}
```

This hashtable will produce a new field called **PasswordExpiry** which will hold the expiration date for me.

Now that I have all the fields I need, I can work on breaking up the original output into two distinct groups (employee information and account status information), but using select-object to display the fields probably isnt going to give me the desired result. I could feed that data from Get-ADUser into two seprate arrays and then I could display each array seperatly. Let's see what that looks like. 

```
$AccountInfo = [PSCustomObject]@{
  FirstName    = $Employee.GivenName
  LastName     = $Employee.Surname
  Title        = $Employee.Title
  Department   = $Employee.department
  Manager      = $Employee.Manager
  City         = $Employee.city
  EmployeeID   = $Employee.EmployeeID
  UserName     = $Employee.SamAccountName
  DisplayNme   = $Employee.displayname
  EmailAddress = $Employee.emailaddress
  OfficePhone  = $Employee.officephone
  MobilePhone  = $Employee.mobilephone
}

$AccountStatus = [PSCustomObject]@{
  PasswordExpired       = $Employee.PasswordExpired
  AccountLockedOut      = $Employee.LockedOut
  LockOutTime           = $Employee.LockOutTime
  AccountEnabled        = $Employee.Enabled
  AccountExpirationDate = $Employee.AccountExpirationDate
  PasswordLastSet       = $Employee.PasswordLastSet
  PasswordExpireDate    = $Employee.PasswordExpiry
}
```

The result is we have taken the information from our original lookup and split the data into two smaller groups. You could argue that this isn't necessary and you would not be wrong. The reason for doing this extra work is get the output organized in the way I want it displayed. I could have instead ran Get-ADuser twice in one script. The first Get-ADUser could have just the accountinfo fields and then run Get-ADuser again to lookup the Account status fields. However, that's two calls to the directory and not terribly efficient. Also, I have tried that already and I did not like the look of the output. For me, the arrays offer more flexibility with formatting. 

Also, I believe that it's not enough to just build a tool that gives me the desired result. Instead, the tool should also be well documented, easy to follow, and built using best practices. Arrays are better solution to the problem and my code will be easier to understand. 

Lastly, I'd like to remind you that the fields I chose many not be the ones you want to use. it's only a small amount of work to customize this as you wish. Let's see what the output of the two arrays looks like:

```
    FirstName    : Joe
    LastName     : Smith
    Title        : Marketing Analyst
    Department   : Marketing
    Manager      : Tom Jones
    City         : New York
    EmployeeID   : 1234
    UserName     : Joe_smith
    DisplayNme   : Smith, Joe
    EmailAddress : Joe_swmith@bigfirm.biz
    OfficePhone  : +1 631-555-1212
    MobilePhone  : +1 631-888-6789

    PasswordExpired       : False
    AccountLockedOut      : False
    LockOutTime           : 0
    AccountEnabled        : True
    AccountExpirationDate :
    PasswordLastSet       : 3/26/2018 9:29:02 AM
    PasswordExpireDate    : 9/28/2018 9:29:02 AM
```

We have broke up the output into two smaller groups. I think that output is better organized and easier to read at a glance which was my main concern when starting this project. The end result is that a helpdesk staff member will have an easier time finding the info they need. 


I prefer to run my tools as functions. That means we need to name our function and include help files so that people have soemthing to reference when thy have questions. The script below is formatted as a function which means you'll have to load the function in order to run it. I'll assume you already know how to do that. I named my function Get-EmployeeInfo. The syntax is simple to type:

```
PS C:\Scripts> Get-EmployeeInfo Joe_Smith

    FirstName    : Joe
    LastName     : Smith
    Title        : Marketing Analyst
    Department   : Marketing
    Manager      : Tom Jones
    City         : New York
    EmployeeID   : 1234
    UserName     : Joe_smith
    DisplayNme   : Smith, Joe
    EmailAddress : Joe_swmith@bigfirm.biz
    OfficePhone  : +1 631-555-1212
    MobilePhone  : +1 631-888-6789

    PasswordExpired       : False
    AccountLockedOut      : False
    LockOutTime           : 0
    AccountEnabled        : True
    AccountExpirationDate :
    PasswordLastSet       : 3/26/2018 9:29:02 AM
    PasswordExpireDate    : 9/28/2018 9:29:02 AM
```

The end result is that we have a tool that is easy to use, with a simple syntax that results in a customized output that is easy to read. I also created code that is following best practices, portable and documented.

My hope is that you found this article insightful and helps you build your own functions using customized lookups. Please leave me some feedback in the comments section so I can continue to write useful articles. If there is something you would love to see me write about or if you hvae a specific question about this or any or my other articles, please feel free to leave me a note as well. 


```
Function Get-EmployeeInfo {

<#
 .Synopsis
  Returns a customized list of Active Directory account information for a single user

 .Description
  Returns a customized list of Active Directory account information for a single user. The customized list is a combination of the fields that
  are most commonly needed to review when an employee calls the helpdesk for assistance.

 .Example
  Get-EmployeeInfo Joe_Smith
  Returns a customized list of AD account information for Joe_Smith

  PS C:\Scripts> Get-EmployeeInfo Joe Smith

    FirstName    : Joe
    LastName     : Smith
    Title        : Marketing Analyst
    Department   : Marketing
    Manager      : Tom Jones
    City         : New York
    EmployeeID   : 1234
    UserName     : Joe_smith
    DisplayNme   : Smith, Joe
    EmailAddress : Joe_swmith@bigfirm.biz
    OfficePhone  : +1 631-555-1212
    MobilePhone  : +1 631-888-6789

    PasswordExpired       : False
    AccountLockedOut      : False
    LockOutTime           : 0
    AccountEnabled        : True
    AccountExpirationDate :
    PasswordLastSet       : 3/26/2018 9:29:02 AM
    PasswordExpireDate    : 9/28/2018 9:29:02 AM

 .Parameter UserName
  The employee account to lookup in Active Directory

  .Notes
  NAME: Get-EmployeeInfo
  AUTHOR: Mike Kanakos
  LASTEDIT: 2018-04-14
  .Link
    www.networkadmin.com

#>

    [CmdletBinding()]
    Param(
        [Parameter(Mandatory = $True, Position = 1)]
        [string]$UserName
    )


    #Import AD Module
    Import-Module ActiveDirectory



    $Employee = get-aduser $UserName -Properties *, "msDS-UserPasswordExpiryTimeComputed" | Select-Object GivenName, Surname, `
        SamAccountName, @{Name = 'Manager'; Expression = {(Get-ADUser ($_.manager)).samaccountname}}, DisplayName, City, `
        EmailAddress, EmployeeID, Enabled, Department, OfficePhone, MobilePhone, LockedOut, LockOutTime, AccountExpirationDate, `
        PasswordExpired, PasswordLastSet, Title, `
    @{Name = "PasswordExpiry"; Expression = {[datetime]::FromFileTime($_."msDS-UserPasswordExpiryTimeComputed")}}


    $AccountInfo = [PSCustomObject]@{
        FirstName    = $Employee.GivenName
        LastName     = $Employee.Surname
        Title        = $Employee.Title
        Department   = $Employee.department
        Manager      = $Employee.Manager
        City         = $Employee.city
        EmployeeID   = $Employee.EmployeeID
        UserName     = $Employee.SamAccountName
        DisplayNme   = $Employee.displayname
        EmailAddress = $Employee.emailaddress
        OfficePhone  = $Employee.officephone
        MobilePhone  = $Employee.mobilephone
    }

    $AccountStatus = [PSCustomObject]@{
        PasswordExpired       = $Employee.PasswordExpired
        AccountLockedOut      = $Employee.LockedOut
        LockOutTime           = $Employee.LockOutTime
        AccountEnabled        = $Employee.Enabled
        AccountExpirationDate = $Employee.AccountExpirationDate
        PasswordLastSet       = $Employee.PasswordLastSet
        PasswordExpireDate    = $Employee.PasswordExpiry
    }

    $AccountInfo

    $AccountStatus


} #END OF FUNCTION
```

