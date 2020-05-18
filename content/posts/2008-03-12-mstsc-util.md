---
categories:
- Tech Stuff
date: "2008-03-12T16:44:02Z"
meta:
  dsq_thread_id: "6099514297"
status: publish
tags:
- AutoIT
- scripts
title: MSTSC Util
---
A while back I needed to connect to different servers quite often via mstsc, and a lot of the times I needed a console connection to the specific server, so I decided to write a small app, so I did not have to type Mstsc /v:server /console all the time.

I decided to make a small AutoIT app, that would dock in the systray.  
[![Systray icon](/assets/images/2008-03-12_1630.thumbnail.png)](http://www.xipher.dk/assets/images/uploads/2008-03-12_1630.png "Systray icon")

What it does is, it reads a list of servers from an .ini file, you need to prepopulate the list with the servers in your own environment like this.

[![Servers.INI](/assets/images/2008-03-12_1620.thumbnail.png)](http://www.xipher.dk/assets/images/uploads/2008-03-12_1620.png "Servers.INI")

[So what it ends up looking like in "production" is something like this...](http://screencast.com/t/f5dqaHkGmZe)

You can download the compiled app here...  
[Flex Console connect app by Xenophane](http://www.xipher.dk/assets/images/uploads/mstsc-tray-flex1.exe "Flex Console connect app by Xenophane")

For the paranoid I will attach the AutoIT source code, so you can compile it your self :)

`
#Include `

Opt("TrayOnEventMode",1)  
Opt("TrayAutoPause",0)  
Opt("TrayMenuMode",1) ; Default tray menu items (Script Paused/Exit) will not be shown.  
Global $server[100][3]

$Console = TrayCreateMenu("Console Connect")  
$Regular = TrayCreateMenu("Regular Connect")

$data = IniReadSection ( @ScriptDir&"\servers.ini", "servers" )

;~ For $i = 1 To $data[0][0]  
;~ MsgBox(4096, "", "Key: " & $data[$i][2] & @CRLF & "Value: " & $data[$i][1])  
;~ Next

$server[0][0] = $data[0][0]  
For $i = 1 to $data[0][0]  
 $server[$i][0] = TrayCreateItem($data[$i][0], $Console)  
 TrayItemSetOnEvent(-1, "ServerConsole")  
 $server[$i][1] = TrayCreateItem($data[$i][0], $Regular)  
 TrayItemSetOnEvent(-1, "ServerRegular")  
 $server[$i][2] = $data[$i][1]  
Next

TrayCreateItem("")  
TrayCreateItem("About")  
TrayItemSetOnEvent(-1, "DoAbout")  
TrayCreateItem("")  
TrayCreateItem("Exit")  
TrayItemSetOnEvent(-1, "DoExit")

TraySetState()

While 1  
 Sleep(100)  
WEnd

Func DoAbout()  
 Msgbox(64,"About:","Tool by Claus T. Nielsen")  
EndFunc

Func DoExit()  
 Exit  
EndFunc

Func ServerConsole()  
 For $i = 1 to $server[0][0]  
 If $server[$i][0] = @TRAY\_ID Then  
 Run("mstsc -v:"&$server[$i][2]&" /F -console")  
 ExitLoop  
 EndIf  
 Next  
EndFunc

Func ServerRegular()  
 For $i = 1 to $server[0][0]  
 If $server[$i][1] = @TRAY\_ID Then  
 Run("mstsc -v:"&$server[$i][2]&" ")  
 ExitLoop  
 EndIf  
 Next  
EndFunc

Remember that the /console switch has been deprecated in Vista SP1, and replaced by the /admin switch, the above code does not reflect this change currently, but it is very easily changed.

