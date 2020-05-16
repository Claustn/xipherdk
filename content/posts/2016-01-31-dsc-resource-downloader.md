---
categories:
- Everyday
date: "2016-01-31T22:26:39Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6103212114"
status: publish
tags: []
title: DSC resource downloader
---
I have been playing a lot with Azure automation, and Azure DSC pullserver in particular. I will not get into DSC or Azure automation in great detail in this post.

My goal was to set up an Automation Account, that could be used to handle multiple different servers and setups, so that meant that I had to install multiple DSC resources into the Automation account.  
Currently there is a limitation in Azure DSC pullserver, that it does not support versioned modules (https://azure.microsoft.com/en-gb/documentation/articles/automation-dsc-overview/)

> The traditional PowerShell DSC pull server expects module zips to be placed on the pull server in the format ModuleName\_Version.zip”. Azure Automation expects PowerShell modules to be imported with names in the form of ModuleName.zip. See this blog post for more info on the Integration Module format needed to import the module into Azure Automation.

This means that you have to pack your modules differently depending on if you are going to use them on an internal DSC pullserver which supports versioning or in Azure.

So I wrote a small script, that will download DSC resources and zip them, depending on if they are going to be used in Azure or on a regular pullserver (When downloading for a "regular" pullserver, it will also create a checksum file)

```
```

One thing to notice here is that when I create the zip files, I am using [io.compression.zipfile], the reason for this is that there is a bug in Compress-Archive, that sometimes prevents you from unpacking the zip files with 3rd party tools like 7-zip, I have also come across that Azure Automation cannot unzip the files (Caused me to waste a looooot of time, to figure that one out)

