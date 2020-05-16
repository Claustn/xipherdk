---
categories:
- Tech Stuff
date: "2009-03-11T19:56:46Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6099514348"
status: publish
tags: []
title: New Powershell brushes for Syntax Highlighter plus
---
Since I once in a while post powershell scripts on my site, I like that fact that I can have them color highlighted for better readability, so I played around with several different syntax highlighters, but none of them was able to do powershell, so I created a brush for Syntax highligther plus.

This is my initial attempt, updated to work with Syntax Highlighter plus 2  
[Sourcecode lang='posh']  
#  
# http://www.xipher.dk  
#  
# This program is free software: you can redistribute it and/or modify  
# it under the terms of the GNU General Public License as published by  
# the Free Software Foundation, version 3 of the License.  
#  
# This program is distributed in the hope that it will be useful,  
# but WITHOUT ANY WARRANTY; without even the implied warranty of  
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the  
# GNU General Public License for more details.  
#  
# You should have received a copy of the GNU General Public License  
# along with this program. If not, see <http :></http>.

SyntaxHighlighter.Brushes.PoSh = function()  
{  
 var builtins = 'ForEach-Object Format-Custom Format-List Format-Table Format-Wide Get-Acl Get-Alias Get-AuthenticodeSignature Get-ChildItem ' +  
 'Disable-PSBreakpoint Disable-QADUser Disconnect-EventListener Disconnect-QADService Enable-PSBreakpoint Enable-QADUser Export-Alias Export-Clixml Export-Console ' +  
 'Get-Content Get-Credential Get-Culture Get-Date Get-Event Get-EventBinding Get-EventLog Get-ExecutionPolicy Get-Help ' +  
 'Get-PSJob Get-PSProvider Get-PSSnapin Get-QADComputer Get-QADGroup Get-QADGroupMember Get-QADObject Get-QADPasswordSettingsObject Get-QADPSSnapinSettings ' +  
 'Get-Runspace Get-Service Get-TraceSource Get-UICulture Get-Unique Get-Variable Get-WmiObject Group-Object Import-Alias ' +  
 'Import-Csv Import-LocalizedData Invoke-Expression Invoke-History Invoke-Item Invoke-WmiMethod Join-Path Measure-Command Measure-Object ' +  
 'Move-ItemProperty Move-QADObject New-Alias New-Event New-Item New-ItemProperty New-Object New-PSBreakpoint New-PSDrive ' +  
 'New-QADObject New-QADPasswordSettingsObject New-QADUser New-Runspace New-Service New-TimeSpan New-Variable Out-Default Out-File ' +  
 'Out-Host Out-Null Out-Printer Out-String Pop-Location Push-Location Read-Host Receive-PSJob Remove-Item ' +  
 'Remove-PSBreakpoint Remove-PSDrive Remove-PSJob Remove-PSSnapin Remove-QADGroupMember Remove-QADObject Remove-QADPasswordSettingsObjectAppliesTo Remove-Runspace Remove-Variable ' +  
 'Rename-Item Rename-ItemProperty Rename-QADObject Resolve-Path Restart-Service Resume-Service Select-Object Select-String Set-Acl ' +  
 'Set-AuthenticodeSignature Set-Content Set-Date Set-ExecutionPolicy Set-Item Set-ItemProperty Set-Location Set-PSDebug Set-QADGroup ' +  
 'Set-QADPSSnapinSettings Set-QADUser Set-Service Set-TraceSource Set-Variable Set-WmiInstance Sort-Object Split-Path Start-KeyHandler ' +  
 'Start-Service Start-Sleep Start-Transcript Step-Into Step-Out Step-Over Stop-KeyHandler Stop-Process Stop-PSJob ' +  
 'Stop-Transcript Suspend-Service Tee-Object Test-Path Trace-Command Unlock-QADUser Update-FormatData Update-TypeData Wait-PSJob ' +  
 'Write-Debug Write-Error Write-Host Write-Output Write-Progress Write-Verbose Write-Warning ' +  
 'suspend test times trap type typeset ulimit umask unalias unset wait';

var keywords = 'case do done elif elseif esac fi for function if in select then ' +  
 '-band -bor -bnot -match -notmatch -like -notlike -eq -ne -gt -ge -lt -le -is ' +  
 '-imatch -inotmatch -ilike -inotlike -ieq -ine -igt -ige -ilt -ile ' +  
 'time until while filter foreach not ft sort';

this.regexList = [  
 /\* comments \*/  
 {regex: SyntaxHighlighter.RegexLib.SingleLinePerlComments, css: 'comment'},  
 /\* text qualifiers \*/  
 {regex: SyntaxHighlighter.RegexLib.DoubleQuotedString, css: 'string'},  
 {regex: SyntaxHighlighter.RegexLib.SingleQuotedString, css: 'string'},  
 /\* deliminators \*/  
 {regex: new RegExp('[()[\\]{}]', 'g'), css: 'delim'},  
 /\* variables \*/  
 {regex: new RegExp('\\$\\w+', 'g'), css: 'vars'},  
 {regex: new RegExp('\\w+=', 'g'), css: 'vars'},  
 /\* flags \*/  
 {regex: new RegExp('\\s-\\w+', 'g'), css: 'test'},  
 /\* builtins \*/  
 {regex: new RegExp(this.GetKeywords(builtins), 'igm'), css: 'builtin'},  
 /\* keywords \*/  
 {regex: new RegExp(this.GetKeywords(keywords), 'igm'), css: 'keyword'}  
 ];

};

