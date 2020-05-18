---
categories:
- Everyday
date: "2015-05-02T22:36:05Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6433958211"
status: publish
tags: []
title: Domain requirement for ORK
---
I was playing around with Microsoft ORK (Operational Readiness Kit), which is a frontend for the PowerShell deployment toolkit, aimed at hosters.

I wanted to try to do a greenfield install, using a clean Server 2012R2 machine, which had not been joined to any domain, and run everything off that machine. I had downloaded all the pre reqs, and started the install, but it kept failing.

[![2015-05-02_22-18-41](/assets/images/2015-05-02_22-18-41-300x81.png)](http://www.xipher.dk/assets/images/uploads/2015-05-02_22-18-41.png)  
It entered an infinite loop, trying to access the PowerShell AD provider, so my first thought is that I will just turn off the autoload of the AD provider, by setting an environment variable

```
$env:ADFS\_LoadDefaultDrive=0
```

But the script still failed, so I looked at the code, and found this snippet.

```
While (!(Test-Path "AD:\")) { if (Get-Module ActiveDirectory) { Remove-Module ActiveDirectory -ErrorAction 'SilentlyContinue' sleep -Seconds 2 } Enable-PSModule ActiveDirectory -ErrorAction 'SilentlyContinue' }
```

This is what is creating the infinite loop, it tests to see if the AD:\ provider is loaded, if not remove the module and install it again and try to find the drive again.

I have not had time to dig through the entire script, to see if the "installing" machine actually needs the PowerShell AD module or not.. So for now, the machine on which you run the installer on, need to be in a domain.

