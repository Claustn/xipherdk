---
categories:
- Everyday
date: "2014-01-08T23:51:04Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6102479577"
status: publish
tags: []
title: Readable Password generator
---
Because of a report from our auditors, I was tasked with writing a password generator  
for our helpdesk staff, so instead of using a generic variation of the same password,  
they needed something more "secure".

One of the issues is that this password is often read to the user over the phone, so just generating  
a random string of characters, would make it very hard to convey over the phone.

So what I did was take a list of common words and generate a password based on the words from a wordlist.

The package consists of 1 script file, and 6 wordlist files (I have taken 100 words with 2,3,4,5,6,7 characters, more words can easily be added to the files)  
 (This package have wordlists, the one I have for work, I am using Danish words)

```
#The wordlist files should be located in the same folder as the script. $FilePath = Split-path $script:MyInvocation.MyCommand.Path Function Get-RandomPWD { [CmdletBinding()] param ( [switch]$RandomizeOrder ) #I generate 10 password, because sometimes the passwords generated fall in NSFW category For ($i=1;$i -le 10;$i++) { $FullPWD = @() #First I pick the length of the first word between 2 and 6 characters $value1 = Get-Random -Minimum 2 -Maximum 6 #Getting a random number $Value2 = Get-Random -Minimum 1 -Maximum 99 # Figure out how many characters I have to the second word $value3 = 10 - $value1 - ($Value2.ToString().Length) #Actually getting the first and second random word from the txt file containing the wordlist $PWD1 = Get-Content "$FilePath\uk$($value1).txt" | Get-Random $PWD2 = Get-Content "$FilePath\uk$($value3).txt" | Get-Random #To make sure I have at least 1 uppercase letter, I select a random letter from the second word [STRING]$PWD2RandomChar = $PWD2.ToCharArray() | Get-Random #I now do a simple replace of the randomly selected character to an uppercase letter. $PWD3 = $PWD2 -replace ($PWD2RandomChar, $PWD2RandomChar.ToUpper()) $FullPWD += $PWD1,$Value2,$PWD3 If ($RandomizeOrder) { $r = new-object Random #Randomize the order of 3 parts of the password $PassWD += "$(($FullPWD | Get-Random -Count $FullPWD.Count) -join '')`n" } Else { #Output the password as Word Number Word $PassWD += "$PWD1$value2$PWD3`n" } } $passwd } Get-RandomPWD -RandomizeOrder
```

Download full package here: <font size="5"><a href="http://www.xipher.dk/assets/images/uploads/PWDGenerator.zip">PWDGenerator</a></font>

