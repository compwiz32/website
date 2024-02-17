---
date: 2021-02-15T15:10:00 +0300
draft: false
image: "/images/2021/One-PSProfile/OneProfile-Common-Location-1.png"
slug: "configure-one-powershell-profile-for-many-users"
description: "Learn how to use one PowerShell profile script for multiple users on a box. Configure your profile settings once and be done for all users! "
tags: ["PowerShell", "PowerShell Profiles"]
title: "Configure one PowerShell profile for many users"
featured: true
---


Most workplaces today practice the idea of working with the least set of privileges as possible. In these IT shops, admins and support staff use multiple ID's throughout the day to get their job done. PowerShell allows for each ID to have its own profile, each with its own set of configurations, shortcuts, code snippets, etc. This can be incredibly useful for customizing your work environment differently for each user ID.

But what about if you don't want to customize each profile? What if you have a profile you configured EXACTLY as you like and you want all your ID's to use the same profile? Let me walk you through how I tackled this issue with a simple, elegant solution that is easy to implement and maintain.

### Article Contents

- [**How I handle multiple profiles**](#how-i-handle-multiple-profiles)
  - [Managing all users and hosts from one profile](#managing-all-users-and-hosts-from-one-profile)
- [**Configuring your workstation to use one profile**](#configuring-your-workstation-to-use-one-profile)

### A primer on PowerShell profiles

Before I can fully explain my solution, we need to cover some of the important parts of PowerShell profiles and how they work. Profiles allow a user to customize their experience to their liking. Each profile allows for custom configurations to be tailored to each user. If you're not super knowledgeable on PowerShell profiles, you can get up to speed by reading the built-in help inside of PowerShell: `help about_profiles` . I love the description they give so much that I am quoting it verbatim:

*You can create a PowerShell profile to customize your environment and to add session-specific elements to every PowerShell session that you start.*

*A PowerShell profile is a script that runs when PowerShell starts. You can use the profile as a logon script to customize the environment. You can add commands, aliases, functions, variables, snap-ins, modules, and PowerShell drives. You can also add other session-specific elements to your profile so they are available in every session without having to import or re-create them.*

Each user who launches a PowerShell session will have a PowerShell profile. It is a PowerShell script that runs every time you launch a PowerShell session, and you can add many things to the profile to make it work exactly as you wish. The automatic variable `$profile` reveals the path to your PowerShell profile location. As you might expect, your PowerShell profile is part of your Windows profile.

{{< figure src="/images/2021/02/Displaying-yourprofile-path.png" caption="<strong>Displaying Your Profile Path</strong>" >}}

But if you try to browse to the location listed above, you will find that there is no file in the folder listed above. Oddly enough, the profile script file isn't created automatically; you have to do it manually. Here's what happens when I tried to open the file stated on one of my test VM's in PowerShell v5:



{{< figure src="/images/2021/02/Missing-Profile-Script.png" caption="<strong>Profile script is missing from user profile</strong>" >}}

There's multiple ways to create the script file. You can browse to the folder an create and empty .ps1 file with the correct name and you are set. You can also use PowerShell to create a blank file for you very easily.

```
# Create a blank .ps1 profile script

if (!(Test-Path -Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force
}
```



Since I will be talking about profiles located in multiple locations throughout the article, I'll state this once here and it will cover all profiles mentioned in this article. PowerShell will populate the `$profile` variable with a path to a file. **You will need to create this file in order to do what is outlined in this article. You will also need to create profile scripts for the various other profiles I reference in this article.** I don't know why Microsoft doesn't auto create the file for you, but that is the default behavior. Refer to this [Microsoft doc](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.1) for a good explainer on all things related to PowerShell profiles.

Once the file is created, you can add code to the file. From that point forward, whenever a PowerShell session starts, it loads the code inside your profile script into memory. Adding information into your profile saves you the hassle of manually loading all of your various settings and preferences every time you use PowerShell.

You probably already know that if you create a function, you need to dot-source the file to load it into memory before you can use it. You could place the code for your functions directly into your profile, then your functions will be loaded automatically every time you launch a new PowerShell session. Another great use of a profile is to add aliases to drives and folders that you often use. For example, you can set aliases to common folders so you don't have to type the full path. The possibilities with profile customization are quite extensive.

```
# Aliases to frequently used folders

$GitRepoParent: "c:\gitrepos"
$Mydocs: "C:\Users\mkana\Documents"
$Downloads: "C:\Users\mkana\Downloads"
$OutputDir: "C:\Scripts\Output"
$InputDir: "C:\Scripts\Input"
```

### The problem with PowerShell profiles

PowerShell offers many ways to use profiles to cover many scenarios. Each user that starts PowerShell has their own profile script. But that's not it, there are more PowerShell profiles to consider! Profiles are not just based per user, there are also PowerShell profiles for the different versions of PowerShell. That means there are profiles available for legacy Windows PowerShell (PSv5.x) and the newer PowerShell core (PSv6.x / 7.x).

I work with both Windows PowerShell (i.e. PowerShell v5.x) AND PowerShell Core (PowerShell v6.x / v7.x). I use PSv7 over 90% of the time, but I still need to run v5 to handle the few bits of legacy code that are still incompatible with v6/v7 versions of PowerShell. Even though they are both PowerShell, they are different and that's why they each get a profile.



```
# Separate Profile Paths for Windows PowerShell and PowerShell Core

# PowerShell core profile path
$profile
C:\Users\mkana\Documents\PowerShell\Microsoft.PowerShell_profile.ps1

# Windows PowerShell profile path
$profile
C:\Users\mkana\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```



Earlier I mentioned the idea of operating with the least set of privileges and multiple ID's. I am required use to different ID's at work for the different roles I fulfill. My standard domain user ID is for everyday non-administrative activities, while I handle the more important tasks with my admin credentials. I also use other one-off credentials for very specific tasks, but I spend most of my time using two IDs.

Two versions of PowerShell means two profiles to manage, and two IDs also means two more profiles to manage. That means I have four potential PowerShell profiles to maintain and configure. Having that much choice is wonderful... until it isn't. Adding settings for profiles in multiple locations can be tedious and a burden.

You can imagine having four profiles to maintain could be confusing, but in reality there's more profiles that I can use then what I have mention so far. PowerShell has profiles that are per user, per application AND then there are shared profiles, which can be used for more than one user. The table below shows the location of the user and shared profiles.

Keep in mind that this table reflects PowerShell Core only; the same options exist for Windows PowerShell. I hope your seeing that using multiple ID's with PowerShell can provide many choices for customization but can become overwhelming if you don't have a plan. This article is not going to cover every option available or how to configure those options. I am merely pointing out that profiles have many options and that if you're aware of how they work, you could get yourself confused quickly. The [Microsoft doc](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.1) on profiles and locations is one of the best resources available to dive in deeper on the myriad of options.



| **Description**            | **Path**                                                     |
| :------------------------- | ------------------------------------------------------------ |
| All Users, All Hosts       | $PSHOME\Profile.ps1                                          |
| All Users, Current Host    | $PSHOME\Microsoft.PowerShell_profile.ps1                     |
| Current User, All Hosts    | $Home\[My ]Documents\PowerShell\Profile.ps1                  |
| Current user, Current Host   | $Home\[My ]Documents\PowerShell\ Microsoft.PowerShell_profile.ps1 |



## **How I handle multiple profiles**

When I work with PowerShell, I like to have a certain set of information available to me. I decided it was worth the effort to not only customize my profile but also customize my PowerShell command prompt. I have [written extensively](__GHOST_URL__/customize-pscmdprompt/) about how I did this. That article is a thorough analysis on how I made a specialized PowerShell command prompt so that every time I work in PowerShell I have the information displayed the way I prefer. I encourage you to take some time and read that article; I wrote as a lead-in to this article.

One thing I did not cover in that article was how I use the same profile for all users and all versions of PowerShell. In short, I have ONE PROFILE for PS5 and PS7 regardless if I am logged in as my standard user account or my admin account. As I outlined earlier, many PowerShell options are great. But in reality I want the same information to be available for all profiles except for variables that point to file paths that I commonly use.

So how did I do it? I started by looking at the various profiles and locations offered by PowerShell and figured out which one might give me the most flexibility. Looking at the table above, you can see there are two variables to understand: users and hosts. "Users" refers to the users of a particular computer. "All users" means all user profiles on my workstation.

"Hosts" refers to the programs or software applications that can use PowerShell. I discussed how PS5x and PS6x/7x have each have their own profiles. The same concept extends to PowerShell ISE and VSCode. Each can also have their own PowerShell profiles. It just adds two more choices and takes me farther away from what I was looking to accomplish.

Looking through the profiles available, you'll see one choice is `ALL HOSTS, ALL USERS`. It uses one profile script for all users of the computer and all applications that interact with PowerShell. This is exactly what I was looking for: one profile for all things PowerShell! Well, almost what I was looking for. There's one caveat here... ALL HOSTS isn't exactly ALL HOSTS FOR ALL VERSIONS of PowerShell. Instead, `AllHosts` means all programs that use the PSv5 engine and as you can imagine there is also an `AllHosts` that uses the PSv7 engine. I made a graphical representation to help explain this concept:



{{< figure src="/images/2021/02/AllHosts-AllUsers-2.jpg" caption="<strong>How the AllHosts,AllUsers profile works</strong>" >}}



The image above shows that there is one `AllHosts, All Users` profile for PSv5 and another `AllHosts, All Users` profile for PowerShell v7. All applications and user ID's interacting with the PowerShell v5 engine use the same profile script, and all applications and user IDs interacting with PowerShell v7 use another profile script. For my setup, this isn't EXACTLY what I wanted, but it's close. Using this setup, I went from four profiles (PSv5-MKANAKOS, PSV5-MKANAKOS-ADMIN, PSv7-MKANAKOS, and PSV7-MKANAKOS-ADMIN) to just two profiles (PSv5 and PSv7). But I felt I could optimize this setup further. I continued to hunt for a better solution. What I didn't know at the time was that I already knew how to solve this issue, I just didn't realize it.

### Managing all users and hosts from one profile

The piece of information that I didn't realize that would be useful to solve my dilemma was dot-sourcing. I spent some time looking at my profile configuration and one thing popped out to me: I could use my functions without a profile by dot-sourcing them and loading them into memory. Having mentioned dot-sourcing twice now, maybe there's someone reading this who is not sure what I mean by dot-sourcing.

[Dot-sourcing](https://mcpmag.com/articles/2017/02/02/exploring-dot-sourcing-in-powershell.aspx) is a concept in PowerShell that allows you to reference code defined in one script (Script A) from a second script (Script B). If I have my function (Script A) loaded in a directory called `C:\Scripts` , I can call that function from my second script (Script B) and load it by starting a line of code with a `.` and a space and then typing the location where the file is located. The line of code would look this this: `. c:\scripts\myPowerShellScript.ps1` . Using this concept I could accomplish my goal: one profile script for all All Hosts, All users, and all versions of PowerShell. Let's look at how I implemented this. Actually, before we do that, let's talk about one caveat: I skipped that can be important in some scenarios.

If you SHARE a machine (or a VM), then the solution I have been outlining needs some modifications. If you work on a server, then maybe you don't want the same profile for ALL users. In that case, there is another profile called `All Hosts, Current User` which would be a better fit. This would keep profiles between users separate, which is probably more appropriate in a shared environment.

The goal here is to educate and let you understand the tools available, and then you configure your environment to fit your needs. Now that we are aware of that one caveat, let's march forward to how I implemented one singular profile to manage all scenarios.

## **Configuring your workstation to use one profile**

Dot-sourcing is a method you can use to load scripts from other locations. But maybe you're not clear how this helps with the multiple profile problem. Remember, I am using the `AllHosts, AllUsers` profile script location. That means we have two versions of our profile to consider:



```
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

I have all my profile configs inside my `PSProfileConfig.ps1` script. All I need to do to access it is go to the `$PROFILE.AllUsersAllHosts` location for each of my PowerShell environments (PSv5 and PSv7) and dot-source to the file I just mentioned. A graphic representation on my setup looks like this:



{{< figure src="/images/2021/02/OneProfile-Common-Location.png" >}}



My PSv5 and V7 profile scripts only have only one line contained within the files:

```
# The only line contained in my AllHosts, Allusers profile scripts

. C:\GitRepos\Powershell\Profile\PSProfileConfig.ps1
```

When I fire up my PowerShell command prompt in either PowerShell v5 or PowerShell v7, they both point to the `PSProfileConfig.ps1` file and load those configs into memory! The result is that I have gone from having to manage possibly four or more profiles down to just one. My profile script sits in a location accessible by all user ID's and since I am using GitHub, it saves a copy of the script in the GitHub cloud for safe keeping. I have turned the chaos of managing multiple profile locations into a simple, easy to manage solution.My hope is that going through this explainer of how script profiles work and some of the choices has made it a little more understandable for you. Thanks for reading, I'd love to know what you think. Leave me a message in the comment section at the bottom of the page.

