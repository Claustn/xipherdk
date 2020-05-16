---
categories:
- Tech Stuff
date: "2010-05-11T22:14:45Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
status: publish
tags:
- PowerShell
title: Remoting examples from my presentation
---
Example #1

[ps]  
#Sessions

#Single Session  
$Session = New-PSSession -ComputerName "computername"

#Multiple Sessions

$Session = New-PSSession -ComputerName "comp1","comp2".....

#Remove all Sessions

Remove-PSSession \*

#Remove Named Session

Remove-PSSession $Session

#Using $session  
Invoke-Command {ipconfig} -Session $Session

Enter-PSSession -Session $session  
[/sourcecode]

Example #2

[sourcecode lang="PosH"]  
$session = New-PSSession -ComputerName "compname"

Invoke-Command {Import-Module ActiveDirectory} -Session $session

Import-PSSession -Session $session -Module ActiveDirectory -Prefix "ctn"

notepad (Get-Module).path

Get-Command \*ctn\*

[/ps]

