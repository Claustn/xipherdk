---
categories:
- Everyday
date: "2012-01-25T12:19:58Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6101709326"
status: publish
tags: []
title: Checking Site sizes in SharePoint 2007 and 2010
---
Our Sharepoint admin asked me to help him write a script, that found out how much space each DocumentLibrary in our sharepoint farm took up, so after some googeling I found that I could use the StorageManagementInformation Method on the SPSite object, so I cam up with this little script

[ps]  
#First we load the SharePoint assembly  
[void][System.Reflection.Assembly]::LoadWithPartialName("Microsoft.SharePoint")

#Then we create a function that returns tje SPSite  
function Get-SPSite($url){  
return new-Object Microsoft.SharePoint.SPSite($url)  
}

$site = Get-SPSite http://URL  
# We use the StorageManagementInformation Method on the $SPSite object, StorageManagementInformation returns a DataTable, and takes 4 input values  
# System.Data.DataTable StorageManagementInformation(Microsoft.SharePoint.SPSite+StorageManagementInformationType ltVar, Microsoft.SharePoint.SPSite+StorageManagementSortOrder sordVar, Microsoft.SharePoint.SPSite+StorageManagementSortedOn soVar, System.UInt32 nMaxResults)  
#  
# ltVar: What kind of storage management information to display  
# List = 1  
# DocumentLibrary = 2  
# Document = 3  
# sordVar: the direction in which the items are to be sorted  
# Increasing = 0×10  
# Decreasing = 0×11  
# soVar: whether the items are sorted by size or by date  
# Size=0  
# Date = 1  
# nMaxResults: the number of results to return

$DT = $site.StorageManagementInformation(2,0x11,0,$(($site.allwebs).count));  
$DT | Select @{Label="Size"; Expression={[INT]($\_.Size/1MB)}},Directory | out-gridview  
$site.Dispose()  
[/ps]

Seems as if I forgot the last line, where I dispose of the Site object, I have added that now.

