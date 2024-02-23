---
tags: ["Active Directory", "PowerShell"]
date: 2022-03-03
description: "Learn what the AdminCount value in Active Directory is used for, its purpose, and how to set it correctly."
draft: false
authors: [mike]
image: '/images/2022/AD-Admin-Count/AdminCount-Header.jpg'
slug: "activedirectory-admincount-attribute-usage"
title: "Learn to adjust the AdminCount attribute for protected accounts"
---

Many admins often misunderstand or find the concept of [AdminCount](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/appendix-b--privileged-accounts-and-groups-in-active-directory/?WT.mc_id=CDM-MVP-5004073) in Active Directory unfamiliar. If you're interested in learning about its function, purpose, and proper configuration, look at my [TechTarget article](https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts).

The article covers everything about AdminCount and what you should focus on. There is a hidden attribute called AdminCount in all Active Directory objects, and its default value is Null. When you set the AdminCount value to 1, accounts are treated differently, disabling inheritance and enforcing security through the AdminSDHolder object.

Dedicated processes monitor the AdminCount on accounts. It's common for people to come across AdminCount while making permissions changes on a protected account, only to have those changes reversed within an hour. If this sounds familiar, join me as I guide you on where to look and how to configure your environment for optimal security and convenience.

To access the article, click the earlier link or use the link below.\
[https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts](https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts)
