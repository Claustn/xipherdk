---
categories:
- Everyday
date: "2009-12-14T20:38:14Z"
meta:
  _edit_last: "1"
status: publish
tags: []
title: MSTSC Util update
---
I was asked to update my MSTSC util, so here goes.. It will now check for the configuration file and display a message if it is not there, instead of just erroring out.

I have tested it to work on Windows 7

Here is the source code:

[sourcecode lang="bash"]  
#Include <constants .au3></constants>

Opt("TrayOnEventMode",1)  
Opt("TrayAutoPause",0)  
Opt("TrayMenuMode",1) ; Default tray menu items (Script Paused/Exit) will not be shown.  
Global $server[100][3]

$Console = TrayCreateMenu("Console Connect")  
$Regular = TrayCreateMenu("Regular Connect")

If FileExists("servers.ini") Then  
 $data = IniReadSection ( @ScriptDir&"\servers.ini", "servers" )  
 Else  
MsgBox(4096,"No Ini File found","I could not find the servers.ini file, Exiting")  
Exit  
EndIf

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
 Run("mstsc -v:"&$server[$i][2]&" /F -admin")  
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

[/sourcecode]

Here is an example on the servers.ini file.

[sourcecode lang="bash"]  
;Server.ini example  
; Right side of the Equal sign is the server name/address,  
; left side is the name you want displayed  
;This files has to be located in the same dir as the .exe file.

[Servers]  
ServerTest 10.0.0.1 = ServerTest  
ExchangeServer = 10.0.0.2  
Fileserver01 = Fileserver01.domain.com  
[/sourcecode]

For those of you who trust me, here are two precompiled editions:  
[64 Bit](http://xipher.dk/Tools/MSTSCTrayx64.exe)  
[32 Bit](http://xipher.dk/Tools/MSTSCTray.exe)

EDIT  
Sorry about that folks, seems as if there was some trouble with the HTML formatting, some of the quotes had been replaced with "AMP;", this should be fixed now.

