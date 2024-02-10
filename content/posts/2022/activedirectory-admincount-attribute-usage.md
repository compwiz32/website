+++
categories = ["Active-Directory", "AdminCount", "PowerShell", "Get-ADUser", "Get-ADGroup"]
date = 2022-03-03T14:30:00Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1606724003362-e5726b8cc65d?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDExfHxvZmZ8ZW58MHx8fHwxNjQ2MjM5NjQ0&ixlib=rb-1.2.1&q=80&w=2000"
slug = "activedirectory-admincount-attribute-usage"
summary = "Learn what the AdminCount value in Active Directory is for, what use it serves and how to set it correctly."
tags = ["Active-Directory", "AdminCount", "PowerShell", "Get-ADUser", "Get-ADGroup"]
title = "Learn to adjust the AdminCount attribute in protected accounts"

+++


The [AdminCount](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/appendix-b--privileged-accounts-and-groups-in-active-directory/?WT.mc_id=CDM-MVP-5004073) of accounts in Active Directory is often a misunderstood or foreign concept to many admins. If you would like to learn what it is, what use it serves and how to set it correctly, then please check out this [recent article](https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts) I wrote for TechTarget.

The article is a thorough analysis of all things related to AdminCount and what you need to pay attention to. All Active Directory objects have a hidden attribute called ****AdminCount****, which is set to ****Null**** by default. Accounts considered special have the AdminCount value set to 1, which disables inheritance on the object and sets the security on the object to be governed by the AdminSDHolder object.

There are special processes that run and "watch" the AdminCount on accounts. Most people bump into AdminCount when they make permissions changes on a protected account and then see the changes reverted after an hour. If this sounds familiar to you, I invite you to read along and I'll show you where to look and how to configure your environment correctly to offer the best mix of security and convenience.

As always, I value your feedback, please share your thoughts on this topic in the comments section below. You can get to the article by clicking the link earlier in the article or by following the link below. [https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts](https://www.techtarget.com/searchwindowsserver/tutorial/Learn-to-adjust-the-AdminCount-attribute-in-protected-accounts)







