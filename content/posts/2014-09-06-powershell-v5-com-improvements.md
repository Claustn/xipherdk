---
categories:
- Everyday
date: "2014-09-06T10:03:43Z"
meta:
  _css: ""
  _edit_last: "1"
  _raindrops_this_header_image: default
status: publish
tags: []
title: PowerShell v5 COM improvements
---
When I saw that Lee Holmes twittered about COM had been improved in WMF 5 September release, I just had to try it right away..

I have several scripts that does some auditing of computers in AD, and populates an Excel spreadsheet, where different properties get highlighted depending on its value. It usually takes around 10 minutes pr 200 servers, I upgraded the machine from PS v3 to PS v 5 September release and went down to 2 minutes for the same 200 machines. Almost 5x improvement. If you don't believe me I have to screnshots to prove it :)

**PowerShell v3**  
[![PS 3 COM](/assets/images/PS-3-COM-300x173.png)](http://www.xipher.dk/WordPress/wp-content/uploads/PS-3-COM.png)  
**PowerShell v5**  
[![PS 5 COM](/assets/images/PS-5-COM-300x175.png)](http://www.xipher.dk/WordPress/wp-content/uploads/PS-5-COM.png)

