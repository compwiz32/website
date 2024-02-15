---
tags: ["Active Directory", "PowerShell"]
date: 2022-03-03 14:30 +0300
description: "Learn what the AdminCount value in Active Directory is used for, its purpose, and how to set it correctly."
draft: false
image: '/images/2022/PS-Learning-Resources/AdminCount-Header.jpg'
slug: "activedirectory-admincount-attribute-usage"

title: "Learn to adjust the AdminCount attribute for protected accounts"
---


The [AdminCount](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/appendix-b--privileged-accounts-and-groups-in-active-directory/?WT.mc_id=CDM-MVP-5004073) of accounts in Active Directory is often a misunderstood or foreign concept to many admins. If you would like to learn what it is, what use it serves and how to set it correctly, then please check out this [recent article](https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts) I wrote for TechTarget.

The article is a thorough analysis of all things related to AdminCount and what you need to pay attention to. All Active Directory objects have a hidden attribute called ****AdminCount****, which is set to ****Null**** by default. Accounts considered special have the AdminCount value set to 1, which disables inheritance on the object and sets the security on the object to be governed by the AdminSDHolder object.

There are special processes that run and "watch" the AdminCount on accounts. Most people bump into AdminCount when they make permissions changes on a protected account and then see the changes reverted after an hour. If this sounds familiar to you, I invite you to read along and I'll show you where to look and how to configure your environment correctly to offer the best mix of security and convenience.

As always, I value your feedback, please share your thoughts on this topic in the comments section below. You can get to the article by clicking the link earlier in the article or by following the link below. [https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts](https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts)

Many admins often misunderstand or find the concept of [AdminCount](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/appendix-b--privileged-accounts-and-groups-in-active-directory/?WT.mc_id=CDM-MVP-5004073) in Active Directory unfamiliar. If you're interested in learning about its function, purpose, and proper configuration, look at my [TechTarget article](https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts).

The article covers everything about AdminCount and what you should focus on. There is a hidden attribute called AdminCount in all Active Directory objects, and its default value is Null. When you set the AdminCount value to 1, accounts are treated differently, disabling inheritance and enforcing security through the AdminSDHolder object.

Dedicated processes monitor the AdminCount on accounts. It's common for people to come across AdminCount while making permissions changes on a protected account, only to have those changes reversed within an hour. If this sounds familiar, join me as I guide you on where to look and how to configure your environment for optimal security and convenience.

To access the article, click the earlier link or use the link below.\
[https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts](https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts)