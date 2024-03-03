---
title: "Managing Scheduled Jobs with PowerShell"
date: 2019-10-28
authors: [mike]
draft: false
image: "/images/2019/manage-Scheduled-Jobs/Task-Master.webp"
slug: "managing-scheduled-jobs-with-powershell"
description: "Learn how to manage PowerShell scheduled jobs. Learn the benefits of managing scheduled jobs from the command line."
tags: ["PowerShell"]
---

I'd like to walk you through how to manage PowerShell scheduled jobs. The concept of PowerShell jobs is not familiar territory for many PowerShell users. At first glance, the benefits of running any kind of job from the command line may not be obvious. This [article](https://4sysops.com/archives/managing-powershell-scheduled-jobs/), which I wrote for the [4sysops website](https://4sysops.com/archives/managing-powershell-scheduled-jobs/) peels back the covers on managing scheduled jobs and the benefits that come with them.

The write-up focuses on managing existing jobs and scheduled jobs. If you are unfamiliar with the basics of creating PowerShell jobs, then I suggest you read [PowerShell Background Job Basics](https://4sysops.com/archives/powershell-background-job-basics/) by Tim Warner. From there, you should also read my primer on scheduled jobs [Create PowerShell scheduled jobs](https://4sysops.com/archives/create-powershell-scheduled-jobs/) to get you up to speed on creating Scheduled Jobs.

### What Are Jobs

PowerShell jobs are background tasks. Jobs run in the background instead of interactively in your PowerShell session. The benefit here is that jobs run unattended and allow you to do other work. By running in the background, you can "submit a job" and go onto other things. This is especially useful for long running queries or information you need to gather but don't need the results right away.

Jobs do not send any results to the console. Instead, the results of the PowerShell code submitted is saved to a job queue for later retrieval. You could submit many jobs to be run and then retrieve the job results at a later time.

### What are Scheduled Jobs

Scheduled jobs are jobs that are scheduled to run at a pre-determined date and time, either once or on a repetitive schedule. Scheduled Jobs are the PowerShell equivalent of scheduled tasks but better. Scheduled Jobs are managed from the command line and scheduled tasks are managed from the GUI. The management portion of scheduled jobs relates to enabling, disabling, and removing scheduled jobs and getting the job results for completed jobs.

Head over to the [4sysops website](https://4sysops.com/archives/managing-powershell-scheduled-jobs/) for the rest of this [article](https://4sysops.com/archives/managing-powershell-scheduled-jobs/)!

Please let me know what you think in the comments below or at the 4sysops website.  Your feedback and comments are always welcome!
