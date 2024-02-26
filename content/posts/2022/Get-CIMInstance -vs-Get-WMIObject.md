---
title: "Get-CIMInstance Vs Get-WMIObject: What's The Difference?"
description: "PowerShell provides two similar management interfaces for accessing data on a computer: WMI and CIM. What's the difference between these management interfaces? Which one should you use, and why?"
date: 2022-03-16
authors: [mike]
draft: false
image: /images/2022/CIM-vs-WMI/CIM-vs-WMI-Header.webp
slug: get-ciminstance-vs-get-wmiobject
---


CALLOUT:
I wrote this article for the [IPSwitch website](https://www.ipswitch.com/blog/get-ciminstance-vs-get-wmiobject-whats-the-difference) in Oct 2019. I am posting it here for archival purposes. I have reformatted it for readability and grammar.

PowerShell provides two similar management interfaces for accessing data on a computer: WMI and CIM.

Sysadmins have been using WMI for years, and then Microsoft gave us data access via CIM with the rollout of PowerShell v3.0. What's the difference between these management interfaces? Which one should you use, and why?

## A history lesson on WMI and CMI

Windows Management Interface (WMI) is a well-known management interface that can access data about a computer. A lesser-known fact is that WMI is based on the "Common Information Model" standard of how to display managed data in an easy-to-read format. If you're paying close attention, you'll see that the abbreviation for Common Information Model is CIM.
Wait. What? Aren't CIM and WMI different?
Actually, they have more in common than not. Let's dive in and take a closer look.

The "Common Information Model" (CIM) is an [open-source standard](https://www.dmtf.org/standards/cim) for accessing and displaying information about a computer. It's an industry standard that's been around for many years, but it has no method included to access data on a remote computer. WMI is Microsoft's version of CIM. Microsoft added DCOM and RPC to the CIM management framework, along with other small changes, and called it the Windows Management Interface. WMI was Microsoft's solution for how to use CIM on remote computers over a network. The main knock against WMI is that it isn't very firewall-friendly. You need to poke a bunch of big holes in a firewall to make it work.

## WMI and CIM Network requirements

It's easy to see that WMI and CIM access the same set of data. Here I am querying the Win32_operatingsystem datastore with each cmdlet and you'll notice the output is identical:

```PowerShell
PS C:\> Get-WmiObject win32_operatingsystem | Format-List

SystemDirectory : C:\WINDOWS\system32
Organization    :
BuildNumber     : 17763
RegisteredUser  : Windows User
SerialNumber    : 00331-60000-00000-AA085
Version         : 10.0.17763


PS C:\> Get-CimInstance Win32_OperatingSystem | Format-List

SystemDirectory : C:\WINDOWS\system32
Organization    :
BuildNumber     : 17763
RegisteredUser  : Windows User
SerialNumber    : 00331-60000-00000-AA085
Version         : 10.0.17763
```

Keep in mind, in this example; I queried data from my local PC. When accessing local data, WMI and CIM are nearly identical except for minor cosmetic differences in the output. However, accessing from a remote PC is different between the two at the network level. For a WMI connection to succeed, the remote computer must permit incoming network traffic on TCP ports 135, 445, and additional dynamically assigned ports between 1024 to 1034. The [dynamic range of ports and the number of ports](https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/service-overview-and-network-port-requirements?/WT.mc_id=CDM-MVP-5004073) needed to make a successful connection make security pros nervous about using WMI on a corporate network.

In 2012, Microsoft addressed these concerns by releasing a new version of WMI called Windows Remote Management (WinRM) or more commonly called PSRemoting. It uses the Web Services for Management protocol (WS-Man) for data transfer between computers instead of DCOM and needs only two ports to make a secure connection.

From a security perspective, the default configuration for PSRemoting is secure by default. Windows Remote Management (WinRM) is the service on a Windows computer that creates and maintains the connection to another computer on a Windows network. WinRM uses the WSMan protocol to transfer data between computers securely. The WSMan protocol uses ports 5985 and 5986 and those ports connect via HTTP and HTTPS. All traffic is encrypted by default, even when using an insecure protocol like HTTP. Also, PSRemoting leverages Active Directory for authentication. By default, PSRemoting uses Kerberos to authenticate users and determine what level of access they should have on the remote computer. WMI has similar controls you can put in place, but they are much harder to configure and are not secure by default.

## What's in a name?

[New cmdlets were created in PowerShell v3.0](https://devblogs.microsoft.com/powershell/introduction-to-cim-cmdlets/?WT.mc_id=CDM-MVP-5004073) to take advantage of WinRM. These new cmdlets are called "CIM-based" cmdlets, meaning they work with the CIM standard. CIM is in the name of almost all the cmdlets. The only exception is Invoke-Command:

```PowerShell
PS C:\> Get-Command -noun cimsession

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-CimSession                                     1.0.0.0    CimCmdlets
Cmdlet          New-CimSession                                     1.0.0.0    CimCmdlets
Cmdlet          Remove-CimSession                                  1.0.0.0    CimCmdlets


PS C:\> Get-Command -noun ciminstance

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-CimInstance                                    1.0.0.0    CimCmdlets
Cmdlet          New-CimInstance                                    1.0.0.0    CimCmdlets
Cmdlet          Remove-CimInstance                                 1.0.0.0    CimCmdlets
Cmdlet          Set-CimInstance                                    1.0.0.0    CimCmdlets
```

Anytime you use these cmdlets, you are using the more secure method of data transfer called PSRemoting. The confusion comes from Microsoft using the name CIM in the cmdlets. That is a bit of a misnomer because the older WMI cmdlets AND the new CIM cmdlets are all CIM based. The difference between WMI and CIM cmdlets is the protocols and security in use when accessing a remote computer. Once they get to the remote computer and authenticate, WMI and CIM access the same data.

## Which one should you use

Without a doubt, WMI is being deprecated. PowerShell today relies heavily on PSRemoting and the security features included. Microsoft introduced the CIM based cmdlets and PSRemoting with PowerShell v3.0. Any computer running Windows 7 or greater and Server 2008 SP2 and greater can AND should use PSRemoting instead of WMI. If you have a modern computer, you can AND should use PSRemoting. Moving from WMI to CIM is usually as simple as swapping the Get-WMIObject cmdlet with Get-CIMInstance. When you make the switch, you are increasing your security greatly.

It is unfortunate that Microsoft used the word CIM in their newer cmdlets. CIM has always been in use but the new cmdlets make it appear CIM is something new. Now that you know the history and the differences between WMI and CIM, I recommend you stop using WMI wherever possible and replacing it with PSRemoting and CIM-Based cmdlets.
