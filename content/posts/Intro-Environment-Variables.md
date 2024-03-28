---
title: 'How to set and manage Environment Variables with PowerShell'
slug: 'manage-environment-variables'
description: 'Learn how to create and manage Environment Variables using PowerShell'
date: '2024-03-24'
authors: [mike]
image: '/images/2024/Petri-Environment-Variables/Environment-Variables-Header.webp'
tags: [PowerShell]
draft: false
---

Hey there PowerShell family!

Today, I'm excited to share my latest article from [petri.com](https://petri.com), which discusses setting and managing Environment Variables with PowerShell. The article covers various capabilities of PowerShell, exploring key areas and scenarios that you should understand and master. Here's a summary of the major topics covered in the article.

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

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Env Vars article explainer</title>
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
  <p><b>So where is the rest of the article?</b>
  <br>
  <br>
   You might wonder why some articles like this one only have a sneak peek here. I write for a few websites, and they own the articles, so I can't post the full article here. But I can give you a summary of the article so you can check it out.
  <br>
  <br>
  If you enjoyed this summary, please <a href="https://petri.com/powershell-set-environment-variable/">follow this link</a> to read the full article on the Petri website.</p>
</div>
