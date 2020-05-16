---
categories:
- Everyday
date: "2011-10-09T20:12:12Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
status: publish
tags: []
title: Updating .Htacces file based on Apache log files
---
I am still seeing massive amounts of referal traffic hitting my site, eating up my bandwidth.. I did not get time to update my .htaccess file for the last 2 days.. and within the last 24 hours I have had more than 6000 hits, generating in almost 24.000 pageviews... Generating more than 1 GB worth of traffic (So at that speed I will reach my 10 GB limit soon)

Looking through the Apache logs, figuring out which sites I get most referral traffic from, getting the hostnames, transforming them into a format that can be used by the Apache rewrite engine in the .htaccess file has been time consuming. So I decided that some powershell magic, might speed up the process a bit.

[ps]  
function Select-FileDialog  
{  
 param(  
 [string]$Title,  
 [string]$Directory,  
 [string]$Filter="All Files (\*.\*)|\*.\*")  
 [System.Reflection.Assembly]::LoadWithPartialName("System.Windows.Forms") | Out-Null  
 $objForm = New-Object System.Windows.Forms.OpenFileDialog  
 $objForm.InitialDirectory = $Directory  
 $objForm.Filter = $Filter  
 $objForm.Title = $Title  
 $Show = $objForm.ShowDialog()  
 If ($Show -eq "OK")  
 {  
 Return $objForm.FileName  
 }  
 Else  
 {  
 Write-Error "Operation cancelled by user."  
 }  
}

#Function to create the http rewrite rules.

Function Create-Rewrite {  
 Param (  
 $Hostname  
 )

$HtaRule = "RewriteCond %{HTTP\_REFERER} ^http://" + "$($hostname.replace(".","\."))" +" [OR]"  
 $script:BlockList += $HtaRule  
}

Function add-htaccess {  
 Param (  
 $HtaRules  
 )  
 (Get-Content $htaccess) | foreach-object {  
 $\_  
 if ($\_ -match "RewriteEngine") {  
 if (!(Select-String -simplematch "$htarules" -Path $htaccess))  
 {  
 $HtaRules  
 }  
 }

} | set-Content $tempFile  
 Copy-Item $tempFile $htaccess  
}

Function Upload-Ftp {  
Param ([Parameter(Position=0, Mandatory=$true)]  
 [ValidateNotNullOrEmpty()]  
 [System.String]  
 $FTPHost,  
 [Parameter(Position=1)]  
 [ValidateNotNull()]  
 $File  
 )  
 $webclient = New-Object System.Net.WebClient  
 $uri = New-Object System.Uri($ftphost)

"Uploading $File..."

$webclient.UploadFile($uri, $File)  
 }

#Variables  
$log = Select-FileDialog -Title "Select an Apache logfile"  
$htaccess = "c:\Temp\.htaccess"  
$tempFile = [IO.Path]::GetTempFileName()  
$URLCount = 15  
$FTPUsername = "Username"  
$FTPPassword = "PassW0rd"

$BlockList = ""  
#Create list of sites to block  
$script:BlockList = @()

#Get the list of URLS in the the logfile, capturing each element into different named capturing groups

$urls = Select-String '^(?\<client\>\S+)\s+(?\<auth\>\S+\s+\S+)\s+\[(?\<datetime\>[^]]+)\]\s+"(?:GET|POST|HEAD) (?\<file\>[^ ?"]+)\??(?\<parameters\>[^ ?"]+)? HTTP/[0-9.]+"\s+(?\<status\>[0-9]+)\s+(?\<size\>[-0-9]+)\s+"(?\<referrer\>[^"]\*)"\s+"(?\<useragent\>[^"]\*)"$' $log |  
 Select -Expand Matches | Foreach { $\_.Groups["referrer"].value }

#Output statistics for the referer hostnames (Only show top 15)  
$urls | group | ForEach -begin { $total = 0 } `  
 -process { $total += $\_.Count; $\_ } |Sort Count | Select Count, Name |  
 Add-Member ScriptProperty Percent { "{0,15:0.00}%" -f (100\*$this.Count/$Total) } -Passthru | select -Last $URLCount

#Getting the base hostnames from the complete URLS, and outputs statistics to the screen.

$hosts = $urls | Select-String '\b[a-z][a-z0-9+\-.]\*://([a-z0-9\-.\_~%!$&()\*+,;=]+@)?(?\<host\>[a-z0-9\-.\_~%]+|\[[a-z0-9\-.\_~%!$&()\*+,;=:]+\])' |  
Select -Expand Matches | Foreach { $\_.Groups["host"].value } | group | sort count | where {($\_.name -notlike "\*xipher.dk\*") -and ($\_.Count -gt 100)} |  
 ForEach -begin { $total = 0 } `  
 -process { $total += $\_.Count; $\_ } | Sort Count | Select Count, Name |  
 Add-Member ScriptProperty Percent { "{0,10:0.00}%" -f (100\*$this.Count/$Total) } -Passthru

