---
categories:
- Everyday
date: "2010-05-11T21:58:16Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
status: publish
tags:
- PowerShell
title: .Net Examples from Presentation
---
Example #1 (From Lee Holmes' Powershell cookbook)

"Compile C# code on the fly"  
[ps]

$code =@'  
using System.Management.Automation;

[Cmdlet("Write", "InputObject")]  
public class MyWriteInputObjectCmdlet : Cmdlet  
{  
 [Parameter]  
 public string Parameter1;

[Parameter(Mandatory = true, ValueFromPipeline=true)]  
 public string InputObject;

protected override void ProcessRecord()  
 {  
 if (Parameter1 != null)  
 WriteObject(Parameter1 + ":" + InputObject);  
 else  
 WriteObject(InputObject);  
 }  
}  
'@

Add-Type -TypeDefinition $code -OutputAssembly .\ExampleModule.dll

Import-Module .\ExampleModule.dll

[/ps]

Example #2 (From Lee Holmes' Powershell cookbook)  
Showing how static functions are available directly

[ps]  
$source = @"  
public class BasicTest  
{  
public static int Add(int a, int b)  
{  
return (a + b);  
}

public int Multiply(int a, int b)  
{  
return (a \* b);  
}  
}  
"@

Add-Type -TypeDefinition $source

[BasicTest]::Add(4, 3)

$basicTestObject = New-Object BasicTest  
$basicTestObject.Multiply(5, 2)  
[/ps]

Example #3  
Access .Net function to move mouse, in order to prevent screensaver from kicking in.  
[ps]  
Function Move-Mouse {

param($minutes = 60)

for ($i = 0; $i -lt $minutes; $i++) {  
 Start-Sleep -Seconds 60  
 $Pos = [System.Windows.Forms.Cursor]::Position  
[System.Windows.Forms.Cursor]::Position = New-Object System.Drawing.Point((($Pos.X) + 1) , $Pos.Y)

}  
}

[/ps]

