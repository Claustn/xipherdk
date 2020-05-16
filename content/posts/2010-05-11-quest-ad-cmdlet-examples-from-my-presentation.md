---
categories:
- Everyday
date: "2010-05-11T22:06:28Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6107704446"
status: publish
tags:
- PowerShell
title: Quest AD Cmdlet examples from my Presentation
---
Example #1  
List users that expires within X days  
[ps]  
$DaysToExpire = 14  
$MaxPassAge = (Get-QADObject (Get-QADRootDSE).defaultNamingContextDN).MaximumPasswordAge.days  
[array]$a = Get-QADUser -Enabled -PasswordNeverExpires:$false -SizeLimit 0 -Email \* |Select-Object Name,Email,@{Name="Expires";Expression={ $MaxPassAge - $\_.PasswordAge.days }} | where {$\_.Expires -lt 0} | Sort-Object expires  
[/ps]

Example #2  
Locate that Hyper-V Host  
[ps]  
Get-QADComputer | Where {$\_.OSname -match "2008"} | % { Get-Service -ComputerName $\_.Name} | where {$\_.Displayname -match "hyper"} | select Machinename, Displayname

#Kirk Munroe pointed out that Hyper-V actually registers ans SCP (Service Connection Point)  
Get-QADObject -Name 'Microsoft Hyper-V' -Type serviceConnectionPoint | Get-QADComputer -Identity {$\_.ParentContainerDN}  
[/ps]

Example #3  
Create test users and OU's in AD  
[ps]  
#Gets the default naming context  
$RootDN = (Get-QADRootDSE).DefaultNamingContextDN  
#Name of OU going to be created in the root of "AD"  
$RootOUName = "MinasiTest"  
#Create "root" OU  
new-qadObject -ParentContainer $RootDN -type 'organizationalUnit' -NamingProperty 'ou' -name $RootOUName  
#Create sub OU's, create as many as you like, you only need to change the -name property in the end of the line  
new-qadObject -ParentContainer "OU=$RootOUName,$RootDN" -type 'organizationalUnit' -NamingProperty 'ou' -name 'Administrators'  
new-qadObject -ParentContainer "OU=$RootOUName,$RootDN" -type 'organizationalUnit' -NamingProperty 'ou' -name 'Marketing'  
new-qadObject -ParentContainer "OU=$RootOUName,$RootDN" -type 'organizationalUnit' -NamingProperty 'ou' -name 'Sales'  
new-qadObject -ParentContainer "OU=$RootOUName,$RootDN" -type 'organizationalUnit' -NamingProperty 'ou' -name 'IT'  
new-qadObject -ParentContainer "OU=$RootOUName,$RootDN" -type 'organizationalUnit' -NamingProperty 'ou' -name 'HR'

#  
#  
#  
#Enter the DN path of the OU you want to add users to  
#Note I manually put in the DN of an OU in my test environment, you will have to put one in that exists in your environment

#$OuPath = 'OU=Aprismo Users,DC=aprismo,DC=test'  
$OuPath = "OU=$RootOUName,$RootDN"

#Number of users to create in each OU

$No = 5

Function AddUsers  
{  
param([string]$OUDN, [string]$OUName)

foreach($user in 1..$No)  
{  
$UserName = "Test$OUName$User"  
$UserPassword = "P@ssw0rd123!"  
$ExpiresOn = $((Get-Date).AddMonths(1))  
$userDescription = "User added for Performance Testing on $(Get-date) and Expires on $ExpiresOn"

$user = New-QADUser -name $UserName `  
-SamAccountName $UserName `  
-Description $UserDescription `  
-ParentContainer $OUDN `  
-UserPassword $UserPassword

$user | Set-QADUser -AccountExpires ((Get-Date).AddMonths(1))  
}  
}

#This foreach loops through all OU's "below" $OUPath  
Foreach ($OU in Get-QADObject -SearchRoot $OUPath -Type 'organizationalUnit'){  
Addusers $OU.DN $OU.Name  
}

[/pa]

Example #4  
Rename Groups in AD  
[ps]  
Get-QADGroup -Name "xyz\*" | %{Set-QADGroup -Identity $\_ -SamAccountName ($\_.Name).Replace("xyz","abc") -whatif ;Rename-QADObject -Identity $\_ -NewName ($\_.Name).Replace("xyz","abc") -WhatIf }  
[/ps]

