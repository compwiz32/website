---
title: "SAML vs OAuth vs OIDC: What's the Difference"
slug: 'SAML-OAUTH-OIDC-Differences'
description: 'Confused about SAML vs OAuth vs OIDC? This guide explains what each protocol does and when to use them.'
date: '2025-10-26'
authors: [mike]
image: '/images/2025/SAML-OAUTH-OIDC-Differences/protocol-chooser-header-resize.webp'
tags: '[Identity Mgmt]'
draft: false
---

- [What is SSO?](#what-is-sso)
- [The One-Sentence Explanations](#the-one-sentence-explanations)
- [SAML: Enterprise Single Sign-On](#saml-enterprise-single-sign-on)
  - [How SAML Works](#how-saml-works)
  - [What Makes SAML Powerful](#what-makes-saml-powerful)
  - [Where SAML Excels](#where-saml-excels)
  - [SAML Limitations](#saml-limitations)
- [OAuth: Authorization instead of Authentication](#oauth-authorization-instead-of-authentication)
  - [The Problem SAML Can't Solve](#the-problem-saml-cant-solve)
  - [How OAuth Works: The Photo Printing Story](#how-oauth-works-the-photo-printing-story)
  - [What Does an OAuth Access Token Look Like?](#what-does-an-oauth-access-token-look-like)
  - [The OAuth Delegation Model](#the-oauth-delegation-model)
  - [Using OAuth for Authentication](#using-oauth-for-authentication)
- [OIDC: Authentication Built on OAuth](#oidc-authentication-built-on-oauth)
  - [What OIDC Adds: The ID Token](#what-oidc-adds-the-id-token)
- [Which One Should You Use?](#which-one-should-you-use)
- [Real-World Use Cases](#real-world-use-cases)
- [Wrapping Up](#wrapping-up)

I work on an automation team that also handles all the SSO configurations for our organization. We're a talented group - we've set up hundreds of SAML integrations, configured countless app registrations in Azure, and automated authentication workflows across our enterprise. We know what we're doing.
Or so I thought.

One day we got to talking about OAuth and OIDC, and I was surprised to discover something: while we could all configure these protocols successfully, the group couldn't clearly explain the fundamental differences between them. We knew SAML was for SSO, OAuth was for "API stuff," and OIDC was "OAuth but newer" - but the why behind it all was fuzzy.

Here's the thing - you can go really far in configuring app registrations in Azure without actually understanding what's happening behind the scenes. You follow the documentation, check the right boxes, copy the right scope strings, and it works. But when someone asks "Why are we using OAuth here instead of SAML?" or "What's the difference between OAuth and OIDC?" - that's when the gaps show up. I'd like to help you understand the differences.

## What is SSO?

Before we can talk about authentication protocols, let's make sure we all have a good understand of what SSO is, because that in itself is a pretty big topic. Single Sign-On (SSO) is using a single set of credentials stored in a common directory to access multiple applications. Instead of maintaining separate usernames and passwords for Salesforce, Office 365, your internal HR system, and dozens of other apps, you log in once using your credentials and gain access to everything.

Think of it like checking into a hotel. You show your ID and credit card at the front desk. The front desk doesn't store your credit card details - they verify it with the credit card company, get confirmation that it's valid, and then issue you a key card. Your room door, the pool gate, the fitness center - they all accept your key card without needing to see your ID or credit card again. Each door trusts that the front desk already verified who you are and that you're authorized to be there. The credit card company never shares your actual account details with the hotel rooms.

SSO works the same way. The application doesn't receive a copy of your password. Instead, the application relies on the directory to authenticate you. Popular directories include Azure AD (now called Microsoft Entra ID), Okta, Salesforce, and Google Cloud Identity. While traditional Active Directory can also serve this role, these cloud-based directories are designed to work over the internet and offer more options for integrating with modern applications. Once you authenticate with the directory, it sends a secure token to the application that essentially says "this person is authenticated and authorized to use your service." The application trusts that token without ever seeing your password.

**SSO is the user experience** - the goal we're trying to achieve. **SAML, OAuth, and OIDC are the technical protocols that make SSO possible**, but they achieve it in very different ways. Understanding what makes them different starts with understanding what problem each one was designed to solve.

## The One-Sentence Explanations

Let's start with the simplest possible explanations:

**SAML**: Enterprise single sign-on that proves who you are to web applications

**OAuth 2.0**: Lets apps access your stuff elsewhere without your password

**OIDC**: OAuth plus proper login - gives apps your identity AND access to your stuff

Those one-liners give a high level overview of what each protocol was designed for. We're going to get into more detail on each and by the end, you'll understand why SAML can't do what OAuth does and what's the actual difference between OAuth and OIDC.

Below is a table of the main differences between each protocol. We're going to build on this table as we go to help illustrate the differences more clearly.

Protocol | Primary Purpose
---------|----------
 SAML | Authentication
 OAuth | Authorization
 OIDC | Authentication + Authorization

The key insight: these aren't competing protocols - they solve different problems. SAML was built for enterprise identity. OAuth was built for delegating access. OIDC bridges both worlds.

## SAML: Enterprise Single Sign-On

_**Enterprise single sign-on that proves who you are to web applications**_

SAML (Security Assertion Markup Language) has been the defacto standard for enterprise SSO for nearly two decades. If you've ever logged into your company's portal and then accessed applications like Salesforce, Workday, or ServiceNow without entering your password again - you've used SAML.

### How SAML Works

SAML operates on a federation model with two main players:

- **Identity Provider (IdP)**: This is your directory service - Azure AD, Okta, or similar - that stores and verifies your credentials
- **Service Provider (SP)**: The application you're trying to access - like Workday or Salesforce

Before you ever attempt to log in, your company's identity team has already set up a trust relationship between your IdP and Workday. This pre-configured relationship is what allows the two systems to communicate securely and trust each other's information.

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Callout Box Federation</title>
<style>
.callout {
  padding: 20px;
  background-color: #E3F2FD;
  border-left: 6px solid #2196F3;
}
</style>
</head>
<body>

<div class="callout">
  <p><strong>What is Federation?</strong></p>
  <p>Federation is a trust relationship established between an identity provider and a service provider. Your identity team configures this relationship by exchanging metadata between the two systems - essentially teaching them how to communicate securely and what to trust from each other. <br>

Once federated, the service provider trusts authentication assertions from your IdP without needing to verify credentials itself. This is why SAML is often called a "federated identity" protocol.</p>
</div>

</body>
</html>

<br>

Here's what happens when you log into a SAML-enabled application:

1. You try to access Workday
2. Workday redirects you to your company's IdP (like Azure AD)
3. You authenticate with the IdP using your username and password (and hopefully MFA!)
4. The IdP creates a SAML assertion - an XML document that says "this person is authenticated"
5. The IdP sends this assertion back to Workday
6. Workday trusts the assertion and logs you in

The key point: Workday never sees your password. It trusts that your IdP has already verified who you are.

### What Makes SAML Powerful

SAML assertions don't just say "this person is authenticated" - they can include rich user attributes like:

- Name and email address
- Department and job title
- Employee ID
- Manager information
- Group memberships

This attribute delivery is one of SAML's biggest strengths. Applications can use these attributes for access control, personalization, or provisioning without making additional API calls.

Here's what a simplified SAML assertion looks like:

```XML
<saml:Assertion>
  <saml:Subject>
    <saml:NameID>john.doe@company.com</saml:NameID>
  </saml:Subject>
  <saml:AttributeStatement>
    <saml:Attribute Name="email">
      <saml:AttributeValue>john.doe@company.com</saml:AttributeValue>
    </saml:Attribute>
    <saml:Attribute Name="department">
      <saml:AttributeValue>Engineering</saml:AttributeValue>
    </saml:Attribute>
    <saml:Attribute Name="title">
      <saml:AttributeValue>Senior Developer</saml:AttributeValue>
    </saml:Attribute>
  </saml:AttributeStatement>
</saml:Assertion>
```

All of this information is delivered to the application in a single, signed assertion. The application receives your identity and relevant attributes in one package, ready to use for access decisions and personalization.

### Where SAML Excels

SAML is ideal for enterprise environments where:

- You have a central identity provider (like Azure AD or Okta)
- Multiple applications need to trust that provider
- You need to pass user attributes to applications
- Everything operates within a trust federation
- Applications are primarily web-based (they can be cloud based or internal company apps)

For corporate SSO scenarios, SAML has been and continues to be the first choice for SSO authentication. It's mature, well-supported, and does exactly what it was designed to do. At my org we have built hundreds of SAML federations and continue to do so regularly.

### SAML Limitations

SAML works beautifully within its federation model - where your IdP and Service Providers have a pre-configured trust relationship. But what happens when you need something different? What if a third-party application needs to access your data at a completely different service? That's where SAML's model breaks down, and it's exactly the problem OAuth was created to solve.

Protocol | Primary Purpose | What You Get
---------|---------- | -------
 SAML | Authentication | Assertion with user identity and attributes

## OAuth: Authorization instead of Authentication

_**Lets apps access your stuff elsewhere without your password**_

SAML handles enterprise SSO beautifully, but it is basically a one trick pony. Yes it works well, but for one scenario only: a person needs to login to a web based application AND that person only needs to work or perform activities inside that one application. However, if person needs to login into an application and that application needs to access data from a third party, SAML can't handle that situation.

### The Problem SAML Can't Solve

Let me reinforce SAML's limitations with a very relatable example. Let's say you want to use a fictional photo printing service called PrintMyPhotos. You have all your photos stored in Google Photos, and PrintMyPhotos needs to access them to print your selected images.

Here's the challenge:

- Using SAML, you would authenticate to the PrintMyPhotos service (that part works fine)
- But now PrintMyPhotos needs to call Google Photos API to fetch your images
- PrintMyPhotos can't use a SAML assertion to authenticate to Google
- Google Photos isn't part of your company's SAML federation
- There's no pre-configured trust relationship between PrintMyPhotos and Google

Before OAuth existed, you'd have to give PrintMyPhotos your Google username and password. They would log in as you and download your photos. This was terrible for several reasons:

- PrintMyPhotos could access everything in your Google account, not just photos
- They could continue accessing your account even after you were done with them
- If you changed your Google password, PrintMyPhotos would break
- You had to trust them completely with your credentials

OAuth was created to solve this exact problem.

### How OAuth Works: The Photo Printing Story

Here's what happens when PrintMyPhotos uses OAuth to access your Google Photos:

1. You visit PrintMyPhotos and click "Import from Google Photos"
2. PrintMyPhotos redirects you to Google with a request: "I need access to this user's photos"
3. You log into Google with your credentials (Google authenticates you)
4. Google shows you a consent screen: "PrintMyPhotos wants to access your Google Photos. Allow?"
5. You click Allow (you're authorizing PrintMyPhotos)
6. Google gives PrintMyPhotos an access token with specific permissions (called scopes)
7. PrintMyPhotos uses this token to call Google Photos API and download your selected images

**What PrintMyPhotos Actually Knows**
<br>
This is the critical part that many people miss. After this OAuth flow, here's what PrintMyPhotos KNOWS:

✅ I have an access token that can read photos from account XYZ

✅ I have read-only permission (can't delete or modify)

✅ This token expires in 1 hour

**What PrintMyPhotos DOESN'T KNOW**

❌ Your name

❌ Your email address

❌ Who you actually are

❌ Your Google password

**This is authorization, not authentication.** OAuth grants PrintMyPhotos permission to access a specific resource (your photos) with specific limitations (read-only, expires soon). It says nothing about your identity.

### What Does an OAuth Access Token Look Like?

Here's an example of an encoded OAuth access token:

```text
ya29.a0AfH6SMBx7qK9X5n3C8vQwZ2R1pN8mK3jL9vH4sT6uY2wX0...
```

This looks like random text, but it's actually encoded information. When decoded, some access tokens (particularly JWT-formatted ones) look like this:

```json
{
  "aud": "https://photoslibrary.googleapis.com",
  "scope": "https://www.googleapis.com/auth/photoslibrary.readonly",
  "exp": 1234567890,
  "iat": 1234564290
}
```

Notice what's in there: the API endpoint it can access `aud`, the specific permissions `scope`, and when it expires `exp`. What's NOT in there? Your name, email, or any identity information. The token grants access to resources, nothing more.

### The OAuth Delegation Model

OAuth works differently than SAML's federation model:

**SAML:** Pre-configured trust between IdP and SP. Your company's identity team sets up the federation before you ever log in.

**OAuth:** Dynamic authorization at the moment you need it. You authorize PrintMyPhotos to access Google Photos without any pre-existing trust relationship between the two companies. Google has never heard of PrintMyPhotos until you authorize them.

This delegation model is OAuth's superpower. It enables:

- Third-party integrations without sharing passwords
- Granular permissions (read-only, specific resources)
- Revocable access (you can revoke PrintMyPhotos' token anytime)
- No pre-configuration required between services

### Using OAuth for Authentication

With OAuth, it's possible to get information about who you are. OAuth can be used to request access to your basic profile information - things like your name and email address. Once an app gets an access token, it can call the provider's API to fetch that profile data. When the API returns your name and email, the app can treat that as authentication and log you in.

This approach works, but it's a workaround. The biggest issue is that there's no standard for how to get user information. Google has one API endpoint for profile data, Facebook has a different one, GitHub uses another. Every provider returns the data in a different format with different field names. If you want to support "Sign in with Google" and "Sign in with Facebook," you have to write custom code for each provider.

OAuth also wasn't designed with identity verification in mind. Access tokens are meant for ongoing API access to resources like photos or calendars. These tokens are typically valid for 1 hour or longer - some can last for days depending on the security requirements. This makes sense when you need sustained access to call APIs repeatedly. But when you're trying to prove who someone is at the moment they log in, you want a token that expires quickly - usually within minutes. Using a long-lived access token to verify identity at login creates a serious security gap.

Let's refer back to our table and add what we've learned about OAuth:

Protocol | Primary Purpose | What You Get
---------|---------- | -------
 SAML | Authentication | Assertion with user identity and attributes
 OAuth | Authorization | Access token for API calls

## OIDC: Authentication Built on OAuth

_**Gives apps your identity AND access to your stuff**_

The gaps between what OAuth provided and what developers needed is exactly why OIDC was created. In 2014, the OpenID Foundation published OpenID Connect (OIDC) with contributions from major tech companies like Google, Microsoft, Ping Identity, and Salesforce. Their goal was simple: add proper authentication to OAuth 2.0.

OIDC doesn't replace OAuth - it builds directly on top of it. Think of OIDC as OAuth 2.0 plus a standardized identity layer. You still get all of OAuth's authorization capabilities, but now you also get a clean, standardized way to verify who someone is.

### What OIDC Adds: The ID Token

The key innovation in OIDC is the ID token. When you authenticate with OIDC, you get two things:

**ID Token:** A standardized token (in JWT format) that contains your identity information
**Access Token:** The same OAuth access token for calling APIs

Here's what an ID token contains:

```json
{
  "sub": "12345",
  "name": "John Doe",
  "email": "john@example.com",
  "email_verified": true,
  "iss": "https://accounts.google.com",
  "aud": "your-app-client-id",
  "exp": 1234567890
}
```

This is identity information delivered directly, with no need to call additional APIs. Every OIDC provider returns this information in the same standardized format. Whether you're authenticating with Google, Microsoft, or Okta, the ID token structure is consistent.

**How OIDC Solves the OAuth Authentication Problem**
Remember the workaround where apps used OAuth to request profile access and then called APIs to get user info? OIDC eliminates that:

**The OAuth workaround:**

1. Get access token
2. Call provider-specific API (/userinfo, /me, /user)
3. Parse provider-specific response format
4. Extract identity information
Treat as authentication

**With OIDC:**

1. Get ID token (identity) + access token (API access)
2. Done - you have standardized identity information immediately

The ID token is also specifically designed for authentication - it's short-lived and includes security features like audience validation and expiration timestamps. You're not repurposing an API access token for something it wasn't meant to do.

**OIDC in Practice**
When you click "Sign in with Google" today, you're almost certainly using OIDC, not raw OAuth. The difference is subtle from a user perspective - you still see the same consent screen, you still authorize access. But behind the scenes, the app receives both an ID token (proving who you are) and optionally an access token (if it needs to access your resources).
This makes OIDC incredibly versatile:

- Need just authentication? Request only the OpenID scope, get just an ID token
- Need authentication plus API access? Request OpenID plus resource scopes, get both tokens
- Building a modern web or mobile app? OIDC handles it
- Need to access user data across services? OIDC gives you OAuth's delegation model

**OIDC as a Modern Alternative to SAML**
OIDC has become the modern alternative to SAML for many use cases. While SAML remains dominant in enterprise environments with established federations, OIDC offers:

- Lighter weight (JSON instead of XML)
- Better mobile and SPA support
- Modern developer experience
- Same SSO capabilities
- Built-in API access delegation

For new applications, OIDC is often the first choice. It gives you enterprise-grade authentication like SAML, but with the flexibility and modern architecture of OAuth.

Now we can complete our table and get a very clear view of the differences between the protocols.

Protocol | Primary Purpose | What You Get | Common Use Case
---------|---------- | ------- | -----
 SAML | Authentication | Assertion with user identity and attributes | Corporate SSO - logging into internal apps
 OAuth | Authorization | Access token for API calls | Third-party app accessing your data elsewhere
 OIDC | Authentication + Authorization | ID token (identity) + Access token (API access) | Modern app login with optional API access

Now you can see the complete picture. SAML authenticates within federations. OAuth delegates access to resources. OIDC does both - it authenticates users AND provides OAuth's delegation capabilities.

## Which One Should You Use?

Now that you understand what these protocols are and how they differ, the question becomes: which one should you use for your situation? Here's a simple flowchart to help you make a quick decision.

![Protocol Decision Matrix](/images/2025/SAML-OAUTH-OIDC-Differences/Which-Protocol-to-Use.png)

## Real-World Use Cases

**Scenario 1: Corporate SSO for Workday and Salesforce**
You're setting up SSO so employees can access internal applications with their company credentials. → SAML. Your identity team will configure federations between Azure AD and each application.

**Scenario 2: Building a New Mobile App**
You're building a mobile app that needs users to log in and want to support "Sign in with Google." → OIDC. Modern, mobile-friendly, and gives you standardized identity information.

**Scenario 3: Calendar App Integration**
You're building a scheduling app that needs to read users' Google Calendar events. → OAuth 2.0. You need delegated access to their calendar data, not their identity.

**Scenario 4: New Web App with API Access**
You're building a web application where users log in and you need to access their Microsoft 365 data. → OIDC. You get authentication (ID token) plus authorization to call Microsoft Graph APIs (access token).

**Scenario 5: Integrating with Legacy Enterprise System**
You need to integrate with an existing system that only supports SAML. → SAML. Work with what the system supports, even if OIDC would be your preference for a greenfield project.

SAML isn't going away - it's still the standard for enterprise SSO with established federations. But for new applications, especially those targeting mobile or modern web architectures, OIDC has become the preferred choice. It gives you SAML's authentication capabilities plus OAuth's flexibility for API access.

The important thing is understanding that these protocols aren't competing - they're solving different problems. SAML authenticates within federations. OAuth delegates access across services. OIDC does both.

## Wrapping Up

These three protocols confused my team for a long time. We could configure them successfully, check the right boxes in Azure, and get applications working. But understanding why we used one over another - that took digging into what problem each protocol was actually designed to solve.

**SAML** excels at enterprise single sign-on within trust federations. It's mature, widely supported, and handles rich attribute delivery beautifully. When you need corporate SSO for internal applications, SAML remains the go-to choice.

**OAuth** revolutionized how applications access resources by enabling delegation without password sharing. It solved the critical problem of third-party integrations - letting apps access your data at other services without compromising your credentials. But OAuth doesn't handle identity.

**OIDC** bridges both worlds. It adds standardized authentication to OAuth's authorization framework, giving you identity verification plus resource access delegation. For modern applications, it's often the best choice - you get enterprise-grade authentication with the flexibility of OAuth.

Now when you configure app registrations or set up federations, you'll understand the "why" behind the protocol choice. SAML for enterprise federations. OAuth for API delegation. OIDC for modern authentication that might also need API access. Each protocol has its place, and understanding the differences helps you make better architecture decisions.

If you're interested in learning more about how these protocols work in practice, don't forget to subscribe to get notified when I publish more identity management topics.
