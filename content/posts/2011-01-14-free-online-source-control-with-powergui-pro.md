---
categories:
- Everyday
date: "2011-01-14T11:52:49Z"
meta:
  dsq_thread_id: "6099514113"
status: publish
tags: []
title: Free online Source Control with PowerGui Pro.
---
Currently I am using a free plan with Dynamsoft for source control with PowerGui, you get 25 MB for free, which will last me a long time at my current speed of writing PowerShell scripts ![Smile](/assets/images/wlEmoticon-smile.png)

&nbsp;

Here is a quick guide to setting up an account.

Go to [http://www.dynamsoft.com/products/SAWHosted\_FreePlan.aspx](http://www.dynamsoft.com/products/SAWHosted_FreePlan.aspx "http://www.dynamsoft.com/products/SAWHosted\_FreePlan.aspx") and sign up for a free account.

Just fill in your information, and wait for the confirmation email. When the mail arrives you have to click the link to activate your account, on that page there is a link to download the Dynamsoft Source AnyWhere Hosted Server Manager:

[http://www.dynamsoft.com/downloads/sawhosted\_download.aspx](http://www.dynamsoft.com/downloads/sawhosted_download.aspx "http://www.dynamsoft.com/downloads/sawhosted\_download.aspx")

I my case running on Windows I choose the Windows version ![Smile](/assets/images/wlEmoticon-smile.png)

&nbsp;

[![image](/assets/images/image_thumb.png "image")](http://www.xipher.dk/assets/images/image.png)

Download and install the client.

It prompts you if you want to make it your default Source Control provider, which in my case I choose yes, since this is my private machine.

[![SNAGHTML562d4a8](/assets/images/SNAGHTML562d4a8_thumb.png "SNAGHTML562d4a8")](http://www.xipher.dk/assets/images/SNAGHTML562d4a8.png)

&nbsp;

You then fire you PowerGui Pro, and go to Tools –\> Options

[![image](/assets/images/image_thumb1.png "image")](http://www.xipher.dk/assets/images/image1.png)

Under options you choose&nbsp; “Version Control” and in Current provider you choose **Dynamsoft SourceAnyware Hosted** then click advanced.

[![SNAGHTML5665897](/assets/images/SNAGHTML5665897_thumb.png "SNAGHTML5665897")](http://www.xipher.dk/assets/images/SNAGHTML5665897.png)

In the General Pane I choose a new path for saving Temp files.

[![SNAGHTML5687a49](/assets/images/SNAGHTML5687a49_thumb.png "SNAGHTML5687a49")](http://www.xipher.dk/assets/images/SNAGHTML5687a49.png)

Under External Programs I put in the path for PowerGUI Pro, in my Case: C:\Program Files (x86)\Quest Software\PowerGUI Pro\ScriptEditor.exe

(I use WinMerge for file comparison, so that is what you see in the image below WinMerge can be downloaded here: [http://winmerge.org/](http://winmerge.org/) )

[![SNAGHTML56e1b87](/assets/images/SNAGHTML56e1b87_thumb.png "SNAGHTML56e1b87")](http://www.xipher.dk/assets/images/SNAGHTML56e1b87.png)

If you do not want to view the DynamSoft Login log every time you login.

[![SNAGHTML573002b](/assets/images/SNAGHTML573002b_thumb.png "SNAGHTML573002b")](http://www.xipher.dk/assets/images/SNAGHTML573002b.png)

First time you try to save a script in PowerGui Pro, it will prompt you if you want to check it into your Source Control system.

A login box will appear, fill in the fields with the data from your welcome mail, and choose if you want to be prompted for a password, everything you check something in/out.

[![SNAGHTML57787e1](/assets/images/SNAGHTML57787e1_thumb.png "SNAGHTML57787e1")](http://www.xipher.dk/assets/images/SNAGHTML57787e1.png)

You get prompted to choose a Repository, in this case just choose “default”

[![SNAGHTML57a1a7e](/assets/images/SNAGHTML57a1a7e_thumb.png "SNAGHTML57a1a7e")](http://www.xipher.dk/assets/images/SNAGHTML57a1a7e.png)

In my case I have a few existing Projects, but I choose to create a new Project called “Test Project”

[![SNAGHTML57b4852](/assets/images/SNAGHTML57b4852_thumb.png "SNAGHTML57b4852")](http://www.xipher.dk/assets/images/SNAGHTML57b4852.png)[![SNAGHTML589f256](/assets/images/SNAGHTML589f256_thumb.png "SNAGHTML589f256")](http://www.xipher.dk/assets/images/SNAGHTML589f256.png)

You then get prompted to put in a comment

[![SNAGHTML58aa56f](/assets/images/SNAGHTML58aa56f_thumb.png "SNAGHTML58aa56f")](http://www.xipher.dk/assets/images/SNAGHTML58aa56f.png)

In PowerGui you now have an extra menu called “Version Control” and you can see a mark on each tab if your script is in the source control and if it is checked in or out.

[![image](/assets/images/image_thumb2.png "image")](http://www.xipher.dk/assets/images/image2.png) (The arrow on the tab is blue if the script is checked in, and red if it is checked out)

I have checked the script out (If you try to edit the script, it will automatically prompt you to check out the script), I made some changes, and try to check it in again.

[![image](/assets/images/image_thumb3.png "image")](http://www.xipher.dk/assets/images/image3.png)

[![image](/assets/images/image_thumb4.png "image")](http://www.xipher.dk/assets/images/image4.png)

You get prompted to enter a comment, after that is entered the file is checked back in.. (You can leave to comment field blank if you do not want to write something)

[![SNAGHTML590ed57](/assets/images/SNAGHTML590ed57_thumb.png "SNAGHTML590ed57")](http://www.xipher.dk/assets/images/SNAGHTML590ed57.png)

You can manage your software projects from the DynamSoft application.

Go to start menu and choose **Dynamsoft SourceAnywhere Hosted**

Sign in with your username/Password

&nbsp;

First thing do to is go to Tools –\> Options –\> External Programs

&nbsp;

[![SNAGHTML5bbb316](/assets/images/SNAGHTML5bbb316_thumb.png "SNAGHTML5bbb316")](http://www.xipher.dk/assets/images/SNAGHTML5bbb316.png)

As you can see my test project is now in my list of projects.

[![image](/assets/images/image_thumb5.png "image")](http://www.xipher.dk/assets/images/image5.png)

One thing I have noticed is that Dynamsoft default checks files in as Binary files, which means it does not allow you to do a text comparison of the two, in order to change that&nbsp; right click the .ps1 file and choose properties.

[![image](/assets/images/image_thumb6.png "image")](http://www.xipher.dk/assets/images/image6.png)

Then you change the type from Binary to Mergeable, you can now compare your different versions of checked in files.

[![SNAGHTML5a0136e](/assets/images/SNAGHTML5a0136e_thumb.png "SNAGHTML5a0136e")](http://www.xipher.dk/assets/images/SNAGHTML5a0136e.png)

&nbsp;

Right click the file you want to “compare”, then choose&nbsp; “Show History”, you get a lot of options to choose from, in most cases the default options are enough. Click OK

&nbsp;

In this example I have made an initial version of the script then made some changes and checked it back in.

I highlight both files and choose Diff

[![SNAGHTML5a42317](/assets/images/SNAGHTML5a42317_thumb.png "SNAGHTML5a42317")](http://www.xipher.dk/assets/images/SNAGHTML5a42317.png)

This will bring up WinMerge and show the differences (This is a horrible example of file comparison I Know)

&nbsp;

[![image](/assets/images/image_thumb8.png "image")](http://www.xipher.dk/assets/images/image8.png)

&nbsp;

This guide should be enough to get you started…&nbsp; ![Smile](/assets/images/wlEmoticon-smile.png)

&nbsp;

Edit:

&nbsp;

Here is a better example of script a script comparison.

[![SNAGHTML5a9248d](/assets/images/SNAGHTML5a9248d_thumb.png "SNAGHTML5a9248d")](http://www.xipher.dk/assets/images/SNAGHTML5a9248d.png)

[![image](/assets/images/image_thumb9.png "image")](http://www.xipher.dk/assets/images/image9.png)

