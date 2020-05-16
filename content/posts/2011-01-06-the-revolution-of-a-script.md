---
categories:
- Tech Stuff
date: "2011-01-06T23:58:53Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  _wp_old_slug: ""
  dsq_thread_id: "6099514109"
status: publish
tags:
- PowerShell
title: The (r)evolution of a script
---
As those of you who a following my blog know, I have been fortunate enough to become a Microsoft MVP in Powershell, and because of that I have been allowed on the Powershell MVP mailing list, an let me tell, there are some amazing people out there, doing some amazing stuff. Some of the stuff floating around I don't even have a clue what does ;)

But people are also very helpful, and quick to jump in if someone has a problem or even if someone just posts a piece of code they find useful, people tend to jump in with suggestions/additions to make the script even better... Here is an example of a script that went from 14 lines of code to 334 in half a day.

Tome Tanasovski wrote a quick and dirty script that connects to the MVP site, and cycles through all the categories, and returns how many MVP's there are in each. He ended off by saying, that the results where not entirely correct, since some MVP has hidden the profile from the public list, meaning you would have to be logged in to see them.

[ps]  
$web = New-Object System.Net.WebClient  
$regex = [regex]'(?m)\<a href=\"(\/communities\/mvp.aspx\?product=1&competency=(\S+))\"'  
$objects = @()  
$return = $web.DownloadString('http://mvp.support.microsoft.com/communities/mvp.aspx')  
$regex.Matches($return) |% {  
 $obj = New-Object psobject  
 $obj |Add-Member NoteProperty -Name 'Group' -Value ($\_.Groups[2].Value -replace '\+',' ')  
 if ($web.DownloadString(('http://mvp.support.microsoft.com' + $\_.Groups[1].Value)) -match 'Results 1 \- \d+ of (\d+)') {  
 $obj |Add-Member NoteProperty -Name 'Count' -Value ([int]$matches[1])  
 }  
 $objects += $obj  
}

$objects |sort Count -Descending |ogv  
[/ps]

So I decided to write some simple IE automation that would log you you on to the Microsoft passport site, and count all the MVP's, including those who do not have a public profile.

[ps]  
$URI = "https://login.live.com/ppsecure/secure.srf?lc=1033&sf=1&id=259214&ru=https://mvp.support.microsoft.com/communities/mvp.aspx&tw=0&fs=0&kv=0&cb=wizid%3df4502d34-3b8f-4a04-b741-289e08aa1782%26lcid%3d1033%26returnurl%3dhttps%253a%252f%252fmvp.support.microsoft.com%252fcommunities%252fmvp.aspx%26brand%3dMicrosoft&cbcxt=&wp=MCMBI&wa=wsignin1.0&wreply=https://mvp.support.microsoft.com/communities/mvp.aspx&wlsu=1"  
$ie = new-object -com "InternetExplorer.Application"  
$ie.visible = $false  
$ie.navigate($URI)  
while ($ie.Busy -eq $true) { Start-Sleep -Milliseconds 1000; }  
$doc = $ie.Document  
$ie.Document.Body.InnerText.ToString()  
$user = $doc.getElementByID("Login")  
$user.value = "xxxxxxxxxx"  
$pass = $doc.getElementByID("Passwd")  
$pass.value = "xxxxxxxxxx"  
$Logon = $doc.getElementByID("SI")  
$Logon.click()  
while ($ie.LocationURL -ne "https://mvp.support.microsoft.com/communities/mvp.aspx") { Start-Sleep -Milliseconds 1000; }  
while ($ie.Busy -eq $true) { Start-Sleep -Milliseconds 1000; }  
#$ie.Document.Body.InnerHTML.ToString()

$regex = [regex]'(?m)\<A href=\"(\/communities\/mvp.aspx\?product=1&amp;competency=(\S+))\"'  
$objects = @()

$return = $ie.Document.Body.InnerHTML.ToString()

