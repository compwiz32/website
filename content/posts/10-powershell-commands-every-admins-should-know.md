---
title: '10 PowerShell commands every admin should know'
slug: '10-powershell-commands-every-admin-should-know'
description: To get started with PowerShell, these ten commands provide a solid foundation.'
date: 2017-06-18
authors: [mike]
image: /images/2017/10-commands-admins-should-know/CheckList.webp
tags: [PowerShell, Jumpstarts]
draft: false
---

originally written by Brien Posey - [TechRepublic](http://www.techrepublic.com/blog/10-things/10-powershell-commands-every-windows-admin-should-know/) - March 24th 2011

Over the last few years, Microsoft has been trying to make PowerShell the management tool of choice. Almost all the newer Microsoft server products require PowerShell, and there are lots of management tasks that can't be accomplished without delving into the command line. As a Windows administrator, you need to be familiar with the basics of using PowerShell. Here are 10 commands to get you started.

## Get-Help

The first PowerShell cmdlet every administrator should learn is Get-Help. You can use this command to get help with any other command. For example, if you want to know how the Get-Process command works, you can type: `Get-Help -Name Get-Process` and Windows will display the full command syntax.

You can also use `Get-Help` with individual nouns and verbs. For example, to find out all the commands you can use with the Get verb, type: `Get-Help -Name Get-*` .

## Set-ExecutionPolicy

Although you can create and execute PowerShell scripts, Microsoft has disabled scripting by default in an effort to prevent malicious code from executing in a PowerShell environment. You can use the `Set-ExecutionPolicy` command to control the level of security surrounding PowerShell scripts. You can set an execution policy by entering the `Set-ExecutionPolicy` command followed by the name of the policy.

Four levels of security are available to you:

- _Restricted_ - Restricted is the default execution policy and locks PowerShell down so that commands can be entered only interactively. PowerShell scripts are not allowed to run.
- _All Signed_ - If the execution policy is set to All Signed then scripts will be allowed to run, but only if they are signed by a trusted publisher.
- Remote Signed - If the execution policy is set to Remote Signed, any PowerShell scripts that have been locally created will be allowed to run. Scripts created remotely are allowed to run only if they are signed by a trusted publisher.
- _Unrestricted_ - As the name implies, Unrestricted removes all restrictions from the execution policy.

For example, if you wanted to allow scripts to run in an unrestricted manner you could type: `Set-ExecutionPolicy Unrestricted` .

## Get-ExecutionPolicy

If you're working on an unfamiliar server, you'll need to know what execution policy is in use before you attempt to run a script. You can find out by using the Get-ExecutionPolicy command.

## Get-Service

The `Get-Service` command provides a list of all of the services that are installed on the system. If you are interested in a specific service you can append the `-Name` switch and the name of the service (wildcards are permitted) When you do, Windows will show you the service's state.

## ConvertTo-HTML

PowerShell can provide a wealth of information about the system, but sometimes you need to do more than just view the information on screen. Sometimes, it's helpful to create a report you can send to someone. One way of accomplishing this is by using the `ConvertTo-HTML` command.

To use this command, simply pipe the output from another command into the `ConvertTo-HTML` command. You will have to use the `-Property` switch to control which output properties are included in the HTML file and you will have to provide a filename. To see how this command might be used, think back to the previous section, where we typed `Get-Service` to create a list of every service that's installed on the system.

Now imagine that you want to create an HTML report that lists the name of each service along with its status (regardless of whether the service is running). To do so, you could use the following command: `Get-Service | ConvertTo-HTML -Property Name, Status > C:\services.htm` .

## Export-CSV

Just as you can create an HTML report based on PowerShell data, you can also export data from PowerShell into a CSV file that you can open using Microsoft Excel. The syntax is similar to that of converting a command's output to HTML. At a minimum, you must provide an output filename. For example, to export the list of system services to a CSV file, you could use the following command: `Get-Service | Export-CSV c:\service.csv`

## Select-Object

If you tried using the command above, you know that there were numerous properties included in the CSV file. It's often helpful to narrow things down by including only the properties you are really interested in. This is where the Select-Object command comes into play.

The `Select-Object` command allows you to specify specific properties for inclusion. For example, to create a CSV file containing the name of each system service and its status, you could use the following command: `Get-Service | Select-Object Name, Status | Export-CSV c:\service.csv`

## Get-EventLog

You can actually use PowerShell to parse your computer's event logs. There are several parameters available, but you can try out the command by simply providing the `-Log` switch followed by the name of the log file. For example, to see the Application log, you could use the following command: `Get-EventLog -Log "Application"` .

Of course, you would rarely use this command in the real world. You're more likely to use other commands to filter the output and dump it to a CSV or an HTML file.

## Get-Process

Just as you can use the `Get-Service` command to display a list of all of the system services, you can use the `Get-Process` command to display a list of all of the processes that are currently running on the system.

## Stop-Process

Sometimes, a process will freeze up. When this happens, you can use the `Get-Process` command to get the name or the process ID for the process that has stopped responding.
You can then terminate the process by using the `Stop-Process` command. You can terminate a process based on its name or on its process ID. For example, you could terminate Notepad by using one of the following commands:

```PowerShell
Stop-Process-Name notepad
Stop-Process -ID 2668
```

Keep in mind that the process ID may change from session to session.
