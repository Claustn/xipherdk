---
categories:
- Everyday
date: "2014-02-22T20:22:37Z"
meta:
  _edit_last: "1"
  _oembed_0ac3952d2aa033dc81844d9bf586cbc4: '{{unknown}}'
  _oembed_590fef62a1263a9e2c915903926d8b7e: '{{unknown}}'
  _oembed_5aa31df53496032b3def69a3cef67a6f: '{{unknown}}'
  _oembed_16494e4cef4dbe33f007c44c6f1d9f30: '{{unknown}}'
  _oembed_1bd23d27a962aecae5d47cb361d7238e: '{{unknown}}'
  _oembed_28688598a0e687bda9b039b867cf9f76: '{{unknown}}'
  _oembed_a53d277557729e932779cd2efc011ec6: '{{unknown}}'
  dsq_thread_id: "6101228791"
status: publish
tags: []
title: Updating UPN names in Active Directory
---
<p>We are in the process of testing out Office 365, to see if it will be useful for us, so initially we are just going to use DirSync for some specific users, instead of setting up the complete ADFS solution. I have been extremely busy lately, so I decided to hire someone to come in and setup the DirSync and change the UPN's for the users who are going to the cloud.</p>
<p>Everything went fine, I gave him a list of OU's which contained the users who needed the change, and he started opening each user going in and changing the UPN from the GUI.. But apparently I have gotten allergic to doing stuff in the GUI, so I ended writing a small script for him. Which after some procrastinating ended up with a GUI.</p>
<p>It will load a tree view of the current directory (Requires AD cmdlets to be present on the system), then you can select each OU you want to change the containing users UPN name, it will also let you choose to recurse through multiple OU's.</p>
<p>Be aware this is version 1, so there are no "are you sure prompts", proceed with caution.. </p>
<p>I have exported the code from PowerShell studio with recovery info, so you can load the form, and work with it.</p>
<p><a href="http://www.xipher.dk/assets/images/uploads/UPNChanger.jpg"><img src="/assets/images/UPNChanger-258x300.jpg" alt="UPNChanger" width="258" height="300" class="alignnone size-medium wp-image-747" /></a></p>
<p>The treeview code is based on code from Thepip3r:<br />
http://thepip3r.blogspot.dk/2011/06/powershell-guis-active-directory.html</p>
<pre class="lang:ps decode:true ">#------------------------------------------------------------------------
# Source File Information (DO NOT MODIFY)
# Source ID: c420d64b-72fd-475f-8ca7-939cd9a6285e
# Source File: C:\Users\ctn\Documents\SAPIEN\PowerShell Studio 2012\Files\OU Picker.pff
#------------------------------------------------------------------------
#region File Recovery Data (DO NOT MODIFY)
&lt;#RecoveryData:
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
PnImKgAA#&gt;
#endregion
#========================================================================
# Code Generated By: SAPIEN Technologies, Inc., PowerShell Studio 2012 v3.1.29
# Generated On: 22-02-2014 20:16
# Generated By: CTNADM
#========================================================================
#----------------------------------------------
#region Application Functions
#----------------------------------------------

function OnApplicationLoad {
	#Note: This function is not called in Projects
	#Note: This function runs before the form is created
	#Note: To get the script directory in the Packager use: Split-Path $hostinvocation.MyCommand.path
	#Note: To get the console output in the Packager (Windows Mode) use: $ConsoleOutput (Type: System.Collections.ArrayList)
	#Important: Form controls cannot be accessed in this function
	#TODO: Add snapins and custom code to validate the application load
	#Import-Module Pipeworks
	#Import-Module Pscx
	Import-Module Pipeworks
	
	
	if (-not (Get-Module ActiveDirectory)){            
 		 Import-Module ActiveDirectory            
	}            
   
	#TODO: Place custom script here
	$domain = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()            
	$Script:strDomainDN = ($domain.GetDirectoryEntry()).distinguishedName 
	$Script:upnDN = "cn=Partitions,cn=Configuration,$Script:strDomainDN" 
	
	return $true #return true for success or false for failure
}

function OnApplicationExit {
	#Note: This function is not called in Projects
	#Note: This function runs after the form is closed
	#TODO: Add custom code to clean up and unload snapins when the application exits
	
	$script:ExitCode = 0 #Set the exit code for the Packager
}

#endregion Application Functions

