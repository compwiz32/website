---
title: 'PowerShell: Find Windows Services Running as Another User'
description: 'Learn to fetch Windows service Run as details using CIM data with Get-Service.'
date: 2022-02-14
authors: [mike]
image: '/images/2022/Use-PowerShell-Find-Svcs/Get-Service.jpg'
tags: [PowerShell, Server-Admin]
authors: [mike]
featured: true
weight: 1
---

Article Contents:

- [Finding Logon As or Run As information](#finding-logon-as-or-run-as-information)
- [Searching CIM Data for properties](#searching-cim-data-for-properties)
- [Building a query with Get-CIMInstance](#building-a-query-with-get-ciminstance)
- [Using PowerShell Remoting to connect to computers](#using-powershell-remoting-to-connect-to-computers)
- [Formatting output](#formatting-output)
- [Simplifying our code](#simplifying-our-code)
- [Conclusion](#conclusion)

One day at work my manager asked me to find all services running under different credentials on a handful of a specific servers. The ask went something like this:

**_"Hey Mike, can you find all the services configured to run as another user on a specific group of servers?"_**

My manager hadn't even finished his sentence, and I had already thought in my head, "No problem! I'll crank that out in PowerShell in a mere couple of minutes!" The ask was benign, but as I soon found out, the process for discovering the requested information was not as straightforward as I thought. Let's dive in and look at how I solved this request.

First, let's review the task and break it down into relevant pieces:

> "Find all the services configured to run as another user on a group of servers."

From that sentence, we can break that down to these separate tasks to solve:

- query a group of servers
- find all the services configured to run as another user
- return the results
- (implied but not said) format the output of the results so that we can understand the information

## Finding Logon As or Run As information

The first thing we'll need to figure out is a way to find the _"logon as"_ properties for services on Windows computer. Let's start by querying a service on my local computer to see what fields are available. I'll start with a simple help lookup:

```PowerShell
PS C:\Users\mkana> help get-service

NAME
    Get-Service

SYNOPSIS
    Gets the services on a local or remote computer.


SYNTAX
    Get-Service [-ComputerName <String[]>] [-DependentServices] -DisplayName <String[]> [-Exclude <String[]>]
    [-Include <String[]>] [-RequiredServices  [<CommonParameters>]

    Get-Service [-ComputerName <String[]>] [-DependentServices] [-Exclude <String[]>] [-Include <String[]>]
    [-InputObject <ServiceController[]>] [-RequiredServices] [<CommonParameters>]

    Get-Service [[-Name] <String[]>] [-ComputerName <String[]>] [-DependentServices] [-Exclude <String[]>]
        [-Include <String[]>] [-RequiredServices] [<CommonParameters>]

```

I see there are `ComputerName` and `DisplayName` fields which can be helpful, but nothing that gives me '_Logon As_' or '_Run As_'.
Let's try piping `get-service` to `get-member` and see if there are additional properties available to me. I can look at the _MemberType_ column and see all the properties available.

```PowerShell
PS C:\Users\mkana> get-service LanmanServer | get-member
   TypeName: System.ServiceProcess.ServiceController
Name                      MemberType    Definition
----                      ----------    ----------
Name                      AliasProperty Name = ServiceName
RequiredServices          AliasProperty RequiredServices = ServicesDependedOn
Disposed                  Event         System.EventHandler Disposed(System.Object, System.EventArgs)
Close                     Method        void Close()
Continue                  Method        void Continue()
CreateObjRef              Method        System.Runtime.Remoting.ObjRef CreateObjRef(type requestedType)
Dispose                   Method        void Dispose(), void IDisposable.Dispose()
Equals                    Method        bool Equals(System.Object obj)
ExecuteCommand            Method        void ExecuteCommand(int command)
GetHashCode               Method        int GetHashCode()
GetLifetimeService        Method        System.Object GetLifetimeService()
GetType                   Method        type GetType()
InitializeLifetimeService Method        System.Object InitializeLifetimeService()
Pause                     Method        void Pause()
Refresh                   Method        void Refresh()
Start                     Method        void Start(), void Start(string[] args)
Stop                      Method        void Stop()
WaitForStatus             Method        void WaitForStatus(System.ServiceProcess.ServiceControllerStatus desiredStatus), void WaitForStat
CanPauseAndContinue       Property      bool CanPauseAndContinue {get;}
CanShutdown               Property      bool CanShutdown {get;}
CanStop                   Property      bool CanStop {get;}
Container                 Property      System.ComponentModel.IContainer Container {get;}
DependentServices         Property      System.ServiceProcess.ServiceController[] DependentServices {get;}
DisplayName               Property      string DisplayName {get;set;}
MachineName               Property      string MachineName {get;set;}
ServiceHandle             Property      System.Runtime.InteropServices.SafeHandle ServiceHandle {get;}
ServiceName               Property      string ServiceName {get;set;}
ServicesDependedOn        Property      System.ServiceProcess.ServiceController[] ServicesDependedOn {get;}
ServiceType               Property      System.ServiceProcess.ServiceType ServiceType {get;}
Site                      Property      System.ComponentModel.ISite Site {get;set;}
StartType                 Property      System.ServiceProcess.ServiceStartMode StartType {get;}
Status                    Property      System.ServiceProcess.ServiceControllerStatus Status {get;}
ToString                  ScriptMethod  System.Object ToString();
```

As I look through the list, I am looking for properties that may be helpful or a match. However, I see nothing that will return the '_Logon As_' or '_Run_As_' information that I need; that's a problem. I can use `Get-Service` to query the service information of a local or remote computer, but that cmdlet does not return any '_Logon As'_ or '_Run_As_' information at all.

Here's an example:

```PowerShell
PS C:\Users\mkana> get-service LanmanServer | Select-Object *
Name                : LanmanServer
RequiredServices    : {SamSS, Srv2}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
DisplayName         : Server
DependentServices   : {}
MachineName         : .
ServiceName         : LanmanServer
ServicesDependedOn  : {SamSS, Srv2}
ServiceHandle       :
Status              : Running
ServiceType         : Win32OwnProcess, Win32ShareProcess
StartType           : Automatic
Site                :
Container           :
```

Houston, we have a problem!

<iframe width="560" height="315" src="https://www.youtube.com/embed/lTSVOnhLtCs?si=ykBShvuzxWIjamxe" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Searching CIM Data for properties

We'll need to consider using some other method of retrieving the relevant information in order to complete this request. Let's see what's available from WMI/CIM.

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
.callout {
  padding: 20px;
  background-color: #f3f3f3;
  border-left: 6px solid #2196F3;
}
</style>
</head>
<body>

<div class="callout">
  <p>A note about CIM and WMI

I love CIM and you should learn to love it as well. CIM and WMI are the same thing, but you access CIM using the newer `Get-CIMInstance` cmdlet instead of `Get-WMIObject`. It's the newer, more modern version of WMI but uses PS remoting (aka WinRM) and falls back to WMI in most cases if CIM fails.
It's like the best of both worlds! Your network team will love CIM over WMI because it uses standard ports instead of a range of ports like WMI uses!

If you haven't already transitioned to using `Get-CIMInstance` when querying WMI, then you need to use `Get-CIMInstance` as your default method, instead of `Get-WMIObject` .

`Get-WMIObject` doesn't work on newer versions of Windows (Windows 10, 11, Server2016, Server2019 & Server 2022)

I discuss the differences between CIM and WMI at length in my article on [How to secure PowerShell Remoting in a Windows Domain](https://commandline.ninja/securing-powershell/)</p>
</div>

</body>
</html>

The lookup I need to perform for CIM is:

```PowerShell
PS > Get-CIMInstance -Class Win32_Service -Filter "name ='LanmanServer' " | Select-Object *
```

I'll explain this syntax in just a moment. But first, let's look at the results. I am showing you a screen cap because it's easier to highlight the information I am looking for.

![CIMInstance Example](/images/2022/Use-PowerShell-Find-Svcs/CIMInstance-Sample.jpg)

Alright, now we're getting somewhere! CIM gives us what we need, now we just need to figure what fields we want to return then do some filtering. First, we need to decide what info we want to return from CIM. I think the following fields cover everything we need for this task:

- SystemName
- Name
- Caption
- StartMode
- StartName
- State

Your mileage may vary and you can choose whatever fields you prefer, but for this problem, those fields fill the bill.

## Building a query with Get-CIMInstance

Now that we know what we want to query, we need to work on some filters. Let's query the fields we just identified and make those properties contain the data we expect. Here's a quick test to see how that looks for one service query:

```PowerShell
PS > Get-CIMInstance -Class Win32_Service -filter "StartName != 'LocalSystem' AND NOT StartName LIKE 'NT Authority%' " | Select-Object-object SystemName, Name, Caption, StartMode, StartName, State | Sort-Object-oject StartName

Systemname : DC02
Name       : tapisrv
Caption    : Telephony
StartMode  : Manual
StartName  : mk\mkadmin
State      : Stopped
```

That looks like everything I need for my original ask. Let's review where we're at so far.

We have queried the information we need, and we have figured out the properties to query. Now, let's figure out how to search for services configured to run as another user. If we look at a typical PC, the vast majority of services run as one of three built-in accounts:

- LocalSystem
- NT AUTHORITY\LocalService
- NT AUTHORITY\NetworkService

I can build a filter to return accounts not configured as one of the three accounts listed above. Since we're looking for ANY account used to RUN AS, any account except those three above would be valid results.

To search CIM (or WMI) for any account except the three above, we would use the filter parameter and the syntax would look like this:

```PowerShell
 -filter " StartName != 'LocalSystem' AND NOT StartName LIKE 'NT Authority%' "
 ```

The `!=` is equivalent to NOT EQUAL TO and the statement `AND NOT StartName LIKE` is the same as `"StartName -notlike 'NT Authority%'"` in  traditional PowerShell code. We can interpret the search as _"Find all accounts which are not equal to the name LocalSystem and also do not start with NT Authority* "_. In simpler terms, it translates to _"all accounts that are not one of three built in we listed above"_.

Let's test that query to make sure it works as we expect. In order to do so, I reconfigured the Telephony Service to run under my admin credentials.

![telephony-screenshot](/images/2022/Use-PowerShell-Find-Svcs/Telephony-Service.png)

Let's see if our filter works as expected. If it does, we should see the one service (Telephony Service) returned as the only service configured with an alternate Logon As credential.

```PowerShell
Get-CIMInstance -Class Win32_Service -filter "StartName != 'LocalSystem' AND NOT StartName LIKE 'NT Authority%' " | Select-Object SystemName, Name, Caption, StartMode, StartName, State | Sort-Object StartName

SystemName : DC02
Name       : tapisrv
Caption    : Telephony
StartMode  : Manual
StartName  : mk\mkadmin
State      : Stopped
```

**Success!!!**

Now that we have a working search criteria, we can run this query against the servers. We should get a list of services that match our criteria of running with an alternate Logon As credential.

I have three servers I am going to query: DC01, DC02, and AZBuild01. There are multiple methods that can connect to each server and perform the query. I could use `ForEach-Object` and connect using `Get-CIMInstance` and add the `-ComputerName` parameter. For this task, I prefer to use PowerShell remoting (aka WinRM).

## Using PowerShell Remoting to connect to computers

The syntax for PowerShell Remoting is dead simple to use. The cmdlet we'll use is `Invoke-Command` (or ICM alias). We could have used `Enter-PSSession` to connect to each server interactively. Instead, `Invoke-Command` will connect to up to 32 computers in one shot in the background. It will retrieve the information I ask for and return the data to me in a formatted output.

> If you're not familiar with how to use `Invoke-Command`, visit my [Jump Start: PowerShell Remoting Article](https://commandline.ninja/jump-start-powershell-remoting/) for a refresher on all the different methods available to connect and the right syntax to use.

The syntax is the cmdlet name, the computer name and then the script block. The script block is where you place the actual lookup you would like to have performed on each computer.

```PowerShell
Invoke-Command -ComputerName MyServerNames -ScriptBlock {Insert your code here}
```

If you recall out earlier, lookup via CIMInstance was:

```PowerShell
Get-CIMInstance -Class Win32_Service -filter "StartName != 'LocalSystem' AND NOT StartName LIKE 'NT Authority%' " | select systemname, name, caption, startmode, startname, State | sort startname
```

the syntax using `Invoke-Command` would be:

```PowerShell
Invoke-Command "DC01", "DC02", "AzBuild01" -Scriptblock {Get-CIMInstance -Class Win32_Service -filter "StartName != 'LocalSystem' AND NOT StartName LIKE 'NT Authority%' " | select systemname, name, caption, startmode, startname, State | sort startname}
```

The output we get is:

![Invoke-Command output](/images/2022/Use-PowerShell-Find-Svcs/Invoke-Command-Results-01.png)

The results return quick because we are using a `-filter` instead of `where-object`. The difference is that filter sends the code to the remote computer. The computer does the lookup and finds the matches and only sends back the matches. If I used `where-object`, then all of the services form the remote machine would have been sent back and then after all the computers have responded with all their services, where-object would then filter out the results on my local computer.

I am only contacting three computers so the difference in speed is noticeable. But I sent this to 100 or 200 computers, then the difference between using `-filter` or `Where-object` becomes a big deal. The difference is usually seconds vs ten's of seconds for Where-object. You also want to use `-filter` instead of `where-object` unless -filter is not an option that is available for a cmdlet.

## Formatting output

We have fulfilled the request, but I see two problems. The first problem is that the output has some fields that my manager will not know what they mean. The fields Name and Caption are ambiguous and need better clarification. We can fix by using expressions to customize the names.

```PowerShell
Invoke-Command "DC01", "DC02", "AzBuild01" -Scriptblock {
    Get-CIMInstance -Class Win32_Service -filter "StartName != 'LocalSystem' AND NOT StartName LIKE 'NT Authority%' " |
        Select-Object SystemName, @{ Name = 'ServiceName';  Expression = {$_.Name}}, @{ Name = 'Service DisplayName';  Expression = {$_.Caption}}, StartMode, StartName, State |
            sort StartName
}
```

![Invoke-Command Output02](/images/2022/Use-PowerShell-Find-Svcs/Invoke-Command-Results-02.png)

That's better. Now when I send the results to my manager, he will understand the output and not have to ask me questions.

## Simplifying our code

The second problem I see in my code is that it's now a monster command that is hard for others to understand. If I save my code and pass it to a junior admin or a PowerShell novice, they may struggle trying to comprehend the code. We solved the problem, but we should try to improve the readability for others. The solution for readability is variables.

If we take the various parts of the code and save them to variables, then the cmdlet gets much easier to read. We can assign variables for the server names and the two expressions.

Let's start with the server names:

```PowerShell
$Servers = "DC01", "DC02", "AzBuild01"
```

and we can also do the same for expressions:

```PowerShell
$ServiceName =  @{ Name = 'ServiceName'; Expression = {$_.Name}}
$ServiceDisplayname = @{ Name = 'Service DisplayName';  Expression = {$_.Caption}}
```

Now we can substitute those values in our code:

```PowerShell
$Servers = "DC01", "DC02", "AzBuild01"
$ServiceName =  @{ Name = 'ServiceName'; Expression = {$_.Name}}
$ServiceDisplayname = @{ Name = 'Service DisplayName';  Expression = {$_.Caption}}

Invoke-Command $servers -ScriptBlock { Get-CimInstance -Class Win32_Service -filter "StartName != 'LocalSystem' AND NOT StartName LIKE 'NT Authority%' " } | Select-Object SystemName, $ServiceName, $ServiceDisplayname, StartMode, StartName, State
```

![Invoke-Command Results 03](/images/2022/Use-PowerShell-Find-Svcs/Invoke-Command-Results-03.png)

Our code is much more readable than earlier and when we hand it over to someone else, they will have a much better chance of understanding what this code does.

The last change I will make is to change the output to list as a table.

```PowerShell
$Servers = "DC01", "DC02", "AzBuild01"
$ServiceName =  @{ Name = 'ServiceName'; Expression = {$_.Name}}
$ServiceDisplayname = @{ Name = 'Service DisplayName';  Expression = {$_.Caption}}

Invoke-Command $servers -ScriptBlock { Get-CimInstance -Class Win32_Service -filter "StartName != 'LocalSystem' AND NOT StartName LIKE 'NT Authority%' " } |
    Select-Object SystemName, $ServiceName, $ServiceDisplayname, StartMode, StartName, State |
        format-table -autosize
```

![Invoke Command Results 04](/images/2022/Use-PowerShell-Find-Svcs/Invoke-Command-Results-04.png)

## Conclusion

I hoped you enjoyed this look into how I tackled a real-world problem. We started with a task and found the pertinent information needed to fulfill the request. After we found what and how to query the information, we worked on how to repeat that query against multiple computers. Then we worked on formatting the output to something that is human readable for non admin staff to work with. We could still take these results and export them to a CSV or better yet, an Excel file, which are standard office format almost anyone can open and consume.
