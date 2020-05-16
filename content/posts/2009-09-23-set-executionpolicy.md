---
categories:
- Everyday
date: "2009-09-23T21:21:29Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6883743979"
status: publish
tags: []
title: Set-ExecutionPolicy
---
I was just playing with the Set-ExecutionPolicy cmdlet today, when I noticed that in V2, it is possible to change the execution policy for different scopes, even though you are not running your session using the "Run as administrator".

Lets say you want to set the executionpolicy to unrestricted for a single powershell process, you can do that using the -scope option like this

Set-ExecutionPolicy -Scope Process Unrestricted

This will prompt you if you want to change the execution policy for your "current" process.

There are 3 different scopes, Process, CurrentUser and LocalMachine, where Localmachine is computer wide setting for all users, this still requires "admin rights" to set.

