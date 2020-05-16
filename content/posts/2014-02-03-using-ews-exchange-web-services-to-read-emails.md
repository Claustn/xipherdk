---
categories:
- Everyday
date: "2014-02-03T21:27:21Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6100669124"
status: publish
tags: []
title: Using EWS (Exchange Web Services) to read email subjects.
---
Today I had a discussion with a vendor, they are delivering a solution that reads email from a specific email account on Exchange, and create a ticket in our helpdesk system. Often the email subjects shows up mangled in the helpdesk system, and the particular vendor blames Exchange EWS for not messing up the data.. I had a hard time believing that, so I set out to prove it is not Exchange that is a fault.

First off I needed to download Exchange EWS, which is a separate download from Microsoft.

So just download and install the MSI file.

Then it is time to fire up PowerShell, first thing we need to do is load the EWS assembly

```
Add-Type -Path "C:\Program Files\Microsoft\Exchange\Web Services\1.2\Microsoft.Exchange.WebServices.dll"
```

Then we need to specify a username, Password, domain and the mailbox that we want to look at. There are several ways to do this, the simplest way is just writing this in clear text.

```
$Username = "PeterJ" $Password = "PetersSecurePassword" $Domain = "PetersDomain" $Email = "PeterJ@PetersDomain.com"
```

Or you could query for it:

```
$cred = (Get-Credential).GetNetworkCredential()
```

Then we have to create a EWS Exchange object.

```
$EWS = New-Object Microsoft.Exchange.WebServices.Data.ExchangeService -ArgumentList "Exchange2010\_SP1"
```

Possible options for ExchangeService types are Exchange2007\_SP1, Exchange2010, Exchange2010\_SP1 or Exchange2010\_SP2.

Then we have to pass credentials to the $EWS objects depending on how we chose to supply the password initially, we have 2 options again.

Here is the way if we supplied the information in clear text

```
$EWS.Credentials = New-Object System.Net.NetworkCredential -ArgumentList $Username, $Password, $Domain
```

Here is the way if we prompt for the username/password with Get-Credential

```
$EWS.Credentials = New-Object System.Net.NetworkCredential -ArgumentList $cred.UserName, $cred.Password, $cred.Domain
```

Then we use the AutoDiscoverURL method to look up the Exchange Server URL endpoint.

```
$EWS.AutodiscoverUrl($Email)
```

In this example we will iterate through the inbox and list the first 20 items and output the subjects.

```
$results = $EWS.FindItems("Inbox",( New-Object Microsoft.Exchange.WebServices.Data.ItemView -ArgumentList 20 )) $results.Items | ForEach-Object { $\_.Subject }
```

Here is the script in its entirety.

```
Add-Type -Path "C:\Program Files\Microsoft\Exchange\Web Services\1.2\Microsoft.Exchange.WebServices.dll" $Email = "ctn@appension.dk" $EWS = New-Object Microsoft.Exchange.WebServices.Data.ExchangeService -ArgumentList "Exchange20101\_SP1" $cred = (Get-Credential).GetNetworkCredential() $EWS.Credentials = New-Object System.Net.NetworkCredential -ArgumentList $cred.UserName, $cred.Password, $cred.Domain #Use Autodiscover to find the right URL endpoint $EWS.AutodiscoverUrl($Email) #Search the inbox $results = $EWS.FindItems("Inbox",( New-Object Microsoft.Exchange.WebServices.Data.ItemView -ArgumentList 20 )) $results.Items | ForEach-Object { $\_.Subject }
```

After analyzing the output from Exchange many times, I am convinced that the vendor is mistaken when he claims Exchange is screwing up the data.

