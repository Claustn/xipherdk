---
categories:
- Tech Stuff
date: "2009-07-27T07:27:05Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6099514378"
status: publish
tags:
- PowerShell
title: Prevent-Screensaver
---
A while back I created a small AutoIt script, to prevent the screensaver from kicking in, last employer had screensaver kick after 5 min, which was rather annoying when you are out doing presentations, and you have to punch in your password everytime the screensaver kicks in..

Then I read a post from [Dimitry Sotnikov](http://dmitrysotnikov.wordpress.com/2009/06/29/prevent-desktop-lock-or-screensaver-with-powershell/) that he had written a small PowerShell script to do something similar, and figured out that he uses Wshell to send a keystroke, I thought that could be annoying, if you start working on the computer, and forget to stop the execution of the script, and it suddenly types a character.  
So I decided to figure out how to move the mouse from PowerShell, l assumed that I would have to do it using a .Net class, after looking around MSDN I found

[System.Windows.Forms.Cursor]

So combining that with Dimitry's script we get:  
[ps]

param($minutes = 60)

for ($i = 0; $i -lt $minutes; $i++) {  
 Start-Sleep -Seconds 60  
 $Pos = [System.Windows.Forms.Cursor]::Position  
[System.Windows.Forms.Cursor]::Position = New-Object System.Drawing.Point((($Pos.X) + 1) , $Pos.Y)

}  
[/ps]

