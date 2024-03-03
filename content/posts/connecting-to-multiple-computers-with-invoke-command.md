---
date: 2020-01-13
title: Connecting to multiple computers with Invoke-Command
draft: false
authors: [mike]
image: /images/2020/Connect-Multiple-Computers-Invoke-Command/roadways-compressed.webp
slug: connecting-to-multiple-computers-with-invoke-command
description: Learn how to connect to multiple computers from PowerShell with my guide on PowerShell Remoting and Invoke-Command.
tags: [PowerShell, 'PowerShell Remoting']
---

PowerShell remoting is a method you can use to connect securely to many machines with very little effort. Using remoting, a user can quickly query information or deploy a change to multiple computers without ever having to login via a GUI or RDP. Let's review the basics of this cmdlet and how you can get started using Invoke-Command.

## What is PowerShell Remoting?

PowerShell Remoting allows you to connect to multiple computers on your network from a PowerShell command prompt. Remoting makes a secure connection behind the scenes to each computer you specify and then executes the commands on the remote machine(s). When the commands finish processing, it sends the results back to your machine. It differs from connecting via WMI or using some other tool like PSEXEC or RDP.

Remoting allows data to move between machines via encrypted, authenticated sessions. To successfully connect to a remote machine, ports 5985 and 5986 must be open and the WinRM service must be running. The user attempting to connect needs to be an administrator on the remote machine or a member of the Remote Management Users group unless a special configuration is in place.

Today I will be focusing on how to connect to remote machines. However, there are many configuration items for PS-Remoting that you can configure to limit and control connections and log all the activity. I have written previously about PowerShell remoting configuration options [here](https://commandline.ninja/posts/securing-powershell/) and recorded a video presentation on the topic here [here](https://commandline.ninja/posts/psremoting-video/). You can read up on remoting by typing `Help About_Remoting` in your PowerShell cmd prompt.

## Create an Interactive Remote Session

The easiest way to make a remote connection using PSRemoting is to use the cmdlet `Enter-PSSession`. This cmdlet creates an interactive command prompt on a remote machine. Once the connection to the remote machine is established, the command prompt on your session changes to the name of the remote computer.

```PowerShell
Enter-PSSession -ComputerName RemotePCName -Credential UserID
```

Using that syntax, I can connect by IP or Computer DNS name

![Connect_by_IPAddress](/images/2020/Connect-Multiple-Computers-Invoke-Command/Connect_by_IPAddress.png)

![Connect_by_ComputerName](/images/2020/Connect-Multiple-Computers-Invoke-Command/Connect_by_ComputerName.png)

Running remote PowerShell commands works the same as if you were running them on your local PC. The only difference is that I am now working via remote a connection to the remote computer.

![Running-commands-on-remote-PC](/images/2020/Connect-Multiple-Computers-Invoke-Command/Running-commands-on-remote-PC.png)

But this method of connecting has one significant downside: your PowerShell cmd prompt can only connect to one computer interactively at a time. This may work for some quick data gathering but it doesn't allow for an admin to execute commands against a large number of computers at the same time.

## Invoke-Command

Invoke-Command allows an admin to connect to and run code against multiple computers at one time. I can still to connect one computer or I can connect yo many; the process is similar to using `Enter-PSSession`. Let's connect to one computer and get the current date.

```PowerShell
PS C:\ invoke-command Svr01 -ScriptBlock {get-date} -Credential $MKAdmin
Tuesday, December 10, 2019 10:33:45 AM
```

So what's different?

When I used `Enter-PSSession`, I interactively connected to the remote computer and stayed in the session until I end the session. When using `Invoke-Command`, the cmdlets are sent to the remote computer, the cmdlets are processed on the remote computer and are sent back just like before. The connection between our computer and remote computer ends once it has processed the code.

Let's bump this up one small step. Now let's connect to two computers:

```PowerShell
PS C:\ invoke-command Svr01, Svr02 -ScriptBlock {get-date} -Credential $MKAdmin
Tuesday, December 10, 2019 10:37:14 AM
Tuesday, December 10, 2019 10:37:16 AM
```

I have ran only one command but I was able to get results from two computers. But now I have a new problem. How do I tell which results came from which computer?

When retrieving data via PSRemoting, PowerShell automatically creates a new variable called `PSComputerName`. It's purpose to store the remote computer name that returned the data you queried. If I go back to my previous example, all I need to do is save the results to a variable.

```PowerShell
PS C:\ $results: invoke-command Svr01, Svr02 -ScriptBlock {get-date} -Credential $MKAdmin

PS C:\> $results | Select-Object PSComputerName, Date

PSComputerName      Date
--------------      ----
Svr01               12/10/2019 12:00:00 AM
Svr02               12/10/2019 12:00:00 AM

```

Using this technique, you can query many computers at once with little effort. However, what if you don't want to type a bunch of computer names every time you type your `invoke-command` syntax?

You can save your computer names to a variable through many different methods.
You could query an AD group, import a list of computers using `get-content` or `import-csv`, and so on. For my examples, I'll add a few computers to a variable named `$svrs`.

```PowerShell
$svrs: "svr01","svr02","svr03","svr04","svr05"
```

Now I can use that variable in my syntax like so:

```PowerShell
PS C:\ $results: invoke-command $svrs -ScriptBlock {get-date}  -Credential $MKAdmin

PS C:\> $results | Select-Object PSComputerName, Date

PSComputerName      Date
--------------      ----

Svr03               Tuesday, December 10, 2019 11:26:07 AM
Svr04               Tuesday, December 10, 2019 11:26:16 AM
Svr02               Tuesday, December 10, 2019 11:26:17 AM
Svr01               Tuesday, December 10, 2019 11:26:10 AM
Svr05               Tuesday, December 10, 2019 11:26:11 AM
```

Notice the results don't return in a particular order. That happens because Invoke-Command is making multiple connections across the network and it received results from computers at different times. It lists the results based on which computer responded fastest. Once the results come back, I can sort the results.

I used a variable so I didn't have to keep running the query over and over again. The variable allows me to manipulate the output afterwards to the way I prefer.

```PowerShell
PS C:\> $results | Select-Object PSComputerName, Date | Sort-Object PSComputerName

PSComputerName      Date
--------------      ----

Svr01               Tuesday, December 10, 2019 11:26:10 AM
Svr02               Tuesday, December 10, 2019 11:26:17 AM
Svr03               Tuesday, December 10, 2019 11:26:07 AM
Svr04               Tuesday, December 10, 2019 11:26:16 AM
Svr05               Tuesday, December 10, 2019 11:26:11 AM
```

## Summary

This was a brief introduction on how to get started connecting to a remote computer with PowerShell remoting. I started by connecting to an interactive session on one computer and then built on that example to connect to multiple pc's. This is only the beginning of learning how to use PowerShell remoting to retrieve data from remote computers. Let me know what you think down below in the comments.

## Want more content on PowerShell Remoting?

Head over to the [Jump Starts](https://commandline.ninja/tags/jumpstarts/) section on my website for more articles on how to work with and configure PowerShell remoting.

Cover Photo Credit: Denys Nevozhai on Unsplash