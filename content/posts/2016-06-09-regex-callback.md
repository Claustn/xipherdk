---
categories:
- Everyday
date: "2016-06-09T22:26:24Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6113163883"
status: publish
tags: []
title: Regex CallBack
---
A friend of mine had had to go through a lot of data, and substitute some values... But these weren't 1-1 replacement, he had to find the value which was in HEX and then convert them to regular characters.  
The files he had to traverse had several million lines each, so certain performance considerations had to be taken.

My initial thinking was to use regular expressions, since they excel at manipulating text. So I started playing around with the -replace operator in PowerShell

In PowerShell you can do something like:

```
$Text -Replace '(?ismx)\[([0-9A-F]+)\]','$1'
```

Which will replace the matching string, with numbered capturing group 1 from the Regex in this case the HEX value matching ([0-9A-F]+) in Regex parenthesis denotes a capturing group.

But I needed to change the HEX string to a "string", so I tried different methods, but the PowerShell -Replace does not support callbacks.

So what I did was to create a callback function (Just named it Callback, could be called anything)

Instead of using -Replace I use the .Net type accelerator [Regex] to define the regex, and call the ::Replace method in that, which does support callbacks

```
$CallBack = { param($match) HexConvert -HEX $($match.Groups[1].Value) } $Regex = [regex]'(?ismx)\[([0-9A-F]+)\]' $Text = (Get-Content C:\temp\Input.txt -Raw ) [Regex]::Replace($Text,$Regex,$CallBack) | Set-Content C:\temp\Output.txt}
```

This literally cut the running time down from hours to minutes.

