---
date: 2021-02-15
draft: false
image: /images/2021/One-PSProfile/OneProfile-Common-Location-Header.png
slug: "configure-one-powershell-profile-for-many-users"
description: "Learn how to use one PowerShell profile script for multiple users on a box. Configure your profile settings once and be done for all users! "
tags: [PowerShell]
title: "Configure one PowerShell profile for many users"
authors: [mike]
featured: true
weight: 5
---


The prevailing practice in most workplaces is to operate with the fewest privileges necessary. Admins and support staff in IT shops rely on multiple IDs to perform their tasks. In PowerShell, it's possible to assign a separate profile to each ID, complete with individual configurations, shortcuts, code snippets, and so on. This feature allows you to customize your work environment uniquely for each user ID, which can be incredibly useful.

But what if you don't feel like customizing every profile? Imagine you have a profile that is set up exactly the way you want it. What if you want all your IDs to use that same profile? Let me show you step by step through how I solved this problem with a solution that is both simple and elegant, making it easy to implement and maintain.

## Article Contents

- [Article Contents](#article-contents)
- [A primer on PowerShell profiles](#a-primer-on-powershell-profiles)
  - [The problem with PowerShell profiles](#the-problem-with-powershell-profiles)
  - [How I handle multiple profiles](#how-i-handle-multiple-profiles)
  - [Managing all users and hosts from one profile](#managing-all-users-and-hosts-from-one-profile)
- [Configuring your workstation to use one profile](#configuring-your-workstation-to-use-one-profile)

## A primer on PowerShell profiles

Before delving into my solution, it is essential to discuss the crucial components of PowerShell profiles and how they work. Profiles allow a user to customize their experience to their liking. Each user can personalize their PowerShell profiles with custom configurations. If you're not well-versed in PowerShell profiles, you can familiarize yourself by reading the built-in help in PowerShell: `help about_profiles`. I'm quoting the description exactly because I love it so much.

*You can create a PowerShell profile to customize your environment and to add session-specific elements to every PowerShell session that you start.*

*A PowerShell profile is a script that runs when PowerShell starts. You can use the profile as a logon script to customize the environment. Commands, aliases, functions, variables, snap-ins, modules, and PowerShell drives can be added. You can also add other session-specific elements to your profile so they are available in every session without having to import or re-create them.*

When a user starts a PowerShell session, it generates a corresponding PowerShell profile. This PowerShell script runs whenever you start PowerShell, and you can customize it to your liking. You can find your PowerShell profile location with the `$profile` variable. Naturally, your PowerShell profile is included in your Windows profile.

![Displaying Your Profile Path](/images/2021/One-PSProfile/Displaying-yourprofile-path.png)

However, attempting to access the mentioned location will reveal an empty folder. Strangely, the profile script file does not generate on its own. You need to create it manually. When I tried to open the file mentioned on one of my test VMs in PowerShell v5, here's what happened.

![Profile script is missing from user profile](/images/2021/One-PSProfile/Missing-Profile-Script.png)

You can create the script file in several ways.

- You can browse to the folder and create an empty .ps1 file.
-You can also use PowerShell to create a blank file for you.

```PowerShell
# Create a blank .ps1 profile script

if (!(Test-Path -Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force
}
```

Since profiles mentioned in this article are in multiple locations, I'll state this once to cover them all. PowerShell assigns the path to a file to the `$profile` variable. In order to follow the instructions in this article, you must create this file. You will also need to create profile scripts for the various other profiles I reference in this article.** I don't know why Microsoft doesn't auto create the file for you, but that is the default behavior. Refer to this [Microsoft doc](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.1) for a good explainer on all things related to PowerShell profiles.

Once the file is created, you can add code to the file. From that moment on, any time a PowerShell session starts, it will load the code in your profile script into memory. By adding information to your PowerShell profile, you can avoid the hassle of manually loading settings and preferences every time you use it.

When creating a function, it is necessary to dot-source the file for it to be loaded into memory prior to usage. Placing the code of your functions directly into your profile auto-loads them in every new PowerShell session. Another useful way to use a profile is by adding aliases to frequently used drives and folders. As a case in point, you can establish aliases for popular directories, sparing you the effort of typing out the entire path. There are many possibilities for customizing your profile.

```PowerShell
# Aliases to frequently used folders

$GitRepoParent: "c:\gitrepos"
$Mydocs: "C:\Users\mkana\Documents"
$Downloads: "C:\Users\mkana\Downloads"
$OutputDir: "C:\Scripts\Output"
$InputDir: "C:\Scripts\Input"
```

### The problem with PowerShell profiles

PowerShell offers a range of profile choices to handle diverse scenarios. When starting PowerShell, every user has their own profile script. But wait, there are more PowerShell profiles you need to consider! PowerShell profiles are not limited to individual users; there are also profiles specific to different versions of PowerShell. Profiles exist for both legacy Windows PowerShell (v5.x) and the newer PowerShell core (v6.x).

I use both Windows PowerShell (PowerShell v5.x) and PowerShell Core (PowerShell v6.x/v7.x) for my work. Despite relying heavily on PowerShell 7.x, there are still instances where I have to use v5 to handle incompatible legacy code. Although they are both PowerShell, they warrant separate profiles due to their differences.

```PowerShell
# Separate Profile Paths for Windows PowerShell and PowerShell Core

# PowerShell core profile path
$profile
C:\Users\mkana\Documents\PowerShell\Microsoft.PowerShell_profile.ps1

# Windows PowerShell profile path
$profile
C:\Users\mkana\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```

Earlier, I talked about the idea of utilizing the least number of privileges and having multiple IDs for operation. I have to use separate IDs for my various roles at work. I rely on my standard domain user ID for everyday tasks, but I switch to my admin credentials for more critical responsibilities. I occasionally use other unique credentials for specific tasks, but I primarily use two IDs.

Having two versions of PowerShell means managing two profiles and two IDs means I will need to manage two more profiles. In the end, I'm stuck dealing with and personalizing four PowerShell profiles. Having such a wide range of choices is fantastic. At some point, I realized that juggling four profiles was a hassle.

You can imagine having four profiles to maintain could be confusing, but in reality there are more profiles that I can use than what I have mentioned so far. PowerShell includes per user profiles, per application profiles, and shared profiles, which can be used by multiple users. The table below shows the location of the user and shared profiles.

Remember that this table reflects PowerShell Core only; the same options exist for Windows PowerShell. Using multiple IDs in PowerShell allows for customization but can be overwhelming without proper planning. This article will not cover every option available or how to configure those options. If you're familiar with how profiles work, the multitude of options can easily become overwhelming. The [Microsoft doc](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.1) on profiles and locations is one of the best resources available to dive in deeper on the myriad of options.

| **Description**            | **Path**                                                     |
| :------------------------- | ------------------------------------------------------------ |
| All Users, All Hosts       | $PSHOME\Profile.ps1                                          |
| All Users, Current Host    | $PSHOME\Microsoft.PowerShell_profile.ps1                     |
| Current User, All Hosts    | $Home\[My ]Documents\PowerShell\Profile.ps1                  |
| Current user, Current Host   | $Home\[My ]Documents\PowerShell\ Microsoft.PowerShell_profile.ps1 |

### How I handle multiple profiles

When I work with PowerShell, I like to have a certain set of information available to me. I decided it was worth the effort to not only customize my profile but also customize my PowerShell command prompt. I have [written extensively](https://www.commandline.ninja/customize-pscmdprompt/) about how I did this. That article is a thorough analysis of how I made a specialized PowerShell command prompt so that every time I work in PowerShell I have the information displayed the way I prefer. I encourage you to take some time and read that article; I wrote as a lead-in to this article.

One thing I did not cover in that article was how I use the same profile for all users and all versions of PowerShell. In short, I have ONE PROFILE for PS5 and PS7 regardless if I am logged in as my standard user account or my admin account. As I outlined earlier, many PowerShell options are great. In actuality, I would like the same information to be accessible across all profiles, excluding the variables for file paths that could be unique to a particular profile.

How did I manage to do it? I looked at all the profiles and locations available in PowerShell and tried to figure out which one would give me the most flexibility. Take a look at the table above, and you'll notice two variables to understand: users and hosts. "Users" means the people using a specific computer. "All users" refers to every user profile on my computer.

The term "hosts" in this context refers to programs or software applications that can use PowerShell. I talked about how PS5x and PS6x/7x have their own profiles. This also goes for PowerShell ISE and VSCode. Each can also have their own PowerShell profiles. It just gives me two more options and takes me farther from my original goal.

If you look at the profiles, you'll see the option 'ALL HOSTS, ALL USERS'. It uses one profile script for everyone on the computer and all the apps that use PowerShell. This is exactly what I was searching for: one profile for everything PowerShell! Well, almost what I was looking for. There's one caveat here... ALL HOSTS isn't exactly ALL HOSTS FOR ALL VERSIONS of PowerShell. Instead, `AllHosts` means all programs that use the PSv5 engine and there is also an `AllHosts` that uses the PowerShell 7.x engine. I made a graphical representation to help explain this concept:

![How the AllHosts,AllUsers profile works](/images/2021/One-PSProfile/AllHosts-AllUsers-2.jpg)

The image above shows that there is one `AllHosts, All Users` profile for PowerShell 5.x and another `AllHosts, All Users` profile for PowerShell v7. All applications and user ID's interacting with the PowerShell v5 engine use the same profile script, and all applications and user IDs interacting with PowerShell v7 use another profile script. For my setup, this isn't EXACTLY what I wanted, but it's close. Using this setup, I went from four profiles (PowerShell 5.x-MKANAKOS, PowerShell 5.x-MKANAKOS-ADMIN, PowerShell 7.x-MKANAKOS, and PowerShell 7.x-MKANAKOS-ADMIN) to just two profiles (v5 and v7). But I felt I could optimize this setup further. I continued to hunt for a better solution. Turns out, I actually knew how to fix this problem without even realizing it.

### Managing all users and hosts from one profile

The info I didn't know I needed was dot-sourcing. I spent some time looking at my profile configuration and one thing popped out to me: I could use my functions without a profile by dot-sourcing them and loading them into memory. Having mentioned dot-sourcing twice now, maybe there's someone reading this who is not sure what I mean by dot-sourcing.

[Dot-sourcing](https://mcpmag.com/articles/2017/02/02/exploring-dot-sourcing-in-powershell.aspx) is a concept in PowerShell that allows you to reference code defined in one script (Script A) from a second script (Script B). If I have my function (Script A) loaded in a directory called `C:\Scripts` , I can call that function from my second script (Script B) and load it by starting a line of code with a `.` and a space and then typing the location where the file is located. The line of code would look this this: `. c:\scripts\myPowerShellScript.ps1` . Using this concept, I could accomplish my goal: one profile script for all All Hosts, All users, and all versions of PowerShell. Let's look at how I implemented this. Actually, before we do that, let's talk about one caveat I skipped that can be important in some scenarios.

If you SHARE a machine (or a VM), then the solution I have been outlining needs some modifications. If you work on a server, then maybe you don't want the same profile for ALL users. In that case, there is another profile called `All Hosts, Current User` which would be a better fit. This would keep profiles between users separate, which is probably more appropriate in a shared environment.

The idea is to teach you about the tools and help you set up your environment to suit you. Now that we know about that one issue, let's move on to how I made one profile to handle it all.

## Configuring your workstation to use one profile

Dot-sourcing is a method you can use to load scripts from other locations. But maybe you're not clear how this helps with the multiple profile problem. Remember, I am using the `AllHosts, AllUsers` profile script location. That means we have two versions of our profile to consider:

```PowerShell
# Locations of AllHosts,AllUsers profile scripts

# PowerShell v5
$PROFILE.AllUsersAllHosts
C:\Windows\System32\WindowsPowerShell\v1.0\profile.ps1

# PowerShell v7
$profile.AllUsersAllHosts
C:\Program Files\PowerShell\7\profile.ps1
```

We can't change the locations of those files easily, and I wouldn't recommend it even if you could do it. But through the magic of dot-sourcing we can make those files REFERENCE another file in an alternate location. For example, if we saved a profile script to a location all users could access, then we could add a pointer or a reference (aka dot-sourcing) to that file and dot-sourcing would load the file.

So what does that look like? I created a profile script called `PSProfileConfig.ps1` and saved that file inside one of my local GitHub repos. My local Git Repos are saved to `C:\GitRepos`. The full path to my profile script is `C:\GitRepos\Powershell\Profile\PSProfileConfig.ps1` . By using GitHub, I gain the benefit of having a backup in case of a disaster, and it would be easy to set up again if I switched to a new computer.

If you don't use GitHub repos, then you could make a folder at the root of your C drive or maybe on your D drive if you have one. For example, I could have done the same thing by saving the file to a directory like `C:\Scripts` or something similar. A network location could work as well, but you want to make sure it is accessible at all times. You could even use a USB stick or portable SSD if you wanted to.

I have all my profile configs inside my `PSProfileConfig.ps1` script. All I need to do to access it is go to the `$PROFILE.AllUsersAllHosts` location for each of my PowerShell environments (PowerShell 5.x and PowerShell 7.x) and dot-source to the file I just mentioned. A graphic representation on my setup looks like this:

![One Profile configuration for all Hosts, All Users](/images/2021/One-PSProfile/OneProfile-Common-Location-Body.png)

My PowerShell 5.x and V7 profile scripts only have only one line contained within the files:

```PowerShell
# The only line contained in my AllHosts, Allusers profile scripts

. C:\GitRepos\Powershell\Profile\PSProfileConfig.ps1
```

When I fire up my PowerShell command prompt in either PowerShell v5 or PowerShell v7, they both point to the `PSProfileConfig.ps1` file and load those configs into memory! The result is that I have gone from having to manage possibly four or more profiles down to just one. My profile script sits in a location accessible by all user ID's and since I am using GitHub, it saves a copy of the script in the GitHub cloud for safe keeping. I have turned the chaos of managing multiple profile locations into a simple, easy to manage solution. My hope is that going through this explainer of how script profiles work and some of the choices has made it a little more understandable for you.
