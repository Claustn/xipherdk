---
categories:
- Everyday
date: "2009-09-25T20:40:36Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6111694430"
status: publish
tags: []
title: Finding out how many active sessions are on a Server 2008 TS
---
<p>A question was posted on a forum today, on how to find out how many active sessions was on a Server 2008 Terminal Server, the poster said that on his 2003 Terminal Server he could query the WMI class Win32_PerfFormattedData_TermService_TerminalServices, but that did not work on Server 2008.</p>
<p>So I thought I would give it a shot and see if I could find it, I assumed that it was replaced by a new class, so I had to search the different WMI classes to find the new one.<br />
[ps]<br />
# Find WMI TS Class<br />
#CTN<br />
Get-WMIObject -ComputerName &quot;TSServer&quot;  -List  *term* |   % {($_.Properties | where {$_.Name -like &quot;*session*&quot;}) } | ft name, Origin<br />
[/ps]</p>
<p>What I do here is tell Get-Wmiobject to list all wmi classes on server "TSServer" that contains the word "term" (I guessed it would be called something with terminal services still)</p>
<p>The foreach of the classes returned I check if it has a property that contains the word "session", and just output that as a table, resulting in this on a Server 2008 R2 running Remote Desktop Services.</p>
<p>Name Origin<br />
---- ------
  
DisconnectedSessions Win32\_TerminalService  
TotalSessions Win32\_TerminalService  
ActiveSessions Win32\_PerfFormattedData\_LocalSessionManager\_TerminalServices  
InactiveSessions Win32\_PerfFormattedData\_LocalSessionManager\_TerminalServices  
TotalSessions Win32\_PerfFormattedData\_LocalSessionManager\_TerminalServices  
ActiveSessions Win32\_PerfRawData\_LocalSessionManager\_TerminalServices  
InactiveSessions Win32\_PerfRawData\_LocalSessionManager\_TerminalServices  
TotalSessions Win32\_PerfRawData\_LocalSessionManager\_TerminalServices

Of course to be able to do this, I had to know/guess that the class contained "term" and that the property would be named something with "session"

