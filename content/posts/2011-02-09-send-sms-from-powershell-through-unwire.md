---
categories:
- Everyday
date: "2011-02-09T14:38:39Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  _wp_old_slug: ""
  dsq_thread_id: "6120885334"
status: publish
tags:
- PowerShell
- sms
title: Send SMS from Powershell through Unwire
---
At work we have bought an SMS gateway service through the danish company Unwire, so after we the deal was signed we got the information on how to send email through their services, so in order to test I wrote a quick Powershell script to test it.

Of course all company/Unwire specific data has been removed, since it relates to our company account.

[ps]  
\<#  
 .SYNOPSIS  
 Send SMS through Unwire GW Server

.DESCRIPTION  
 Send SMS Text messages through Unwire.com SMS Gateway services

.PARAMETER Content  
 The text that the message should contain (Strings above 160 Chars will be split) [Mandatory]

.PARAMETER Recipient  
 The recipient phonenumber (Either 8 or 10 digits) [Mandatory]

.PARAMETER Sender  
 The name/number that shows up on the recipients phone (Maximum 11 digits/chars)

.EXAMPLE  
 PS C:\\>Send-SMS -Content "Hello handsome" -Recipient 42424242

.EXAMPLE  
 PS C:\\> Send-SMS -Content "Hello handsome" -Recipient 42424242 -Sender "IT Helpdesk"

.INPUTS  
 System.String,System.Int64,System.String

.OUTPUTS  
 Sends SMS text message

.NOTES

.LINK  
 about\_functions\_advanced

.LINK  
 about\_comment\_based\_help

#\>

function Send-SMS {  
 [CmdletBinding()]  
 param(  
 [Parameter(Position=0, Mandatory=$true)]  
 [System.String]  
 $Content= "Hello There",  
 [Parameter(Position=1, Mandatory=$true)]  
 [System.Int64]  
 $Recipient=42424242,  
 [Parameter(Position=2, Mandatory=$false)]  
 [System.String]  
 $Sender="IT-Drift"  
 )  
 begin {

If ( $Recipient -notmatch '^(\d{8}|\d{10})$' )  
 {Write-Host "Error in Phone Number"  
 break  
 }  
 If ($Sender.Length -gt 11)  
 {Write-Host "Sender name too long"  
 break  
 }  
 [Void] [System.Reflection.Assembly]::LoadWithPartialName("System.Web")  
 $objHTTP = New-Object -ComObject MSXML2.XMLHTTP

$User = "xxxxx"  
 $Password = "xxxxxx"  
 $AppNr = "xxxxx"  
 $smsc = "xxxxxx"  
 $Price = "xxxxx"

$Message = [System.Web.HttpUtility]::UrlEncode([System.Text.Encoding]::GetEncoding("ISO-8859-1").GetBytes("$Content"))  
 $SenderName = [System.Web.HttpUtility]::UrlEncode([System.Text.Encoding]::GetEncoding("ISO-8859-1").GetBytes("$Sender"))

[String] $unwireURL = "xxxxxxxx"  
[String] $UnwireServer = "xxxxxxxx"  
[String] $UnwireHost ="xxxxxxxxxx"

}

process {

$PushString = @"  
user=$user&password=$password&to=$Recipient&text=$Message&smsc=$smsc&price=$Price&type=text&appnr=$AppNr&from=$SenderName  
"@

$objHTTP.open('POST', $UnwireServer, $False)

$objHTTP.setRequestHeader('POST', $unwireURL + ' HTTP/1.1')  
$objHTTP.setRequestHeader('Host', $UnwireHost)  
$objHTTP.setRequestHeader('User-Agent:', 'CP agent')  
$objHTTP.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')  
$objHTTP.setRequestHeader('Content-Length', $($XMLString.Length))  
$objHTTP.send("$PushString")  
$objHTTP.status  
$objHTTP.statusText

}  
}

[/ps]

