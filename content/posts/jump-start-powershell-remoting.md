---
date: 2020-01-13
title: "Jump Start: PowerShell Remoting"
authors: [mike]
draft: false
tags: [Jumpstarts, 'PowerShell Remoting',PowerShell]
image: /images/2020/Jumpstart-Remoting/BartSimpson-Remoting.jpg
slug: jump-start-powershell-remoting
description: "Get up to speed quickly on all aspects of PowerShell remoting, logging and security with easy to follow articles and guides"
---

**TABLE OF CONTENTS**

- [Connecting to multiple computers with Invoke-Command](#connecting-to-multiple-computers-with-invoke-command)
- [Invoke-Command: Connecting to computers requiring different credentials](#invoke-command-connecting-to-computers-requiring-different-credentials)
- [Invoke-Command: Dealing with offline computers](#invoke-command-dealing-with-offline-computers)
- [Invoke-Command: Compensating for slow responding computers](#invoke-command-compensating-for-slow-responding-computers)
- [How to secure PowerShell Remoting in a Windows Domain](#how-to-secure-powershell-remoting-in-a-windows-domain)
- [Video: Configuring PowerShell Remoting Security \& Logging](#video-configuring-powershell-remoting-security--logging)

PowerShell remoting is a method you can use to connect securely to many machines with very little effort. Using remoting, a user can quickly query information or deploy a change to multiple computers without ever having to login via a GUI or RDP.

Here's a collection of content I have created on PowerShell remoting that you can use to learn how to configure and use PowerShell remoting:
<br>

### [Connecting to multiple computers with Invoke-Command](https://www.commandline.ninja/connecting-to-multiple-computers-with-invoke-command/)

This article will get you started with how to connect to multiple computers from a PowerShell command prompt. Topics include connecting with Enter-PSSession, Invoke-Command and how to save and organize results.
<br>

### [Invoke-Command: Connecting to computers requiring different credentials](https://4sysops.com/archives/invoke-command-connecting-to-computers-requiring-different-credentials/)

Connecting to computers that require different credentials via the PowerShell cmdlet Invoke-Command is a common occurrence. This post explains how you manage these situations.
<br>

### [Invoke-Command: Dealing with offline computers](https://4sysops.com/archives/invoke-command-dealing-with-offline-computers/)

When you need to run PowerShell commands against a large set of computers with the PowerShell cmdlet Invoke-Command, you often have to deal with offline computers. In this post, you will learn how to deal with unresponsive machines.
<br>

### [Invoke-Command: Compensating for slow responding computers](https://4sysops.com/archives/invoke-command-compensating-for-slow-responding-computers/)

Sometimes running remote code with the PowerShell cmdlet Invoke-Command can take a long time to run—and not for any reason having to do with your code itself. Connecting to older computers means that long bits of code take longer to run. Computers on the other side of slow WAN links will always be slower in responding than something close and well connected.
<br>

### [How to secure PowerShell Remoting in a Windows Domain](https://www.commandline.ninja/securing-powershell/)

This is a reference guide I originally built for myself that I thought would be useful for others when trying to figure out how to secure PowerShell remoting or at least answer the questions someone may be asked from management or security teams in a corporate environment. It's deep dive on logging and security options and what they mean.
<br>

### [Video: Configuring PowerShell Remoting Security & Logging](https://www.commandline.ninja/psremoting-video/)

A recent presentation I gave on configuring PowerShell remoting security and logging for my user user group. This talk focused on how to properly setup remoting security and logging options. I talk briefly about how remoting works with some simple examples of what a real-world remoting connection looks like.
