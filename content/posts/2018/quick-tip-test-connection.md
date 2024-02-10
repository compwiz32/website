+++
categories = ["PowerShell", "Test-Connection"]
date = 2018-05-22T19:05:37Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2018/05/thought-2123970_640.jpg"
slug = "quick-tip-test-connection"
summary = "Check if a computer is online with the Test-Connection cmdlet"
tags = ["PowerShell", "Test-Connection"]
title = "Quick-Tip: Test-Connection"

+++


Today I am starting a new series of posts on my site called **Quick-Tips**. Each week, I will post one (or more!) quick tips that you may find useful for day-to-day activities or for smart ideas to incorporate into your code. These "tips" will not be as long as a regluar blog post. Instead, they're meant to be short and to the point. Think of them as little bite-size snippets or pointers that you can take with you right away.

So here goes... my very first quick-tip is using **Test-Connection** to verify a computer is online. Think of Test-Connection as a fancy way to say **PING**. 

Below is a snippet of code that you can add to your scripts to verify a computer is online. The output from this command will be a true or false. You can turn this code into a function and then simply call it via the pipeline.


```
$online= Test-Connection -ComputerName $computer -Count 1 -Buffersize 16 -Quiet
    if ($online -eq $true)
     {
        # Computer is online
        # Code goes here
     }
    else
     {
        # Computer is not reachable!
        Write-Host "Error: $computer not online" -Foreground white -BackgroundColor Red
     }


```

Look for one or more quicktips every week. Remember you can keep up on new content by following me on [Twitter](https://twitter.com/MikeKanakos), subscribing to my [RSS Feed](__GHOST_URL__/rss/) or by signing up for my weekly newsletter at the bottom of this page. 

Also, I invite you to leave comments below. I try to write useful articles and feedback always helps. Your comments are always welcomed!

