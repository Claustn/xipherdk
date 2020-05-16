---
categories:
- Everyday
date: "2010-09-08T22:00:53Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  _wp_old_slug: ""
  dsq_thread_id: "6099514084"
status: publish
tags:
- PowerShell
title: My account keeps getting locked out, where did I use it ??
---
A few weeks ago my admin account kept getting locked out, after I had changed my password.. So I assumed I had used it somewhere to either run a service or a scheduled task on a test server. But where???

So I decided to write a little PowerShell script to help me find out where, and give me a chance to be more proactive next time I need to change my Password, or the password of one of our service accounts.

So I decided to try to write a script that connects to all our servers, and list all services running under domain credentials, and do the same for scheduled tasks. In our environment we have an issue with some COM+ objects that are either running as a domain admin user, or as "interactive" meaning that a domain admin user has to be logged into a machine at all times for the app to work.. (Yes I have yelled and screamed at the developers).

So what this script does, it connects info from domain machines, and puts it in a Excel spreadsheet.  
In order to run the script, you need to have Powershell V2, Quest AD cmdlet (preferably ver. 1.2 or above) and permissions on the remote machines to connect via WMI.

[ps]  
#==========================================================================  
# NAME: GetServiceScheduledtaskandCom.ps1  
#  
# Version: 0.9  
# Date: 07/09/10  
#  
# COMMENT:  
# This script is based on a script by Stephen Wheet, a lot of the code has been rewritten, and  
# and a bunch of code has been added.  
# The script connects to remote computers, and lists all services running under domain accounts,  
# it does the same for Scheduled Tasks and COM+ Applications.  
#  
# The Script required the QUEST AD cmdlets and excel installed on the computer  
#  
# Missing features: IIS APP Pool Credentials, Logged In Users  
#==========================================================================  
$domainname = (Get-QADRootDSE.Domain).Domain.ToString()

$searchroot = "appension.local/"

$ErrorActionPreference = "SilentlyContinue"

[System.Threading.Thread]::CurrentThread.CurrentCulture = "en-US"  
$ErrorActionPreference = "SilentlyContinue"  
$ExcelAPP = New-Object -comobject Excel.Application  
$ExcelAPP.visible = $True

$ExcelWB = $ExcelAPP.Workbooks.Add()  
$ServiceSheet = $ExcelWB.Worksheets.Item(1)  
$ScheduledSheet = $ExcelWB.Worksheets.Item(2)  
$COMPlusSheet = $ExcelWB.Worksheets.Item(3)

$ServiceSheet.Name = "Services"  
$ScheduledSheet.Name = "Scheduled Tasks"  
$COMPlusSheet.Name = "COM+"

# Accounts to ignore  
$IgnoreAcct =  
 "NT AUTHORITY\LOCAL SERVICE",  
 "NT AUTHORITY\LOCALSERVICE",  
 "LocalSystem",  
 ".\*",  
 "NT AUTHORITY\NETWORK SERVICE",  
 "NT AUTHORITY\NetworkService"

$DefaultCOMObjects =  
 'COM+ Utilities',  
 'COM+ QC Dead Letter Queue Listener',  
 'COM+ Explorer',  
 'COM+ Utilities (32 bit)',  
 'IIS Out-Of-Process Pooled Applications',  
 '.NET Utilities'

#Place Headers on out-put file

$ServiceSheet.Cells.Item(1,1) = "Server"  
$ServiceSheet.Cells.Item(1,2) = "Service"  
$ServiceSheet.Cells.Item(1,3) = "Account"

$ScheduledSheet.Cells.Item(1,1) = "Server"  
$ScheduledSheet.Cells.Item(1,2) = "Task"  
$ScheduledSheet.Cells.Item(1,3) = "Next Run Time"  
$ScheduledSheet.Cells.Item(1,4) = "Account"

$COMPlusSheet.Cells.Item(1,1) = "Server"  
$COMPlusSheet.Cells.Item(1,2) = "COM+ Name"  
$COMPlusSheet.Cells.Item(1,3) = "Identity"

$d = $ServiceSheet.UsedRange  
$d.Interior.ColorIndex = 19  
$d.Font.ColorIndex = 11  
$d.Font.Bold = $True

$l = $ScheduledSheet.UsedRange  
$l.Interior.ColorIndex = 19  
$l.Font.ColorIndex = 11  
$l.Font.Bold = $True

