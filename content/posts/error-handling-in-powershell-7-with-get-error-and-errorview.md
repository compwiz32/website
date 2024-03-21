---
title: "Error Handling in PowerShell 7 with Get-Error and $Errorview"
date: 2020-03-10
authors: [mike]
description: "Learn about the new improvements to Error Handling and Error Messages in PowerShell 7"
draft: false
image: "/images/2020/Error-Handling-PS7/errorhandling-header.webp"
slug: "error-handling-in-powershell-7-with-get-error-and-errorview"
tags: [PowerShell]
---

**TABLE OF CONTENTS**

- [Error Views](#error-views)
- [Get-Error](#get-error)
- [PowerShell 7 Blog Week](#powershell-7-blog-week)
- [PowerShell 7 Blog Week Authors](#powershell-7-blog-week-authors)

By now, I am sure you've heard the news: **PowerShell 7 is no longer a beta product and the latest release has reached GA (generally available) status**. In short, this means that Microsoft now considers PowerShell7 production ready! From this point forward you should begin to use PowerShell7 as your daily driver for all things PowerShell. Today I'd like to show you one aspect of PowerShell that is greatly improved: Error Handling and Error Messages.

<br>

### Error Views

Before I can jump into the new features; let's review what's was already available in PowerShell prior to the v7 release. Let's run some code and generate an error.

```PowerShell
[int]$number: Read-Host "What is the latest version of PowerShell ?"
```

In the above code, I am prompting the user to answer a question and saving the answer to a variable called `$number`. I am expecting the answer to be a number so I cast the variable as an INT, meaning that the variable will only accept numbers as being valid. Anything else will return an error.

When I run the code I get prompted to answer the question. For our purposes, I will input a letter instead of a number causing an error to be thrown. The error returned contains a bunch of information, but notice it has a standard format you have probably seen many times prior.

The layout of this message is called the "normal view". This layout should look pretty familiar; it's the default error view in PowerShell 5 and earlier. However, there is another way this error could have been displayed.

![PowerShell 5 Error Message](/images/2020/Error-Handling-PS7/PS5-Error-Message.png)

<br>

Let's re-run the code again, but this time set a view called "CategoryView" for a variable called `$errorview` and see what happens.

```PowerShell
$errorview: "categoryview"
[int]$number: Read-Host "what is the latest version of PowerShell ?"
```

![PowerShell 5 Error Message using Category View](/images/2020/Error-Handling-PS7/PS5-Error-CategoryView.png)

<br>

In our example above, running the same code with this other error view produces a different error message. This view is called the Category View and it can be set manually to override the normal view which we saw in the previous example.

Here's a better example of the category view showing an error that describes the problem.

```PowerShell
$errorview: "categoryview"
test-connection -ComputerName "notrealcomputer" -count 1
```

![Test-Connection error using Category View](/images/2020/Error-Handling-PS7/PS5-Errorview-TestConnection.png)

<br>

So what does this have to do with PowerShell 7?

PowerShell 7 contains the same two views but now includes a third view called concise view. The same code in PowerShell 7 produces a short but useful error message.

![PowerShell 7 error message](/images/2020/Error-Handling-PS7/PS7-ConciseView-ErrorMessage.png)

<br>

One of the first things you will notice using PowerShell 7 is that error messages are much improved and more succinct. This error message for `Test-Connection` is short but adequately describes the issue.

![PowerShell7 Test-Connection Error](/images/2020/Error-Handling-PS7/PS7-TestConnection-ErrorMessage.png)

<br>

In this code sample, I have purposely misspelled the `Select-Object` cmdlet. Again we see that the message clearly states the problem.

![PowerShell 7 Select Object typo](/images/2020/Error-Handling-PS7/PS7-SelectObject-Typo.png)

<br>

"Conciseview" is the default view in PowerShell. Nothing needs to be configured to get these shorter, clearer messages. All errors produced will be easier to understand and point more directly to what caused the error in your code. If you prefer one of the other views, you can switch at anytime by updating the value of `$Errorview` and then PowerShell will produce errors similar to the message you have seen in previous versions.

<br>

### Get-Error

PowerShell 7 also includes a new cmdlet named `Get-Error`. The cmdlet retrieves and displays extended error information for recent error messages from the current PowerShell session. Extended error information is ALL the error information that is recorded when an error is thrown. The views I discussed earlier are only presenting a subset of the error data available.

In the screencap below, I generate an error by calling an invalid CIM namespace. Afterwards, I then run Get-Error to see the extended information available for that error. You'll notice that there is quite a bit of information available in addition to the original error message that was displayed at the first error.

![Get-Error Extended Information](/images/2020/Error-Handling-PS7/Get-Error-ExtendedInformation.png)

<br>

`Get-error` not only displays extended error information on the most recent error message, it also can display all the errors that occurred in a session.

In my current sessionI have generated quite a few errors that are still in memory. I can use `Get-Error` to get the entire list and then retrieve the information as needed. For this example, I am going to use `Out-Gridview` to illustrate how many errors I have in memory.

```PowerShell
Get-Error -newest 20 | Out-Gridview
```

![Get-Error list of errors in current session](/images/2020/Error-Handling-PS7/Get-Error-OutGridview.png)

<br>

All the information from past errors can be recalled and reviewed as long as the session is not cleared. That information can be useful when trying to pinpoint errors.

These new views and cmdlets provide more useful data when errors occur and more ways to go back and look at past errors. We also can dive in deep and look at the extended data for an extra deep view of what happened when an error occurred.
These new tools are just a scratch of the surface of the new features available in PowerShell 7!

<br>

### PowerShell 7 Blog Week

This post is part of the **#PS7Now PowerShell Blog Week** celebrating the general availability launch of PowerShell 7.

All week long look for the #PS7Now hashtag on Twitter for more great content or checkout the table below for direct links to authors who are participating in the #PS7Now PowerShell Blog Week.

<br>

### PowerShell 7 Blog Week Authors

These authors will be publishing content all week highlighting the new features available in PowerShell 7. Make sure you stop by their blogs or follow them on social media to keep up on all the latest PowerShell 7 posts!

| Author | Twitter | Blog |
| :----- | :----- | :----- |
| Josh King | @WindosNZ | https://toastit.dev |
| Josh Duffney | @joshduffney | http://duffney.io |
| Adam Bertram | @adbertram | https://adamtheautomator.com |
| Jonathan Medd | @jonathanmedd | https://www.jonathanmedd.net |
| Thomas Lee | @doctordns | https://tfl09.blogspot.com |
| Prateek Singh | @singhprateik | https://ridicurious.com |
| Dave Carroll | @thedavecarroll | https://powershell.anovelidea.org |
| Dan Franciscus | @danfranciscus | https://winsysblog.com |
| Jeff Hicks | @jeffhicks | https://jdhitsolutions.com |
| Tommy Maynard | @thetommymaynard | https://tommymaynard.com |
