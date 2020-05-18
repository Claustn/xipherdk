---
categories:
- Everyday
date: "2013-06-27T10:37:35Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6117446778"
status: publish
tags: []
title: Culture Gotchas
---
We are not everyone running Powershell on machines with ”en-US” locale, and sometimes that gives some unexpected problems, if you are running on a non US system, and have been working with Excel for instance, you have probably run into this error when trying to add something to Excel like this:

$xl= New-Object -COM Excel.Application

$xl.Visible = $true

$wb=$xl.Workbooks.Add()

[![clip_image002](/assets/images/clip_image002_thumb.jpg "clip\_image002")](http://www.xipher.dk/assets/images/clip_image002.jpg)

I stumbled upon another culture “gotcha” when trying to get some eventlog info using (Wasn’t obvious to me at the time it was a Culture issue)

$daysBack = (Get-Date).Adddays(-2)

Get-WinEvent -ComputerName Localhost -FilterHashTable @{LogName='Application'; StartTime=$daysBack; ID=8224}

[![clip_image004](/assets/images/clip_image004_thumb.jpg "clip\_image004")](http://www.xipher.dk/assets/images/clip_image004.jpg)

When I ran it I would get basic information back, just not the contents of the “Message” property, which in this case I very relevant.

I tried running both commands on a test server, where both command worked without any problems. Since the server culture was set to ”en-US” that led me to dig a little deeper into the culture settings.

First thing I tried was to set the culture manually:

[System.Threading.Thread]::CurrentThread.CurrentCulture = "en-US"

Then check to see if the culture was changed.

[System.Threading.Thread]::CurrentThread.CurrentCulture

[![clip_image006](/assets/images/clip_image006_thumb.jpg "clip\_image006")](http://www.xipher.dk/assets/images/clip_image006.jpg)

To my surprise my culture was still da-DK, my initial thought was that PowerShell in v2 is default is running in MTA mode (Multi Threaded Apartment), meaning that my culture query might have been executed by a different thread. I tried to set the CurrentCulture to ”en-US” 100 times, but still every time I queried CurrentCulture I would get ”da-DK” back. (PowerShell v3 is running in STA (Single Threaded Apartment) mode per default)

Next I tried was to wrap it in a ScriptBlock

& {[System.Threading.Thread]::CurrentThread.CurrentCulture = "en-US"

[System.Threading.Thread]::CurrentThread.CurrentCulture }

[![clip_image008](/assets/images/clip_image008_thumb.jpg "clip\_image008")](http://www.xipher.dk/assets/images/clip_image008.jpg)  
Which worked, and returned ”en-US” as CurrentCulture

I then tried wrapping it in a function

Function Test {[System.Threading.Thread]::CurrentThread.CurrentCulture = "en-US"

[System.Threading.Thread]::CurrentThread.CurrentCulture }

[![clip_image010](/assets/images/clip_image010_thumb.jpg "clip\_image010")](http://www.xipher.dk/assets/images/clip_image010.jpg)  
Which also gave me the Expected ”en-US” Culture.

After conferring with Joel Bennet, this led is to believe that the CurrentCulture is being reset after every pipeline.

We then tried (on a single line)

[System.Threading.Thread]::CurrentThread.CurrentCulture = "en-US";[System.Threading.Thread]::CurrentThread.CurrentCulture

[![clip_image012](/assets/images/clip_image012_thumb.jpg "clip\_image012")](http://www.xipher.dk/assets/images/clip_image012.jpg)

Which confirmed that the culture change is only “active” in the current pipeline.

I found this strange and wrote to the PowerShell team at Microsoft to have them shed some light on why this was happening.

Lee Holmes replied:

“PowerShell saves and restores the thread’s current culture before and after invoking a pipeline so that scripts don’t trash your entire session. When you invoke the command to set the culture, it impacts the entire pipeline – but then we restore it when the pipeline completes. When you put the two commands in the same pipeline, our culture restoration code hasn’t kicked in yet and thus you get to see what the pipeline’s culture was changed to”

So remember if you are trying to run a script that requires a different Culture setting, that it gets reset after each pipeline completes.

Last thing we tried was to see if PowerShell was using $PSCulture to reset the Culture settings, so I tried changing $PSCulture to “en-US” instead of “da-DK” (It is a “read-only” variable, so you have to use –Force)

Set-Variable PSCulture -Value en-US -Force

[![clip_image014](/assets/images/clip_image014_thumb.jpg "clip\_image014")](http://www.xipher.dk/assets/images/clip_image014.jpg)

But to no avail, it seems as if PowerShell checking the system culture, and not something defined within the PowerShell session.

