---
categories:
- Everyday
date: "2009-03-30T16:17:26Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6099514386"
status: publish
tags:
- Exchange
- Folder
- Mail
- Outlook
- PowerShell
title: Deleting empty folders in Outlook
---
A friend of mine called me, and asked me for help... He had had a migration go wrong and had ended up with a bunch of .pst files containing 1000's of emtpy folders. He had started cleaning up the .pst's manually, after having spent several hours on one .pst file he decided on another approach, and this is where I came in.

He asked if it was possible to write a script that would delete all the empty folders for him, I said to him that I bet it is possible, just not sure if I can do it, I have never really "scripted" Outlook before..

So I started looking for ways to manipulate data in Outlook and found out that the Outlook.Application COM object allowed me to manipulate folders and messages within Outlook.. Did some quick testing, told my friend that it should be possible, and that I would get back to him...

After some mucking around here is what I ended up with.  
[ps]  
$olApp = new-object -com Outlook.Application  
$namespace = $olApp.GetNamespace("MAPI")  
#Root folder to match, look in your outlook and look at what folders you have.  
#RooFolders could be Mailbox - \<username\>, Public folders, or other mapped in PST's or mailboxes.  
$RootfolderToMatch = "Inbox"

$Global:StopValue = 1

Function ListEmpty {  
Param ($infolder)  
 Foreach ($fldr in $infolder.Folders) {

If ($fldr.Items.Count -eq 0 -and $fldr.Folders.Count -eq 0) {

# I have had to check for system folders, these cannot be deleted via the script, so to prevent the script for erroring out, I check for it.  
 if ($fldr.name -match "Slettet" -or $fldr.name -match "Deleted" -or $fldr.name -match "journal" -or $fldr.name -match "rss"`  
 -or $fldr.name -match "note" -or $fldr.name -match "kalender" -or $fldr.name -match "kontakt" -or $fldr.name -match "udbakke" `  
 -or $fldr.name -match "Sync" -or $fldr.name -match "Server" -or $fldr.name -match "contacts" -or $fldr.name -match "Outbox" )  
 {"Cannot delete systemfolder : """ + $fldr.FolderPath + ""

}

Else {  
 if ($fldr.Parent.name -notmatch "slettet" -and $fldr.Parent.name -notmatch "Deleted" ) {  
 #Remove # from the below line, to actually delete Outlook folders.  
 #$fldr.delete()  
 $fldr.FolderPath + " has been deleted!!"

$Global:StopValue = 0  
 }  
 }  
 }

Else {  
 if ($fldr.name.length -gt 0) {

ListEmpty($fldr)}  
 }

}

}

$RootFolders = $namespace.Folders | ?{$\_.name -match $RootfolderToMatch}

Listempty($RootFolders)

While ($Global:StopValue -eq 0) {  
$Global:StopValue = 1  
Listempty($RootFolders)  
}

[/ps]

I start by initializing the Outlook COM object, and declaring some variables  
[ps]  
$olApp = new-object -com Outlook.Application  
$namespace = $olApp.GetNamespace("MAPI")  
#Root folder to match, look in your outlook and look at what folders you have.  
#RooFolders could be Mailbox - \</username\>\<username\>, Public folders, or other mapped in PST's or mailboxes.  
$RootfolderToMatch = "Inbox"

$Global:StopValue = 1  
[/ps]

I then create a function that takes an Outlook folder as a parameter.  
I then loop through each subfolder, and checks to see if each folder contains any files or other folders, if it finds a folder that does not contain any files/folders it then checks to see if the folder name is something like "outbox, sent items, Journal etc etc." The reason I do this is because these are system folders, and cannot be deleted, so if the script tried to delete one of them, it would come back with an error.

So if the folder that is currently being checked is empty and is not a system folder it will be deleted.(Actually the line where the folder is deleted is commented out, just to make sure there is not accidental deletions) The script then recurses through all subfolders to check for empty folders.

[ps]

Function ListEmpty {  
Param ($infolder)  
 Foreach ($fldr in $infolder.Folders) {

If ($fldr.Items.Count -eq 0 -and $fldr.Folders.Count -eq 0) {

# I have had to check for system folders, these cannot be deleted via the script, so to prevent the script for erroring out, I check for it.  
 if ($fldr.name -match "Slettet" -or $fldr.name -match "Deleted" -or $fldr.name -match "journal" -or $fldr.name -match "rss"`  
 -or $fldr.name -match "note" -or $fldr.name -match "kalender" -or $fldr.name -match "kontakt" -or $fldr.name -match "udbakke" `  
 -or $fldr.name -match "Sync" -or $fldr.name -match "Server" -or $fldr.name -match "contacts" -or $fldr.name -match "Outbox" )  
 {"Cannot delete systemfolder : """ + $fldr.FolderPath + ""

}

Else {  
 if ($fldr.Parent.name -notmatch "slettet" -and $fldr.Parent.name -notmatch "Deleted" ) {  
 #Remove # from the below line, to actually delete Outlook folders.  
 #$fldr.delete()  
 $fldr.FolderPath + " has been deleted!!"

$Global:StopValue = 0  
 }  
 }  
 }

Else {  
 if ($fldr.name.length -gt 0) {

ListEmpty($fldr)}  
 }

}

}

[/PS]

Rootfolders is the initial "path"/folder that the scripts starts to look for empty folders from.  
$namespace.folders is the highest folder level in Outlook, it contains the user mailbox, public folders and any mapped in .pst files, so what I do here is match a single top level folder.

Here is an example, lets say I have opened a .pst file called BusinessCases09, I would set $RootfolderToMatch equal to \<em\>BusinessCases09\</em\> (This is set at the beginning of the script), that means when the ListEmpty function is called for the first time, it will scan through all subfolders of the .pst file.  
[ps]

$RootFolders = $namespace.Folders | ?{$\_.name -match $RootfolderToMatch}

Listempty($RootFolders)

[/ps]

In order to make sure the script runs to all empty folders is deleted, I have set up a while loop to call function until no more empty folders are found.  
[ps]  
While ($Global:StopValue -eq 0) {  
$Global:StopValue = 1  
Listempty($RootFolders)  
[/ps]