#----------------------------------------------
# Generated Form Function
#----------------------------------------------
function Call-OU_Picker_pff {

	#----------------------------------------------
	#region Import the Assemblies
	#----------------------------------------------
	[void][reflection.assembly]::Load("mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089")
	[void][reflection.assembly]::Load("System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089")
	[void][reflection.assembly]::Load("System.Windows.Forms, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089")
	[void][reflection.assembly]::Load("System.Data, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089")
	[void][reflection.assembly]::Load("System.Drawing, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a")
	[void][reflection.assembly]::Load("System.Xml, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089")
	[void][reflection.assembly]::Load("System.DirectoryServices, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a")
	[void][reflection.assembly]::Load("System.Core, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089")
	[void][reflection.assembly]::Load("System.ServiceProcess, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a")
	#endregion Import Assemblies

	#----------------------------------------------
	#region Define SAPIEN Types
	#----------------------------------------------
	try{
		$local:type = [ProgressBarOverlay]
	}
	catch
	{
		Add-Type -ReferencedAssemblies ('System.Windows.Forms', 'System.Drawing') -TypeDefinition  @" 
		using System;
		using System.Windows.Forms;
		using System.Drawing;
        namespace SAPIENTypes
        {
		    public class ProgressBarOverlay : System.Windows.Forms.ProgressBar
	        {
	            protected override void WndProc(ref Message m)
	            { 
	                base.WndProc(ref m);
	                if (m.Msg == 0x000F)// WM_PAINT
	                {
	                    if (Style != System.Windows.Forms.ProgressBarStyle.Marquee || !string.IsNullOrEmpty(this.Text))
                        {
                            using (Graphics g = this.CreateGraphics())
                            {
                                using (StringFormat stringFormat = new StringFormat(StringFormatFlags.NoWrap))
                                {
                                    stringFormat.Alignment = StringAlignment.Center;
                                    stringFormat.LineAlignment = StringAlignment.Center;
                                    if (!string.IsNullOrEmpty(this.Text))
                                        g.DrawString(this.Text, this.Font, Brushes.Black, this.ClientRectangle, stringFormat);
                                    else
                                    {
                                        int percent = (int)(((double)Value / (double)Maximum) * 100);
                                        g.DrawString(percent.ToString() + "%", this.Font, Brushes.Black, this.ClientRectangle, stringFormat);
                                    }
                                }
                            }
                        }
	                }
	            }
              
                public string TextOverlay
                {
                    get
                    {
                        return base.Text;
                    }
                    set
                    {
                        base.Text = value;
                        Invalidate();
                    }
                }
	        }
        }
"@ | Out-Null
	}
	#endregion Define SAPIEN Types

	#----------------------------------------------
	#region Generated Form Objects
	#----------------------------------------------
	[System.Windows.Forms.Application]::EnableVisualStyles()
	$formChangeUPNNames = New-Object 'System.Windows.Forms.Form'
	$checkboxRecurseOUs = New-Object 'System.Windows.Forms.CheckBox'
	$progressbaroverlay1 = New-Object 'SAPIENTypes.ProgressBarOverlay'
	$labelNewUPNPicker = New-Object 'System.Windows.Forms.Label'
	$buttonChangeUPNs = New-Object 'System.Windows.Forms.Button'
	$combobox1 = New-Object 'System.Windows.Forms.ComboBox'
	$buttonListOUs = New-Object 'System.Windows.Forms.Button'
	$treeview1 = New-Object 'System.Windows.Forms.TreeView'
	$errorprovider1 = New-Object 'System.Windows.Forms.ErrorProvider'
	$imagelistButtonBusyAnimation = New-Object 'System.Windows.Forms.ImageList'
	$InitialFormWindowState = New-Object 'System.Windows.Forms.FormWindowState'
	#endregion Generated Form Objects

	#----------------------------------------------
	# User Generated Script
	#----------------------------------------------
	
	
	
	
	
	
	
	$formChangeUPNNames_Load={
		#TODO: Initialize Form Controls here
		Load-ComboBox $combobox1 $(Get-ADObject -Identity $Script:upnDN  -Properties upnsuffixes | select -ExpandProperty upnsuffixes)
		$errorprovider1.SetError($combobox1, "Choose UPN")
		
	}
	
	
	
	#region Control Helper Functions
	function Load-ListBox 
	{
	&lt;#
		.SYNOPSIS
			This functions helps you load items into a ListBox or CheckedListBox.
	
		.DESCRIPTION
			Use this function to dynamically load items into the ListBox control.
	
		.PARAMETER  ListBox
			The ListBox control you want to add items to.
	
		.PARAMETER  Items
			The object or objects you wish to load into the ListBox's Items collection.
	
		.PARAMETER  DisplayMember
			Indicates the property to display for the items in this control.
		
		.PARAMETER  Append
			Adds the item(s) to the ListBox without clearing the Items collection.
		
		.EXAMPLE
			Load-ListBox $ListBox1 "Red", "White", "Blue"
		
		.EXAMPLE
			Load-ListBox $listBox1 "Red" -Append
			Load-ListBox $listBox1 "White" -Append
			Load-ListBox $listBox1 "Blue" -Append
		
		.EXAMPLE
			Load-ListBox $listBox1 (Get-Process) "ProcessName"
	#&gt;
		Param (
			[ValidateNotNull()]
			[Parameter(Mandatory=$true)]
			[System.Windows.Forms.ListBox]$ListBox,
			[ValidateNotNull()]
			[Parameter(Mandatory=$true)]
			$Items,
		    [Parameter(Mandatory=$false)]
			[string]$DisplayMember,
			[switch]$Append
		)
		
		if(-not $Append)
		{
			$listBox.Items.Clear()	
		}
		
		if($Items -is [System.Windows.Forms.ListBox+ObjectCollection])
		{
			$listBox.Items.AddRange($Items)
		}
		elseif ($Items -is [Array])
		{
			$listBox.BeginUpdate()
			foreach($obj in $Items)
			{
				$listBox.Items.Add($obj)
			}
			$listBox.EndUpdate()
		}
		else
		{
			$listBox.Items.Add($Items)	
		}
	
		$listBox.DisplayMember = $DisplayMember	
	}
	
	function Load-ComboBox 
	{
	&lt;#
		.SYNOPSIS
			This functions helps you load items into a ComboBox.
	
		.DESCRIPTION
			Use this function to dynamically load items into the ComboBox control.
	
		.PARAMETER  ComboBox
			The ComboBox control you want to add items to.
	
		.PARAMETER  Items
			The object or objects you wish to load into the ComboBox's Items collection.
	
		.PARAMETER  DisplayMember
			Indicates the property to display for the items in this control.
		
		.PARAMETER  Append
			Adds the item(s) to the ComboBox without clearing the Items collection.
		
		.EXAMPLE
			Load-ComboBox $combobox1 "Red", "White", "Blue"
		
		.EXAMPLE
			Load-ComboBox $combobox1 "Red" -Append
			Load-ComboBox $combobox1 "White" -Append
			Load-ComboBox $combobox1 "Blue" -Append
		
		.EXAMPLE
			Load-ComboBox $combobox1 (Get-Process) "ProcessName"
	#&gt;
		Param (
			[ValidateNotNull()]
			[Parameter(Mandatory=$true)]
			[System.Windows.Forms.ComboBox]$ComboBox,
			[ValidateNotNull()]
			[Parameter(Mandatory=$true)]
			$Items,
		    [Parameter(Mandatory=$false)]
			[string]$DisplayMember,
			[switch]$Append
		)
		
		if(-not $Append)
		{
			$ComboBox.Items.Clear()	
		}
		
		if($Items -is [Object[]])
		{
			$ComboBox.Items.AddRange($Items)
		}
		elseif ($Items -is [Array])
		{
			$ComboBox.BeginUpdate()
			foreach($obj in $Items)
			{
				$ComboBox.Items.Add($obj)	
			}
			$ComboBox.EndUpdate()
		}
		else
		{
			$ComboBox.Items.Add($Items)	
		}
	
		$ComboBox.DisplayMember = $DisplayMember	
	}
	
	function Get-CheckedNodes 
	{
	&lt;#
		.SYNOPSIS
			This function collects a list of checked nodes in a TreeView
	
		.DESCRIPTION
			This function collects a list of checked nodes in a TreeView
	
		.PARAMETER  $NodeCollection
			The collection of nodes to search
	
		.PARAMETER  $CheckedNodes
			The ArrayList that will contain the all the checked items
		
		.EXAMPLE
			$CheckedNodes = New-Object System.Collections.ArrayList
			Get-CheckedNodes $treeview1.Nodes $CheckedNodes
			foreach($node in $CheckedNodes)
			{	
				Write-Host $node.Text
			}
	#&gt;
		param(
		[ValidateNotNull()]
		[System.Windows.Forms.TreeNodeCollection] $NodeCollection,
		[ValidateNotNull()]
		[System.Collections.ArrayList]$CheckedNodes)
		
		foreach($Node in $NodeCollection)
		{
			if($Node.Checked)
			{
				[void]$CheckedNodes.Add($Node)
			}
			Get-CheckedNodes $Node.Nodes $CheckedNodes
		}
	}
	
	#endregion
	
	
	
	#Add-Node, get-NextLevel and Build Treeview code is taken from http://thepip3r.blogspot.nl/2011/06/powershell-guis-active-directory.html
	function Add-Node { 
	        param ( 
	            $selectedNode, 
	            $name
	        ) 
	        $newNode = new-object System.Windows.Forms.TreeNode  
	        $newNode.Name = $name 
	        $newNode.Text = $name 
	        $selectedNode.Nodes.Add($newNode) | Out-Null 
	        return $newNode 
	} 
	
	function Get-NextLevel {
	    param (
	        $selectedNode,
	        [String]$dn
	   )
	   [System.Windows.Forms.Application]::DoEvents()
	    $OUs = Get-ADObject -Filter 'ObjectClass -eq "organizationalUnit" -or ObjectClass -eq "container"' -SearchScope OneLevel -SearchBase $dn
	
	    If ($OUs -eq $null) {
	        $node = Add-Node $selectedNode $dn
	    } Else {
	        $node = Add-Node $selectedNode $dn
	        
	        $OUs | % {
	            Get-NextLevel $node $_.distinguishedName
	        }
	    }
	}
	 
	function Build-TreeView { 
	    if ($treeNodes)  
	    {  
	          $treeview1.Nodes.remove($treeNodes) 
	        $formChangeUPNNames.Refresh() 
	    } 
	    
	    $treeNodes = New-Object System.Windows.Forms.TreeNode 
	    $treeNodes.text = "Active Directory Root" 
	    $treeNodes.Name = "Active Directory Root" 
	    $treeNodes.Tag = "root" 
	    $treeView1.Nodes.Add($treeNodes) | Out-Null 
	     
	    #$treeView1.add_AfterSelect({ 
	    #    $textbox1.Text = $this.SelectedNode.Name
	    #}) 
	     
	    #Generate Module nodes 
	    $OUs = Get-NextLevel $treeNodes $Script:strDomainDN
	    
	    $treeNodes.Expand() 
	} 
	$buttonListOUs_Click={
		
		
		$progressbaroverlay1.Visible = $true
		
		$progressbaroverlay1.Style = 'Marquee'
		[System.Windows.Forms.Application]::DoEvents()
		Build-TreeView
		
		$progressbaroverlay1.Visible = $false
	  
		
		#TODO: Place custom script here
		
	}
	
	$button2_Click={
		
			
		Load-ComboBox $combobox1 $(Get-ADObject -Identity $Script:upnDN  -Properties upnsuffixes | select -ExpandProperty upnsuffixes)
		
	}
	
	
	$buttonChangeUPNs_Click={
		#TODO: Place custom script here
		$CheckedNodes = New-Object System.Collections.ArrayList
		Get-CheckedNodes $treeview1.Nodes $CheckedNodes
			foreach($node in $CheckedNodes)
			{	
				
				If ($checkboxRecurseOUs.Checked) {
					Get-ADUser -Filter * -SearchBase "$($node.text)"  |
						ForEach-Object {
	    					$UPN = $_.UserPrincipalName -Replace '(?im)@.+$', "@$($combobox1.SelectedItem.ToString())"
							Write-Host $UPN
							Write-Host $_.UserPrincipalName
	    					Set-ADUser $_ -UserPrincipalName $UPN 
						}				
					$combobox1.SelectedItem
					Write-Host $node.Text
				}
			
				Else {
					Get-ADUser -Filter * -SearchBase "$($node.text)" -SearchScope OneLevel |
						ForEach-Object {
	    					$UPN = $_.UserPrincipalName -Replace '(?im)@.+$', "@$($combobox1.SelectedItem.ToString())"
							Write-Host $UPN
							Write-Host $_.UserPrincipalName
	    					Set-ADUser $_ -UserPrincipalName $UPN 
						}				
					$combobox1.SelectedItem
					Write-Host $node.Text
				}
			}
	}
	
	
	$combobox1_Validating=[System.ComponentModel.CancelEventHandler]{
	#Event Argument: $_ = [System.ComponentModel.CancelEventArgs]
		#TODO: Place custom script here
		
			
	}
	
	$combobox1_Leave={
		#TODO: Place custom script here
		If ((-not $combobox1.SelectedItem) -or ($combobox1.SelectedItem.ToString() -like "")) {
				$errorprovider1.SetError($combobox1,"Select a value from the list")	
			}
			Else {
				$errorprovider1.SetError($combobox1,"")
				}
			
	}
	
	$combobox1_DropDownClosed={
		#TODO: Place custom script here
			If ((-not $combobox1.SelectedItem) -or ($combobox1.SelectedItem.ToString() -like "")) {
				$errorprovider1.SetError($combobox1,"Select a value from the list")	
			}
			Else {
				$errorprovider1.SetError($combobox1,"")
				}
	}
	
	
	
	
	$combobox1_TextChanged={
		#TODO: Place custom script here
		If ((-not $combobox1.SelectedItem) -or ($combobox1.SelectedItem.ToString() -like "")) {
				$errorprovider1.SetError($combobox1,"Select a value from the list")	
			}
			Else {
				$errorprovider1.SetError($combobox1,"")
				}
	}
	
	$checkboxRecurseOUs_CheckedChanged={
		#TODO: Place custom script here
		
	}
	
	# --End User Generated Script--
	#----------------------------------------------
	#region Generated Events
	#----------------------------------------------
	
	$Form_StateCorrection_Load=
	{
		#Correct the initial state of the form to prevent the .Net maximized form issue
		$formChangeUPNNames.WindowState = $InitialFormWindowState
	}
	
	$Form_Cleanup_FormClosed=
	{
		#Remove all event handlers from the controls
		try
		{
			$checkboxRecurseOUs.remove_CheckedChanged($checkboxRecurseOUs_CheckedChanged)
			$buttonChangeUPNs.remove_Click($buttonChangeUPNs_Click)
			$combobox1.remove_TextChanged($combobox1_TextChanged)
			$combobox1.remove_Leave($combobox1_Leave)
			$combobox1.remove_Validating($combobox1_Validating)
			$buttonListOUs.remove_Click($buttonListOUs_Click)
			$formChangeUPNNames.remove_Load($formChangeUPNNames_Load)
			$formChangeUPNNames.remove_Load($Form_StateCorrection_Load)
			$formChangeUPNNames.remove_FormClosed($Form_Cleanup_FormClosed)
		}
		catch [Exception]
		{ }
	}
	#endregion Generated Events

	#----------------------------------------------
	#region Generated Form Code
	#----------------------------------------------
	#
	# formChangeUPNNames
	#
	$formChangeUPNNames.Controls.Add($checkboxRecurseOUs)
	$formChangeUPNNames.Controls.Add($progressbaroverlay1)
	$formChangeUPNNames.Controls.Add($labelNewUPNPicker)
	$formChangeUPNNames.Controls.Add($buttonChangeUPNs)
	$formChangeUPNNames.Controls.Add($combobox1)
	$formChangeUPNNames.Controls.Add($buttonListOUs)
	$formChangeUPNNames.Controls.Add($treeview1)
	$formChangeUPNNames.ClientSize = '569, 640'
	$formChangeUPNNames.Name = "formChangeUPNNames"
	$formChangeUPNNames.Text = "Change UPN names"
	$formChangeUPNNames.add_Load($formChangeUPNNames_Load)
	#
	# checkboxRecurseOUs
	#
	$checkboxRecurseOUs.Location = '456, 569'
	$checkboxRecurseOUs.Name = "checkboxRecurseOUs"
	$checkboxRecurseOUs.Size = '104, 24'
	$checkboxRecurseOUs.TabIndex = 9
	$checkboxRecurseOUs.Text = "Recurse OU's"
	$checkboxRecurseOUs.UseVisualStyleBackColor = $True
	$checkboxRecurseOUs.add_CheckedChanged($checkboxRecurseOUs_CheckedChanged)
	#
	# progressbaroverlay1
	#
	$progressbaroverlay1.Location = '12, 509'
	$progressbaroverlay1.Maximum = 10
	$progressbaroverlay1.Name = "progressbaroverlay1"
	$progressbaroverlay1.Size = '540, 23'
	$progressbaroverlay1.Style = 'Marquee'
	$progressbaroverlay1.TabIndex = 8
	$progressbaroverlay1.TextOverlay = "Loading OU Structure"
	$progressbaroverlay1.Visible = $False
	#
	# labelNewUPNPicker
	#
	$labelNewUPNPicker.Location = '14, 535'
	$labelNewUPNPicker.Name = "labelNewUPNPicker"
	$labelNewUPNPicker.Size = '100, 23'
	$labelNewUPNPicker.TabIndex = 7
	$labelNewUPNPicker.Text = "New UPN Picker"
	#
	# buttonChangeUPNs
	#
	$buttonChangeUPNs.Location = '456, 599'
	$buttonChangeUPNs.Name = "buttonChangeUPNs"
	$buttonChangeUPNs.Size = '96, 23'
	$buttonChangeUPNs.TabIndex = 6
	$buttonChangeUPNs.Text = "Change UPN's"
	$buttonChangeUPNs.UseVisualStyleBackColor = $True
	$buttonChangeUPNs.add_Click($buttonChangeUPNs_Click)
	#
	# combobox1
	#
	$combobox1.DropDownStyle = 'DropDownList'
	$combobox1.FormattingEnabled = $True
	$combobox1.Location = '14, 559'
	$combobox1.Name = "combobox1"
	$combobox1.Size = '165, 21'
	$combobox1.TabIndex = 3
	$combobox1.add_TextChanged($combobox1_TextChanged)
	$combobox1.add_Leave($combobox1_Leave)
	$combobox1.add_Validating($combobox1_Validating)
	#
	# buttonListOUs
	#
	$buttonListOUs.Location = '14, 599'
	$buttonListOUs.Name = "buttonListOUs"
	$buttonListOUs.Size = '84, 23'
	$buttonListOUs.TabIndex = 1
	$buttonListOUs.Text = "List OU's"
	$buttonListOUs.UseVisualStyleBackColor = $True
	$buttonListOUs.add_Click($buttonListOUs_Click)
	#
	# treeview1
	#
	$treeview1.CheckBoxes = $True
	$treeview1.Location = '12, 27'
	$treeview1.Name = "treeview1"
	$treeview1.Size = '540, 478'
	$treeview1.TabIndex = 0
	#
	# errorprovider1
	#
	$errorprovider1.ContainerControl = $formChangeUPNNames
	#
	# imagelistButtonBusyAnimation
	#
	$Formatter_binaryFomatter = New-Object System.Runtime.Serialization.Formatters.Binary.BinaryFormatter
	#region Binary Data
	$System_IO_MemoryStream = New-Object System.IO.MemoryStream (,[byte[]][System.Convert]::FromBase64String('
AAEAAAD/////AQAAAAAAAAAMAgAAAFdTeXN0ZW0uV2luZG93cy5Gb3JtcywgVmVyc2lvbj00LjAu
MC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODkFAQAA
ACZTeXN0ZW0uV2luZG93cy5Gb3Jtcy5JbWFnZUxpc3RTdHJlYW1lcgEAAAAERGF0YQcCAgAAAAkD
AAAADwMAAAB2CgAAAk1TRnQBSQFMAgEBCAEAATABAAEwAQABEAEAARABAAT/ASEBAAj/AUIBTQE2
BwABNgMAASgDAAFAAwABMAMAAQEBAAEgBgABMP8A/wD/AP8A/wD/AP8A/wD/AP8A/wD/AP8A/wD/
AP8AugADwgH/AzwB/wM8Af8DwgH/MAADwgH/A1wB/wOCAf8DwgH/sAADPAH/AwAB/wMAAf8DPAH/
MAADggH/AzwB/wM8Af8DXAH/gAADwgH/AzwB/wM8Af8DwgH/IAADPAH/AwAB/wMAAf8DPAH/A8IB
/wNcAf8DggH/A8IB/xAAA8IB/wM8Af8DPAH/A8IB/wNcAf8DPAH/AzwB/wNcAf8EAAOSAf8DkgH/
A8IB/3AAAzwB/wMAAf8DAAH/AzwB/yAAA8IB/wM8Af8DPAH/A8IB/wOCAf8DPAH/AzwB/wOCAf8Q
AAM8Af8DAAH/AwAB/wM8Af8DwgH/A1wB/wOCAf8DwgH/A5IB/wOCAf8DggH/A5IB/3AAAzwB/wMA
Af8DAAH/AzwB/zAAA1wB/wM8Af8DPAH/A1wB/xAAAzwB/wMAAf8DAAH/AzwB/xAAA5IB/wOSAf8D
kgH/A8IB/3AAA8IB/wM8Af8DPAH/A8IB/zAAA8IB/wNcAf8DggH/A8IB/xAAA8IB/wM8Af8DPAH/
A8IB/xAAA8IB/wOSAf8DkgH/A8IB/zgAA8IB/wM8Af8DPAH/A8IB/zAAA8IB/wOCAf8DXAH/A8IB
/zAAA8IB/wPCAf8DkgH/A8IB/zQAA8IB/wPCAf80AAM8Af8DAAH/AwAB/wM8Af8wAANcAf8DPAH/
AzwB/wNcAf8wAAOSAf8DggH/A4IB/wOSAf8wAAPCAf8DwgH/A8IB/wPCAf8wAAM8Af8DAAH/AwAB
/wM8Af8wAAOCAf8DPAH/AzwB/wOCAf8wAAPCAf8DggH/A5IB/wOSAf8wAAPCAf8DwgH/A8IB/wPC
Af8wAAPCAf8DPAH/AzwB/wPCAf8wAAPCAf8DggH/A1wB/wPCAf8wAAPCAf8DkgH/A5IB/wPCAf80
AAPCAf8DwgH/EAADwgH/A8IB/xQAA8IB/wOCAf8DXAH/A8IB/zAAA8IB/wOSAf8DkgH/A8IB/zQA
A8IB/wPCAf9UAAPCAf8DwgH/A8IB/wPCAf8QAANcAf8DPAH/AzwB/wNcAf8wAAOSAf8DggH/A5IB
/wOSAf8wAAPCAf8DwgH/A8IB/wPCAf9QAAPCAf8DwgH/A8IB/wPCAf8DwgH/A8IB/wOSAf8DwgH/
A4IB/wM8Af8DPAH/A4IB/yQAA8IB/wPCAf8EAAPCAf8DggH/A5IB/wOSAf8wAAPCAf8DwgH/A8IB
/wPCAf9UAAPCAf8DwgH/BAADkgH/A4IB/wOCAf8DkgH/A8IB/wOCAf8DXAH/A8IB/yAAA8IB/wPC
Af8DwgH/A8IB/wPCAf8DkgH/A5IB/wPCAf80AAPCAf8DwgH/ZAADkgH/A5IB/wOSAf8DkgH/MAAD
wgH/A8IB/wPCAf8DwgH/sAADwgH/A5IB/wOSAf8DwgH/NAADwgH/A8IB/7QAA8IB/wPCAf8DkgH/
A8IB/zQAA8IB/wPCAf+0AAOSAf8DggH/A4IB/wOSAf8wAAPCAf8DwgH/A8IB/wPCAf+gAAPCAf8D
XAH/A4IB/wPCAf8DkgH/A5IB/wOSAf8DwgH/BAADwgH/A8IB/xQAA8IB/wPCAf8DkgH/A8IB/wPC
Af8DwgH/A8IB/wPCAf8kAAPCAf8DwgH/dAADggH/AzwB/wM8Af8DggH/A8IB/wOSAf8DkgH/A8IB
/wPCAf8DwgH/A8IB/wPCAf8QAAOSAf8DggH/A4IB/wOSAf8EAAPCAf8DwgH/JAADwgH/A8IB/wPC
Af8DwgH/cAADXAH/AzwB/wM8Af8DggH/EAADwgH/A8IB/wPCAf8DwgH/EAADkgH/A5IB/wOSAf8D
kgH/MAADwgH/A8IB/wPCAf8DwgH/cAADwgH/A1wB/wNcAf8DwgH/FAADwgH/A8IB/xQAA8IB/wOS
Af8DkgH/A8IB/zQAA8IB/wPCAf9sAAPCAf8DPAH/AzwB/wPCAf8wAAPCAf8DXAH/A4IB/wPCAf8w
AAPCAf8DwgH/A5IB/wPCAf80AAPCAf8DwgH/NAADPAH/AwAB/wMAAf8DPAH/MAADggH/AzwB/wM8
Af8DXAH/MAADkgH/A4IB/wOCAf8DkgH/MAADwgH/A8IB/wPCAf8DwgH/MAADPAH/AwAB/wMAAf8D
PAH/MAADXAH/AzwB/wM8Af8DggH/MAADkgH/A5IB/wOSAf8DkgH/MAADwgH/A8IB/wPCAf8DwgH/
MAADwgH/AzwB/wM8Af8DwgH/MAADwgH/A1wB/wNcAf8DwgH/MAADwgH/A5IB/wOSAf8DwgH/NAAD
wgH/A8IB/3wAA8IB/wM8Af8DPAH/A8IB/zAAA8IB/wNcAf8DggH/A8IB/zAAA8IB/wPCAf8DkgH/
A8IB/xAAA8IB/wM8Af8DPAH/A8IB/1AAAzwB/wMAAf8DAAH/AzwB/zAAA4IB/wM8Af8DPAH/A1wB
/zAAA5IB/wOCAf8DggH/A5IB/xAAAzwB/wMAAf8DAAH/AzwB/1AAAzwB/wMAAf8DAAH/AzwB/zAA
A1wB/wM8Af8DPAH/A4IB/wOSAf8DPAH/AzwB/wPCAf8gAAOSAf8DkgH/A5IB/wOSAf8DwgH/A1wB
/wOCAf8DwgH/AzwB/wMAAf8DAAH/AzwB/1AAA8IB/wM8Af8DPAH/A8IB/zAAA8IB/wOCAf8DXAH/
A8IB/wM8Af8DAAH/AwAB/wM8Af8gAAPCAf8DkgH/A5IB/wPCAf8DggH/AzwB/wM8Af8DXAH/A8IB
/wM8Af8DPAH/A8IB/6AAAzwB/wMAAf8DAAH/AzwB/zAAA1wB/wM8Af8DPAH/A4IB/7AAA8IB/wM8
Af8DPAH/A8IB/zAAA8IB/wOCAf8DXAH/A8IB/xgAAUIBTQE+BwABPgMAASgDAAFAAwABMAMAAQEB
AAEBBQABgAEBFgAD/4EABP8B/AE/AfwBPwT/AfwBPwH8AT8D/wHDAfwBAwHAASMD/wHDAfwBAwHA
AQMD/wHDAf8DwwP/AcMB/wPDAf8B8AH/AfAB/wHwAf8B+QH/AfAB/wHwAf8B8AH/AfAB/wHwAf8B
8AH/AfAB/wHwAf8B8AH/AfAB/wHwAf8B+QHnAcMB/wHDAf8B5wL/AsMB/wHDAf8BwwL/AcABAwH+
AUMB/wHDAv8B5AEDAfwBAwH/AecC/wH8AT8B/AE/BP8B/AE/Af4BfwT/AfwBPwH+AX8E/wH8AT8B
/AE/BP8BwAEnAcABPwHnA/8BwAEDAcIBfwHDA/8DwwH/AcMD/wHDAecBwwH/AecD/wEPAf8BDwH/
AQ8B/wGfAf8BDwH/AQ8B/wEPAf8BDwH/AQ8B/wEPAf8BDwH/AQ8B/wEPAf8BDwH/AQ8B/wGfA/8B
wwH/AcMB/wLDAv8BwwH/AcMB/wLDAv8BwwH/AcABPwHAAQMC/wHDAf8BwAE/AcABAwT/AfwBPwH8
AT8E/wH8AT8B/AE/Cw=='))
	#endregion
	$imagelistButtonBusyAnimation.ImageStream = $Formatter_binaryFomatter.Deserialize($System_IO_MemoryStream)
	$Formatter_binaryFomatter = $null
	$System_IO_MemoryStream = $null
	$imagelistButtonBusyAnimation.TransparentColor = 'Transparent'
	#endregion Generated Form Code

	#----------------------------------------------
#Save the initial state of the form $InitialFormWindowState = $formChangeUPNNames.WindowState #Init the OnLoad event to correct the initial state of the form $formChangeUPNNames.add\_Load($Form\_StateCorrection\_Load) #Clean up the control events $formChangeUPNNames.add\_FormClosed($Form\_Cleanup\_FormClosed) #Show the Form return $formChangeUPNNames.ShowDialog() } #End Function #Call OnApplicationLoad to initialize if((OnApplicationLoad) -eq $true) { #Call the form Call-OU\_Picker\_pff | Out-Null #Perform cleanup OnApplicationExit }

