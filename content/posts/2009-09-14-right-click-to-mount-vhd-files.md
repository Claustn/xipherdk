---
categories:
- Tech Stuff
date: "2009-09-14T13:41:54Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6099514462"
status: publish
tags:
- PowerShell
- vhd
- Windows 7
title: Right click to mount vhd files
---
I have just upgraded my machines to Windows 7 RTM, before I did the reinstall I did full backup of my old Vista machines, of course I figured out that I had not gotten all the files out of the Vista machines before I upgraded, So I had to restore some files from the resulting .vhd backup images. But when I started Windows 7 restore, it did not recognize the backup folders.. So I thought I would just mount the vhd backup files manually, by going into Computer Management -\> Disk management. But after having done that a few times, I thought it was too much hassle, so I decided to write a little PowerShell script to mount the vhd files for me.  
[ps]  
# PowerShell function to create a script file for diskpart  
# and the execute diskpart with the scriptfile  
function Mount-Vhd {param([String]$InputFile)  
"SELECT VDISK FILE=""$InputFile""" + "`r`n" + "ATTACH VDISK" | Out-File $env:TEMP\Mountvhd.txt -Encoding "ASCII"  
Invoke-Expression -Command "Diskpart.exe /s $env:TEMP\Mountvhd.txt"  
}  
Mount-Vhd $args[0]  
[/ps]  
The above script has to be saved as "Mount-vhd.ps1"

But I wanted something even more simple, I just wanted to be able to right-click a .vhd file and choose open, so I created this little .reg file, that will allow you to right click a .vhd file and choose open.

Windows Registry Editor Version 5.00

[HKEY\_CLASSES\_ROOT\.vhd]

[HKEY\_CLASSES\_ROOT\.vhd\shell]  
@="Open VHD"

[HKEY\_CLASSES\_ROOT\.vhd\shell\Open VHD]  
@="Open &VHD"

[HKEY\_CLASSES\_ROOT\.vhd\shell\Open VHD\command]  
@=hex(2):22,00,43,00,3a,00,5c,00,57,00,69,00,6e,00,64,00,6f,00,77,00,73,00,5c,\  
 00,53,00,79,00,73,00,74,00,65,00,6d,00,33,00,32,00,5c,00,57,00,69,00,6e,00,\  
 64,00,6f,00,77,00,73,00,50,00,6f,00,77,00,65,00,72,00,53,00,68,00,65,00,6c,\  
 00,6c,00,5c,00,76,00,31,00,2e,00,30,00,5c,00,70,00,6f,00,77,00,65,00,72,00,\  
 73,00,68,00,65,00,6c,00,6c,00,2e,00,65,00,78,00,65,00,22,00,20,00,2d,00,57,\  
 00,69,00,6e,00,64,00,6f,00,77,00,53,00,74,00,79,00,6c,00,65,00,20,00,68,00,\  
 69,00,64,00,64,00,65,00,6e,00,20,00,2d,00,66,00,69,00,6c,00,65,00,20,00,22,\  
 00,63,00,3a,00,5c,00,74,00,6f,00,6f,00,6c,00,73,00,5c,00,4d,00,6f,00,75,00,\  
 6e,00,74,00,2d,00,76,00,68,00,64,00,2e,00,70,00,73,00,31,00,22,00,20,00,22,\  
 00,25,00,31,00,22,00,00,00

When you import the file, all the hex values are converted back to ascii chars, which you can see under [HKEY\_CLASSES\_ROOT\.vhd\shell\Open VHD\command] of course

Here is what will be written:

"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -WindowStyle hidden -file "c:\tools\Mount-vhd.ps1" "%1"

As you can see it expects the Mount-Vhd powershell file to be located in C:\tools\ in order to work. I have so far not been able to make it work, without referencing the file directly.

But when you have imported the .reg file, you can just navigate to [HKEY\_CLASSES\_ROOT\.vhd\shell\Open VHD\command] and change the path to anything you like.

You can download both files from here: [http://xipher.dk/Tools/ImportVHDsettings.zip](http://xipher.dk/Tools/ImportVHDsettings.zip)

