---
categories:
- Tech Stuff
date: "2009-09-19T16:45:10Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
status: publish
tags:
- PowerShell
title: Finding that Hyper-V machine
---
<p>Yesterday I was doing some maintenance on a machine at work, and when I looked in device manager, i saw that it was a Hyper-V guest machine, and I asked a few colleagues about it, because I was pretty sure that we didn't have any machines running Hyper-V.. So I needed to find out which machine, so I wrote a quick and  dirty PowerShell script to find it.</p>
<p>First off I knew that it had to be a Windows 2008 machine, that boiled down the possible machines quite a lot.</p>
<p>Here is what I did<br />
[ps]<br />
Get-QADComputer | Where {$_.OSname -match &quot;2008&quot;} | % { Get-Service -ComputerName $_.Name} | where {$_.Displayname -match &quot;hyper&quot;} |  select  Machinename, Displayname<br />
[/ps]</p>
<p>I get all computers that are Windows server 2008, and for each server 2008, I look for services containing the word "hyper".<br />
This is not a very efficient search, but a quick and dirty way to find the info that I needed.</p>
<p>----EDIT-----
Kirk Munro pointed out to me that Hyper-V actually registers a SCP (Service Connection Point) in AD, so instead of querying all the machines, you can just query the list of SCP's in AD instead, which is much more efficient.

[ps]  
Get-QADObject -Name 'Microsoft Hyper-V' -Type serviceConnectionPoint | Get-QADComputer -Identity {$\_.ParentContainerDN}  
[/ps]

