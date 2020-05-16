---
categories:
- Everyday
date: "2016-01-05T00:02:55Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6130415356"
status: publish
tags: []
title: Run Azure Automation Hybrid Worker using "RunAs"
---
Today I was working at a client, doing a bunch of Azure Automation stuff, moving a lot of the scheduled jobs into Azure Automation, when I noticed this little button.  
[![Runas](/assets/images/Runas.png)](http://www.xipher.dk/WordPress/?attachment_id=809)

So I had to dig a little deeper, you can now run your Hybrid Worker Jobs as user, that you have stored in your Azure Automation credential store. Before Hybrid Worker jobs, would run as "LocalSystem" since that is the account the agent is running under. I don't know, when this was added, but it has to be recently.

In order to set it up, you have to logon to the azure portal and go to

Automation Accounts -\> <automation account name> -&gt; Hybrid Worker Groups -&gt; </automation><automation workergroup name> -&gt; Settings -&gt; Hybrid worker group settings</automation>

