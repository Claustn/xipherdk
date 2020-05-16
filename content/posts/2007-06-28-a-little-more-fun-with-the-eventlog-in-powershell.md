---
categories:
- Tech Stuff
date: "2007-06-28T19:37:25Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6099514214"
status: publish
tags:
- PoSH
- PowerShell
title: A little more fun with the eventlog in PowerShell
---
Imagine that you want to list, the last 15 events in all the eventlogs on the system, do you have to write several lines of codes to do that, like this below??  
`
get-eventlog -logname Application -newest 15
get-eventlog -logname System -newest 15
get-eventlog -logname Application -newest 15
`  
Of course not, PowerShell is much smarter than that...  
You can use the 'foreach' command.

`
foreach ($eventlog in (get-eventlog -list -asString)) {write-host $eventlog; get-eventlog $eventlog -newest 15}`

This will give you the newest 15 log entries in each of the eventlogs on the system, let me try to break it down.  
&nbsp;One thing that is important to know about PowerShell is how it handles () and {}  
As in math the () parenthesis' are evaluated first, the {} curly brackets are used for compulsory arguments.

The foreach makes the script run through all eventlogs on the system, and add them to the variable $eventlog, then PowerShell evaluates the {} arguments for each.  
`
{write-host $eventlog; get-eventlog $eventlog -newest 15}
`  
simply writes the variable $eventlog (this contains the name of a eventlog on the system) to the screen, then it goes on to invoke the get-eventlog again, and tells that to return the newest 15 entries, and write them to the screen.&nbsp;&nbsp;

> Kiddo... This isnt very readable I would like to have a better indication of when a new eventlog listing is started.

I say we can take a look at the properties of write-host (You can type help write-host -full or man write-host -full)  
and low and behold it has a feature called background color and foregroundcolor, let try them out.

`foreach ($eventlog in (get-eventlog -list -asString)) {write-host -foregroundcolor blue -backgroundcolor green $eventlog; get-eventlog $eventlog -newest 15}`

> But kiddo I want some specific properties from the eventlogs, how do I do that....

In order to figure out what properties an obeject has, you can use the get-member command as described in another post.  
Get-eventlog -Logname system | get-member (The -logname paramenter is required)  
Now you have a list of properties on the eventlog object, say you would like (Index,EventID,TimeGenerated and Message)

`foreach ($eventlog in (get-eventlog -list -asString)) {write-host -foregroundcolor blue -backgroundcolor green $eventlog; get-eventlog $eventlog -newest 15| select Index,EventID,TimeGenerated,Message}`

