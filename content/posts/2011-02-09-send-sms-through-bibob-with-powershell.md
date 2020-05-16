---
categories:
- Everyday
date: "2011-02-09T21:55:42Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  _wp_old_slug: ""
  dsq_thread_id: "6148864645"
status: publish
tags:
- bibob
- PowerShell
- sms
title: Send SMS through bibob with Powershell
---
I thought I had posted this script more than a year ago, but apparently I have not. It is quite similar to the script, I posted recently sending SMS messages through unwire, this just allows bibob customers to send messages through bibobs online gateway.

One thing to notice is that you are required to convert your password in an MD5 hash, before you send it. Here is a function to convert your plain passwords into an MD5 hash (Adapted from a script by Dennis Damen)

[ps]  
Function New-MD5Hash {  
 param(  
 [Parameter(Position=0, Mandatory=$true)]  
 [System.String]  
 $inputString  
 )  
 $cryptoServiceProvider = [System.Security.Cryptography.MD5CryptoServiceProvider];  
 $hashAlgorithm = new-object $cryptoServiceProvider  
 $hashByteArray = $hashAlgorithm.ComputeHash($([Char[]]$inputString));  
 foreach ($byte in $hashByteArray) { if ($byte -lt 16) {$result += “0{0:X}” -f $byte } else { $result += “{0:X}” -f $byte }}  
write-host $result  
}  
[/ps]  
Example of usage:

```
New-MD5Hash -inputString "Pa$$W0rd1"
```

Here is the bibob send sms script.  
[ps]  
\<#  
 .SYNOPSIS  
 Send SMS through Bibob (Danish mobile phone company)

.DESCRIPTION  
 Send SMS Text messages through bibob SMS Gateway services

.PARAMETER Content  
 The text that the message should contain (Strings above 160 Chars will be split) [Mandatory]

.PARAMETER Recipient  
 The recipient phonenumber (8 digits) [Mandatory]

.EXAMPLE  
 PS C:\\>Send-SMS -Content "Hello handsome" -Recipient 42424242

.INPUTS  
 System.String,System.Int32

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
 $Content= "Hello Handsome",  
 [Parameter(Position=1)]  
 [System.Int32]  
 $Recipient=42424242  
 )  
 begin {  
 If ($Recipient -notmatch '^(\d{8})$' )  
 {Write-Host "Error in Phone Number"  
 break  
 }  
 $Username = "42424242" # The same as your login to bibob.dk (You cannot spoof you sender number, so it is used as sender as well)  
 $MD5Password = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

}

process {  
$objHTTP = New-Object -ComObject MSXML2.XMLHTTP  
$SoapServer = "https://www.bibob.dk/SmsSender.asmx"

$XMLString = @"  
\<?xml version="1.0" encoding="utf-8"?\>  
\<soap:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"\>  
\<soap:Body\>  
\<SendMessage xmlns="http://www.bibob.dk/"\>  
\<cellphone\>$Username\</cellphone\>  
\<password\>$MD5Password\</password\>  
\<smsTo\>  
\<string\>$recipient\</string\>  
\</smsTo\>  
\<smscontents\>$content\</smscontents\>  
\<fromNumber\>$username\</fromNumber\>  
\</SendMessage\>  
\</soap:Body\>  
\</soap:Envelope\>  
"@

$objHTTP.open('POST', $SoapServer, $False)

$objHTTP.setRequestHeader('Man', 'POST' + ' ' + $SoapServer + ' HTTP/1.1')  
$objHTTP.setRequestHeader('Host', 'www.bibob.dk')  
$objHTTP.setRequestHeader('Content-Type', 'text/xml; charset=utf-8')  
$objHTTP.setRequestHeader('Content-Length', $($XMLString.Length))  
$objHTTP.setRequestHeader('SOAPAction', 'http://www.bibob.dk/SendMessage')

$objHTTP.send($XMLString)  
 }  
}

[/ps]

