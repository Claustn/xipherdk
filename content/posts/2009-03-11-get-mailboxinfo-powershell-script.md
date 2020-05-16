---
categories:
- Everyday
date: "2009-03-11T20:25:56Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6099514375"
status: publish
tags: []
title: Get-Mailboxinfo Powershell script
---
<p>My CEO asked me for a report about mailbox usage in the company I work for, so I thought this has to be a job for powershell.</p>
<p>So I sat down and tried to figure out how to get info from Exchange, I know there is  a WMI class that I can query about information about the different mailboxes, so I thought I would give it a try to write a script to output things like Number of items in each mailbox, the size, deleted size etc.<br />
I stumbled on a script by Michael B. Smith, who had made a similar script, and really done some nice work on the output of the script, so I decided not to reinvent the wheel and use his formatting.</p>
<p>[sourcecode lang='posh']<br />
# Get-MailBoxSize.ps1<br />
# Claus T N 2009<br />
# This script connects to an Exchange server(s) and returns all mailbox sizes<br />
# number of items, plus a summary.<br />
# All formatting is "stolen" from a script by Michael B. Smith</p>
<p>$day = Get-Date -UFormat "%Y%m%d"<br />
#File to output results to<br />
$OutPutFile = "c:\scripts\MailBoxInfo-$day.txt"</p>
<p>[System.Int64]$script:totalSize      = 0<br />
[System.Int64]$script:totalItems     = 0<br />
[System.Int64]$script:totalDeleted   = 0<br />
[System.Int64]$script:totalMailboxes = 0</p>
<p># Gets data through WMI from specified Exchange mailbox servers<br />
$ExchangeServers = "dksrv01"</p>
<p>Function Get-MailboxInfo {<br />
foreach ($computer in $ExchangeServers) {<br />
$Mailboxes = Get-Wmiobject -namespace root\MicrosoftExchangeV2 -class Exchange_Mailbox -computer $computer | sort-object Size `</p>
<p>				Foreach ($mailbox in $mailboxes){<br />
				If ($mailbox.MailBoxDisplayName -like "SMTP (*" -or $mailbox.MailBoxDisplayName -like "SystemMailbox*"){<br />
				}<br />
				else {<br />
				[string]$str = ([string]$Mailbox.MailboxDisplayname).PadRight(40)<br />
                $str += ([System.Int64]$Mailbox.Size).ToString("N0").PadLeft(12)<br />
                $str += " KB"<br />
                $str += ([System.Int32]$MailBox.TotalItems).ToString("N0").PadLeft(9)<br />
                $str += "   "<br />
                $str += ([System.Int64]$Mailbox.DeletedMessageSizeExtended).ToString("N0").PadLeft(12)<br />
                $str += " KB"<br />
                $str | Out-File $OutPutFile -append<br />
				#"----------------------------------------------------------------------------------
" | Out-File $OutPutFile -append

$script:totalSize += ([System.Int64]$mailbox.Size)  
 $script:totalItems += ([System.Int32]$mailbox.TotalItems)  
 $script:totalDeleted += ([System.Int64]$mailbox.DeletedMessageSizeExtended)  
 $script:totalMailboxes += 1  
 }  
}  
}  
}

"Mailbox name Mailbox size Item count Deleted size" | Out-File $OutPutFile -append  
"==================================================================================" | Out-File $OutPutFile -append  
Get-MailBoxInfo

"==================================================================================" | Out-File $OutPutFile -append  
$string = ($script:totalMailboxes.ToString("N0") + " mailboxes").PadRight(40)  
$string += $script:totalSize.ToString("N0").PadLeft(12) + " KB"  
$string += $script:totalItems.ToString("N0").PadLeft(9) + " "  
$string += $script:totalDeleted.ToString("N0").PadLeft(12) + " KB"  
$string | Out-File $OutPutFile -append  
"==================================================================================" | Out-File $OutPutFile -append

[/sourcecode]

