---
title: CPU consumed on server by System Interrupts
tags: [Server Admin]
date: 2017-08-07 22:53:10 +0300
description: How I hunted down the fix for a CPU hog
draft: false
image: "/images/2018/04/heating-931930_640.jpg"
authors: [admin]
featured: false
----

I came across an interesting issue today on one of our production file/print servers running in a remote office. This server is an older server that has been virtualized and is still running Server 2008 - yeah I know it's old, but please don't ask why ...

The issue started first with an end user calling in complaining that many users in the remote office were experiencing slowness opening files from the server in question. At first, they bounced the ticket around thinking it was a network issue but eventually someone realized that the CPU was 100% consumed on the server.

I logged into the server to see what was consuming all the CPU; logins to the server were painfully slow taking more than 5 minutes to get to the desktop! After getting logged in, I was surprised to see the **"System Interrupts"** process consuming nearly all the CPU.

![Imgur](https://i.imgur.com/BX6AznN.png)

A collegue of mine immediately suggested a reboot to solve the issue but I was worried that a reboot would "fix" the issue today but not stop the issue from happening again in the near future. We had been having some ongoing issues with this particular server and I wasn't ready to simply go with the reboot option just yet, so I decided to dig in deeper...

A quick google search led me to this great article:
[How to Fix High CPU Usage Caused by System Interrupts](https://www.makeuseof.com/tag/fix-high-cpu-usage-caused-system-interrupts/)

The article quotes [Wikipedia](https://en.wikipedia.org/wiki/Interrupt) for a proper definition of *System Interrupts*:
> An interrupt alerts the processor to a high-priority condition requiring the interruption of the current code the processor is executing. The processor responds by suspending its current activities, saving its state, and executing a function called an interrupt handler to deal with the event.

The article goes on to suggest that using two tools would allow me to see if I had a faulty driver. The first tool they suggested was [DPC Latency Checker](https://www.thesycon.de/eng/latency_check.shtml) which I have used before. It's a great tool, but I didn't think I was going to get my answer from that tool, instead, I would have only confirmed if I had a latency issue. However, I sort of made that assumption already, so I decided to skip ahead and try the second tool: [LatencyMon](https://www.resplendence.com/downloads)

I grabbed the tool, did a quick install and waited for the results.

![Imgur](https://i.imgur.com/tpat7Xu.png)

The tool highlighted that I was having huge latency issues with one of the USB drivers. I was a little nervous though because usually virtual servers don't have much going on with USB and I wasn't so sure this information was correct.

However, the article from above stated that I could disable devices one by one and I may be able to find the offending driver or device.

I proceeded to slowly disable items and see what the results were.

- First I disabled the CD ROM, but that had no immediate effect
- Next, I disabled an extra mouse driver that was installed for some reason but it also had no effect.

I then decided to switch tactics a bit and see if A/V was getting in the way since I wasn't having an immediate success and I had seen the A/V service consuming a decent amount of CPU earlier. We use Symantec A/V and the client did show two scans that should have finished but was still running.

![Imgur](https://i.imgur.com/N9tIlIT.png)

At first glance, I figured this was definitely my issue. However, I am not an A/V admin and I couldn't disable the scans or the service on my own. I reached out to one of my security peeps and they could not get the client disabled either; the remote console couldn't talk successfully to the client scanner. After some debate, I hypothesized that I had one of two possible scenarios going on:

- Either the A/V client scans was causing the CPU to be consumed or something was consuming the CPU and thus causing the A/V to be unresponsive.

We wasted a bunch of time trying to get the A/V scans stopped but had no success. Since I was getting a bit frustrated, I decided to go back to the idea of a faulty driver. LatencyMon was reporting that my issue was a USB driver. I took a closer look at Device Manager again and dove deeper into the USB Bus Controllers section.
![Imgur](https://i.imgur.com/qNv3DGF.png)

On a complete whim, I decided to disable the **USB2 Enhanced Host Controller**. Why? I chose USB2 simply because it was different than the other controllers. As soon as I disabled the driver, the CPU came down from 100% to 2% instantly!!!

I did some follow up tests re-enabling and disabling the Host Controller. I could not get the high CPU condition to reproduce itself. I guess there was a hung process tied to that driver. A quick follow-up with a few end users and we were able to determine that the issue was resolved and there was no immediate impact to temporary disabling of the USB Host Controller.

Sweet Victory!!!
