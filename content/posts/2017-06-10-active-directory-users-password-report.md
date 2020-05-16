---
categories:
- Everyday
date: "2017-06-10T09:02:28Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6102033477"
  enclosure: "http://www.xipher.dk/WordPress/wp-content/uploads/Password-report.mp4\r\n24309219\r\nvideo/mp4\r\n"
status: publish
tags: []
title: Active Directory Users Password Report
---
This is a test
I was working with a customer; all their admin users had at least three accounts, a regular account, a domain admin account and a "server admin" account, some even had a "client admin" account as well per domain.

In a discussion with security, they told me that now, everything was good, and they were more secure. I told them that in theory, they were more secure, but what if their admins just used the same password for all their accounts.

They did not think that, that was a real problem, and that none of their admins would reuse their passwords across their different accounts.

I had a sneaking suspicion that that was not the case, so I decided to test it out, which required a little ingenuity.

So I sat down and wrote a list of things that I would like to get from AD. ( I was specifically told not to use any "brute force" methods to guess their user's passwords, but I was allowed to compile a list of "improbable" used passwords like summer2017, Fall2017 etc. that security was sure no one would use ;) )

**• All current users with the same password  
 • Currently Most used Hash/Password statistics  
 • Most Used Hash Historically  
 • Users with "weak" password (Based on list of known passwords)  
 • User who have never changed password  
 • Users with a blank password  
 • Users who have had a blank password  
 • Users who have reused a password**

If I could get that data out of AD, I would be able to give the security team an idea about the current status of their "users."

**Just to get one thing straight, by running the below scripts you are probably violating every security guideline in your company, do not play around with this, unless you understand what you are doing, and have written consent from your company. Also in the below examples, I am not adhering to "good" security practice by revealing the passwords of users (In my example it is only test data), again this was done to prove a point about insecure passwords. Last but NOT least, you have just copied ALL the keys to the kingdom from AD to unprotected files, I cannot stress this enough, you have to protect everything you export from AD!!!**

I had previously helped Danish Security researcher Jakob H Heidelberg in regards to a PowerShell script using [DSinternals](http://dsinternals.com) from Michael Grafnetter he had written to do something similar, so I knew it was possible to get this from AD, but I wanted to create a solution that would scale, and be able to create a report that was easily searchable.  
You can find Jakob's original script [here](https://github.com/ZilentJack/Get-bADpasswords)

As you probably know, by default you cannot get AD to reveal a users Password hash, you need some way to extract that from the AD database, I am not going to go into details about that here, and in the below example I will use DSInternals functionality to "pretend" to be a domain controller, and that way get a real domain controller to sync over all its information. There are other ways to obtain the same information, but that is out of scope for this blog post.

The first thing I did was to compile a short list of very easily guessable passwords, and simple permutations thereof, again we are talking Summer2016, Winter2017, CompanyName01 etc. etc.

In the sample data, I am using for this blog post, I created an AD and created 500 accounts using a modified version of Helge Kleins tool here: [https://helgeklein.com/blog/2015/02/creating-realistic-test-user-accounts-active-directory/](https://helgeklein.com/blog/2015/02/creating-realistic-test-user-accounts-active-directory/) This way of using "real names" the users are more easily told apart than if I had just created random strings.

[![](/assets/images/mstsc_2017-06-11_22-51-08-1024x695.png)](http://www.xipher.dk/WordPress/wp-content/uploads/mstsc_2017-06-11_22-51-08.png)

(/assets/images/mstsc_2017-06-11_22-51-08-1024x695.png)](http://www.xipher.dk/WordPress/wp-content/uploads/mstsc_2017-06-11_22-51-08.png)


After creating the users I randomly assigned them as a password from a list of known passwords, I created a list of passwords MuchoSecuros1 through MuchoSecuros2000, I assigned these password to users at random, I did this just to make sure I had users with the same password. Again this was chosen for easy recognition of the results.

Then I needed a way to query the data that I pulled from AD, and here I ended up choosing SQLite, for those of you that do not know it, SQLite is a small database engine, that does not require any installation, and can be run just using an SQLite DLL file. Here I am using Warren "RamblingCookieMonster" Frames SQLite PowerShell module, which includes the DLL files and code to manipulate it.

In the database I created three tables:  
 • HashHistory (Containing all users Hash History, in this example AD stores the last 20 password hashes a user have used)  
 • PWDTranslation (All the "known" passwords and their generated hash value)  
 • Users (All the user's data)

[![](/assets/images/DB-Overview.png)](http://www.xipher.dk/WordPress/wp-content/uploads/DB-Overview.png)

By putting it in a database like this, I made it very easy (fast) to query, and as an added benefit I could add data to this over time, to store more than the 20 last password hashes.

Let's get down to business!

First off, the script is not 100% self-contained it requires some external modules as well (It is, of course, easy to create a package, that contains all the modules, and copy them to an offline machine)

Required PowerShell modules:

• PSSQLite  
 • EnhancedHTML2  
 • Dsinternals

Currently, the "main" script is split into three files.

• Setup.ps1 (Setup the SQLite database)  
 • LoadDataIntoSQL.ps1 (Loads data into SQLite)  
 • ReportGenerator.ps1 (Takes data from DB and creates HTML report)

In all of the three above files, there is a reference to the SQLite Database file, that has to be changed to reflect where you want to store the DB file.

In LoadDataIntoSQL.ps1 there is also a variable called $Passwords this gets populated with a list of "known" passwords, in the script, this just refers to a file with a single password on each line, each password will get converted into a hash and stored in the DB.

It is also from this file that Get-ADReplAccount is called, which is the cmdlet that syncs with a domain controller, so if you do not have permissions to do that, it will fail.

ReportGenerator has a variable called $ReportPath, which is where the report will be stored.

One thing to note is that the Report loads a client side javascript to do the formatting, only Internet Explorer allows you to do that.

Javascripts are located here:  
'http://ajax.aspnetcdn.com/ajax/jquery.dataTables/1.9.3/jquery.dataTables.min.js'  
'http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.2.min.js'

To get more detailed data I also wrote a small snippet of code, to change the password for each user the same amount of times that is configured in AD, I did this to show how password history is used in the script.

First I generate a list of 2000 passwords, and load them into a variable:

```powershell
1..2000 | % { "MuchoSecuros$\_" | Out-file C:\temp\PasswordChange\Passwords.txt -Append } $Passwords = Get-Content C:\temp\PasswordChange\Passwords.txt
```

Then I use ADSI to get information about the domain:

```powershell
$ADDomain = [ADSI]"WinNT://$env:userdomain"
```

Then I use this information to figure out, what the password history is on the domain and change the password for each account that amount of times

```
Measure-Command -Expression { 1..$($ADDomain.PasswordHistoryLength) | % { Get-ADUser -Filter \* -SearchScope Subtree -SearchBase "OU=TestUsers,DC=pwdtest,DC=test" | % { Set-ADAccountPassword -Identity $\_.DistinguishedName -Reset -NewPassword (ConvertTo-SecureString -AsPlainText $($Passwords | Get-Random) -Force) } } }
```

Here is a video showing the scripts in action. (Be gentle this is my first attempt at creating a video)  
[video width="1920" height="1080" mp4="http://www.xipher.dk/WordPress/wp-content/uploads/Password-report.mp4"][/video]

Link to the files on Github [https://github.com/Claustn/Password-Report](https://github.com/Claustn/Password-Report)

