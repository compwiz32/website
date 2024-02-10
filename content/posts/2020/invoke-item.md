+++
categories = ["PowerShell", "Invoke-Item"]
date = 2020-10-12T11:35:00Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2020/10/magic-unsplash-squoosh.jpg"
slug = "invoke-item"
summary = "Invoke-Item is a cmdlet that is not used frequently in PowerShell. Learn how it can save time and speed up tasks. "
tags = ["PowerShell", "Invoke-Item"]
title = "Getting familiar with Invoke-Item in PowerShell"

+++


A few weeks ago I was browsing the [tips and tricks page](https://jdhitsolutions.com/blog/powershell-tips-tricks-and-advice/) on my good friend, Jeff Hicks’ website and stumbled across a cmdlet I was not familiar with: `Invoke-Item`. His tips page included a one liner that stated:

> Use Invoke-Item or its alias ii, to open a folder in Windows Explorer: `ii c:\windows`.
> 

I am not sure why that one line stuck out for me, but it got me thinking about Invoke-Item. The cmdlet has some obvious use cases and but there’s a few that aren’t apparent at first. Let’s dive in and review what we can do with this cmdlet.

### Opening Files and Folders

Using `Invoke-Item` to open files and folders is probably the use case that everyone recognizes immediately. Using `ii` to launch folders and files is quick and easy. I am not showing it here, but you can launch ANYTHING using Invoke-item: pictures, log files, word docs, excel files, CSV’s, executables, etc.

```PowerShell
#opening a file using invoke-item 
Invoke-Item .\Twitter.jpg

# same use case using the ii alias
ii .\twitter.jpg

```

To be clear though; they built this functionality into windows WITHOUT using the Invoke-Item cmdlet. This line of code opens the jpg, just like `Invoke-Item`

```
.\Twitter.jpg

```

### Opening Multiple Files of the Same Type

`Invoke-item` becomes more useful when we look at specific use cases. If I want to open many of the same file type at once, then the cmdlet shows it’s usefulness. 

```PowerShell
ii *.log

```

![Opening multiple files at once](__GHOST_URL__/content/images/2020/10/Qe5WJCNcU3.png)

### Opening Multiple Files using the -include parameter

`Invoke-Item` can filter files on the fly; opening multiple files or folders at once that either match or don’t match a pattern. This functionality is more of an edge case, but let me show how it works. 

The cmdlet has an `-include` parameter that you can use to in combination with the `-path` parameter. The result allows you to specify a filter by file type and then filter for only files that match a pattern you specify. 

I have designed a very simple demo to show how it could work. For this demo, I have a created a bunch of dummy log files in c:\temp. The log files highlighted have a naming pattern of *ApplicationName-DayOfMonth-TimeOfDay* . For example, one log is named “IISlog-2020-10-10_14_22.log”. It’s an IIS log from Oct 10th and the time created is 14:22 or 2:22 PM. Again, this is a file I created for demonstration; it has no data inside it. 

![Log Files in a folder](__GHOST_URL__/content/images/2020/10/H23AKW3rWo.png)

Looking at the log files, you’ll notice there are there three sets of logs: IIS, System and Security logs. For each of those logs, there are logs for multiple days. This what a typical log directory could look like. 

I could launch a subset of these files using Using `Invoke-Item` with the `-include` parameter. We start with the `-path` parameter, which can accept a wildcard. The path parameter should contain the file type that you want to open. Then you can use the `-include` parameter to filter the list down to a specific pattern. Here’s an example that would open all the logs from 2020-10-10.

```PowerShell
Invoke-Item -path C:\temp\*.log -Include “*2020-10-10*”

```

![Results of a filtered Invoke-Item lookup](__GHOST_URL__/content/images/2020/10/YIguGHqWLe.png)

### Opening Multiple Files using the -exclude parameter

We can also accomplish the opposite behavior using the `-exclude` parameter. I have included a list of the logs file in my temp directory to help explain this parameter. 

```PowerShell
PS C:\temp> dir *.log

    Directory: C:\temp

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          10/10/2020  5:45 PM              0 IISlog-2020-10-08_17_12.log
-a---          10/10/2020  5:42 PM              0 IISlog-2020-10-09_14_18.log
-a---          10/10/2020  5:42 PM              0 IISlog-2020-10-10_14_22.log
-a---          10/10/2020  5:45 PM              0 securitylog-2020-10-08_11_11.log
-a---          10/10/2020  5:42 PM              0 securitylog-2020-10-09_08_22.log
-a---          10/10/2020  5:41 PM              0 securitylog-2020-10-10_15_41.log
-a---          10/10/2020  5:45 PM              0 Systemlog-2020-10-08_10_46.log
-a---          10/10/2020  5:42 PM              0 Systemlog-2020-10-09_11_12.log
-a---          10/10/2020  5:41 PM              0 Systemlog-2020-10-10_12_20.log
-a---          10/10/2020 10:54 AM             12 test.log
-a---           3/12/2020  1:36 AM             54 test2.log
-a---           3/12/2020 12:41 AM             30 test3.log

```

I want to open all the log files EXCEPT the ones named TEST. I can use `Invoke-Item` with the `-exclude` parameter. Here’s what that code would look like. 

```
Invoke-Item -path C:\temp\*.log -exclude “Test*”

```

The result is that nine files match the pattern. It then opens the files all at once.

![Files that don’t match the exclude pattern](__GHOST_URL__/content/images/2020/10/9SZow2zgnG.png)

### Opening Aliases

We talked earlier using `Invoke-item` to open files and folders, but remember I pointed out that Windows does this without using the cmdlet by default. That built-in capability works because of default apps assigned to file extensions. One case that only works with `Invoke-Item` is launching folders saved inside variables.

Let’s look at a variable and see what we can do. We’ll use the built-in variable `$PWD` for demonstration. If you’re not familiar with `$PWD`, it is an alias to `Get-Location` which returns the current directory you are in. `$PWD` is a command rooted in Unix/Linux that is recognized by PowerShell; it’s an abbreviation for “Print Working Directory” and is handy for returning the current directory.

```PowerShell
$pwd

Path
----
C:\temp

```

I can then open that directory using `Invoke-Item`

```PowerShell
ii $pwd

```

![Opening a file path from an alias](__GHOST_URL__/content/images/2020/10/xoTkxhMt9z.png)

This can be a great time saver if you have a handful of variables you create in your PowerShell profile for commonly used folders on your machine or another machine. 

### Launching Files Immediately after Creation

I came across this next use case from a [Reddit conversation](https://www.reddit.com/r/PowerShell/comments/9pfhm3/invokeitem_vs_startprocess/). One of the great things about PowerShell is the concept of the pipeline. The pipeline allows one cmdlet to “pass” the results to another cmdlet. Using the pipeline allows us to chain multiple cmdlets together into one or more lines of code. 

One of the most common use cases of the pipeline is to produce a result and pass the result to a file. In my test environment, I have a temp directory with a handful of log files along with a mix of other files.

```PowerShell
dir C:\temp\

    Directory: C:\temp

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           9/14/2020  5:00 PM         337271 Docs_Microsoft.jpg
-a---            8/5/2020  8:08 PM              0 Encoding Time.csv
-a---           9/30/2020  3:49 PM         104025 PI.jpg
-a---           9/30/2020  3:42 PM         754329 Pumpkin-Bread.png
-a---          10/10/2020 10:54 AM             12 test.log
-a---           3/12/2020  1:36 AM             54 test2.log
-a---           3/12/2020 12:41 AM             30 test3.log
-a---           4/19/2020  7:00 AM             24 test-log
-a---            9/9/2020  1:37 PM          62264 using Powershell.docx

```

I might want to produce a list of the logs file and export that list to a text file. 

```PowerShell
Get-ChildItem *.log | Out-file C:\scripts\Output\loglist.txt

```

The result is I have a text file in my `c:\scripts\output` directory. However, if I run the code above and nothing else, my next step is to either browse to the file in Windows or use the cmd line to find file and then open it. 

Another option could have been to add to the end of the code and have invoke-item open the file for me all in one step by using a semi-colon. The semi-colon allows the ability to run multiple commands on the same line of code.

```PowerShell
Get-ChildItem *.log | Out-file C:\scripts\Output\loglist.txt ; ii c:\scripts\output\loglist.txt

```

We can simplify the code through the use of a variable:

```PowerShell
$outputdir = “c:\scripts\output\”

Get-ChildItem *.log | Out-file $outputdir\loglist.txt ; ii $outputdir\loglist.txt

```

### Conclusion

Invoke-item gives us various ways to open files, launch programs, and act upon variables. Play with the command and find new uses for your code! As I always say, your feedback is welcome and I encourage you to leave comments below. If you have another use case for Invoke-Item, please leave a note so others can learn from your knowledge. 

-----
Image Credit:
<span>Photo by <a href="https://unsplash.com/@art_maltsev?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Artem Maltsev</a> on <a href="https://unsplash.com/s/photos/magic?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>



