---

title: How-to start and stop Azure VMs via PowerShell
date: 2015-02-23T08:02:29+01:00


guid: http://www.lybecker.com/blog/?p=1323
permalink: /2015/02/23/how-to-start-and-stop-azure-vms-via-powershell/
dsq_thread_id:
  - "3538037771"
  - "3538037771"
categories:
  - PowerShell
  - Windows Azure
tags:
  - PowerShell
  - Windows Azure
---
With PowerShell it is fast and convenient to manage my development and test servers running on Windows Azure. It is just easier to use command line tools than logging into the Azure management portal shutting down each VM. To set up PowerShell:

  1. Install the [Azure PowerShell cmdlets](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
  2. Start the Azure PowerShell (do not start the regular PowerShell as it is not preconfigured with the Azure PowerShell cmdlets)
  3. Authorize Azure PowerShell to access your Azure subscriptions by typing in the Azure PowerShell shell: <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Add-AzureAccount
</pre>

    In the sign-in window, provide your Microsoft credentials for the Azure account.</li> </ol>

    If you like me have multiple Azure subscriptions &#8211; change the default subscription with:

    <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Select-AzureSubscription [-SubscriptionName]
</pre>

    To start an Azure VM the syntax is:

    <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Start-AzureVM [–Name] [-ServiceName]
</pre>

    To start a VM named _vs2015_ in the cloud service _lybCloudService_ requires as little as:

    <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Start-AzureVM vs2015 lybCloudService
</pre>

    To stop the VM is just as easy

    <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Stop-AzureVM [-Name] [-ServiceName]
</pre>

    If it is the last running VM in the cloud service, then you will be asked if you want to deallocate the cloud service or not, as the cloud service will release the public IP address. That is not a problem if you access your VM via DNS name – which most people do.
    You can override the question by appending _–Force_ like this:

    <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Stop-AzureVM vs2015 lybCloudService –Force
</pre>

    There are many useful Azure PowerShell cmdlets to use. To list all Azure PowerShell cmdlets:

    <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Help Azure
</pre>

    Get details on Azure PowerShell cmdlet:

    <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Man &lt;cmdlet name&gt;
</pre>

    List all VMs:

    <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Get-AzureVM
</pre>

    Get details of a specific VM:

    <pre class="brush: powershell; gutter: false; title: ; notranslate" title="">Get-AzureVM [–Name] [-ServiceName]
</pre>

    The PowerShell prompt is just like a normal command prompt, so you can use tab completion and F7 to show all executed commands.
