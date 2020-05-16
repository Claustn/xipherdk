---
categories:
- Everyday
date: "2014-09-08T20:34:14Z"
meta:
  _css: ""
  _edit_last: "1"
  _raindrops_this_header_image: default
status: publish
tags:
- EMET
- PowerShell
title: Adding Applications to EMET
---
If you have been using EMET (Enhanced Mitigation Experience Toolkit ) toolkit from Microsoft you probably have had to need to add custom application, one of the main "culprits" is Flash Player with its ever changing name (filename contains it's version number)  
In EMET wildcards are only allowed in paths not filenames, so I wrote a little script to add applications to the EMET "Watch list" :)

It consist of 2 functions one to remove an application and one to add an application.

```
function Add-EMETSetting { \<# .SYNOPSIS Add files to EMET configuration .DESCRIPTION Add files to EMET configuration .EXAMPLE Add-EMETSetting -Path "C:\Windows\System32\Macromed\Flash" -Extension "exe" #\> [CmdletBinding()] [OutputType([String])] Param ( #Path to given file to add [Parameter(Mandatory = $true, HelpMessage = 'Path to the folder of the files you want added to EMET', Position = 0)] [ValidateNotNull()] [ValidateNotNullOrEmpty()] [String] $Path, # Extension of the files to add [Parameter(Mandatory = $true, HelpMessage = 'Extension of the files you want added to EMET', Position = 1)] [ValidateNotNull()] [ValidateNotNullOrEmpty()] [String] $Extension, [switch]$Recurse ) Write-Verbose "Finding files to add to EMET. Path: $Path ; Extension = $Extension" $Command = "${env:ProgramFiles(x86)}\EMET 5.0\EMET\_Conf.exe" Write-Verbose "Path to EMET Executable $Command" If ($Recurse) { Write-Verbose "Recursing through files from root folder: $path" $Files = Get-ChildItem $Path -Filter $("\*.$Extension") -Recurse } Else { Write-Verbose "Looking through files from folder: $path" $Files = Get-ChildItem $Path -Filter $("\*.$Extension") } Write-verbose "Adding the following files to EMET: $Files.Split(' ')" $Files | ForEach-Object { $CMDLineParms = "--set $($\_.Name)" $CMDLineParams = $CMDLineParms.Split(' ') Write-Verbose "Parameters to EMET Executable $CMDLineParams" Write-Verbose "Command to be executed: $Command $CMDLineParams " & "$Command" $CMDLineParams & "$Command" '--refresh' } }
```

```
function Remove-EMETSetting { \<# .SYNOPSIS Remove files from EMET configuration .DESCRIPTION Remove files from EMET configuration .EXAMPLE Remove-EMETSetting -Pattern 'Flash([^\s]+)' #\> [CmdletBinding()] [OutputType([String])] Param ( # Extension of the files to add [Parameter(Mandatory = $true, HelpMessage = 'Executable you want to remove from EMET in Regex', Position = 1)] [ValidateNotNull()] [ValidateNotNullOrEmpty()] [String] $Pattern ) $Command = "${env:ProgramFiles(x86)}\EMET 5.0\EMET\_Conf.exe" Write-Verbose "Path to EMET Executable $Command" $CMDLineParms = '--list' $CMDLineParams = $CMDLineParms.Split(' ') Write-Verbose "Parameters to EMET Executable $CMDLineParams" Write-Verbose "Command to be executed: $Command $CMDLineParams " $AppsToRemove = & "$Command" $CMDLineParams Write-Debug "$AppsToRemove" ($AppsToRemove | select-string -Pattern $Pattern -AllMatches | Select-Object -ExpandProperty matches ).value | ForEach-Object { $CMDLineParms = "--delete $\_" $CMDLineParams = $CMDLineParms.Split(' ') Write-Verbose "Parameters to EMET Executable $CMDLineParams" Write-Verbose "Command to be executed: $Command $CMDLineParams " & "$Command" $CMDLineParams & "$Command" '--refresh' } }
```

The below example will first remove all applications that starts with Flash, then it will add all .exe found in the path: 'C:\Windows\System32\Macromed\Flash', so if there were multiple versions of flash in there, they would all be removed.

```
Remove-EMETSetting -Pattern 'Flash([^\s]+)' -Verbose Add-EMETSetting -Path 'C:\Windows\System32\Macromed\Flash' -Extension 'exe' -Verbose -Recurse
```
