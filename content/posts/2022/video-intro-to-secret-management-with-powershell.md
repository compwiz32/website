+++
categories = ["PowerShell", "SecretManagement"]
date = 2022-01-03T15:00:00Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1586661615438-349a276d098b?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDV8fGxvY2t8ZW58MHx8fHwxNjQwOTkzMDA5&ixlib=rb-1.2.1&q=80&w=2000"
slug = "video-intro-to-secret-management-with-powershell"
summary = "The Secret management module does the hard work of managing secrets. This demo covers the basics on installation and usage, along with some information on using Secret Management module with AZ KeyVault."
tags = ["PowerShell", "SecretManagement"]
title = "Video Intro to Secret Management with PowerShell"

+++


Storing and accessing secrets with PowerShell used to be a challenge. Prior to 2021, there was no simple way to create and store secrets. Microsoft released the [Secret Management module](https://www.powershellgallery.com/packages/Microsoft.PowerShell.SecretManagement/) in early 2021 and made life much easier. The module gives you an easy way to create, store and recall secrets with just a few simple commands.

The Secret management module does the hard work of managing secrets. You create secrets from the command line and then the module converts the data into a secure, encrypted secret that is stored in one of the many vaults that can work with Secret Management. Those other vaults can be popular vaults like LastPass or 1Password or you can store the secrets on your machine. For local storage, you would also install the [PowerShell Secret Store](https://www.powershellgallery.com/packages/Microsoft.PowerShell.SecretStore/) module, which then stores the secrets for you encrypted on your local hard drive.

I've been an avid fan of the secret management module since its early beginnings. I did a live demo for the [NY PowerShell User Group](https://www.meetup.com/NycPowershellMeetup/) in October that shows how to get started. This demo covers the basics on installation and usage, along with some information on using Secret Management module with AZ KeyVault. This is a great demo for those of you looking to get started with secret management, but have no previous experience.



<iframe width="200" height="113" src="https://www.youtube.com/embed/vEniQPooUSs?feature=oembed" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



I'll be doing a series of articles that dive deeper into Secret Management in the coming weeks. The articles will break down everything in the demo in greater detail and cover some topics not covered in demo. If you would like to know more about secret management, visit the [Microsoft Docs page.](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.secretmanagement/?view=ps-modules)Thanks for reading, I'd love to know what you think. Leave me a message in the comment section at the bottom of the page.

[Photo credit: Georg Bommeli - Unsplash](https://unsplash.com/@calina?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

