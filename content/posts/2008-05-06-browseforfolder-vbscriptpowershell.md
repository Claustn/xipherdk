---
categories:
- Tech Stuff
date: "2008-05-06T08:35:47Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6111201126"
status: publish
tags:
- BrowseForFolder
- PoSH
- PowerShell
title: BrowseForFolder VBScript/PowerShell
---
I had to run a script the other day, that I wrote some time ago to change some files in a folder, the script uses the Shell.Application COM object BrowseForFolder method, to show a GUI that lets you browse through the directory structure and choose a folder. I thought that it might come in handy one day, to have this functionality in PowerShell as well.

First off I remembered seeing someone creating a PowerShell script, that read the input from WSH's InputBox() by using the COM object MSScriptControl.ScriptControl.

[sourcecode language='CSS']  
$a = new-object -comobject MSScriptControl.ScriptControl  
$a.language = "vbscript"  
$a.addcode("function getInput() getInput = inputbox(`"Message box prompt`",`"Message Box Title`") end function" )  
$b = $a.eval("getInput")

[/sourcecode]  
So I thought that if you can do the simple script above, you can do something a little more advanced.

I started out by looking at the example I had allready created, just copied that to the $a.addcode line of code, that didn't work of course. After playing with it for a while, I figured out that it was problems with my ' " ''s, they had to be escaped in order for the code to be evaluated correctly, so I ended up with something like this.

[sourcecode language='CSS']  
Function Get-Folder {  
$a = New-Object -com MSScriptControl.ScriptControl  
$a.language = "vbscript"  
$a.addcode("Function ShowBrowse() `r Set objShell = CreateObject(`"Shell.Application`") `r Set objFolder = objShell.BrowseForFolder (0, `"Choose a Folder:`", 0, `"`") `r If objFolder Is Nothing Then `r WScript.Quit `r End If `r Set objFolderItem = objFolder.Self `r ShowBrowse = objFolderItem.Path `r End Function" )  
$b = $a.eval("ShowBrowse")  
Return $b  
}  
[/sourcecode]  
The above has been incapsulated into a function for ease of use.

I posted the above script on the Minasi forum, and only minutes went by, before I had the first reply.. It was from no other than Don Jones himself, asking if I knew I could use COM objects directly in PowerShell, I thought to my self, Yes, I have just called one in the script above.

Then it dawned on me, I used PowerShells functionality to create a "MSScriptControl.ScriptControl" object, that then creates a "Shell.Application" object in WSH "MSScriptControl.ScriptControl" runspace. Why not create the "Shell.Application" object directly.  
That gave me a lot simpler code, and a chance to slap myself for not thinking of that earlier, here is what it looks like

[sourcecode language='CSS']  
Function Get-folder {  
$a = New-Object -ComObject "Shell.application"  
$b = $a.BrowseForFolder(0,"Choose Folder",0,"")  
$c = $b.Self  
$c.path  
}

[/sourcecode]

