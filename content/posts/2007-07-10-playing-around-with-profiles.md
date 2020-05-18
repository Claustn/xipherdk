---
categories:
- Tech Stuff
date: "2007-07-10T11:08:02Z"
meta:
  _edit_last: "1"
status: publish
tags:
- code
- link
- playing
- PoSH
- PowerShell
- Profile
- prompt
- PS
- Scripting
- Xenophane
title: Playing around with profiles.
---
In PowerShell you have the option create custom profiles that you can use, in the profiles you can create different aliases, change the prompt and a lot of other things, today we will look at adding some info to prompt. First we need to open profiles.ps1 , this file can be put in the WindowsPowershell folder under System32, or in the users \My Documents\WindowsPowerShell. If you are running vista, and want to play around with your profiles.ps1 file, I would put it in the \My Documents\WindowsPowerShell folder, since you have permission to write to files in that dir. (You can read more about profiles[here](http://pshell.info/basics/133/customizing-powershell-with-a-profile/)  
Well ok… on with the coding.. Lets say you want to add some basic info to your prompt.  
When you start up powershell for the first time, your prompt will be defined as

&nbsp;`“PS “ + $(get-location) + ">”`

When you open your profile.ps1 file (There is a template in C:\Windows\System32\WindowsPowerShell\v1.0\Examples\profile.ps1) you will find a function called  
“ prompt”, this is where you will make the following changes.  
The simplest alteration you can make is changing PS to your name

`“Xenophane “ + $(get-location) + ">” `  
Will give a prompt like this “Xenophane C:\Users\xen\_”

What if you want to know something about the system at the prompt, is that possible…. Of course it is…  
Let say you want to know how many processes you are running

`
function prompt
{
"Processes " + (Get-Process).Count+ " " + $(get-location) + "> "
}
`  
Will give you a prompt like this: “ Processes 71 C:\Users\ctn\_”

But it is very annoying to see that info on the same line as your cursor, so lets add the info on the line over the cursor, and add some more information.

`
function prompt
{
[wmi]$Global:Cdrive=Get-WmiObject -query "Select * from win32_logicaldisk where deviceid='c:'"
#Define Cdrive as global variable, which is type cast as a wmi
$processcount=(Get-Process).Count
#Define variable processcount as number of processes running
$cpuload=(Get-WmiObject win32_processor).loadpercentage
#cpuload is defined as a variable
$diskinfoC=" Free C: "+"{0:N2}" -f (($global:Cdrive.freespace/1gb)/($global:cdrive.size/1gb)*100)+"%"
#diskinfoC is defined, {0:N2} is .NET for 2 decimal places, -f is a the "format" property of the string object.
# Then the amount of free space on drive C is divided by the size of drive C multiplies by 100 to get
#the percentage of free space.
$time=Get-WmiObject -class Win32_OperatingSystem
#Define $time is object Win32_OperatingSystem
$t=$time.ConvertToDateTime($time.Lastbootuptime)
#Define #t as the result of the convertion of LastBootuptime into a readable dateTime format.
[TimeSpan]$uptime=New-TimeSpan $t $(get-date)
#Typecast the variable $uptime as a [timespan], the timespan cmdlet in powershell is a way to do date arithmetic in ps
#This way we can compare our variable $t (Which was Lastbootuptime) and the current time Get-date cmdlet
$up="$($uptime.days)d $($uptime.hours)h $($uptime.minutes)m $($uptime.seconds)s"
#define variable $up to display the uptime in days/hours/minuted/seconds
$TextToDisplay="CPU:"+$cpuload+"% Processes:" + $processcount + $diskinfoC+ " " + $diskinfoG+ " "+ ([char]0x25b2)+$up +" "+(Get-Date -format g)
#define one variable that contains all the data we collected so far.
Write-Host -fore red $TextToDisplay
#Write the information to the screen in red
Write-Host "My PoSh:" $(get-location) -nonewline
#Display the regular prompt.
}
`  
One other thing to be aware of is that PowerShell by default will not let you run scripts, so in order to run scripts like profile.ps1 you need to loosen up the security, to get help you can type the command "get-help about\_signing" in a powershell command prompt. I have chosen to use RemotedSigned which will let me run scripts locally on the machine. But you can read about the different security levels in the help file.

[Profile txt file](http://www.xipher.dk/assets/images/2007/07/profile-published.txt "Profile txt file")

