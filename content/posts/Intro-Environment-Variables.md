---
title: 'How to set and manage Environment Variables with PowerShell'
slug: 'manage-environment-variables'
description: 'Learn how to create and manage Environment Variables using PowerShell'
date: '2024-03-19'
authors: [mike]
image: '/images/2024/Petri-Environment-Variables/Environment-Variables-Header.webp'
tags: [PowerShell]
draft: false
---

Hey there PowerShell family!

Today, I'm excited to share my latest article from Petri.com, focusing on setting and managing Environment Variables with PowerShell. The article covers various capabilities of PowerShell, exploring key areas and scenarios that you should understand and master. Let's review the major topics covered.


<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Callout Box</title>
<style>
.callout {
  padding: 20px;
  background-color: #E6F6E8;
  display: flex;
  align-items: center;
}
.emoji {
            font-size: 48px;
            margin-right: 10px;
        }
</style>
</head>
<body>

<div class="callout">
    <div class="emoji">&#10071;</div>
  <p><b>So where is the rest of the article?</b></p>
  <br>

  <p>I wrote this article for the <a href=https://petri.com> Petri website</a>. You can find the <a href=https://petri.com/powershell-set-environment-variable>full guide on how to set environment variables using PowerShell</a> at petri.com. I can only post a summary of the article here. I understand that it's not awesome to have to visit another website. </p>
</div>

## Introduction to Environment Variables

- What are they with a focus on how to create and manage them using various methods.

## Different Scopes of Environment Variables

- An overview of the three different variable scopes, their distinctions, and practical applications. Managing Environment Variables with PowerShell

## Examples of how to access variables using PowerShell

- A discussion about access methods, setting environment variables, adding new ones, and appending existing ones.
- Examining the advantages and disadvantages of using `Set-Item` cmdlet versus `[System.Environment]()` .NET class method.

## Permanence of Changes

- Defining what is the Default scope in PowerShell
- Explaining why some variables disappear when the session ends
- How to make Machine or User scope changes permanent

## Removing Environment Variables

- Methods to remove variables, such as utilizing `$Env` and `[System.Environment]::SetEnvironmentVariable()` .

The full article can be found by visiting the Petri.com website.
