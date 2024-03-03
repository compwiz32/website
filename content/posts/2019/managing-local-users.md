---
title: "Managing Local User Accounts with PowerShell"
date: 2019-01-07
authors: [mike]
draft: false
image: "/images/2019/Manage-Local-UserAccounts/Matrix.jpg"
slug: "managing-local-users"
description: "Explore my PowerShell script that makes it a breeze to add domain accounts to local user groups."
tags: [PowerShell]
---

I've always been frustrated trying to manage local user accounts on servers and PC's. Ok, I'll admit my frustration mostly lies with the management of local PC's because those computers are not always online and sometimes they're in distant offices that are not well connected. For me, managing the user accounts on local PC's can sometimes just be time consuming.

The other thing that I have sort of hated is that managing local accounts with PowerShell has not been a built-in thing until PowerShell v5 was released and those cmdlets included for local accounts are for literally for LOCAL ACCOUNTS only. There is no remote option built-in to the cmdlets and you can't manage down-level versions of PowerShell remotely with the v5 cmdlets. Once every machine is v5, then it's not an issue using `Enter-PSSession` or `Invoke-Command` but until then, you still need a way to manage remote user accounts on local pc's. Other than those v5 cmdlets, you're on your own for managing local accounts in PowerShell.

Many people have written scripts and functions over the years to help solve this problem, but from my point of view, it seems like some of those scripts could be done better. For example, usually there is one script to manage Local Admins, another Remote Desktop Users, etc. Also, I have seen scripts that manage users and groups separately, which probably filled the need for someone but doesn't work for me. At the end of the day, it seems as though there are too many individual scripts to manage these local users and groups... I'd like to see one script/function that handle multiple users AND groups.

So I set out to fix that by creating my own cmdlets for managing local user accounts. I have created three scripts:  `Add-RemotePCLocalGroupMember`, `Get-RemotePCLocalGroupMember`, and `Remove-RemotePCLocalGroupMember` .

Today I am sharing with you the first of three scripts that wrote to help solve my issue. The script named `Add-RemotePCLocalGroupMember` is listed below and is a function, which means you'll need to load it in memory in order to run it.

So what's unique about my take on managing local user accounts? A few things, let me explain. First, in my world, I deal almost exclusively with five local user groups:

- Administrators
- Users
- Remote Desktop Users
- Remote Management Users
- Event Log Readers

I have limited my function to adding users or groups to only these five groups as possibilities via a `ValidateSet` parameter. This helps with typing a bit because the groups can now be filled with tab completion. If no group is specified, local Administrators is chosen by default; again this is for my needs but you can easily modify in the code if you would like a different default group.

Also, one pet peeve I have had with many scripts I found online was that the scripts could only add domain users to the local user groups. I often would find another script to add domain groups to the local user groups. I have coded my script to allow adding domain users and domain groups to the local users groups via the `Account` parameter.

The last bit of customization in my script refers to domain names. My script will auto populate the domain of the user initiating the script. So if you are adding users to a local PC and you are in the *NWTRADERS* domain, then the domain name will assume you are adding users from that domain the local user group. You could argue that this limits this tool if you handle multi-domain environments. Again this is my need, and I do plan to add that as a future possibility. If that's something you think would be useful, then drop me a line in the comments section below.

The rest of the code is fairly straightforward and is easily understandable. I test to make sure the machine is online and alert if it's not. Also I built in the ability to add users to more than one computer at a time.

So there ya have it, here's my take on adding users and groups to a local user groups. I'll be posting a follow-up article that will cover reading the local user group via `Get-RemotePCLocalGroupMember` and then finally another update will cover `Remove-RemotePCLocalGroupMember` .

