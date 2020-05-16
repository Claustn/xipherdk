---
categories:
- Everyday
date: "2009-07-19T21:13:13Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6194545881"
status: publish
tags:
- Excel
- PowerShell
title: Create Excel Spreadsheet
---
The other day I was tasked with creating a spreadsheet containing a list of all servers in our network, manager wanted a "sheet" for each computer, and an "index" sheet with links to all the other sheets.

Since we have more than 150 servers, there was no way I was going to create this list by hand... So I wrote a quick and dirty little Powershell function.

[sourcecode lang="PosH"]

Function Fill-Excel {  
BEGIN {  
 $xl = new-object -comobject excel.application  
 $xl.Visible = $true  
 #I specify an existing filename, since creating the link in Excel requires a filename  
 $wb = $xl.Workbooks.Open("C:\Temp\test.xlsx")  
 $ws = $wb.Worksheets.Item(1)  
 $xl.ActiveSheet.Name = "List"  
 $row = 1  
 }

Process {  
$\_  
$q = $\_  
$z = $q.ToUpper()  
$xl.Sheets.Add()  
$xl.ActiveSheet.Name = $z  
$wh = $xl.ActiveSheet  
$wh.Cells.Item(1,1) = "=HYPERLINK(`"[test.xlsx]List!A1`";`"Back To List`")"  
$ws.Cells.Item($row,1) = "=HYPERLINK(`"[test.xlsx]$z!A1`";`"$z`")"  
$row++  
}  
}

$s= "Computer1","CompPUTER2","comPUTer3"  
$s | Fill-Excel  
[/sourcecode]

This will open the xslx filed called test.xlsx under c:\temp and create an index sheet called "list", and a sheet for each object that is passed to the function, it also creates a Link in A1 that refers back to the index sheet. On the Index sheet a link to each new sheet is created.

In the above example I "manually" pipe in some text string, but it could also be something like this:

[sourcecode lang="PosH"]  
#  
#  
$s = Get-QADComputer | Where {$\_.OSname -match "Server"} | select name  
[/sourcecode]

So since it is a function, you can pass virtually any string to it, and it will populate the excel sheet for you.

