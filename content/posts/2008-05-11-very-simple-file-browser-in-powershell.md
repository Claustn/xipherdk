---
categories:
- Tech Stuff
date: "2008-05-11T20:36:17Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6102325541"
status: publish
tags: []
title: Very simple file browser in PowerShell
---
One of my friends needed a script that would be able to list all files in a folder, and then he should be able to choose the file, and return the name of the file. Quite similar to what I have written about in a previous post. But in the previous example I used the BrowseForFolder method, which works in a very limited way, when you try to choose files with it.. (I guess there is a reason why it is called BrowseForFOLDER)

So I tried to figure out another way to do it.. And I came across a script Marc Van Orsouw wrote, and that gave me an idea..

Use the .Net framework and create a function, that shows all the files in a folder in a Listbox, then use and event trigger to see when the "file" is clicked.

The script is using PsEventing, which you can download from CodePlex... (Eventing should be included in PowerShell CTP 2 -v2, but I have not played with that yet, so you need to download PsEventing )

[Sourcecode lang="ps"]  
[void][System.Reflection.Assembly]::LoadWithPartialName("System.windows.forms")  
Add-PSSnapin PSEventing

Function invoke-TextViewer ($dir = '.'){

$form = New-Object System.Windows.Forms.Form  
$form.text = "PowerShell Text viewer " + (resolve-path $dir)

$lv = New-Object System.Windows.Forms.ListView  
$lv.View = 'list'  
$lv.Dock = 'Fill'

$form.Controls.Add($lv)

$Form.Add\_Shown({  
ls $dir |? {$\_.PSIsContainer -eq $false} |% {$lv.Items.Add($\_.name)}  
$form.Activate()  
})

$form.show()

Connect-Eventlistener -VariableName lv -EventName ItemActivate  
Connect-Eventlistener -VariableName form -EventName Closed

while( -not $path) {  
$event = get-Event -Wait  
if ($event.Name -eq "Closed") {return}  
$path = join-path $dir $event.source.value.selecteditems[0].text  
Return $path

}

}

$Filename = invoke-TextViewer c:\scripts  
$Filename

[/Sourcecode]

As you can see, I have created a function that takes a "Path" as an input, it will then read all the files in the given directory, and populate a listbox with it. You can then select one of the files, in the listbox, then the function will return the full path to the file.