Let me just say that as time progresses, things changes and scripts get updated. You should visit my [GitHub repo](https://github.com/compwiz32) for the latest version of this and any other scripts I create. Blog posts have a habit of getting out of date, so GitHub is your friend!

I also need to give credit where it's due. First off, the basis for my scripts came from some existing scripts made by [François-Xavier Cat](https://twitter.com/LazyWinAdm) . You should definitely check out his work and follow him online, he does some fantastic stuff! Also, [Stephen Valdinger](https://twitter.com/steviecoaster) was gracious enough to help me with some of the coding bits that I personally struggled with, so a big thanks goes out to him as well. He's another guy with a great ability and I recommend you give  him a look as well.

One last bit...

You notice I mentioned that I needed help producing this script. We all need some help from time to time. When you read these blog posts from me and others, please don't assume that everything we do comes easily. It's a never-ending journey of learning. Don't get discouraged if you haven't made it to where you think you should be. Stick with it, you'll get there, and don't be afraid to ask for help. The PowerShell community is amazing!

```PowerShell
Function Add-RemotePCLocalGroupMember {
<#
    .SYNOPSIS
        Adds an AD user or an AD group to local user group on a client PC or server.

    .DESCRIPTION
        Adds an AD user or an AD group to a local user group on a client PC or server. This cmdlet does not create new
        Local PC groups, it only adds users or groups to existing Local PC groups already created.

    .PARAMETER ComputerName
        Specifies a computer to add the users to. Multiple computers can be specificed with commas and single quotes
        (-Computer 'Server01','Server02')

    .PARAMETER Account
        The SamAccount name of an AD User or AD Group that will be added to a local group on the local PC.

    .PARAMETER Group
        The name of the LocalGroup on target computer that the account will be added to. Valid choices are
        Administrators,'Remote Desktop Users','Remote Management Users', 'Users' and 'Event Log Readers'.
        If no choice is made, the default will be Administrators

    .EXAMPLE
        Add-RemotePCLocalGroupMember -Computer Server01 -Account Michael_Kanakos -Group Administrators

        Description:
        Will add the account named Michael_Kanakos to the local Administrators group on the computer named Server01

    .EXAMPLE
        Add-RemotePCLocalGroupMember -Computer 'Server01','Server02' -Account HRManagers -Group 'Remote Desktop Users'

        Description:
        Will add the HRManagers group as a member of Remote Desktop Users group on computers named Server01 and Server02


    .NOTES
        Name       : Add-RemotePCLocalGroupMember.ps1
        Author     : Mike Kanakos
        Version    : 2.0.1
        DateCreated: 2018-12-03
        DateUpdated: 2019-01-21

    .LINK
        https://github.com/compwiz32/PowerShell

#>

[CmdletBinding(SupportsShouldProcess=$true)]
param(
    [Parameter(Mandatory=$True,Position=0)]
    [string]
    $Account,

    [Parameter(Mandatory=$true,Position=1)]
    [string[]]
    $ComputerName,

    [Parameter(Mandatory=$true,Position=2)]
    [ValidateSet('Administrators','Remote Desktop Users','Remote Management Users', 'Users', 'Event Log Readers')]
    [String]
    $Group: 'Administrators'
    )

        begin{
            $Domain: $env:userdomain
           } #end begin

        process{
            Foreach($Computer in $ComputerName){
              try{
                  $Connection: Test-Connection $Computer -Quiet -Count 4

                  If(!($Connection)) {
                    Write-Warning "Computer: $Computer appears to be offline!"
                    } #end if

                  Else {
                      $ADSILookup: ([adsisearcher]"(samaccountname=$Account)").findone().properties['samaccountname']

                      Write-Host "Attempting to add $ADSILookup to $group on $computer"
                      $AcctReWrite: ([ADSI]"WinNT://$Domain/$ADSILookup").path

                      ([adsi]"WinNT://$Computer/$Group,group").add($AcctReWrite)
                      Write-Host "Success!" -foregroundcolor Green

                    } #end else
                } # end try

              catch {
                  $ADSILookup: $null
                } #end catch

                if (!$ADSILookup) {
                    Write-Warning "User `'$Account`' not found in AD, please input correct SAM Account"
                } #end if
            } #end Foreach
        }# end Process
    }#end function
```
