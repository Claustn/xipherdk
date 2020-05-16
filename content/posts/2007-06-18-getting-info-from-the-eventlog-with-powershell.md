---
categories:
- Tech Stuff
date: "2007-06-18T15:14:13Z"
meta: null
status: publish
tags:
- Eventlog
- PoSH
- PowerShell
- PS
- Scripting
title: Getting info from the eventlog with PowerShell
---
Started playing around with PowerShell, so I thought I would post a few simple things on the blog, maybe you won't find it interesting, but it is a good way for me to remember, so BLAH!.

First in order to get information from the eventlog you call the cmdlet called:  
`get-eventlog`

Then you need to tell it what you want to get.  
`get-eventlog -logname Application`

<font face="Georgia">Which will get you the Application log on the local machine.</font>

<font face="Georgia">That will list the entire Application eventlog to the screen, which is usually a lot of data. So we want to be able to pick specific data out of the eventlog, and here is a way to do it.</font>

`get-eventlog -logname Application -newest 50 | where {$_.EventID -eq "1"} `

What this does is, it gets the Application eventlog, and gives me an object with&nbsp;the newest 50 entries, i pipe this object into another function called "where",&nbsp; I tell "where" to show me all objects where EventID is equal to 1. I this case you see I use $\_.EventID, the $\_ in powershell represents the object the as being passed into the new function, so in this case $\_ represents the newest 50 values in the Application Eventlog.

