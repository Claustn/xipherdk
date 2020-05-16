---
categories:
- Tech Stuff
date: "2010-01-24T23:38:05Z"
meta:
  _edit_last: "1"
status: publish
tags:
- PowerShell
- Quest
title: Powershell as a webservice
---
Seems as if Quest has been doing some really cool things with Powershell,&nbsp; from what I can read, they have "rolled" it up as a webservice, meaning that you can now run your Powershell scripts over HTTPS from either you local machine, your phone,&nbsp; anything that supports a newer web browser.

From what I can gather from the manual, you install it on a server running IIS, you set up the site, security etc. Then you can connect to the site from a webbrowser, and you will get a PowerShell commandline in your browser.&nbsp; Combined with Powershell v.2's remoting features this has the potential of becoming an awesome tool. One of the nice features is that you can save "favourites" in your web sessions,&nbsp; here favourites should be seen as snippets of Powershell code, that you can just click on the run.

So imagine, you have a Powershell script that runs and checks if a particular service is running, if not it does "Something". You&nbsp; are sitting by the pool drinking a nice cold beer, when you get a call that there is trouble in the office. You pick up you android phone, open up&nbsp;a browser connects to [www.whereIWork.com/MyPowerShell](http://www.whereIWork.com/MyPowerShell), login.. click your Powershell favourite/snippet, and your are done..

If you figure out that did not solve the problem, you just do

Enter-PsSession -Computername \<Problematic Servername\>, you are now running a Powershell session on the remote server, where you can look around the system, and try to fix the problem, as if you were sitting in front the server, with a Powershell console open.

Can't wait to I can find some time to get it tested, even thoug it still is in Beta.

