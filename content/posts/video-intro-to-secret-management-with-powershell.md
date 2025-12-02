---
date: 2022-01-03
description: "Learn the secure way to manage your passwords and secrets with Microsoft's Secret Management module."
draft: false
image: "/images/2022/Secret-Mgmt-Video-Intro/Lock-and-Chain.webp"
slug: "video-intro-to-secret-management"
tags: ['PowerShell']
title: "Video Intro to Secret Management with PowerShell"
authors: [mike]
featured: true
weight: 2
---

Storing and accessing secrets with PowerShell used to pose a challenge. Until 2021, there was no easy, convenient solution for creating and storing secrets. Microsoft's [Secret Management module](https://www.powershellgallery.com/packages/Microsoft.PowerShell.SecretManagement/), released in early 2021, has made life much easier. The module simplifies creating, storing, and recalling secrets by providing easy-to-use commands.

The Secret management module handles the hard task of managing secrets. You input secrets through the command line, and the module converts them into encrypted form before storing them in a vault that works with Secret Management.

While Microsoft offers its own vault, third party vaults are also supported by the Secret Management module. Well-known options for vaults include LastPass or 1Password, or you can choose to store the secrets on your device. For local storage, you would also install the [PowerShell Secret Store](https://www.powershellgallery.com/packages/Microsoft.PowerShell.SecretStore/) module, which then stores the secrets for you encrypted on your local hard drive.

I've been a dedicated fan of the secret management module since it was first introduced. I showcased a live demo in October for the [NY PowerShell User Group](https://www.meetup.com/NycPowershellMeetup/) that explains the basics. The demo summarizes installation and usage, as well as insights on utilizing the Secret Management module with AZ KeyVault. If you're looking to get started with secret management but lack experience, this demo is a great intro to the module and its capabilities.

<iframe width="200" height="113" src="https://www.youtube.com/embed/vEniQPooUSs?feature=oembed" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

If you would like to know more about secret management, visit the [Microsoft Docs page.](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.secretmanagement/?view=ps-modules)

[Photo credit: Georg Bommeli - Unsplash](https://unsplash.com/@calina?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
