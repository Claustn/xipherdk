---
categories:
- Tech Stuff
date: "2009-01-15T22:27:00Z"
meta:
  _edit_last: "1"
status: publish
tags:
- batch
- Minasi
- prompt
title: Quick and dirty ping script
---
There was a question on the Minasi forum asking if someone had written a batch script to ping a bunch of servers.. So I thought: " I have'nt, but I would like to try".  
So I started putting a few lines of batch code together, here is what I came up with.  
[sourcecode lang="css"]

REM Ping Servers Script

@echo off  
for /f "tokens=\*" %%a in (servers.txt) DO call :pingStuff %%a  
REM Here I run through a .txt file called servers.txt, and jump to "Pingstuff" with every line from the file, parsing the servername in the %%a variable

:pingStuff  
Ping -n 1 -w 100 %1 | find /i "TTL" \> NUL  
REM Here I ping the server 1 time. %1 is a placeholder for the 1st parameter passed to the function, here it is the servername, then I look for  
REM TTL, if TTL is found in the ping result (if the ping is successful) I do nothing, if it is not there I jump to the "failure", and output the server name  
REM to the screen.  
if errorlevel 1 goto failure

goto end

:failure  
echo Address %1 is not responding.

:end  
[/sourcecode]

What I will end up with is a list of computers that are not responding, and all successful pings will not be written to the screen.

