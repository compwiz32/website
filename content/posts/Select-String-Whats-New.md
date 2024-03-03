---
title: "What's new with Select-String in PowerShell7?"
tags: [PowerShell]
date: 2020-03-14
authors: [mike]
draft: false
image: "/images/2020/Select-String-Whats-New/Select-String-PS7.webp"
slug: "select-string-powershell7"
description: "Discover the new parameters available in Select-String in PowerShell7."
---

Select-String is one of those commands that either you use it often or not much at all. There doesn't seem to be a middle of the road for Select-String. I don't get called upon to use it that often, but when I do, it usually saves my bacon at work. The most typical use case for me is for searching flat log files for patterns of text or for all entries of a particular phrase across a bunch of files.

The release of PowerShell7 has brought many features and some great new cmdlets. Select-String received some minor but powerful updates as well in the new release. The updates to Select-String are less "WOW" and more like, "Oh yeah, that's a useful addition." For someone who uses Select-String all the time, these updates are probably super helpful. But if you only occasionally use Select-String, then you may not even notice the new bits available when using this cmdlet.

Select-String has four new parameters included with Powershell7. Overall, the major functionality of the cmdlet has changed little. These four new parameters add some useful upgrades though, so let's review how they work.

### -culture parameter

The `-culture` parameter is a new parameter that is used to detect specific characters for a language. An example of this would be in the English language we have the letter `i` which can be lowercase `i` and uppercase `I`. Most modern computer systems can pick up the difference between a lowercase and uppercase letter. However, that's not always the case for other languages that have special characters.

One example of this would be the Turkish alphabet which has an upper and lowercase `I` and then also has an upper and lowercase `I with two dots above it`. That means the Turkish alphabet has 4 iterations of the letter `i` and the English alphabet only has two. If we were to search for those letters using an English search utility, the search would not match correctly sometimes. This may not seem like a big problem, but if you were trying to programmatically search Turkish text for specific phrases, it could be a bit of a nightmare to deal with. Same goes for other languages that have special characters.

The culture parameter solves this issue by allowing the pattern matching of Select-String to occur using the right language. It's probably not a big change for most people, but it is a nice addition.

### -noemphasis parameter

The `-noemphasis` parameter turns off text highlighting of matched text. Now, you might to yourself, "wait a minute, Select-String doesn't highlight matches" and you would be right... except in PowerShell7 text highlighting is a now a thing, and it's on by default!

I'll need to first show how text highlighting works before I can show what the purpose of `-noemphasis` is. I absolutely LOVE the new highlighting addition to Select-String and I think you will too!

You can see the highlighting change in action by doing a simple text search. Here's a text string match on the word "the".

```PowerShell
$text | select-string -Pattern "the"
```

![Text-Highlight](/images/2020/Select-String-Whats-New/Text-Highlight.png)

However, that only matched on the first match. To match on all instances of the word "the", you need to add the `-Allmatches` parameter.

```PowerShell
$text | select-string -Pattern "the" -AllMatches
```

![Text-highlight-AllMatches](/images/2020/Select-String-Whats-New/Text-highlight-AllMatches.png)

The highlighting can be useful when searching across multiple files at once. Here is a search for the word "dummy" in a directory of log files.

```PowerShell
 Select-String -Path C:\temp\*.log -Pattern "dummy"
 ```

![highlight-multiple-files](/images/2020/Select-String-Whats-New/highlight-multiple-files.png)

The highlighting of the matched text is a small change that makes a big impact.

There are many ways to do the same searches with existing Select-String parameters like `-CaseSensitive`, `-NotMatch`, `-Quiet` and others. I won't review those parameters because they are not new, but keep in mind you can get creative with the parameters and the highlighting will reflect differently for all those results.

Now I am sure you can imagine what the `-noemphasis` parameter does. If we perform the same search and add that parameter, we get the same results without highlighting.

```PowerShell
 Select-String -Path C:\temp\*.log -Pattern "dummy" -noemphasis
```