$q = $COMPlusSheet.UsedRange  
$q.Interior.ColorIndex = 19  
$q.Font.ColorIndex = 11  
$q.Font.Bold = $True

$intRowSer = 2  
$intRowSch = 2  
$intRowCom = 2

#Get all the servers from the specified OU  
$Servers = get-QADComputer -SearchRoot $searchroot -OSName "\*Server\*" #| where {$\_.name -like "appo\*"} # change the container based on site.

Foreach ($server in $Servers ) {  
#$ErrorActionPreference = "SilentlyContinue"  
 $serverFQDN = $server.dnsname  
 #Test ping server to make sure it's up before querying it  
 if (Test-Connection $serverFQDN -Quiet -Count 1){  
 Write-Host "`n ************* $serverFQDN Online `n" -ForegroundColor Red  
 Write-Host "\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* $($server.name) Services \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*"  
 # Get service info  
 $error.clear()  
 $services = gwmi win32\_service -computer $serverFQDN -property name, startname, caption |  
 % {

$name = $\_.Caption  
 $Acctname = $\_.StartName  
 If ( $IgnoreAcct -notcontains $Acctname )  
 {  
 Write-host "$Name $Acctname"  
 $ServiceSheet.Cells.Item($intRowSer, 1) = $serverFQDN  
 $ServiceSheet.Cells.Item($intRowSer, 2) = $name  
 $ServiceSheet.Cells.Item($intRowSer, 3) = $Acctname  
 $intRowSer++  
 } #end If

} #end ForEach

#Write log if no access  
 if (!$?) {  
 $ServiceSheet.Cells.Item($intRowSer, 1) = $serverFQDN  
 $ServiceSheet.Cells.item($intRowSer, 2).Interior.ColorIndex = 3  
 $ServiceSheet.Cells.Item($intRowSer, 2) = "Access Denied/Offline"  
 $ServiceSheet.Cells.Item($intRowSer, 3) = $Error  
 #$errmsg = "$serverFQDN,No RPC server,ACCESS DENIED"  
 Write-Host $Error  
 $intRowSer++  
 } # end Error

$schtask = Schtasks.exe /query /s $serverFQDN /V /FO CSV | ConvertFrom-Csv  
Write-Host "\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* $($server.name) Scheduled Tasks \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*"  
if ($schtask) {

Foreach ($sch in $schtask) {  
#$Sch."Run As User"  
if ($Sch."Run As User" -like "$($domainname)\*") {  
Write-Host $serverFQDN ($Sch.TaskName).replace("\","") $Sch.'Run As User'

$ScheduledSheet.Cells.Item($intRowSch,1) = $serverFQDN  
$ScheduledSheet.Cells.Item($intRowSch,2) = ($sch.TaskName).replace('\','')  
$ScheduledSheet.Cells.Item($intRowSch,3) = $Sch.'Next Run Time'  
$ScheduledSheet.Cells.Item($intRowSch,4) = $sch.'Run As User'  
$intRowSch++

}

}  
}  
 Write-Host "\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* $($server.name) COM+ Applications \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*"  
 #$targetComputer = $serverFQDN  
 $comAdmin = New-Object -comobject COMAdmin.COMAdminCatalog  
 $comAdmin.Connect($serverFQDN)  
 $apps = $comAdmin.GetCollection("Applications")

$apps.Populate()

foreach ($app in $apps) {  
 If ( $IgnoreAcct -notcontains ($app.Value("Identity")) -and ( $DefaultCOMObjects -notcontains ($app.Value("Name")) )) {  
 $q.Cells.Item($intRowCom,1) = $serverFQDN  
 $q.Cells.Item($intRowCom,2) = $app.Value("Name")  
 $q.Cells.Item($intRowCom,3) = $app.Value("Identity")  
 Write-Host $app.Value("Name") $app.Value("Identity")  
 $intRowCom++  
 }  
 }  
}

Else  
 {  
 Write-Host "$serverFQDN \*\*\*\*\*\*\*\*\*\*\*\*\* OFFLINE"  
 } #end Else  
}  
#end ForEach  
$d.EntireColumn.AutoFit()  
$l.EntireColumn.AutoFit()  
$q.EntireColumn.AutoFit()  
[/ps]

