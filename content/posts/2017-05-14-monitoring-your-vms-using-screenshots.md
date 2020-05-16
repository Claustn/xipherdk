---
categories:
- Everyday
date: "2017-05-14T23:33:03Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6147741096"
status: publish
tags: []
title: Monitoring your VM's using screenshots
---
(This is part 1 of 3, in an articles series on how we at Unity started using "screenshots" and machine learning in our monitoring system)

Part 1: Getting screenshots from VMware machines (This Post)  
Part 2: Using image analysis to detect machines in a broken state (Not published yet)  
Part 3: Using Clarifai "Machine learning" to further narrow down our results (Not Published yet)

In my new job as a "DevOps" engineer (I know I know, there is no such thing as a DevOps engineer), I am working with Unity's build farm, we are building the Unity engine, and a lots of artifacts 1000's of times a day, so our build farm is relatively big, running multiple different OS's like Windows, Mac and Linux.

We are continuously trying to improve our monitoring of the platform, both in order to detect failed machines, but also trying to gather information as to what have gone wrong, so we can use this information to prevent the same issues arising in the future, by giving feedback to the relecant teams.

We had a period, where we had some storage related issues, this caused Mac and Linux machines, in particular, to crash and hang, we had no trouble detecting the machines went offline, but since we weren't able to connect to these machines, we could not tell what "state" they were in. So in order to document, what had happened, we had to look at the console of the given machine.  
So I started thinking if there was an automated way that I could test for this, and it hit me, that I had read somewhere, that it I possible in Vmware to take a "screenshot" of a running machine.

So I wrote a PowerShell script that would take a screenshot of all our running VM's in our build farm, then at least I had some documentation of what the "state" of the machines were.

```powershell
# This script 'takes' a screenshot of all running VMs, and outputs a jpeg file to a local folder (C:\Temp\VMImages) 
# This script requires a credential object called $cred # In this case I am reading it from a file, in order to make an encrypted file containing username and password you can do 
# $Cred = Get-Credential 
# $Cred | Export-CliXML C:\what\ever.xml 
# This password is then encrypted by Windows DPAPI, which means it can only be decrypted on the machines it was encrypted on, by the user who encrypted it. 
# It also requires the modules VMware.VimAutomation.Core, SplitPipeline. 
# Example installing module from PowerShell Gallery:
'Install-Module SplitPipeline'
Import-Module -Name 'VMware.VimAutomation.Core' -ErrorAction Stop
Import-Module -Name 'SplitPipeline' -ErrorAction Stop
$Null = Set-PowerCLIConfiguration -WebOperationTimeoutSeconds 10 -Scope Session -Confirm:$false
$Null = Set-PowerCLIConfiguration -InvalidCertificateAction Ignore -Scope Session -Confirm:$false
$Vcenter = 'MyVcenterServer'
#Get Credentials that allow me to connect to Vcenter 
$cred = (Import-Clixml -Path "$PSScriptRoot\Credentials\script-svc.xml") 
Connect-VIServer -Server $Vcenter -Credential $cred 
#Get a list of all VM's 
$VMs = Get-VM 
#Create a websession that I can reuse to connect to Vcenter 
$Null = Invoke-WebRequest -Uri https://$Vcenter/rest/com/vmware/cis/session -Method Post -Credential $cred -SessionVariable session 
#Remove all images from previous runs 
Get-ChildItem -Filter \*.jpg -Path C:\temp\VMImages | Remove-Item
  
$VMs | Split-Pipeline -Variable cred, session -Count 2 -Begin {
  $ProgressPreference = 'SilentlyContinue' 
} -Script {
  Process {$MOID = ($_ | Select-Object -ExpandProperty id).Replace('VirtualMachine-','')
    $ServerName = $_.Name 
    if ($MOID -AND ($_.PowerState -eq 'PoweredOn')) 
    {
      Try 
      { 
        #Use invoke-webrequest to get an image 1024\*768 pixels 
        Invoke-WebRequest -Uri "https://$Vcenter/screen?id=$MOID&w=1024&h=768" -OutFile "C:\temp\VMImages\$($_.Name).jpg" -WebSession $session -TimeoutSec 20 -ErrorAction Stop
      }
      Catch 
      {
        Write-Warning -Message "Failed to ScreenShot: $ServerName, MOID:$MOID" 
      }
    }
  }
}
```

In the above example am creating a "session" to reuse for the calls against Vcenter, so we will not see 100's of connections in Vcenter.

In the next part of the article, I will cover some PowerShell functions I wrote to wrap ImageMagick to make some initial comparisons of each screenshot, sorting them in known good, known bad and What the h\*\*l is going on here ;)