![highlight-multiple-files-noemphasis](/images/2020/Select-String-Whats-New/highlight-multiple-files-noemphasis.png)

### -raw parameter

Another new parameter is the `-raw` parameter. This parameter changes what the result type will be. Remember that one of the great things about PowerShell is that nearly everything is an object. This is true for Select-String results.

If we perform one of our earlier searches and save the results to a variable, we can then look at the variable type. Here we see that the object type is `Microsoft.PowerShell.Commands.MatchInfo`

```PowerShell
PS C:\Scripts> $results = ($text | select-string -Pattern "the")
PS C:\Scripts> $results | gm
```

![MatchInfo-Type-1](/images/2020/Select-String-Whats-New/MatchInfo-Type-1.png)

The output result becomes a string when using the `-raw` switch. Notice also that all the members for the string object are much different from the matchinfo object.

```PowerShell
PS C:\Scripts> $results2 = ($text | select-string -Pattern "the")
PS C:\Scripts> $results2 | gm
```

![String-Type-1](/images/2020/Select-String-Whats-New/String-Type-1.png)

### -simplematch parameter

To this point, I have been showing off text searches. You can do much more with Select-String because behind the scenes the cmdlet uses REGEX by default to do its work.

We can see an example of the REGEX functionality below. In this search, we're matching on a pattern of `\?`. But since Select-String is using REGEX, this search is looking for just the `?` character.

```PowerShell
Select-String -Path "$PSHOME\en-US\*.txt" -Pattern '\?'
```

![Regex-search-result](/images/2020/Select-String-Whats-New/Regex-search-result.png)

The `-simplematch` parameter instructs PowerShell to not use REGEX for this search, instead just do a "simplematch" of the text in the pattern. Using the same search as before, you'll see how the parameter affects the output. We have no match in the result because there is no instance of `\?` in the files searched.

```PowerShell
Select-String -Path "$PSHOME\en-US\*.txt" -Pattern '\?' -simplematch
```

![simplematch-search-result](/images/2020/Select-String-Whats-New/simplesearch-searchresult.png)

This is an important concept to understand because REGEX has special characters that LOOK like regular characters but PRODUCE different results in searches. The `-simplematch` parameter allows for a way to do a search with the literate value of a character rather than REGEX value of that character.

Look at this simple example I have created to highlight the difference. In the search below, we're trying to find the `.` character, also known as a period. What this search LOOKS LIKE is, "look at all .txt files in the specified directory and match on the `.`"

```PowerShell
Select-String -Path "$PSHOME\en-US\*.txt" -Pattern '.'
```

However, that's not what PowerShell interprets it as, because the `.` character has special meaning in REGEX. The `.` character means match ALL characters (or in better terms match ANY character). Below is the output.

![Misleading-REGEX-Search](/images/2020/Select-String-Whats-New/Misleading-REGEX-Search.png)

What the search returned is a match of ANY CHARACTER. I did not use the `-AllMatches` parameter, so it literally matched on the FIRST CHARACTER of every line of text in the files that match the search criteria.

If we were actually trying to find periods in files, this search would have been a giant fail. However, the `-simplemacth` parameter instructs PowerShell to ignore REGEX and do a simple search instead.

Also a side note here. Imagine if we performed this same search prior to PowerShell7 and did not have the highlighting feature? Imagine getting an output without seeing that the first character of every line was the match and trying to troubleshoot that problem...

```PowerShell
Select-String -Path "$PSHOME\en-US\*.txt" -Pattern '.' -SimpleSearch
```

![SimpleSearch-SearchResult](/images/2020/Select-String-Whats-New/SimpleSearch-SearchResult.png)

Now we get an output, that is what we expected. PowerShell returned a list of all lines that contain a period. You can now see how the `-simplesearch` parameter could be useful.

These small changes to the Select-String cmdlet are edge-use cases for most people, but the flexibility that these options provide can be useful when those edge cases arise. Thanks for reading, let me know what you think in the comments section below.
