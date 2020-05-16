---
categories:
- Everyday
date: "2015-06-08T00:17:18Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6103265247"
status: publish
tags: []
title: Configure Web.Config for use with PowerShell DSC v5 Configuration Names and
  RegistrationKey
---
One of the new features of PowerShell v5 DSC, is that you now can use ConfigurationNames in "clear-text" not GUID, meaning that you can now have human readable names for your configurations. Since they are easier to guess, then there is an added layer of security, now the Pull clients have to register themselves with the Pull server, with a preshared key. When this happens the client LCM will generate a unique AgentID that is used to discern the different clients.

In order to add the RegistrationKey settings you need to add a line to the web.config file of the DSC Pull server, that entry points to a location in the file system where it can find a file called RegistrationKeys.txt. (You can read more about it here: [Link](http://blogs.msdn.com/b/powershell/archive/2015/05/29/how-to-register-a-node-with-a-dsc-pull-server.aspx)

Instead of manually editing the web.config file, I wrote a little script to add the configuration, to help automate the building of pull servers for my demo lab

```
$FilePath = 'C:\inetpub\wwwroot\PSDSCPullServer\web.config' [XML]$WebConfig = Get-Content $FilePath $WebConfig.configuration.appSettings.ChildNodes notepad.exe 'C:\inetpub\wwwroot\PSDSCPullServer\web.config' $RegistrationKeyPath = $WebConfig.SelectSingleNode("//configuration/appSettings/add[@key='RegistrationKeyPath']") If ($RegistrationKeyPath ) { Write-Warning -Message "RegistrationKeyPath allready exists. With the value $($RegistrationKeyPath.Value), exiting" } Else { $RegistrationKeyPath = $WebConfig.CreateNode('element','add','') $RegistrationKeyPath.SetAttribute('key', 'RegistrationKeyPath') $RegistrationKeyPath.SetAttribute('value', 'C:\Program Files\WindowsPowerShell\DscService' ) $appSettingsNode = $WebConfig.SelectSingleNode('//configuration/appSettings').AppendChild($RegistrationKeyPath) $WebConfig.Save($FilePath) notepad.exe 'C:\inetpub\wwwroot\PSDSCPullServer\web.config' } Set-Content -Path 'C:\Program Files\WindowsPowerShell\DscService\RegistrationKeys.txt' -Value 'MySecureKey'
```

This assumes you have installed to the Pull server to the "default" location.

Another annoyance that I have come a across is that I usually have a Danish keyboard layout, and when I use the xPSDesiredStateConfiguration Module, and try to setup a pull server it will complain that I cannot find a file in C:\Windows\System32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PullServer\en. In order to fix this, you can create two localized folders containing the same files as in the en and en-US folders.. I wrote a little script to do this, based on the locale of the machine.

```
$CultureShort = (Get-Culture).Parent.Name $CultureLong = (Get-Culture).IetfLanguageTag $CultureShort = New-Item -ItemType Directory -Path "C:\Windows\System32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PullServer\$CultureShort" -Force $CultureLong = New-Item -ItemType Directory -Path "C:\Windows\System32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PullServer\$CultureLong" -Force Copy-Item C:\Windows\System32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PullServer\en\Microsoft.Powershell.DesiredStateConfiguration.Service.resources.dll -Destination $CultureShort Copy-Item C:\Windows\System32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PullServer\en-US\* -Destination $CultureLong
```

Since both scripts are altering files in protected areas of the file system both has to be run as Administrator

