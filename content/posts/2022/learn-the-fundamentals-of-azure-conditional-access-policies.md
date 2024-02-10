---
date: 2022-02-22 13:10:00 +0300
description: Learn how AzureAD Conditional Access policies can control multi-factor authentication SSO attempts to the Azure cloud.
draft: false
image: /images/2022/03/Cars.jpg"
tags: [Azure]
title: "Learn the fundamentals of Azure AD Conditional Access policies"
---

[Azure Active Directory Conditional Access policies](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/concept-conditional-access-policy-common/?WT.mc_id=CDM-MVP-5004073) are a cool new way to use very specific, fine-grained access controls for each individual single sign-on attempt to the Azure cloud.

### ****Changing times require updated security practices****

It is no longer acceptable to rely on simple assumptions to grant access to resources. For example, you can't block all access from countries in the Asia-Pacific region. Companies need more flexibility to handle more unique login combinations.

The company may require a different authentication level when a user logs in from their phone versus the corporate laptop. For compliance reasons, the executive team may be subject to more stringent controls than frontline workers. Access attempts from known good networks can still be a threat because of [phishing and compromised credentials](https://www.techtarget.com/searchsecurity/tip/Nine-email-security-features-to-help-prevent-phishing-attacks).

These factors add up to dynamic environments that don't allow for a simple set of rules to govern access. Organizations must determine if a login attempt is legitimate or a threat and Azure AD conditional access policies give enterprises real-time analysis of logins to stop potential threats.

[This article is hosted over at the TechTarget website](https://www.techtarget.com/searchwindowsserver/tutorial/Build-your-knowledge-of-Azure-AD-conditional-access-policies) and is a walkthrough of some of the common and not so common attributes and methods you can use to get started with Azure Active Conditional Access. I cover several scenarios to use in your organization's Azure configuration for single sign-on.

You can also check [Microsoft's outstanding docs website](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/overview/?WT.mc_id=CDM-MVP-5004073) on Conditional Access for much more information on all the moving piece involved with Conditional Access. You can find all of the articles I have written for TechTarget at [my author webpage](https://www.techtarget.com/contributor/Mike-Kanakos) . Thanks for reading. I'd love to know what you think. Leave me a message in the comment section at the bottom of the page.
