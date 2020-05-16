---
categories:
- Everyday
date: "2013-08-02T12:47:02Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6454478328"
status: publish
tags: []
title: Managing Queue-It queues with PowerShell
---
We are using Queue-IT for some of our external websites, and we needed to start/stop the queues when we doing maintenance on our sites, I found this very easy to do, with Powershell's Invoke-WebRequest cmdlet.

Here are a few examples.

```
\<# # Script to Pause Queue-IT Logs # MaxRedirectPerMin number of requests passed to site/minute # QueueStatus 1 = Queue Open, 2 = Queue Paused. #\> # Pauses the Queue MySite" Invoke-WebRequest -Uri "https://api.queue-it.net/latest/event/MySite/queue/metrics?api-key=API-KEY GOES HERE" -ContentType 'application/json' -Body '{"MaxRedirectsPerMinute":5,"QueueStatus":2}' -Method PUT # Pauses the queue AlsoMySite Invoke-WebRequest -Uri "https://api.queue-it.net/latest/event/AlsoMySite/queue/metrics?api-key=API-KEY GOES HERE" -ContentType 'application/json' -Body '{"MaxRedirectsPerMinute":5,"QueueStatus":2}' -Method PUT
```

```
function Check-QueueIT { [CmdletBinding()] Param ( # Param1 help description [Parameter(Mandatory=$true, ValueFromPipelineByPropertyName=$true, Position=0)] $EventName ) Process { $Result = [xml](Invoke-WebRequest -Uri "https://api.queue-it.net/latest/event/$($EventName)/queue/metrics?api-key=API-KEY GOES HERE" -ContentType "text/html").Content $NewObject = New-Object -TypeName PSObject $NewObject | Add-Member -Type NoteProperty -Name EventName -Value $EventName $NewObject | Add-Member -Type NoteProperty -Name AntalLogins -Value $result.QueueMetrics.MaxRedirectsPerMinute $NewObject | Add-Member -Type NoteProperty -Name Status -Value $result.QueueMetrics.QueueStatus $NewObject } } Check-QueueIT -EventName MySite Check-QueueIT -EventName AlsoMySite
```
