+++
categories = ["PowerShell", "WinRM", "PS-Remoting", "PKI-Certificates", "Group-Policy", "WMI", "CIM"]
date = 2018-10-27T18:36:06Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/10/door-green-closed-lock.jpg"
slug = "securing-powershell"
summary = "Out of the box, PowerShell remoting is very secure but there are enhancements you can make to increase security."
tags = ["PowerShell", "WinRM", "PS-Remoting", "PKI-Certificates", "Group-Policy", "WMI", "CIM"]
title = "How to secure PowerShell Remoting in a Windows Domain"

+++


PowerShell is an awesomely powerful tool for configuring, managing and controlling your environment. Out of the box, PowerShell remoting is very secure but there are enhancements you can make to increase security.

I was recently asked to come up with a security posture for my organization and to communicate that stance to my leaders. I knew PS was secure from the start but I couldn't rely on what I thought, so I needed to dive in and do some research to confirm my opinion of security in PowerShell with respect to remoting.

This is a reference guide I originally built for myself that I thought would be useful for others when trying to figure out how to secure PowerShell remoting or at least answer the questions someone may be asked from management or security teams in a corporate environment. I have pulled relevant data from a number of sources including, but not limited to:

* [Microsoft: Authentication for Remote Connections](https://docs.microsoft.com/en-us/windows/desktop/winrm/authentication-for-remote-connections)
* [Microsoft: PowerShell Remoting Security Considerations](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/winrmsecurity?view=powershell-7)
* [FireEye: Greater Visibility Through PowerShell Logging](https://www.fireeye.com/blog/threat-research/2016/02/greater_visibilityt.html)
* [FoxDeploy.com: Is WinRM Secure or do I need HTTPs?](https://foxdeploy.com/2017/02/08/is-winrm-secure-or-do-i-need-https/)

One big caveat before I begin: There are different settings that can be put in place for domain-based machines and workgroup based machines. I am largely ignoring the workgroup scenarios because I do not have to worry about that consideration. I'll make a few references to configs for workgroup computers, but this guide is targeted at securing connections in an Active Directory domain running mostly Windows computers. Also of note, I am not going to talk at all about PS remoting to Linux because, again, not a big requirement for me or my org. Please visit the links above and see the security considerations for those two scenarios.

Lets' get started!

### Security & Authentication

PowerShell was designed from the start with security in mind. The default method of authentication out of the box is Kerberos. Connections between the two computers in a domain will use Kerberos for initial authentication and fall back to NTLM for workgroup computers. You can change these default settings and actually lower the authentication settings, but I would not recommend that.

Once the authentication phase has completed, all session communications are encrypted using a symmetric 256-bit key, even with HTTP as the protocol. Domain-joined computers never pass credentials in the clear; this is the default setting.

Remote connections between domain-joined computers can be made with a number of different methods. The three most common methods when using PowerShell are:

* Connect via WMI
* Connect via CIM
* Connect via PS remoting/WinRM

The main difference between these remote connection methods is what protocol is used to make the remote connection. Connections via WMI use DCOM to access remote machines. Connections via CIM or WinRM use the WSMan protocol.

* **WMI** is the Microsoft implementation of Web-Based Enterprise Management and provides users with information about the status of local or remote computer systems. The main knock against WMI is that it isn't very firewall friendly. For the connection to succeed, the remote computer must permit incoming network traffic on TCP ports 135, 445, and additional dynamically-assigned ports, typically in the range of 1024 to 1034.
* **WS-Management protocol**, a SOAP-based, firewall-friendly protocol, was designed for systems to locate and exchange management information. The intent of the [WS-Management protocol](https://en.wikipedia.org/wiki/WS-Management) specification is to provide interoperability and consistency for enterprise systems that have computers running on a variety of operating systems from different vendors. It only requires TWO ports (one for http, one for https) to be open and has much greater security posture than WMI.

By default, PowerShell Remoting relies on WinRM to make connections to other machines unless a WMI call is being made. WinRM (and WMI) only allow connections from members of the Administrators group. If you have a handle on who has admin access to your servers and desktops, then you're off to a great start in securing your PS remoting environment. PS remote sessions are launched under the user's context, so all operating system access controls applied to individual users and groups continue to apply to them while connected over PowerShell remoting.

### Certificate-Based Authentication

When you authenticate with Kerberos to a remote PC, you are verifying that you are who you say you are. However, neither you or the remote node can verify if the either computer is who they say they are. Certificate-Based Authentication solves this problem through the use of certificates and the SSL authentication protocol. Using SSL certificates, the authenticity of the remote computer can be proven as well as the the machine initiating the connection. Without SSL, a computer can spoof a name and trick the other side into thinking they are connected to a different computer by falsely building confidence that the spoofed computer is one they can trust. This scenario I have described is simple example of how the first half of a man-in-the-middle attack is crafted.

In order for you to enable Certificate-Based Authentication, you must have a certificate infrastructure in place and each server must have a certificate issued by the the Certificate Authority. You could also build the infrastructure if it doesn't exist but this is not a trivial task. These requirements make Certificate-Based Authentication tough to put in place if you don't already have that infrastructure setup beforehand and a deep understanding of how certificates work.

### Firewall Settings

PowerShell Remoting (and WinRM) listen on the following ports:

* HTTP: 5985
* HTTPS: 5986

Earlier I mentioned that WMI is less firewall friendly because it connects via TCP ports 135, 445, and additional dynamically-assigned ports, typically in the range of 1024 to 1034. WinRM is much easier to secure since you can limit your firewall to only opening two ports.

The default Windows Firewall rule for PowerShell remoting accepts all connections on private networks. On public networks, the default Windows Firewall rule allows PowerShell remoting connections only from within the same subnet. You have to explicitly change that rule to open PowerShell remoting to all connections on a public network.

WinRM runs as a service under the Network Service account and spawns isolated processes running as user accounts to host PowerShell instances. An instance of PowerShell running as one user has no access to a process running an instance of PowerShell as another user.

Up until now, we have been discussing authentication and firewall settings. Hopefully, it's clear that there isn't much that you need to do to improve your security for these two areas but now we're going to start to discuss some areas where you do have some real options that can increase the security of your PowerShell remoting environment.

### Limiting Connections

Out of the box, every Windows computer on your network can make a connection to any other computer on that network if the user connecting has the rights and there is a network path available between the two nodes. **One very easy way to beef up the security of your organization is to limit which machines can start a remote connection.** For example, let's assume you are an admin of a computer network that has 500 computers. Of those 500 computers, let's say you manage 20 servers and that you have an IT support staff of 10 people. You could argue that there are approximately 470 computers that will probably never INITIATE a remote PowerShell connection attempt (500 PC's - 20 servers - 10 IT admins). If you shut off the ability to make remote PowerShell connections for those 470 computers, you just reduce your PowerShell attack surface by 94%!

The idea is that most of your end users would never need to initiate remote connections, so you can make yourself more secure by turning off the ability for them to do so. This would protect your org when a machine gets PWNED and the attacker attempts to run remote PS commands from that machine. It would also stop an attacker from hopping between nodes via PowerShell easily because most PC's on the network would refuse the connection.

So, how could you limit which clients could connect to a host via WinRM? 

**You can't limit source connections via GPO at the WinRM level**. But there are two ways that you can limit connections. The first I mentioned earlier: limit who has administrator access to the box (or who is a member of the Remote Management Users group). The other method is to limit connection sources at the host's firewall. I know that many admins disable the Windows Firewall as a practice because it just makes things easier. However, you need to stop that practice going forward because you are opening the nodes on your network to the greatest possible attack surface. 

I want to also highlight the GPO setting "**Allow remote server management through WinRM**". The usage of the setting in the screencap below can be confusing and I have to admit that I totally misunderstood its purpose the first time I researched the security settings for WinRM. This setting controls what interfaces the HOST listens on, not what clients can connect tot he host.

So, when would you use this setting? 

Let's say you had a server with multiple NIC's and those NIC's all served a different purpose. For example let's say NIC1 is the PROD network, NIC2 is the backup network and NIC3 is a heartbeat network. The setting "**Allow remote server management through WinRM**" allows you to configure the server so that you can limit which of those NIC's would be able to accept WinRM connections from remote hosts.



{{< figure src="__GHOST_URL__/content/images/2018/10/image.png" caption="<strong>Computer Configuration</strong> &gt; <strong>Policies</strong> &gt; <strong>Administrative Templates: Policy definitions</strong> &gt; <strong>Windows Components</strong> &gt; <strong>Windows Remote Management (WinRM)</strong> &gt; <strong>WinRM Service</strong>." >}}



{{< figure src="__GHOST_URL__/content/images/2018/10/image-2.png" caption="<strong>Limit which IP's (networks) on a host can accept connections&nbsp;</strong>" >}}



## Logging

Logging is another area where Microsoft has given considerable flexibility in how much to log and where to store some of the log information. Keep in mind that some of the options discussed below are only available in PowerShell 5.x, so the recommendation is to upgrade all pc's to PS 5.x so you can take advantage of all the latest and greatest security features.

PowerShell logs connection attempts and successful connections by default but does not log what commands or scripts are executed by default. The Microsoft team has given us the ability to log in great detail what happens when PowerShell is executed but you need to enable the logging options. Logging is a little bit of an art form because the more logging you turn on, the greater the chance the logging will have a negative impact on the performance of the PC.

There are three log settings that can be enabled to log activity for WinRM remote sessions:

* Module Logging
* Script Block Logging
* Transcription

### Module Logging

Module logging records pipeline execution details as PowerShell executes, including variable initialization and command invocations. Module logging will record portions of scripts, some de-obfuscated code, and some data formatted for output. This logging will capture some details missed by other PowerShell logging sources, though it may not reliably capture the commands executed.

Module logging has been available since PowerShell 3.0 and logged events are written to Event ID# 4103. Please be aware, **module logging generates a large volume of events!** The information captured is useful, especially when trying to figure out what happened during a security breach, but you will definitely blow up your security log and cause it to roll over very easily. If you have a SIEM tool that captures log data, this probably isn't a concern for you, but if you don't have SIEM in your org then turning on module logging may not be in your best interest.

### Script Block Logging

Script block logging records blocks of code as they are executed by the PowerShell engine, thereby capturing the full contents of code executed by an attacker, including scripts and commands. Due to the nature of script block logging, it also records de-obfuscated code as it is executed. For example, in addition to recording the original obfuscated code, script block logging records the decoded commands passed with PowerShell’s -EncodedCommand argument, as well as those obfuscated with XOR, Base64, ROT13, encryption, etc., in addition to the original obfuscated code. Script block logging will not record output from the executed code.

Script block logging events are recorded in Event ID # 4104. Script blocks exceeding the maximum length of an event log message are fragmented into multiple entries. Scripts are available to re-assemble log messages split across log entries. PowerShell 5.0 will automatically log code blocks if the block’s contents match on a list of suspicious commands or scripting techniques, even if script block logging is not enabled. These suspicious blocks are logged at the “warning” level in Event ID #4104, unless script block logging is explicitly disabled.

Enabling script block logging will capture all activity, not just blocks considered suspicious by the PowerShell process. Script block logging generates fewer events than module logging but the volume still can be considerable. 

### Transcription

**Transcription creates a unique record of every PowerShell session**, **including all input and output, exactly as it appears in the session.** 

Transcripts are written to text files, broken out by user and session. Transcripts also contain timestamps and metadata for each command in order to aid analysis. However, transcription records only what appears in the PowerShell terminal, which will not include the contents of executed scripts or output written to other destinations such as the file system.

PowerShell transcripts are automatically named to prevent collisions, with names beginning with “PowerShell_transcript”. By default, transcripts are written to the user’s documents folder but can be configured to any accessible location on the local system or on the network.

I like the idea of turning on transcription and sending those logs to a file share. The logs are simple text files and are easy to read and scan at a glance if necessary. Also, since they're just text files, they are very efficient to store and can be compressed and archived without much effort. You can setup transcription to write transcripts to a remote, write-only network share. Machines would dump log data there but only a handful of people could remove those logs.

### Conclusion

I have walked you through many of the options available for PowerShell Remoting. The take away from this article hopefully is that you realize that, by default, PowerShell authentication is strong. However, detailed logging is not enabled by default. PowerShell only logs connection attempts in its default config, but there is the ability to log in great detail everything that occurs within a PowerShell session. The downside here is that turning on logging has an impact on machine performance and disk space and you will have to figure out for yourself what the best mix is for your org.

Enabling script block logging and transcription will record most activity while minimizing the amount of log data generated. At a minimum, transcription logging should be enabled, in order to identify commands run and code that was executed.

### My Recommendations

Follow these recommendations and you will have a very strong security posture with respect to running PowerShell in your organization:

* Turn on transcription logging and have logs write to a central file share
* Lock down who has the ability to remove logs from the central share
* Turn on script block logging and assess the impact on your PC's
* Module logging is also recommended but it will generate large volumes of event log data, so think carefully before enabling this setting
* Enable a certificate infrastructure for your domain
* Install SSL certificates on all domain computers
* Disable the HTTP port in use for PowerShell after SSL certs have been deployed
* Limit via GPO which PC's can initiate PowerShell connection attempts
* Install Windows PowerShell v5 on every node in your environment
* Eliminate local administrators on PC's and servers aggressively

I hope you find this practical guide useful in your research. I welcome you to leave comments below and let me know what concerns or feedback you have. If you like articles like this, don't forget to sign up for my mailing list. I usually write one article a week and I never share your info with anyone or any business.

