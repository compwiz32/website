+++
categories = ["PowerShell", "Set-PSWebProxy", "Invoke-WebRequest"]
date = 2017-10-11T16:06:45Z
description = ""
draft = false
image = "__GHOST_URL__/content/images/2019/01/castles-fence-love-symbol-39495.jpg"
slug = "how-to-set-proxy-authentication-with-powershell"
summary = "Configure a PowerShell session use an authenticated web proxy, such as Zscaler. "
tags = ["PowerShell", "Set-PSWebProxy", "Invoke-WebRequest"]
title = "How to set proxy authentication with PowerShell"

+++


I have struggled in the past to get my PowerShell sessions to connect online at work because my employer uses **ZScaler** as our web proxy. For those unfamiliar with ZScaler, it is an off-prem (cloud-based) proxy that requires authentication. Our proxy settings are configured via GPO which points to a PAC file set in the IE control panel. 

For most things, it just works because most apps these days just pick up the IE control settings and away we go. But not so much with PowerShell...

So after much googling, I came across a [blog post](https://www.angryadmin.co.uk/?p=900&cpage=1#comment-20182) from a blogger in Europe named the "Angry Admin". His solution was spot on! 

I have posted the code here and modified it with the proxy address for one of the Zscaler web proxies based on the US east coast. You can modify my code with another cloud-based proxy and it should work without any issues. 

You can save this to your PowerShell profile or save it to a PS1 file and simply run it on-demand. I saved my code to a script named `Set-PSWebProxy.ps1`

```
$proxyString = "http://atl2.sme.zscalertwo.net"
$proxyUri = new-object System.Uri($proxyString)

[System.Net.WebRequest]::DefaultWebProxy = new-object System.Net.WebProxy ($proxyUri, $true)
[System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials](http://)
```

Here's a screencap of me trying to hit a web resource in PowerShell before authenticating to Zscaler:
![Failed Web request ](https://i.imgur.com/97Oxbk6.png)



and here's what it looks like after running my authentication script and then attempting another web request:

```
PS C:\scripts> Invoke-WebRequest https://example.org


StatusCode        : 200
StatusDescription : OK
Content           : <!doctype html>
                    <html>
                    <head>
                        <title>Example Domain</title>

                        <meta charset="utf-8" />
                        <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
                        <meta name="viewport" conten...
RawContent        : HTTP/1.1 200 OK
                    Vary: Accept-Encoding
                    X-Cache: HIT
                    Accept-Ranges: bytes
                    Content-Length: 1270
                    Cache-Control: max-age=604800
                    Content-Type: text/html
                    Date: Sat, 06 Jan 2018 04:23:19 GMT
                    Expires: ...
Forms             : {}
Headers           : {[Vary, Accept-Encoding], [X-Cache, HIT], [Accept-Ranges, bytes], [Content-Length, 1270]...}
Images            : {}
InputFields       : {}
Links             : {@{innerHTML=More information...; innerText=More information...; outerHTML=<A href="http://www.iana.org/domains/example">More information...</A>;
                    outerText=More information...; tagName=A; href=http://www.iana.org/domains/example}}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 1270
```

