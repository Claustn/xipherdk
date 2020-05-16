---
categories:
- Tech Stuff
date: "2009-07-30T22:28:08Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6101745110"
status: publish
tags:
- Excel
- PowerShell
title: '"Editing" Excel using Powershell'
---
At work we have a spreadsheet, where we keep track of our mobile phones, and 3G dongles, in it we keep info about the IMEI code, PUK/PIN codes etc.

Today we found a few SIM cards lying around, the only thing on them was the SIM card number (19 digits). When I went to compare that to the info in the file, I realised that that was not noted in the file, so I had no way to match the SIM with a Phone number, PIN/PUK code.

So I called the telco, they were willing/able to supply me with an csv file containing the phone number, SIM card number, and PUK code.

So I took on the task to update our document, and check that the info in our original document was correct, but doing this for 45+ subscriptions, I thought I would write a script, and what better language than PowerShell.

Here is what I ended up with.

The CSV file contains 3 headers  
MSISDN1,UICCID,PUK1

[ps]

#Location of the Excel file I want to edit  
$FileLoc = "C:\temp\test.xls"  
#Create Excel Com Object, and display it  
$excel = new-object -com Excel.Application  
$excel.visible = $true

#Open Workbook,  
$workbooks = $excel.workbooks.Open($FileLoc)  
$worksheets = $workbooks.Worksheets  
$worksheet = $worksheets.Item(1)  
#Select the range we want to look at  
#In this example, I am only checking within one Column  
$range = $worksheet.Range("M3", "M150")  
#Load CSV file  
$PhoneInfo = Import-Csv c:\Temp\TlfPUK.csv  
#Run through the colums  
foreach($col in $range.Columns){  
 foreach($row in $col.Rows){  
 Foreach ($Phone in $PhoneInfo){  
 #In the CSV file, MSISDN1 represents the phone number, but with the international dial code in front  
 #that is why I use substring to get the last 8 chars and compare it the value of the current cell.  
 #If the phone numbers match, do something  
 If ( $Phone.MSISDN1.substring($Phone.MSISDN1.length -8,8) -eq $row.Text) {  
 #The two colums what will contain the data is set, this is just 4 & 5 columns to the right of the column that contains the phone number  
 $ColIMEI = $row.Column + 5  
 $ColPUK = $row.Column + 4  
 #Here I fill in the SIMCard number (UICCID) and PUK code  
 $worksheet.Cells.Item($row.Row, $ColIMEI) = $Phone.UICCID  
 $worksheet.Cells.Item($row.Row, $ColPUK) = $Phone.PUK1  
 #$Phone.UICCID  
 }  
 }

}

}

[/ps]

