---
categories:
- Tech Stuff
date: "2009-03-01T22:12:29Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6099514334"
status: publish
tags: []
title: Set file permissions with Set-ACL
---
One of the guys at the minasi forum had a problem with setting permissions on multiple .ini files (Had to add the Deny setting to read for a certain group), he joked that I was probably able to write a powershell script to do it.. So I took the challenge, and wrote this little script.

This script will lrecurse through the InitialFolder and all of its subfolders for Desktop.Ini files, it will then loop through each of the files, and add the DENY setting on the read permission for the local administrators group.  
[sourcecode lang="posh"]  
$InitialFolder = "c:\scripts\test\"

$Files = Get-ChildItem $InitialFolder -include desktop.ini -recurse -force

Foreach ($File in $Files) {  
$colRights = [System.Security.AccessControl.FileSystemRights]"Read"

$InheritanceFlag = [System.Security.AccessControl.InheritanceFlags]::None  
$PropagationFlag = [System.Security.AccessControl.PropagationFlags]::None

$objType =[System.Security.AccessControl.AccessControlType]::DENY

$objUser = New-Object System.Security.Principal.NTAccount("Administrators")

$objACE = New-Object System.Security.AccessControl.FileSystemAccessRule ($objUser, $colRights, $InheritanceFlag, $PropagationFlag,$objType)

$objACL = Get-ACL $File  
$objACL.AddAccessRule($objACE)

Set-ACL $file $objACL  
}  
[/sourcecode]