Write-Host "List of root hostnames"

$hosts

Foreach ($Url in $hosts) {

Create-Rewrite $url.Name  
}

Foreach ($Block in $script:BlockList) {  
add-htaccess $Block  
}

notepad $htaccess

$script:BlockList

Upload-Ftp -FTPHost "ftp://$($FTPUsername):$($FTPPassword)@xipher.dk/httpdocs/.htaccess" -File $htaccess  
Upload-Ftp -FTPHost "ftp://$($FTPUsername):$($FTPPassword)@xipher.dk/httpdocs/WordPress/.htaccess" -File $htaccess  
[/ps]

Unfortunately my current hosting company, does not allow me to download the log files via FTP, but I have to connect to the Parallels interface and download it manually.. (I have not had the time looking into automating this part yet, so this is still a manual step)  
That is why I added a little function to use a GUI to pick the access\_log file.

[ps]  
function Select-FileDialog  
{  
 param(  
 [string]$Title,  
 [string]$Directory,  
 [string]$Filter="All Files (\*.\*)|\*.\*")  
 [System.Reflection.Assembly]::LoadWithPartialName("System.Windows.Forms") | Out-Null  
 $objForm = New-Object System.Windows.Forms.OpenFileDialog  
 $objForm.InitialDirectory = $Directory  
 $objForm.Filter = $Filter  
 $objForm.Title = $Title  
 $Show = $objForm.ShowDialog()  
 If ($Show -eq "OK")  
 {  
 Return $objForm.FileName  
 }  
 Else  
 {  
 Write-Error "Operation cancelled by user."  
 }  
}  
[/ps]

I then call the function like this:

[ps]  
$log = Select-FileDialog -Title "Select an Apache logfile"  
[/ps]

A little Regex magic runs through the logfiles, and captures the different elements into different named capturing groups, in this step, I expand all referrer hostnames, and put them into the $urls variable

[ps]  
$urls = Select-String '^(?\<client\>\S+)\s+(?\<auth\>\S+\s+\S+)\s+\[(?\<datetime\>[^]]+)\]\s+"(?:GET|POST|HEAD) (?\<file\>[^ ?"]+)\??(?\<parameters\>[^ ?"]+)? HTTP/[0-9.]+"\s+(?\<status\>[0-9]+)\s+(?\<size\>[-0-9]+)\s+"(?\<referrer\>[^"]\*)"\s+"(?\<useragent\>[^"]\*)"$' $log |  
 Select -Expand Matches | Foreach { $\_.Groups["referrer"].value }  
[/ps]  
I modified a script by Joel Bennet, to get a little statistics as well, since there can be 1000's of hostnames, I have selected only to output top 15 by default (using the $URLCount variable.

[ps]  
$urls | group | ForEach -begin { $total = 0 } `  
 -process { $total += $\_.Count; $\_ } |Sort Count | Select Count, Name |  
 Add-Member ScriptProperty Percent { "{0,15:0.00}%" -f (100\*$this.Count/$Total) } -Passthru | select -Last $URLCount  
[/ps]

Then I loop through all the hostnames, and extract the base domain name, using regex again. (Here I choose to ignore all traffic from my own domain name Xipher.dk, and I choose only to look for referral domains, that have generated 100 hits or more

[ps]  
$hosts = $urls | Select-String '\b[a-z][a-z0-9+\-.]\*://([a-z0-9\-.\_~%!$&()\*+,;=]+@)?(?\<host\>[a-z0-9\-.\_~%]+|\[[a-z0-9\-.\_~%!$&()\*+,;=:]+\])' |  
Select -Expand Matches | Foreach { $\_.Groups["host"].value } | group | sort count | where {($\_.name -notlike "\*xipher.dk\*") -and ($\_.Count -gt 100)} |  
 ForEach -begin { $total = 0 } `  
 -process { $total += $\_.Count; $\_ } | Sort Count | Select Count, Name |  
 Add-Member ScriptProperty Percent { "{0,10:0.00}%" -f (100\*$this.Count/$Total) } -Passthru  
[/ps]

The script expects to find a .htaccess file in c:\temp containing at least the following two lines:

RewriteEngine On  
RewriteRule (.\*) http://%{REMOTE\_ADDR}/$ [R=301,L]

