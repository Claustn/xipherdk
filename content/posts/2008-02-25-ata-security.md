---
categories:
- Tech Stuff
date: "2008-02-25T00:04:38Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6116362977"
status: publish
tags: []
title: ATA security
---
<p>The below Princeton  "Lest we Remember: Cold Boot Attacks on Encryption Keys" article sparked a debate on the minasi forum (Which most people know I participate actively in)</p>
<p>The user Douggg suggested the use of a to mostly unknown security feature of modern ATA drives, which enables a user to password protect the drive it self.</p>
<p>So I decided to do some research into it.</p>
<p>The Security Mode feature set was initially created for 2½" disks (laptop disks) in about 1996.</p>
<blockquote>
<p>--------------------------------------------------------------------------------<br />
The optional Security Mode feature set is a password system that restricts access to user data stored on a<br />
device. The system has two passwords, User and Master and two security levels, High and Maximum. The<br />
security system is enabled by sending a user password to the device with the SECURITY SET PASSWORD<br />
command. When the security system is enabled, access to user data on the device is denied after a power<br />
cycle until the User password is sent to the device with the SECURITY UNLOCK command.<br />
A Master password may be set in a addition to the User password. The purpose of the Master password is to<br />
allow an administrator to establish a password that is kept secret from the user, and which may be used to<br />
unlock the device if the User password is lost. Setting the Master password does not enable the password<br />
system.<br />
The security level is set to High or Maximum with the SECURITY SET PASSWORD command. The security<br />
level determines device behavior when the Master password is used to unlock the device. When the security<br />
level is set to High the device requires the SECURITY UNLOCK command and the Master password to<br />
unlock. When the security level is set to Maximum the device requires a SECURITY ERASE PREPARE<br />
command and a SECURITY ERASE UNIT command with the master password to unlock. Execution of the<br />
SECURITY ERASE UNIT command erases all user data on the device.<br />
The SECURITY FREEZE LOCK command prevents changes to passwords until a following power cycle. The<br />
purpose of the SECURITY FREEZE LOCK command is to prevent password setting attacks on the security<br />
system.<br />
--------------------------------------------------------------------------------
</p></blockquote>
<p>What this seems mean is that you can set two security modes on the drives High & Maximum</p>
<p>In High security mode both the "user" and the "master" password is able to unlock the drive.</p>
<p>In Maximum only the "user" password will unlock the drive, the "master" password needs to be sent with a delete all command, that overwrites the entire disk with 0's and then allows access to the disk.</p>
<p>IBM started making this widely available in their 3½" disks in 1998 as well, Microsoft later asked Seagate to build the security into their 3½ drives for the Xbox to protect them, later WD followed suit.<br />
This means that practically all disks today have the "security features" available.</p>
<p>As a security precaution the devices have a function called "SECURITY FREEZE LOCK", when this command is sent to the device it will not accept any changes to the password until next boot. The problem is that most PC manufacturers have not added this feature to the BIOS of PC's meaning that it is theoretically possible to set the password when the PC is running, rendering the disk unreadable at next boot.</p>
<p>Luckily for us the windows API that sends "ATA" commands does not support the commands:</p>
<blockquote><p>
--------------------------------------------------------------------------------</p>
<p>SECURITY SET PASSWORD<br />
-<br />
SECURITY UNLOCK<br />
-<br />
SECURITY ERASE PREPARE<br />
-<br />
SECURITY ERASE UNIT<br />
-<br />
SECURITY FREEZE LOCK<br />
-<br />
SECURITY DISABLE PASSWORD<br />
--------------------------------------------------------------------------------
So in order for someone to abuse it, that person would have to gain admin access to the PC, install a kernel mode driver that can communicate directly with the disk, and the BIOS should not have sent the "SECURITY FREEZE LOCK" command.

On the security topic Heise.de tested it, and found that it was not enough to remove the circuit board and replace it with a board from an unlocked drive. So password data is stored on the drive platters as well.  
They sent the drive to IBAS (Norwegian data recovery company) who were able to recover the key.. (They call it a trade secret)

As with most closed systems it is not known is there is a master master password, though vendors claim there isn't.

So if you want to add another security hurdle besides encryption, you find a PC with a BIOS that supports the security features, set security the maximum, encrypt the disk with your favorite encryption software. This should scare off most except the mosts adamant hackers or big brother.

There is a tool called WinAAM (German) which is used to manipulate drives acoustics, it will also tell you the current security setting of the drive (You can use it to see if your BIOS sets the SECURITY FREEZE LOCK on the drive) If it doesn't you might consider to check for a BIOS update, and you might be lucky that the new BIOS sets it.

From what I have read I am convinced that the ATA security standard is not unbreakable, with the right experience/equipment it is still possible to bypass the ATA password, and it does not mitigate the initial problem of the possibility to extract encryption keys from RAM. But it is definitely an extra layer of security.

