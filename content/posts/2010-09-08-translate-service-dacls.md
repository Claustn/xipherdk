---
categories:
- Everyday
date: "2010-09-08T22:21:42Z"
meta:
  _css: ""
  _edit_last: "1"
  _raindrops_this_header_image: default
  _syntaxhighlighter_encoded: "1"
  _wp_old_slug: ""
  dsq_thread_id: "6099514103"
status: publish
tags:
- DACL
- PowerShell
title: Translate Service DACL's
---
There was a post on the Minasi forum, where someone was trying to check which users had start/stop permissions on a given server. I knew SC would give the results, but it returns it in DACL format, which as far from readable by humans... So since I had some time to pass I decided to try to write a powershell script to translate the DACL into something human readable (We were having our floor lacquered, and the guy doing it, had a little extra lacquer, and decided to lacquer our hallway, thereby cutting me off from leaving the first floor of our house).

This function takes a service name and a computername as input, if no computername is given, it tries to look it up on the localmachine.

```
Function Get-ServiceDACL { [CmdletBinding()] param( [Parameter(Mandatory=$true,Position=0,ValueFromPipeline=$true)] [String]$Servicename, [Parameter(Mandatory=$false,Position=1)] [String]$Computername= ".") $sddl = Invoke-expression -Command "c:\Windows\System32\sc.exe \\$Computername sdshow $Servicename" # $Servicename $sddl $parts = $sddl -split(":") #$parts.Length $i = 0 Write-Host "Getting Service DACL for $ServiceName on $Computername" While ($i -lt $parts.length) { $part = $parts[$i] Switch ($part) { "D" { $i++; Parse-DACL $parts[$i] } } $i++ } $sddl = '' } Function Parse-DACL { Param([String]$SDDLIN) [Array]$sddls = ($SDDLIN).split('(') Foreach ($SDDLI in $sddls) { #($SDDLI).replace(')';'') #$SDDLI $tokens = (($SDDLI).replace(')','')).split(";") If ($tokens[5]) { If ($tokens[5].length -gt 3) { [wmi]$obj = 'Win32\_SID.SID="{0}"' -f $($tokens[5]) $encoded = [System.Convert]::ToBase64String($obj.BinaryRepresentation) $obj | Add-Member -MemberType NoteProperty -Name base64\_sid -Value $encoded Write-Host "$($obj.ReferencedDomainName)\$($obj.AccountName)" -ForegroundColor red } Else { Write-Host "$($Trustees.get\_item($tokens[5]))" -ForegroundColor red } " " + $AceType.get\_item($tokens[0]) [regex]::split($tokens[2], '(.{2})') | % {Write-host " $($PermissionType.get\_item($\_)) `n" -NoNewline} } } } $AceType = @{"A" = "ACCESS ALLOWED"; "D" = "ACCESS DENIED"; "OA" = "OBJECT ACCESS ALLOWED: ONLY APPLIES TO A SUBSET OF THE OBJECT(S)."; "OD" = "OBJECT ACCESS DENIED: ONLY APPLIES TO A SUBSET OF THE OBJECT(S)."; "AU" = "SYSTEM AUDIT"; "AL" = "SYSTEM ALARM"; "OU" = "OBJECT SYSTEM AUDIT"; "OL" = "OBJECT SYSTEM ALARM"; "ML" = "MANDATORY LABEL"} $AceFlags = @{ "CI" = "CONTAINER INHERIT: Child objects that are containers, such as directories, inherit the ACE as an explicit ACE."; "OI" = "OBJECT INHERIT: Child objects that are not containers inherit the ACE as an explicit ACE."; "NP" = "NO PROPAGATE: ONLY IMMEDIATE CHILDREN INHERIT THIS ACE."; "IO" = "INHERITANCE ONLY: ACE DOESN'T APPLY TO THIS OBJECT; BUT MAY AFFECT CHILDREN VIA INHERITANCE."; "ID" = "ACE IS INHERITED"; "SA" = "SUCCESSFUL ACCESS AUDIT"; "FA" = "FAILED ACCESS AUDIT" } $PermissionType = @{ "CC" = "Query Conf"; "DC" = "Change Conf"; "LC" = "QueryStat"; "SW" = "EnumDeps"; "RP" = "Start"; "WP" = "Stop"; "DT" = "Pause"; "LO" = "Interrogate"; "CR" = "UserDefined"; "GA" = "Generic All"; "GX" = "Generic Execute"; "GW" = "Generic Write"; "GR" = "Generic Read"; "SD" = "Standard Delete"; "RC" = "Read Control"; "WD" = "Write DAC"; "WO" = "Write Owner" } $Trustees = @{ "AO" = "Account operators"; "RU" = "Alias to allow previous Windows 2000"; "AN" = "Anonymous logon"; "AU" = "Authenticated users"; "BA" = "Built-in administrators"; "BG" = "Built-in guests"; "BO" = "Backup operators"; "BU" = "Built-in users"; "CA" = "Certificate server administrators"; "CG" = "Creator group"; "CO" = "Creator owner"; "DA" = "Domain administrators"; "DC" = "Domain computers"; "DD" = "Domain controllers"; "DG" = "Domain guests"; "DU" = "Domain users"; "EA" = "Enterprise administrators"; "ED" = "Enterprise domain controllers"; "WD" = "Everyone"; "PA" = "Group Policy administrators"; "IU" = "Interactively logged-on user"; "LA" = "Local administrator"; "LG" = "Local guest"; "LS" = "Local service account"; "SY" = "Local system"; "NU" = "Network logon user"; "NO" = "Network configuration operators"; "NS" = "Network service account"; "PO" = "Printer operators"; "PS" = "Personal self"; "PU" = "Power users"; "RS" = "RAS servers group"; "RD" = "Terminal server users"; "RE" = "Replicator"; "RC" = "Restricted code"; "SA" = "Schema administrators"; "SO" = "Server operators"; "SU" = "Service logon user" } #Example below queries the WinRM service on RemoteServer Get-ServiceDACL winrm remotemgmt [/pre]
```
