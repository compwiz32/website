---
title: "Getting Started with PowerShell: The First Five Commands You Need to Master"
date: 2019-12-04T13:03:28Z
authors: [mike]
draft: false
image: "/images/2019/First-Five-Commands/Study-time.webp"
slug: "the-first-five-commands-you-need-to-master"
description: "These five commands will help you master the advanced features of PowerShell. "
tags: [PowerShell, Jumpstarts]
---

**TABLE OF CONTENTS**

- [1. Get-Help](#1-get-help)
- [2. Update-Help](#2-update-help)
- [3. Get-Command](#3-get-command)
- [4. Get-Member](#4-get-member)
- [5: Get-ExecutionPolicy](#5-get-executionpolicy)
- [Bonus Commands to Master](#bonus-commands-to-master)
- [6 - Set-ExecutionPolicy](#6---set-executionpolicy)
- [7 - About\_ Files](#7---about_-files)
- [Conclusion](#conclusion)




Getting started with PowerShell is easy. In fact, it's easy enough for some people that they just dive in and start using it every day with little formal knowledge. At some point though, everyone needs a little help. The PowerShell console has a rich set of cmdlets and built-in help that can be useful for learning how to use the PowerShell language correctly.

There are five cmdlets that you can use to get knowledge and understanding of your working environment. These cmdlets will help you find the information you need when you get stuck or want to know more information about PowerShell syntax and commands. Each one has a unique function and serves a different purpose. These five cmdlets will help you master the advanced features of PowerShell. Let's dive in and see what each cmdlet does and why it's so important to know.

- Get-Command
- Get-ExecutionPolicy
- Get-Help
- Get-Member
- Update-Help

## 1. Get-Help

When you need help in PowerShell, `Get-Help` is where you should look first. This cmdlet will give you information on any cmdlet you want more information about. Using this cmdlet is simple; just type `Get-Help` and the name of a cmdlet and hit enter. Every cmdlet included in PowerShell includes a detailed help file that shows the full cmdlet syntax and detailed information on all the different parameters and examples of how to use each cmdlet. Let's look at the help file for the `Get-Item` cmdlet.

```PowerShell
PS C:\> get-help get-item

NAME
    Get-Item

SYNOPSIS
    Gets files and folders.


SYNTAX
    Get-Item [-Credential <PSCredential>] [-Exclude <String[]>] [-Filter <String>] [-Force] [-Include <String[]>] -LiteralPath <String[]> [-Stream
    <String[]>] [-UseTransaction] [<CommonParameters>]

    Get-Item [-Path] <String[]> [-Credential <PSCredential>] [-Exclude <String[]>] [-Filter <String>] [-Force] [-Include <String[]>] [-Stream
    <String[]>] [-UseTransaction] [<CommonParameters>]

    Get-Item [-Stream <string>] [<CommonParameters>]


DESCRIPTION
    The Get-Item cmdlet gets the item at the specified location. It does not get the contents of the item at the location unless you use a wildcard
    character (*) to request all the contents of the item.

    This cmdlet is used by Windows PowerShell providers to navigate through different types of data stores.
    In the file system, the Get-Item cmdlet gets files and folders.

    Note: This custom cmdlet help file explains how the Get-Item cmdlet works in a file system drive. For information about the Get-Item cmdlet in
    all drives, type "Get-Help Get-Item -Path $null" or see Get-Item at http://go.microsoft.com/fwlink/?LinkID=113319.


RELATED LINKS
    Online version: http://technet.microsoft.com/library/jj628239(v=wps.630).aspx
    Get-Item (generic); http://go.microsoft.com/fwlink/?LinkID=113319
    FileSystem Provider
    Add-Content
    Clear-Content
    Get-Content
    Get-ChildItem
    Get-Content
    Get-Item
    Remove-Item
    Set-Content
    Test-Path

REMARKS
    To see the examples, type: "get-help Get-Item -examples".
    For more information, type: "get-help Get-Item -detailed".
    For technical information, type: "get-help Get-Item -full".
    For online help, type: "get-help Get-Item -online"

```

I use the `Get-Help` cmdlet every day that I run PowerShell. I use many different PowerShell commands on any given day but I don't remember every parameter of every cmdlet. Instead, I rely on `Help` to show me how to use the cmdlets properly when my mind forgets some syntax.

The `Get-Help` cmdlet can produce different sets of help information depending on what parameters are specified. The most common output is what I showed in the example above: Type `Get-Help` and the name of a cmdlet. PowerShell will return a summary of the cmdlet information. Another way to run this cmdlet is to type `Get-Help CmdletName -detailed`. This will return a listing of all the parameters and descriptions for each, and examples of how to use each cmdlet.

My favorite way to run `Get-Help` (or just `Help`) is to only see the included examples: `Help Get-Item -examples`.

```PowerShell
PS C:\> help get-item -examples

NAME
    Get-Item

SYNOPSIS
    Gets files and folders.


    -------------------------- EXAMPLE 1 --------------------------

    C:\PS>Get-Item C:\Users\User01\Downloads\InternetFile.docx -Stream *

       FileName: C:\Users\User01\Downloads\InternetFile.docx

    Stream                   Length
    ------                   ------
    :$DATA                    45056
    Zone.Identifier              26

    Description

    -----------

    This command gets all stream data from a file that was downloaded from the Internet. The Zone.Identifier stream identifies a file that
    originated on the Internet. The $DATA stream is the default.

    The Stream parameter is introduced in Windows PowerShell 3.0.




    -------------------------- EXAMPLE 2 --------------------------

    C:\PS>Get-Item C:\ps-test\* -Stream Zone.Identifier -ErrorAction SilentlyContinue


       FileName: C:\ps-test\Copy-Script.ps1

    Stream                   Length
    ------                   ------
    Zone.Identifier              26


       FileName: C:\ps-test\Start-ActivityTracker.ps1

    Stream                   Length
    ------                   ------
    Zone.Identifier              26

    Description

    -----------

    This command gets Zone.Identifier stream data from all files in the C:\ps-test directory. The command uses the Stream parameter to specify the
    alternate stream and he ErrorAction parameter with a value of SilentlyContinue to suppress non-terminating errors that are generated when a file
    has no alternate data streams.

    The Stream parameter is introduced in Windows PowerShell 3.0.
```

If you already understand how a cmdlet works, then just seeing some examples of how it is used may be enough. My second favorite way to run `Help` is to get the help file in a separate standalone window. If I type `Help Get-Item -ShowWindow` then PowerShell will pop up the help into a new window that I can use and keep open as a reference while I run more commands.

## 2. Update-Help

I love the help cmdlet. As I mentioned earlier, I use it every single day and I encourage you to do the same. However, I don't love the help cmdlet when using a machine for the first time. That's because the included help in PowerShell contains only the absolute minimum information on a cmdlet. Nothing is more frustrating for me then running `Help` and getting back a skeleton help file. The solution for this is simple: run `Update-Help` and let PowerShell get the latest help files for all the installed modules on your machine from the internet. Updating help files takes about three minutes to complete and should be done at least once a quarter to keep your help files up to date.

![Update-Help](/images/2019/First-Five-Commands/Update-help.gif)

## 3. Get-Command

`Get-Help` is great if you know what command you need help for, but what if you don't know what command you want to run? What if you want to see the commands that MIGHT be what you are looking for? `Get-Command` helps you find commands that you may want to use but may not be sure of the cmdlet name.

```PowerShell
PS C:\> get-command get-item

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-Item                                           3.1.0.0    Microsoft.PowerShell.Management
```

`Get-Help` showed information about the command syntax. `Get-Command` shows data about the cmdlet itself. At first glance, this may not seem that useful. However, `Get-Command` is best used to search for a cmdlet or cmdlets that match a search pattern. That search pattern can full or partial cmdlet names.

PowerShell syntax is always written as *Verb-Noun*. The verbs are action words: Get, Set, New, Remove, Test, etc. Nouns are after the dash and describe what will be touched. `Get-Command` can search for verbs or nouns. For example, here I am searching for all cmdlets that contain the word *item*. The asterisks tell PowerShell to search for the word *item* anywhere in the cmdlet name.

```PowerShell
PS C:\> Get-Command *item*


CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Get-DAEntryPointTableItem                          1.0.0.0    DirectAccessClientComponents
Function        Get-PSWSUSInstallableItem                          2.3.1.6    PoshWSUS
Function        Get-TestDriveItem                                  3.4.0      Pester
Function        New-DAEntryPointTableItem                          1.0.0.0    DirectAccessClientComponents
Function        New-PSItem                                         6.2.3      ImportExcel
Function        Remove-DAEntryPointTableItem                       1.0.0.0    DirectAccessClientComponents
Function        Rename-DAEntryPointTableItem                       1.0.0.0    DirectAccessClientComponents
Function        Reset-DAEntryPointTableItem                        1.0.0.0    DirectAccessClientComponents
Function        Set-DAEntryPointTableItem                          1.0.0.0    DirectAccessClientComponents
Cmdlet          Clear-Item                                         3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Clear-ItemProperty                                 3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Copy-Item                                          3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Copy-ItemProperty                                  3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Get-ChildItem                                      3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Get-ControlPanelItem                               3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Get-Item                                           3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Get-ItemProperty                                   3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Get-ItemPropertyValue                              3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Invoke-Item                                        3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Move-Item                                          3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Move-ItemProperty                                  3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          New-Item                                           3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          New-ItemProperty                                   3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Remove-Item                                        3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Remove-ItemProperty                                3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Rename-Item                                        3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Rename-ItemProperty                                3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Set-Item                                           3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Set-ItemProperty                                   3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Show-ControlPanelItem                              3.1.0.0    Microsoft.PowerShell.Management
Application     UevTemplateConfigItemGenerator.exe                 10.0.17... C:\WINDOWS\system32\UevTemplateConfigItemGenerator.exe
```

Previously I searched for all matches. In this example, I am specifying the `-noun` parameter to tell PowerShell to only look at cmdlets that have the word *item* located after the dash. The first search was a wildcard search and grabbed many results. This search will only return exact matches on the word *item*.

```PowerShell
PS C:\> Get-Command  -Noun item

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Clear-Item                                         3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Copy-Item                                          3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Get-Item                                           3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Invoke-Item                                        3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Move-Item                                          3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          New-Item                                           3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Remove-Item                                        3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Rename-Item                                        3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Set-Item                                           3.1.0.0    Microsoft.PowerShell.Management
```

Searching using this cmdlet can vary greatly depending on the syntax used to search. Below are some variations of the same search term. Each one will produce different results. Try out all these examples and see how the results vary for each one.

```PowerShell
Get-Command -Noun item
Get-Command -Noun item*
Get-Command -Noun *item*
Get-Command *item*
```

## 4. Get-Member

One of the core concepts about PowerShell is the notion of "everything is an object". This means that everything returned in PowerShell has properties that contain more information and information about itself. If I was searching for Windows services that are installed, I would get a list of the services. In addition to the names, PowerShell also knows more information about each service. It knows if each service is running or stopped, and if the services are set to auto-start along with many more pieces of information.

This "extra" information is meta-data, or information about the information. `Get-Member` allows me to see what properties about the data are included. To illustrate this, I am using `get-item` to get a file on my computer:

```PowerShell

PS C:\> Get-item C:\Users\mkana\Downloads\Storm-Troopers.jpg


    Directory: C:\Users\mkana\Downloads


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       10/28/2019   8:09 AM          94437 Storm-Troopers.jpg

```

This reveals the basic information about this file, but there is more that I can see. PowerShell has only displayed what it considers the common information about this file. If I wanted to find out what other properties about this file are available to view, I can use the `Get-Member` cmdlet to show me what is available.

```PowerShell
PS C:\> Get-item C:\Users\mkana\Downloads\Storm-Troopers.jpg | Get-Member


   TypeName: System.IO.FileInfo

Name                      MemberType     Definition
----                      ----------     ----------
LinkType                  CodeProperty   System.String LinkType{get=GetLinkType;}
Mode                      CodeProperty   System.String Mode{get=Mode;}
Target                    CodeProperty   System.Collections.Generic.IEnumerable`1[[System.String, mscorlib, Version=4.0.0.0, Culture=neutral, Publ...
AppendText                Method         System.IO.StreamWriter AppendText()
CopyTo                    Method         System.IO.FileInfo CopyTo(string destFileName), System.IO.FileInfo CopyTo(string destFileName, bool overw...
Create                    Method         System.IO.FileStream Create()
CreateObjRef              Method         System.Runtime.Remoting.ObjRef CreateObjRef(type requestedType)
CreateText                Method         System.IO.StreamWriter CreateText()
Decrypt                   Method         void Decrypt()
Delete                    Method         void Delete()
Encrypt                   Method         void Encrypt()
Equals                    Method         bool Equals(System.Object obj)
GetAccessControl          Method         System.Security.AccessControl.FileSecurity GetAccessControl(), System.Security.AccessControl.FileSecurity...
GetHashCode               Method         int GetHashCode()
GetLifetimeService        Method         System.Object GetLifetimeService()
GetObjectData             Method         void GetObjectData(System.Runtime.Serialization.SerializationInfo info, System.Runtime.Serialization.Stre...
GetType                   Method         type GetType()
InitializeLifetimeService Method         System.Object InitializeLifetimeService()
MoveTo                    Method         void MoveTo(string destFileName)
Open                      Method         System.IO.FileStream Open(System.IO.FileMode mode), System.IO.FileStream Open(System.IO.FileMode mode, Sy...
OpenRead                  Method         System.IO.FileStream OpenRead()
OpenText                  Method         System.IO.StreamReader OpenText()
OpenWrite                 Method         System.IO.FileStream OpenWrite()
Refresh                   Method         void Refresh()
Replace                   Method         System.IO.FileInfo Replace(string destinationFileName, string destinationBackupFileName), System.IO.FileI...
SetAccessControl          Method         void SetAccessControl(System.Security.AccessControl.FileSecurity fileSecurity)
ToString                  Method         string ToString()
PSChildName               NoteProperty   string PSChildName=Storm-Troopers.jpg
PSDrive                   NoteProperty   PSDriveInfo PSDrive=C
PSIsContainer             NoteProperty   bool PSIsContainer=False
PSParentPath              NoteProperty   string PSParentPath=Microsoft.PowerShell.Core\FileSystem::C:\Users\mkana\Downloads
PSPath                    NoteProperty   string PSPath=Microsoft.PowerShell.Core\FileSystem::C:\Users\mkana\Downloads\Storm-Troopers.jpg
PSProvider                NoteProperty   ProviderInfo PSProvider=Microsoft.PowerShell.Core\FileSystem
Attributes                Property       System.IO.FileAttributes Attributes {get;set;}
CreationTime              Property       datetime CreationTime {get;set;}
CreationTimeUtc           Property       datetime CreationTimeUtc {get;set;}
Directory                 Property       System.IO.DirectoryInfo Directory {get;}
DirectoryName             Property       string DirectoryName {get;}
Exists                    Property       bool Exists {get;}
Extension                 Property       string Extension {get;}
FullName                  Property       string FullName {get;}
IsReadOnly                Property       bool IsReadOnly {get;set;}
LastAccessTime            Property       datetime LastAccessTime {get;set;}
LastAccessTimeUtc         Property       datetime LastAccessTimeUtc {get;set;}
LastWriteTime             Property       datetime LastWriteTime {get;set;}
LastWriteTimeUtc          Property       datetime LastWriteTimeUtc {get;set;}
Length                    Property       long Length {get;}
Name                      Property       string Name {get;}
BaseName                  ScriptProperty System.Object BaseName {get=if ($this.Extension.Length -gt 0){$this.Name.Remove($this.Name.Length - $this...
VersionInfo               ScriptProperty System.Object VersionInfo {get=[System.Diagnostics.FileVersionInfo]::GetVersionInfo($this.FullName);}
```

This cmdlet shows me that there much more information available. The properties are items that contain more information about our file named *StormTroopers.jpg*. For example, If I browse down to the list of properties, I can see that there is a property called `Name`, and another called `LastAccessTime`, and yet another called `IsReadOnly`. If I wanted to see values of those properties, I could display that information like this:

```PowerShell
PS C:\> Get-item C:\Users\mkana\Downloads\Storm-Troopers.jpg  | Select-Object Name, LastAccessTime, IsReadonly

Name               LastAccessTime        IsReadOnly
----               --------------        ----------
Storm-Troopers.jpg 10/28/2019 8:09:17 AM      False
```

There are also items referred to as methods. These are items that do some other function against the file *StormTroopers.jpg*. For example, there is a method named `GetAccessControl`. I can use this method to return the ACL's on this file. As you get more comfortable running PowerShell, you will use `Get-Member` regularly to find out additional "members" that are available to query.

```PowerShell

PS C:\> (Get-item C:\Users\mkana\Downloads\Storm-Troopers.jpg).GetAccessControl() | Format-List

Path   :
Owner  : MIKE-WIN10PC\mkana
Group  : MIKE-WIN10PC\mkana
Access : NT AUTHORITY\SYSTEM Allow  FullControl
         BUILTIN\Administrators Allow  FullControl
         MIKE-WIN10PC\mkana Allow  FullControl
Audit  :
Sddl   : O:S-1-5-21-2814075624-155296687-348844592-1001G:S-1-5-21-2814075624-155296687-348844592-1001D:(A;ID;FA;;;SY)(A;ID;FA;;;BA)(A;ID;FA;;;S-1-5-2
         1-2814075624-155296687-348844592-1001)
```

## 5: Get-ExecutionPolicy

PowerShell has policies built-in that control what can be run. These policies are known as *Execution Policies* and their purpose is to control how PowerShell loads configuration files and runs scripts. They are often thought of as security measures but that's not their intended purpose.

Execution polices are used to configure rules or conditions for when scripts can be run. Doing this allows a person to set an execution policy and prevent themselves from executing something unintentionally. They're not thought of as security policies because they are easily bypassed or changed. Think of them as preferences.

The cmdlet `Get-ExecutionPolicy` will show the current execution policy setting.

```PowerShell
PS C:\> Get-ExecutionPolicy
RemoteSigned
```

There are seven execution policies available: _Allsigned, Bypass, Default, Remote Signed, Restricted, Undefined and Unrestricted_. The default configuration is set to restricted, which prevents users from running scripts downloaded from the internet (or a different network then your own). These execution policies are easy to configure and can be applied to different scopes (user, computer, etc.)

## Bonus Commands to Master

We have covered our original five cmdlets. Here's some bonus information on two more cmdlets you also should master. These two cmdlets are extensions of earlier topics. One cmdlet works alongside another cmdlet and the other offers up more information on bigger topics related to PowerShell.

## 6 - Set-ExecutionPolicy

`Set-ExecutionPolicy` is the related command to `Get-ExecutionPolicy`. The former sets policy and the latter lists the current policy. This is one of the first things I check when I am working on a machine I am not familiar with. My default execution policy setting that I use is *Remote Signed*. Configuring a session is simple.

```PowerShell

PS C:\> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose you to the security risks
described in the About_Execution_Policies help topic at https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): A

```

## 7 - About_ Files

PowerShell includes information not just on syntax and help for individual cmdlets, but also for more generalized concepts. These generalized help files can be found by looking for help on About_*topic* and contain more information then an individual cmdlet help file. Below is a partial result from a search I performed on About*. The total number of About_ files will vary based on what modules you have installed. My machine has 160 *About_* files installed and they cover a large range of topics. Their purpose is to get you broad knowledge on a topic. Take a look at the myriad of topics you can read about.

```PowerShell

PS C:\> help about*

Name                              Category  Module                    Synopsis
----                              --------  ------                    --------
about_ActivityCommonParameters    HelpFile                            Describes the parameters that Windows PowerShell
about_Aliases                     HelpFile                            Describes how to use alternate names for cmdlets and commands in Windows
about_Arithmetic_Operators        HelpFile                            Describes the operators that perform arithmetic in Windows PowerShell.
about_Arrays                      HelpFile                            Describes arrays, which are data structures designed to store
about_Assignment_Operators        HelpFile                            Describes how to use operators to assign values to variables.
about_Automatic_Variables         HelpFile                            Describes variables that store state information for Windows PowerShell.
about_Break                       HelpFile                            Describes a statement you can use to immediately exit Foreach, For, While,
about_Checkpoint-Workflow         HelpFile                            Describes the Checkpoint-Workflow activity, which
about_CimSession                  HelpFile                            Describes a CimSession object and the difference between CIM sessions and
about_Classes                     HelpFile                            Describes how you can use classes to develop in Windows PowerShell
about_Command_Precedence          HelpFile                            Describes how Windows PowerShell determines which command to run.
about_Command_Syntax              HelpFile                            Describes the syntax diagrams that are used in Windows PowerShell.
about_Comment_Based_Help          HelpFile                            Describes how to write comment-based help topics for functions and scripts.
about_CommonParameters            HelpFile                            Describes the parameters that can be used with any cmdlet.
about_Comparison_Operators        HelpFile                            Describes the operators that compare values in Windows PowerShell.
about_Continue                    HelpFile                            Describes how the Continue statement immediately returns the program flow
```

The about_ files can be very lengthy and are an excellent resource for getting up to speed on a subject. The file for scopes can be accessed by typing `Help About_Scopes`

This will return a long and thorough walkthrough of all aspects of PowerShell scopes. Here is a snippet of that file.

```PowerShell

PS C:\> help about_scopes
TOPIC
    about_Scopes

SHORT DESCRIPTION
    Explains the concept of scope in Windows PowerShell and shows how to set
    and change the scope of elements.


LONG DESCRIPTION
    Windows PowerShell protects access to variables, aliases, functions, and
    Windows PowerShell drives (PSDrives) by limiting where they can be read and
    changed. By enforcing a few simple rules for scope, Windows PowerShell
    helps to ensure that you do not inadvertently change an item that should
    not be changed.


    The following are the basic rules of scope:

        - An item you include in a scope is visible in the scope in which it
          was created and in any child scope, unless you explicitly make it
          private. You can place variables, aliases, functions, or Windows
          PowerShell drives in one or more scopes.

        - An item that you created within a scope can be changed only in the
          scope in which it was created, unless you explicitly specify a
          different scope.


    If you create an item in a scope, and the item shares its name with an
    item in a different scope, the original item might be hidden under the
    new item. But, it is not overridden or changed.


  Windows PowerShell Scopes

    Scopes in Windows PowerShell have both names and numbers. The named
    scopes specify an absolute scope. The numbers are relative and reflect
    the relationship between scopes.


    Global:
        The scope that is in effect when Windows PowerShell
        starts. Variables and functions that are present when
        Windows PowerShell starts have been created in the
        global scope. This includes automatic variables and
        preference variables. This also includes the variables, aliases,
        and functions that are in your Windows PowerShell
        profiles.

    Local:
        The current scope. The local scope can be the global
```

## Conclusion

Mastering these seven cmdlets will help you be able to learn more about PowerShell and your current environment configuration anytime you need additional help. These cmdlets can be used many different ways to return just the right amount of information needed to help you solve your issue.

I recommend you use these cmdlets daily until you are comfortable with running them as needed with the correct parameters. There is plenty of information online to help you learn more about PowerShell, but the included files will give you all the knowledge you need to get started before moving onto more advanced topics.
