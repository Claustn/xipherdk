---
categories:
- Everyday
date: "2012-09-10T21:38:40Z"
meta:
  dsq_thread_id: "6103182438"
status: publish
tags: []
title: Troubleshooting Test-Connection
---
I was contacted by a friend who was having some issues with Test-Connection&nbsp; when using TimeToLive

Example:

Test-Connection -ComputerName [www.google.ie](http://www.google.ie/) -Count 1 -TimeToLive 3

Test-Connection : Testing connection to computer '[www.google.ie](http://www.google.ie/)' failed: Problem with some part of the filterspec or providerspecific buffer in general

At line:1 char:1

+ Test-Connection -ComputerName [www.google.ie](http://www.google.ie/)&nbsp; -TimeToLive 3

+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

&nbsp;&nbsp;&nbsp; + CategoryInfo&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : ResourceUnavailable: (www.google.ie:String) [Test-Connection], PingException

&nbsp;&nbsp;&nbsp; + FullyQualifiedErrorId : TestConnectionException,Microsoft.PowerShell.Commands.TestConnectionCommand

So I fired fired up Reflector to see what Test-Connection actually does, and it is using WMI and the&nbsp; Win32\_PingStatus class, I have not been able to reproduce the error calling the class directly, so I was wondering where the problem could be.

So I did a&nbsp; trace command to see if that would give me anything, one thing I noticed in the return value there was a value called 11013, shortly thereafter this was written in the trace.

_MemberResolution Information: 0 :&nbsp;&nbsp;&nbsp;&nbsp; "writeErrorStream" NOT present in type table._

_MemberResolution Information: 0 :&nbsp;&nbsp;&nbsp;&nbsp; Adapted member: not found._

This made me think that there might be an error in converting the Errorcode from its numerical value into text

So I&nbsp; looked up the errorcode from ICMP\_ECHO\_REPLY32 structure, which has an error code called 11013 which is:

[http://msdn.microsoft.com/en-us/library/windows/desktop/bb540657(v=vs.85).aspx](http://msdn.microsoft.com/en-us/library/windows/desktop/bb540657(v=vs.85).aspx)

<dl>
<dt>
<strong>IP_TTL_EXPIRED_TRANSIT</strong> </dt>
<dt>11013 </dt>
<dt></dt>
</dl>

The time to live (TTL) expired in transit.

Which seems fair because TTL is relatively low, then I looked a bit deeper to try and find what 11013 also could mean

In&nbsp; Winsock Error Codes I found:

[http://msdn.microsoft.com/en-us/library/windows/desktop/bb540657(v=vs.85).aspx](http://msdn.microsoft.com/en-us/library/windows/desktop/bb540657(v=vs.85).aspx)

<dl>
<dt>
<strong>WSA_QOS_BAD_OBJECT</strong> </dt>
<dt>11013</dt>
</dl><dl>
<dt>QoS bad object. </dt>
<dd>
<p>A problem was encountered with some part of the filterspec or the provider-specific buffer in general.</p>
</dd>
</dl>

Then I did some additional testing.

[![PingPowerShell](/assets/images/PingPowerShell_thumb.png "PingPowerShell")](http://www.xipher.dk/assets/images/PingPowerShell.png)  
If you do a Trace-Command on the example with TimeToLive = 11 which gives the error: Error due to lack of ressources, you will see that the error code is 11010 which corresponds to:

<dl>
<dt>
<strong>WSA_QOS_ADMISSION_FAILURE</strong> </dt>
<dt>11010</dt>
</dl><dl>
<dt>QoS admission error. </dt>
<dd>
<p>A QoS error occurred due to lack of resources.</p>
</dd>
</dl>

If you look at the WMI Win32\_PingStatus error codes 11010 corresponds to:

<dl>
<dt>
<strong>IP_REQ_TIMED_OUT</strong> </dt>
<dt>11010</dt>
</dl>

The request timed out.

Which you can confirm using ping from the command prompt:

[![PingDOS](/assets/images/PingDOS_thumb.png "PingDOS")](http://www.xipher.dk/assets/images/PingDOS.png)