SyntaxHighlighter.Brushes.PoSh.prototype = new SyntaxHighlighter.Highlighter();  
SyntaxHighlighter.Brushes.PoSh.Aliases = ['PoSh', 'ps'];

[/sourcecode]

I modified a version made by Joel Bennet to work with version 2 as well.

[sourcecode lang='posh']

/\*Updated to v2 by Claus T Nielsen (Xipher.dk \*/  
/\* Powershell 1.0 syntax contributed by Joel Bennett \*/  
SyntaxHighlighter.brushes.PowerShell = function()  
{  
 var keywords = 'while until trap switch return ref process param in if global: function foreach for finally filter end elseif else do default continue break begin \\? % #script #private #local #global';

var operators = 'and as band bnot bor bxor casesensitive ccontains ceq cge cgt cle clike clt cmatch cne cnotcontains cnotlike cnotmatch contains creplace eq exact f file ge gt icontains ieq ige igt ile ilike ilt imatch ine inotcontains inotlike inotmatch ireplace is isnot le like lt match ne not notcontains notlike notmatch or regex replace wildcard';

var verbs = 'Write Wrap Width Where Whatif Warning Wait Version Verify Verbose Value UserName URL Update Unlock Unique Uninstall TypeName TrustLevel Trusted Truncate Trace TimeStamp TimeOut TID Test Temp Tee Suspend Strict Stop Statistic State Start Split Speed SortBy Sort Size SID Shortname Set Send Select Scope SaveCred Role Retry Resume Restore Restart Resolve Resize Repair Rename Remove Regex Recurse Receive Reason ReadOnly Read Push Property Prompt Privilege Priority PrinterName PrincipalName PortName Pop Ping Password PassThru ParentID Parameter Owner Overwrite Output Out Operation Notify NewLine New Name Move Most Modified Minimum Migrate Merge Measure Maximum MacName LogName Log Lock Location LineCount Limit KeyLength KeyContainerName KeyAlgorithm Join JobName IpAddress Invoke Interval InterfaceName Interactive Install Insert Input Initialize Incremental Include Import Ignore Id Group Get From Format ForEach Force Follow Filter FileName Fast Export Exclude Exact EventName ErrorLimit ErrorLevel Erase Encrypt Encoding Enable Elapsed DriveName Drain DomainName Disconnect Disable DirectoryName Description Descending Delete Default Debug Data Verbs CSPType CSPName Created Create Count Copy ConvertTo ConvertFrom Convert Continuous Connect Confirm ComputerName Compress Compatible Compare Command ClusterName Clear ClassName Checkpoint Char CertUsage CertSubjectName CertStoreLocation CertSerialNumber CertRequestFileName CertIssuerName CertFile CaseSensitive Cache BlockCount Binary Before AttributeName AssemblyName Ascending As ApplicationName Allocation All After Add ACL Accessed';

this.regexList = [  
 { regex: SyntaxHighlighter.regexLib.singleLinePerlComments, css: 'color4' }, // one line comments  
 { regex: new RegExp('@"\\n[\\s\\S]\*?\\n"@', 'gm'), css: 'string' }, // double quoted here-strings  
 { regex: new RegExp("@'\\n[\\s\\S]\*?\\n'@", 'gm'), css: 'string' }, // single quoted here-strings  
 { regex: new RegExp('"(?:\\$\\([^\\)]\*\\)|[^"]|`")*[^`]"','g'), css: 'string' }, // double quoted strings  
 { regex: new RegExp("'[^']\*'", 'g'), css: 'string' }, // single quoted strings  
 { regex: new RegExp('(?:'+verbs.replace(/ /g, '\\b|\\b')+')-[a-zA-Z\_][a-zA-Z0-9\_]\*', 'gmi'), css: 'cmdlet' }, // cmdlets  
 { regex: new RegExp('\\[[A-Z\_\\[][A-Z0-9\_. ,\\[\\]]\*\\]\*', 'gi'), css: 'color2' }, // .Net [Type]s  
 { regex: new RegExp('\\$(?:(?:global|script|private|env):)?[A-Z0-9\_]+', 'gi'), css: 'variable' },// $variables  
 { regex: new RegExp(this.getKeywords(keywords), 'gmi'), css: 'keyword' }, // keywords  
 { regex: new RegExp('-'+this.getKeywords(operators), 'gmi'), css: 'constants' }, // operators  
 { regex: new RegExp('\\s+-[a-zA-Z\_][a-zA-Z0-9\_]\*', 'gmi'), css: 'color1' } // operators  
 ]

};

SyntaxHighlighter.brushes.PowerShell.prototype = new SyntaxHighlighter.Highlighter();  
SyntaxHighlighter.brushes.PowerShell.aliases = ['powershell', 'posh', 'ps'];

[/sourcecode]

Be aware that compared to version 1 of the brushes you can no longer have specific css settings in the brush (previously you could define font/colors for different datatypes/keywords in the brush), this has been changed inversion 2 because of a new theme options.

