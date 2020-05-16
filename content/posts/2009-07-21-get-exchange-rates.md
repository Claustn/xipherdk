---
categories:
- Everyday
date: "2009-07-21T20:16:39Z"
meta:
  _edit_last: "1"
status: publish
tags: []
title: Get Exchange rates
---
I stumbled on a post about getting Exchange rates from a bank, from an online XML file.. So I thought I would try that with a local bank, I figured out the Danish national bank, has an XML file online with the current rates...

[sourcecode lang="PosH"]  
#Get Exchange Rates from The Danish National Bank  
$wc = New-Object System.Net.WebClient  
$rates = [xml]$wc.DownloadString("http://www.nationalbanken.dk/dndk/valuta.nsf/valuta.xml")  
$MonCode = @{Name="ValutaKode";Expression = {$\_.Code}}  
$Description = @{Name="Valuta";Expression = {"100 " + $\_.desc + " = "}}  
$Kurs = @{Name="Kurs";Expression = {$\_.Rate + " KR"}}  
$rates.exchangerates.dailyrates.currency | Select-Object $MonCode,$Description,$Kurs | ft -AutoSize

[/sourcecode]

Another option would be to use Out-Gridview as well, then you would get the result in a GUI form, instead of in the command prompt

