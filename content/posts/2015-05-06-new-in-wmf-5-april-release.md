---
categories:
- Everyday
date: "2015-05-06T21:16:35Z"
meta:
  _edit_last: "1"
status: publish
tags: []
title: New in WMF 5 April release
---
<p>I have been playing a lot with DSC, and I have therefore had to use [System.Guid]::NewGuid() a lot, to create GUIDs for the DSC configuration clients.<br />
PS C:\> [System.Guid]::NewGuid()</p>
<p>Guid<br />
----<br />
8a0db54d-7f46-479e-a449-cbe302e4297b</p>
<p>But in this latest revision, there is a new CMDLET that lets you create  GUIDs: New-Guid</p>
<p>PS C:\> New-Guid</p>
<p>Guid<br />
----
  
2889dbbb-0389-4b50-a741-eb4ab7e20210

If you need to copy and paste the GUID into a configuration, you can do something like this:

```
(New-Guid).ToString() | clip
```

or

```
New-Guid | select -ExpandProperty Guid | clip
```