$regex.Matches($return)|% {  
 $obj = New-Object psobject  
 $obj |Add-Member NoteProperty -Name 'Group' -Value ($\_.Groups[2].Value -replace '\+',' ')  
$URI = "https://mvp.support.microsoft.com$($\_.Groups[1].Value -replace 'amp;','')"  
while ($ie.Busy -eq $true) { Start-Sleep -Milliseconds 1000; }  
$ie.navigate($URI)  
$URI  
while ($ie.Busy -eq $true) { Start-Sleep -Milliseconds 1000; }  
 if (($ie.Document.Body.InnerText.ToString()) -match 'Results 1 \- \d+ of (\d+)') {  
 $MVPs=""; $MVPs = [int]$matches[1];$MVPs; $obj |Add-Member NoteProperty -Name 'Count' -Value ($MVPs)

}  
 $objects += $obj  
}  
$objects |sort Count -Descending |ogv  
[/ps]

Then Michael B Smith jumped in on the thread, because he thought my script needed some error handling, which is completely right, and came up with his own version of the script.  
[ps]  
Param(  
 [switch]$gridview,  
 [string]$username,  
 [string]$password  
)

[System.Reflection.Assembly]::LoadWithPartialName("System.Web") | out-null

$ie = $null

try  
{  
 if ($username)  
 {  
 $URI = "https://login.live.com/ppsecure/secure.srf?lc=1033&sf=1&id=259214&ru=https://mvp.support.microsoft.com/communities/mvp.aspx&tw=0&fs=0&kv=0&cb=wizid%3df4502d34-3b8f-4a04-b741-289e08aa1782%26lcid%3d1033%26returnurl%3dhttps%253a%252f%252fmvp.support.microsoft.com%252fcommunities%252fmvp.aspx%26brand%3dMicrosoft&cbcxt=&wp=MCMBI&wa=wsignin1.0&wreply=https://mvp.support.microsoft.com/communities/mvp.aspx&wlsu=1"  
 $ie = new-object -com "InternetExplorer.Application"  
 $ie.visible = $false  
 $ie.navigate($URI)  
 while ($ie.Busy -eq $true) { Start-Sleep -Milliseconds 1000; }  
 $doc = $ie.Document  
 $ie.Document.Body.InnerText.ToString()

$user = $doc.getElementById( "i0116" ) ## login  
 if( $user )  
 {  
 $user.value = $username  
 }  
 else  
 {  
 "Document element named 'Login' not found"  
 }

$pass = $doc.getElementById( "i0118" ) ## passwd  
 if( $pass )  
 {  
 $pass.value = $password  
 }  
 else  
 {  
 "Document element named 'Passwd' not found"  
 }

$Logon = $doc.getElementById( "i0011" ) ## SI  
 if( $Logon )  
 {  
 $Logon.click()  
 }  
 else  
 {  
 "Document element named 'SI' not found"  
 }

$count = 0  
 while( $ie.LocationURL -ne "https://mvp.support.microsoft.com/communities/mvp.aspx" )  
 {  
 if( $ie.LocationURL -eq "https://mvp.support.microsoft.com/mvpinvalidsignin.aspx" )  
 {  
 write-error "Invalid username / password combination"  
 return  
 }

Start-Sleep -Milliseconds 1000  
 if( $count -gt 15 )  
 {  
 "Location URL is $($ie.LocationURL)"  
 }  
 $count++  
 }

$count = 0  
 while( $ie.Busy -eq $true )  
 {  
 Start-Sleep -Milliseconds 1000  
 if( $count -gt 15 )  
 {  
 "IE still busy after $count seconds"  
 }  
 $count++  
 }

#$ie.Document.Body.InnerHTML.ToString()  
 }

$regex = [regex]'(?m)\<A href=\"(\/communities\/mvp.aspx\?product=1&amp;competency=(\S+))\"'  
 $objects = @()

$return = $ie.Document.Body.InnerHTML.ToString()  
 $regex.Matches($return)|% {  
 $obj = New-Object psobject  
 $obj |Add-Member NoteProperty -Name 'Group' -Value ([System.Web.HttpUtility]::UrlDecode( $\_.Groups[2].Value ))

$URI = "https://mvp.support.microsoft.com$($\_.Groups[1].Value -replace 'amp;','')"  
 while ($ie.Busy -eq $true) { Start-Sleep -Milliseconds 1000; }  
 $ie.navigate($URI)  
 $URI  
 while ($ie.Busy -eq $true) { Start-Sleep -Milliseconds 1000; }  
 if (($ie.Document.Body.InnerText.ToString()) -match 'Results 1 \- \d+ of (\d+)') {  
 $MVPs=""; $MVPs = [int]$matches[1];$MVPs; $obj |Add-Member NoteProperty -Name 'Count' -Value ($MVPs)  
 }  
 $objects += $obj  
 }

" "  
 "Total MVP Competencies $($objects.Count)"  
 if( $gridview )  
 {  
 $objects |sort Count -Descending |ogv  
 }  
 else  
 {  
 $objects |sort Count -Descending  
 }

}  
finally  
{  
 if( $ie )  
 {  
 $ie.Quit()  
 $ie = $null  
 }  
}

