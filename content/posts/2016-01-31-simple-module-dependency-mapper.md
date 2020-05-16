---
categories:
- Everyday
date: "2016-01-31T21:27:09Z"
meta:
  _edit_last: "1"
status: publish
tags: []
title: Simple module dependency mapper
---
<p>I was playing around with the Azure RM cmdlets a while back, and had forgotten to read the manual  :) So I was running into some issues, with not getting them to work.</p>
<p>So I figured that there had to be some kind of dependency among the modules for them to work.. So instead of reading the documentation, I decided to write a small script, that would look at the module manifest to check for dependencies.</p>
<pre class="lang:default decode:true " title="List Module Dependencies">#requires -Version 3

# Script to list module dependencies
function Get-Depends
{
    [CmdLetBinding()]
    Param(
        $ModulePath,
        $ModuleName,
        $Depth = 0,
        [switch]$ReturnDependency
    )
    if ($Depth -eq 0) 
    {
        $RootModulePath = $ModulePath
    }
    If ($Depth -ge 8 ) 
    {
        Throw 'Module dependency is deeper than 8 levels, possible cyclical dependency'
    }
    try
    {
        Write-Verbose -Message $($('--' * $Depth)+$ModuleName)
        $ModPath = $(($(Get-ChildItem -Path "$($ModulePath)" -Filter "$ModuleName.psd1" -Recurse ) |Where-Object  -FilterScript {
                    $($_.Directory | Split-Path -Leaf)  -match $ModuleName
        }))      
        $Dependencies = Test-ModuleManifest -Path $(Get-ChildItem -Path "$($ModPath.DirectoryName)" -Filter "$ModuleName.psd1" -Recurse |
        Select-Object -ExpandProperty Fullname) -ErrorAction Stop -Verbose:$false  |
        Where-Object -FilterScript {
            $_.RequiredModules
        }|
        Select-Object -ExpandProperty RequiredModules
        
        if($Dependencies)
        {
            $Dependencies | ForEach-Object -Process {
                $Mod = "$($_.Name)"
                #Write-verbose $ModulePath
                $ModPath = $(($(Get-ChildItem -Path $RootModulePath -Filter "$Mod.psd1" -Recurse ) |Where-Object  -FilterScript {
                            $($_.Directory | Split-Path -Leaf)  -match $Mod
                })) 
                
                Get-Depends -ModulePath "$($ModPath.DirectoryName)" -ModuleName $Mod -Depth ($Depth+1) 
            }

            If ($ReturnDependency) 
            {
                Return $Dependencies
            }
        }
    }
    catch
    {
        $_
    }
}
</pre>
<p>A few Examples</p>
<pre class="lang:default decode:true " title="Examples"># Examples on how to use the Dependency mapper.
# The Below example will output the dependencies of all modules in the C:\Temp\azurePS folder

Get-ChildItem -Path C:\temp\azurePS -Directory  | ForEach-Object -Process {
    "`r`n"
    Get-Depends -ModulePath C:\temp\azurePS -ModuleName $_.basename -Verbose -ReturnDependency
}

# Will get the dependencies of a specific module
Get-Depends -ModulePath C:\temp\azurePS -ModuleName AzureRM.Storage -Verbose
</pre>
<p>Output looks like this:<br />
<code><br />
VERBOSE: Azure.Storage<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.ApiManagement<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Automation<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Backup<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Batch<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Compute<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.DataFactories<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Dns<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.HDInsight<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Insights<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.KeyVault<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Network<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.OperationalInsights<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.profile</p>
<p>VERBOSE: AzureRM.RedisCache<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Resources<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.SiteRecovery<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Sql<br />
VERBOSE: --AzureRM.Profile</p>
<p>VERBOSE: AzureRM.Storage<br />
VERBOSE: --AzureRM.Profile<br />
VERBOSE: --Azure.Storage<br />
VERBOSE: ----
AzureRM.Profile

VERBOSE: AzureRM.StreamAnalytics  
VERBOSE: --AzureRM.Profile

VERBOSE: AzureRM.Tags  
VERBOSE: --AzureRM.Profile

VERBOSE: AzureRM.TrafficManager  
VERBOSE: --AzureRM.Profile

VERBOSE: AzureRM.UsageAggregates  
VERBOSE: --AzureRM.Profile

VERBOSE: AzureRM.Websites  
VERBOSE: --AzureRM.Profile