Example #5  
Output a list of servers in a Excel documents, with ping stats and if the machine is disabled/enabled  
[ps][/ps]  
#$serverlist = Get-QADComputer -LdapFilter '(!(userAccountControl:1.2.840.113556.1.4.803:=2))' | where {$\_.Osname -like "\*server\*"}  
$serverlist = Get-QADComputer -IncludedProperties pwdLastSet -SizeLimit 0 | where {$\_.Osname -like "\*server\*"}

$erroractionpreference = "SilentlyContinue"  
$a = New-Object -comobject Excel.Application  
$a.visible = $True

$b = $a.Workbooks.Add()  
$c = $b.Worksheets.Item(1)

$c.Cells.Item(1,1) = "Machine Name"  
$c.Cells.Item(1,2) = "OS Name"  
$c.Cells.Item(1,3) = "IP Address"  
$c.Cells.Item(1,4) = "Ping Status"  
$c.Cells.Item(1,5) = "Password last set"  
$c.Cells.Item(1,6) = "Enabled/Disabled"  
$c.Cells.Item(1,7) = "Physical/Virtual"

$d = $c.UsedRange  
$d.Interior.ColorIndex = 19  
$d.Font.ColorIndex = 11  
$d.Font.Bold = $True

$intRow = 2

$colComputers = $serverlist  
foreach ($strComputer in $colComputers)  
{  
$c.Cells.Item($intRow, 1) = $strComputer.Name

$ping = new-object System.Net.NetworkInformation.Ping  
$Reply = $ping.send($strComputer.Name)

if ($Reply.status –eq “Success”)  
{  
 $machineType = Get-WmiObject -ComputerName $strComputer.Name -Class Win32\_BIOS

If ($strComputer.AccountIsDisabled) {$enab = "Disabled"} else {$enab = "Enabled"}  
 if ($strComputer.pwdLastSet -le (Get-Date).AddDays(-90)) {$age = "Older than 90 Days" ; $fgColor = 3} else {$age = "Less than 90 days"; $fgColor = 0}

if ($machineType.Serialnumber -like "\*vmware\*") {$type = "VMware"}  
 Elseif ($machineType.Version -like "\*VRTUAL\*") {$type = "Hyper-V"}  
 Elseif (!($machineType.Version)) {$type = "N/A"}  
 else {$type = "Physical"}

$c.Cells.Item($intRow, 2) = $strComputer.OSName  
$c.Cells.Item($intRow, 3) = $Reply.Address.ToString()  
$c.Cells.Item($intRow, 4) = "Online"  
$c.Cells.item($intRow, 5).Interior.ColorIndex = $fgColor  
$c.Cells.Item($intRow, 5) = $age  
$c.Cells.Item($intRow, 6) = $enab  
$c.Cells.Item($intRow, 7) = $type  
$Reply = ""  
$intRow = $intRow + 1

}  
else  
{  
 $machineType = Get-WmiObject -ComputerName $strComputer.Name -Class Win32\_BIOS

If ($strComputer.AccountIsDisabled) {$enab = "Disabled"} else {$enab = "Enabled"}  
 if ($strComputer.pwdLastSet -le (Get-Date).AddDays(-90)) {$age = "Older than 90 Days" ; $fgColor = 3} else {$age = "Less than 90 days"; $fgColor = 0}  
 if ($machineType.Serialnumber -like "\*vmware\*") {$type = "VMware"}  
 Elseif (!($machineType.Version)) {$type = "N/A"}  
 Elseif ($machineType.Version -like "\*VRTUAL\*") {$type = "Hyper-V"}  
 else {$type = "Physical"}

$c.Cells.Item($intRow, 2) = $strComputer.OSName  
$c.Cells.Item($intRow, 3) = $Reply.Address.ToString()  
$c.Cells.Item($intRow, 4) = "Offline"  
$c.Cells.item($intRow, 5).Interior.ColorIndex = $fgColor  
$c.Cells.Item($intRow, 5) = $age  
$c.Cells.Item($intRow, 6) = $enab  
$c.Cells.Item($intRow, 7) = $type  
$Reply = ""

$intRow = $intRow + 1

}  
$d.EntireColumn.AutoFit()

}  
[ps][/ps]