[/ps]

Finally Kirk Munro decided to go "all in" and more or less rewrite the entire script into reusable functions, and using PSCredentials for more secure handling of the password, rewrote the logic to speed up the script, made it possible to only check a single MVP group and lots more.

[ps]  
function Get-MvpCount {  
 [CmdletBinding()]  
 param(  
 [Parameter(Position=0,Mandatory=$false)]  
 [ValidateNotNullOrEmpty()]  
 [System.String[]]  
 $Competency = @('\*'),

[Parameter(Mandatory=$false)]  
 [ValidateRange(0,60)]  
 [System.Int32]  
 $Timeout = 15,

[Parameter(Mandatory=$false)]  
 [ValidateNotNull()]  
 [System.Management.Automation.Credential()]  
 $Credential = [System.Management.Automation.PSCredential]::Empty  
 )

#region Initialize and load helper module.  
 $helperModule = New-Module -ScriptBlock {  
 #region Initialize local variables.  
 $ie = $null  
 #endregion

function Show-IEWindow {  
 #region Create a new hidden instance of Internet Explorer if one does not exist.  
 if (-not $script:ie) {  
 $script:ie = New-Object -ComObject 'InternetExplorer.Application'  
 }  
 #endregion

#region Show the IE window.  
 $script:ie.Visible = $true  
 #endregion  
 }

function Hide-IEWindow {  
 #region Create a new hidden instance of Internet Explorer if one does not exist.  
 if (-not $script:ie) {  
 $script:ie = New-Object -ComObject 'InternetExplorer.Application'  
 }  
 #endregion

#region Hide the IE window.  
 $script:ie.Visible = $false  
 #endregion  
 }

function Open-IEWebSite {  
 [CmdletBinding()]  
 param(  
 [Parameter(Position=0,Mandatory=$true)]  
 [ValidateNotNullOrEmpty()]  
 [System.String]  
 $Uri,

[Parameter(Position=1,Mandatory=$false)]  
 [ValidateNotNullOrEmpty()]  
 [System.String[]]  
 $ErrorUri,

[Parameter(Mandatory=$false)]  
 [ValidateRange(0,60)]  
 [System.Int32]  
 $Timeout = 15  
 )  
 #region Create a new hidden instance of Internet Explorer if one does not exist.  
 if (-not $script:ie) {  
 $script:ie = New-Object -ComObject 'InternetExplorer.Application'  
 $script:ie.Visible = $false  
 }  
 #endregion

try {  
 #region Navigate to the specified web site.  
 Write-Progress -Activity 'Loading a web page' -Status "Loading $Uri..."  
 $script:ie.Navigate($Uri)  
 #endregion

#region Wait until IE has finished loading the web site.  
 $count = 0  
 do {  
 if ($Timeout -and ($count -gt ($Timeout \* 1000))) {  
 throw "The $Uri web page failed to load in $Timeout seconds."  
 }  
 if ((-not $script:ie.Busy) -and $ErrorUri) {  
 foreach ($failedLocationUrl in $ErrorUri) {  
 if ($script:ie.LocationURL -like $failedLocationUrl) {  
 throw "The $Uri web page failed to load ($($script:ie.LocationURL))."  
 }  
 }  
 }  
 Start-Sleep -Milliseconds 500  
 $count++  
 } while ($script:ie.Busy)  
 #endregion  
 }  
 finally {  
 if ($script:ie.Busy) {  
 $script:ie.Stop()  
 }  
 Write-Progress -Activity 'Loading a web page' -Status "Loading $Uri..." -Completed  
 }  
 }  
 function Test-IEWebSiteElement {  
 [CmdletBinding()]  
 param(  
 [Parameter(Position=0,Mandatory=$true)]  
 [ValidateNotNullOrEmpty()]  
 [System.String]  
 $Name  
 )  
 #region Raise an error if you haven't opened a web site yet.  
 if (-not $script:ie.Document) {  
 throw 'You must open a web site using Open-IEWebSite before you can invoke a button on the site.'  
 }  
 #endregion

#region Return true if the element exists; false otherwise.  
 [bool]$script:ie.Document.getElementById($Name)  
 #endregion  
 }

function Set-IEWebSiteFieldData {  
 [CmdletBinding()]  
 param(  
 [Parameter(Position=0,Mandatory=$true)]  
 [ValidateNotNullOrEmpty()]  
 [System.Collections.Hashtable]  
 $DataMap  
 )  
 #region Raise an error if you haven't opened a web site yet.  
 if (-not $script:ie.Document) {  
 throw 'You must open a web site using Open-IEWebSite before you can set field data.'  
 }  
 #endregion

#region Load the data into the appropriate fields.  
 foreach ($key in $DataMap.Keys) {  
 if ($field = $script:ie.Document.getElementById($key)) {  
 $field.Value = $DataMap[$key]  
 } else {  
 throw "Field element '$key' was not found on $($script:ie.LocationURL)."  
 }  
 }  
 #endregion  
 }  
 function Invoke-IEWebSiteButton {  
 [CmdletBinding()]  
 param(  
 [Parameter(Position=0,Mandatory=$true)]  
 [ValidateNotNullOrEmpty()]  
 [System.String]  
 $Name,

[Parameter(Position=1,Mandatory=$false)]  
 [ValidateNotNullOrEmpty()]  
 [System.String[]]  
 $ErrorUri,

[Parameter(Mandatory=$false)]  
 [ValidateRange(0,60)]  
 [System.Int32]  
 $Timeout = 15  
 )  
 #region Raise an error if you haven't opened a web site yet.  
 if (-not $script:ie.Document) {  
 throw 'You must open a web site using Open-IEWebSite before you can invoke a button on the site.'  
 }  
 #endregion

try {  
 #region Click on the specified button.  
 Write-Progress -Activity 'Clicking a web site button' -Status "Clicking on the $Name button on $($script:ie.LocationURL)..."  
 if ($button = $script:ie.Document.getElementById($Name)) {  
 $button.Click()  
 } else {  
 throw "Button '$Name' was not found on $($script:ie.LocationURL)."  
 }  
 #endregion

#region Wait until IE has finished loading the web site.  
 $count = 0  
 do {  
 if ($Timeout -and ($count -gt ($Timeout \* 1000))) {  
 throw "The web page failed to load in $Timeout seconds."  
 }  
 if ((-not $script:ie.Busy) -and $ErrorUri) {  
 foreach ($failedLocationUrl in $ErrorUri) {  
 if ($script:ie.LocationURL -like $failedLocationUrl) {  
 throw "An error occurred ($($script:ie.LocationURL))."  
 }  
 }  
 }  
 Start-Sleep -Milliseconds 500  
 $count++  
 } while ($script:ie.Busy)  
 #endregion  
 }  
 finally {  
 if ($script:ie.Busy) {  
 $script:ie.Stop()  
 }  
 Write-Progress -Activity 'Clicking a web site button' -Status "Clicking on the $Name button on $($script:ie.LocationURL)..." -Completed  
 }  
 }  
 function Get-IEWebSiteMatch {  
 [CmdletBinding()]  
 param(  
 [Parameter(Position=0,Mandatory=$true)]  
 [ValidateNotNullOrEmpty()]  
 [System.Text.RegularExpressions.Regex[]]  
 $Regex  
 )  
 #region Raise an error if you haven't opened a web site yet.  
 if (-not $script:ie.Document) {  
 throw 'You must open a web site using Open-IEWebSite before you can invoke a button on the site.'  
 }  
 #endregion

#region Return all matches for the specified regular expression.  
 $innerHtml = $script:ie.Document.Body.InnerHTML.ToString()  
 foreach ($value in $RegEx) {  
 $value.Matches($innerHtml)  
 }  
 #endregion  
 }  
 function ConvertFrom-EncodedUrl {  
 [CmdletBinding()]  
 param(  
 [Parameter(Position=0,Mandatory=$true,ValueFromPipeline=$true)]  
 [ValidateNotNullOrEmpty()]  
 [System.String[]]  
 $InputObject  
 )  
 begin {  
 #region Load the System.Web assembly.  
 [System.Reflection.Assembly]::LoadWithPartialName('System.Web') | Out-Null  
 #endregion  
 }  
 process {  
 #region Decode the values.  
 foreach ($value in $InputObject) {  
 [System.Web.HttpUtility]::UrlDecode($value)  
 }  
 #endregion  
 }  
 }

function ConvertTo-EncodedUrl {  
 [CmdletBinding()]  
 param(  
 [Parameter(Position=0,Mandatory=$true,ValueFromPipeline=$true)]  
 [ValidateNotNullOrEmpty()]  
 [System.String[]]  
 $InputObject  
 )  
 begin {  
 #region Load the System.Web assembly.  
 [System.Reflection.Assembly]::LoadWithPartialName('System.Web') | Out-Null  
 #endregion  
 }  
 process {  
 #region Encode the values.  
 foreach ($value in $InputObject) {  
 [System.Web.HttpUtility]::UrlEncode($value)  
 }  
 #endregion  
 }  
 }

$ExecutionContext.SessionState.Module.OnRemove = {  
 if ($script:ie) {  
 try {  
 $script:ie.Quit()  
 }  
 finally {  
 $script:ie = $null  
 }  
 }  
 }  
 }  
 #endregion

#region Load the helper module.  
 $helperModule | Import-Module  
 #endregion

try {  
 #region Open the MVP website, using credentials if they were provided.  
 if ($Credential -and ($Credential -ne [System.Management.Automation.PSCredential]::Empty)) {  
 #region If the caller provided credentials, use Passport login before retrieving competency data.  
 Open-IEWebSite -Uri 'https://login.live.com/ppsecure/secure.srf?lc=1033&sf=1&id=259214&ru=https://mvp.support.microsoft.com/communities/mvp.aspx&tw=0&fs=0&kv=0&cb=wizid%3df4502d34-3b8f-4a04-b741-289e08aa1782%26lcid%3d1033%26returnurl%3dhttps%253a%252f%252fmvp.support.microsoft.com%252fcommunities%252fmvp.aspx%26brand%3dMicrosoft&cbcxt=&wp=MCMBI&wa=wsignin1.0&wreply=https://mvp.support.microsoft.com/communities/mvp.aspx&wlsu=1'  
 if (Test-IEWebSiteElement -Name 'i0011') {  
 Set-IEWebSiteFieldData -DataMap @{  
 'i0116' = $Credential.UserName.Trim([Char[]]' \')  
 'i0118' = $Credential.GetNetworkCredential().Password  
 }  
 Invoke-IEWebSiteButton -Name 'i0011' -ErrorUri 'https://mvp.support.microsoft.com/mvpinvalidsignin.aspx'  
 }  
 #endregion  
 } else {  
 #region Otherwise, simply open the MVP website.  
 Open-IEWebSite -Uri 'https://mvp.support.microsoft.com/communities/mvp.aspx'  
 #endregion  
 }  
 #endregion

#region Process the compentencies that were requested.  
 Get-IEWebSiteMatch -Regex '(?m)\<A href=\"(\/communities\/mvp.aspx\?product=1&amp;competency=(\S+))\"' `  
 | Add-Member -MemberType ScriptProperty -Name DecodedUrl -Value {$this.Groups[2].Value | ConvertFrom-EncodedUrl} -PassThru `  
 | Sort-Object -Property DecodedUrl `  
 | ForEach-Object {  
 foreach ($value in $Competency) {  
 if ($\_.DecodedUrl -like $value) {  
 Open-IEWebSite -Uri "https://mvp.support.microsoft.com$($\_.Groups[1].Value -replace 'amp;','')"  
 if ($matches = Get-IEWebSiteMatch -Regex 'Results 1 \- \d+ of (\d+)') {  
 $competencyRecord = New-Object -TypeName System.Management.Automation.PSObject `  
 | Add-Member -MemberType NoteProperty -Name Competency -Value $\_.DecodedUrl -PassThru `  
 | Add-Member -MemberType NoteProperty -Name Count -Value $matches[0].Groups[1].Value -PassThru  
 $competencyRecord.PSTypeNames.Insert(0,'MVPCompetencyRecord')  
 $competencyRecord  
 }  
 break  
 }  
 }  
 }  
 #endregion  
 }  
 finally {  
 $helperModule | Remove-Module  
 }  
}  
[/ps]

**Remember this is not thoroughly tested code, this is just examples of different ways of doing things...**

