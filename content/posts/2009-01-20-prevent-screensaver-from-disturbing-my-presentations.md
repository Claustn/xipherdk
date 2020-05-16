---
categories:
- Tech Stuff
date: "2009-01-20T22:38:24Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6099514360"
status: publish
tags:
- AutoIT
- screensaver
title: Prevent screensaver from disturbing my presentations.
---
Most companies have GPO settings that enforces a company wide screensaver policy, in my case the screensaver kicks in after 5 min.

5 Min is a very short time, when you are doing presentations or showing video content, so I decided to create a little AutoIT script to move the mouse 1 pixel after 4 min and 59 sec.

I decided to create a small tray icon, where I can choose how many minutes I want it to run for, and the icon changes in color, when the "screensaver free" time is running out.

[sourcecode lang="bash"]

Opt("TrayOnEventMode",1)  
Opt("TrayAutoPause",0)  
Opt("TrayMenuMode",1)&nbsp;&nbsp; ; Default tray menu items (Script Paused/Exit) will not be shown.  
HotKeySet("^!q", "\_ExitLoop")

$DisableScreenSaver&nbsp;&nbsp;&nbsp; = TrayCreateMenu("Disable Screensaver")

TrayCreateItem("")  
TrayCreateItem("About")  
TrayItemSetOnEvent(-1, "DoAbout")  
TrayCreateItem("")  
TrayCreateItem("Exit (To exit pres CTRL-ALT-q)")  
TrayItemSetOnEvent(-1, "DoExit")

TrayCreateItem("15 Min", $DisableScreenSaver)  
TrayItemSetOnEvent(-1, "DisableScreenSaver15")  
TrayCreateItem("30 Min", $DisableScreenSaver)  
TrayItemSetOnEvent(-1, "DisableScreenSaver30")  
TrayCreateItem("45 Min", $DisableScreenSaver)  
TrayItemSetOnEvent(-1, "DisableScreenSaver45")  
TrayCreateItem("60 Min", $DisableScreenSaver)  
TrayItemSetOnEvent(-1, "DisableScreenSaver60")  
TrayCreateItem("90 Min", $DisableScreenSaver)  
TrayItemSetOnEvent(-1, "DisableScreenSaver90")  
TraySetState()  
TraySetIcon("shell32.dll",251)

While 1  
Sleep(1000)  
WEnd

Func \_ExitLoop()  
Exit 0  
EndFunc

Func getpos()  
$x = MouseGetPos(0)  
$y = MouseGetPos(1)  
; MsgBox ( 0, "MSG", "MousePos: "&amp;amp;amp; $x &amp;amp;amp;", " &amp;amp;amp; $y , 1 )  
MouseMove($x,$y+1,1)  
endfunc

Func DisableScreenSaver15()  
For $i = 1 to 3 Step 1  
If $i = 1 then TraySetIcon("wpdshext.dll",714)  
If $i = 2 then TraySetIcon("wpdshext.dll",711)  
If $i = 3 then TraySetIcon("wpdshext.dll",710)  
Sleep(299000)  
GetPos()  
Next  
Msgbox(64,"Time is Up!","Time without screensaver is up!")  
TraySetIcon("shell32.dll",251)  
EndFunc

Func DisableScreenSaver30()  
$begin = TimerInit()  
For $i = 1 to 6 Step 1  
If $i = 1 then TraySetIcon("wpdshext.dll",714)  
If $i = 2 then TraySetIcon("wpdshext.dll",713)  
If $i = 3 then TraySetIcon("wpdshext.dll",712)  
If $i = 4 then TraySetIcon("wpdshext.dll",711)  
If $i = 6 then TraySetIcon("wpdshext.dll",710)

Sleep(299000)

GetPos()  
Next

Msgbox(64,"Time is Up!","Time without screensaver is up!")  
TraySetIcon("shell32.dll",251)  
EndFunc

Func DisableScreenSaver45()  
For $i = 1 to 9 Step 1  
If $i = 1 then TraySetIcon("wpdshext.dll",714)  
If $i = 4 then TraySetIcon("wpdshext.dll",713)  
If $i = 6 then TraySetIcon("wpdshext.dll",712)  
If $i = 8 then TraySetIcon("wpdshext.dll",711)  
If $i = 9 then TraySetIcon("wpdshext.dll",710)  
Sleep(299000)  
GetPos()  
Next  
Msgbox(64,"Time is Up!","Time without screensaver is up!")  
TraySetIcon("shell32.dll",251)  
EndFunc

Func DisableScreenSaver60()  
For $i = 1 to 12 Step 1  
If $i = 1 then TraySetIcon("wpdshext.dll",714)  
If $i = 6 then TraySetIcon("wpdshext.dll",713)  
If $i = 8 then TraySetIcon("wpdshext.dll",712)  
If $i = 11 then TraySetIcon("wpdshext.dll",711)  
If $i = 12 then TraySetIcon("wpdshext.dll",710)  
Sleep(299000)  
GetPos()  
Next  
Msgbox(64,"Time is Up!","Time without screensaver is up!")  
TraySetIcon("shell32.dll",251)  
EndFunc

Func DisableScreenSaver90()  
For $i = 1 to 18 Step 1  
If $i = 1 then TraySetIcon("wpdshext.dll",714)  
If $i = 6 then TraySetIcon("wpdshext.dll",713)  
If $i = 12 then TraySetIcon("wpdshext.dll",712)  
If $i = 17 then TraySetIcon("wpdshext.dll",711)  
If $i = 18 then TraySetIcon("wpdshext.dll",710)  
Sleep(299000)  
GetPos()  
Next  
Msgbox(64,"Time is Up!","Time without screensaver is up!")  
TraySetIcon("shell32.dll",251)  
EndFunc

Func DoAbout()  
Msgbox(64,"About:","Tool by Claus T. Nielsen")  
EndFunc

Func DoExit()  
Exit  
EndFunc

[/sourcecode]

In order to make it look good, you need the file wpdshext.dll, which is available in Vista... Otherwise you will not have the changing icon, the program will work fine though.

Not pretty code, but it does what it is supposed to.

