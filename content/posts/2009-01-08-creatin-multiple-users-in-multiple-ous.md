---
categories:
- Tech Stuff
date: "2009-01-08T00:39:23Z"
meta:
  _edit_last: "1"
status: publish
tags: []
title: Creating multiple users in multiple OU's
---
The other day I was setting up a few VmWare test environments for work, where I had to test amongst other apps Quest Recovery Manager for AD..

So I had to create some users in Active Directory, I thought I would create a PowerShell script to do that (Must keep your PS skill fresh&nbsp; :)

So I wrote a little code snippet to create a few OU's.

[sourcecode lang="ps"]  
#Gets the default naming context  
$RootDN = (Get-QADRootDSE).DefaultNamingContextDN  
#Name of OU going to be created in the root of "AD"  
$RootOUName = "Apritest"  
#Create "root" OU  
new-qadObject -ParentContainer $RootDN -type 'organizationalUnit' -NamingProperty 'ou' -name $RootOUName  
#Create sub OU's, create as many as you like, you only need to change the -name property in the end of the line  
new-qadObject -ParentContainer "OU=$RootOUName,$RootDN" -type 'organizationalUnit' -NamingProperty 'ou' -name 'Administrators'  
new-qadObject -ParentContainer "OU=$RootOUName,$RootDN" -type 'organizationalUnit' -NamingProperty 'ou' -name 'Marketing'  
new-qadObject -ParentContainer "OU=$RootOUName,$RootDN" -type 'organizationalUnit' -NamingProperty 'ou' -name 'Sales'  
[/sourcecode]

I wanted it to look more like a production environment, by having multiple OU's.  
A fews ago I stumbled upon a script by Brandon Shell to create users, and I thought I could use that... So I modified his code. With my new modified code it is possible to select a "base" OU, then it will go through all sub OU's, and create x amount of users in each. The users will be named after the OU they are created in.. So an example would be TestSales1, TestSales2, TestMarketing1 etc etc.

[sourcecode lang="ps"]  
#  
#  
#Enter the DN path of the OU you want to add users to  
#Note I manually put in the DN of an OU in my test environment, you will have to put one in that exists in your environment

$OuPath = 'OU=Aprismo Users,DC=aprismo,DC=test'

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

[/sourcecode]

