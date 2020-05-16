---
categories:
- Everyday
date: "2009-09-04T23:33:28Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6099782114"
status: publish
tags:
- Dism
- PoSH
- PS
title: Windows 7 & RSAT
---
I finally found some time today to upgrade my primary work machine to Windows 7 RTM (Yeah). Since I do a lot of system administration one of the first things I put on there was my Powershell tools and RSAT. I went and got the RSAT tools from the MS website, I install it, go into "turn on/off Windows features" menu and click the to level check for RemoteAdministrationTools, in the hope that it would select all the admin tools, but no!  
I have to check each and everyone..

Then I remembered a while back on the Minasi forum, it was discussed if there was an easier way, and Mark said: "There is probably a one line DISM command for that", so I took a look at DISM, and found that to my (albeit limited) knowledge there was no way to install all the tools at once.

So I thought to myself, well I guess I have to do it in powershell then, so I started out writing a small script to enable all the admin tools one by one.

Then suddenly Marks words sprung to mind again, "there is probably a DISM oneliner to do this), so I rewrote my little script to a oneliner, here is what I ended up with:  
[ps]  
#  
#Enable RSAT features  
dism /online /get-features | Select-String -Pattern remote\* | %{$Exec = "dism.exe /online /enable-feature /featurename:RemoteServerAdministrationTools /featurename:" + ($\_).ToString().Replace('Feature Name : ',''); Invoke-expression $Exec}  
[/ps]  
Dism requires admin permissions, so you need to run this elevated.

Here is what it does:  
[ps]  
dism /online /get-features  
[/ps]  
Uses DISM to retrieve a list of available "features"  
That list is then piped into a Select-string statement, that selects all that contain the string "Remote\*", which luckily all the admin tools does :)

Each of the selected strings are then "piped along the pipeline" in the next command, which is a bit more complicated:

[ps]  
%{$Exec = "dism.exe /online /enable-feature /featurename:RemoteServerAdministrationTools /featurename:" + ($\_).ToString().Replace('Feature Name : ',''); Invoke-expression $Exec}  
[/ps]  
% = foreach, so foreach string the Select-String returns, I start to build the $exec variable, the first part of the variable is just text **"dism.exe /online /enable-feature /featurename:RemoteServerAdministrationTools /featurename:"** this next part is where the feature name is extracted: **($\_).ToString().Replace('Feature Name : ','')**. What it does is it converts the output from Select\_String ($\_) into a string, on that string it replaces "'Feature Name : ", with nothing "", so that the $exec variable will end up containing the the initial string, plus the name of the "current" "feature" in the pipeline, giving a full command that can be executed, something like this **dism.exe /online /enable-feature /featurename:RemoteServerAdministrationTools /featurename:RemoteServerAdministrationTools-Roles-AD**. Then it is "executed" by the Invoke-expression $exec.

So since what I do is just creating a regular commandline expression and executing it, there is not reason why I cannot just export the result to a batch file, which I can edit and run.  
[ps]  
#  
#Output DISM RSAT commands to file  
dism /online /get-features | Select-String -Pattern remote\* | %{$Exec = "dism.exe /online /enable-feature /featurename:RemoteServerAdministrationTools /featurename:" + ($\_).ToString().Replace('Feature Name : ','');Out-File -InputObject $Exec -FilePath c:\test.bat}  
[/ps]

Hope this was usefull

