+++
categories = ["PowerShell", "Scripts"]
date = 2019-07-24T14:35:55Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2019/07/acrylic-acrylic-paint-art-1174952-optimized.jpg"
slug = "easily-display-powershell-console-colors"
summary = "A simple PowerShell function to quickly display all available colors on screen elegantly"
tags = ["PowerShell", "Scripts"]
title = "Easily display PowerShell console colors"

+++


I like to customize the color of status messages that are returned to the screen; especially important variables like usernames and computernames. I think it helps the user of my scripts recognize important information. For example, take a look at the screencap below. Notice how the text returned on line 2 is all one color.

{{< figure src="__GHOST_URL__/content/images/2019/07/Screencap1-2.png" caption="line 2 output - no color" >}}

<br />
<br />

Here's the output from another cmdlet with some color added to the returned text. it makes the important information stand out prominently.

{{< figure src="__GHOST_URL__/content/images/2019/07/Screencap2-3.png" caption="line 2 - color added to important information" >}}

<br />
<br />

The `Write-Host` cmdlet contains two parameters that allows you to change the color of the returned text. They're named `-foregroundcolor` and `-backgroundcolor` and there are 16 colors to choose from in PowerShell 5.x.

**But what do they look like on screen?**

When writing code, you can easily use one of those parameters and enter any of the 16 colors. However, when you select CYAN for example, you may not know exactly what CYAN looks like. Another example would be when selecting RED: There is RED and there is DARKRED. I am sure everyone has a rough idea what RED and DARKRED look like. But if you wanted to see a sample of the color before adding it to your code, that's a bit of a pain because outputting a sample of the chosen color is not something everyone knows how to do fast. 

One day I was writing some code and thought, "It would be handy to have a quick way to display the colors on screen so I can see how they look as output." A quick google search pointed me to a [TechNet article](https://blogs.technet.microsoft.com/gary/2013/11/20/sample-all-powershell-console-colors/) on a one-liner that displays the colors. That's great but let's be honest, no one will ever remember `[enum]::GetValues([System.ConsoleColor]) | Foreach-Object {Write-Host $_ -ForegroundColor $_}  ` when it's time to choose colors. These kinds of situations are exactly why you make tools.

I wanted a way to easily see a sample of the colors without having to remember a string of code. I needed something that I could run quickly and easily, so I decided to make a simple function called `Get-ConsoleColors` that I could run at anytime to display the available options. I started with the original one liner I mentioned earlier and added some structure and formatting. 

The function outputs all the colors for both foregrond text and background spaces, which makes it easy to pick a color that looks appealing to you and fits your needs. Now, whenever I want to see how all the colors look, it's just one simple cmdlet away from being presented elegantly on the screen.

{{< figure src="__GHOST_URL__/content/images/2019/07/Screencap3.png" >}}

<br />
<br />
<br />

The code for this cmdlet is pretty simple. I use loops to display what the text looks like for each foreground color (i.e. screen text) and then for each background color. The result is a simple display of all 16 colors on screen. This makes for a great way to call up the colors quickly for reference. The end result is that I now have a tool that I can use to easily get the information I need rather than trying to memorize some complex one liner of code. 

I hope you find this utility useful with everyday code writing. You can find the latest verison this script and all my others at my [GitHub Repo](https://github.com/compwiz32/PowerShell)

```
Function Get-ConsoleColors {

    <#
    .SYNOPSIS
        Displays all color options on the screen at one time

    .DESCRIPTION
        Displays all color options on the screen at one time

    .EXAMPLE
        Get-ConsoleColors

    .NOTES
        Name       : Get-ConsoleColors.ps1
        Author     : Mike Kanakos
        Version    : 1.0.3
        DateCreated: 2019-07-23
        DateUpdated: 2019-07-23

        LASTEDIT:
        - Add loops for foreground and background colors
        - output foreground and background colors for easy selection
        
    .LINK
        https://github.com/compwiz32/PowerShell


#>

[CmdletBinding()]
    Param()
    
    $List = [enum]::GetValues([System.ConsoleColor]) 
    
    ForEach ($Color in $List){
        Write-Host "      $Color" -ForegroundColor $Color -NonewLine
        Write-Host "" 
        
    } #end foreground color ForEach loop

    ForEach ($Color in $List){
        Write-Host "                   " -backgroundColor $Color -noNewLine
        Write-Host "   $Color"
                
    } #end background color ForEach loop
    
} #end function
```



