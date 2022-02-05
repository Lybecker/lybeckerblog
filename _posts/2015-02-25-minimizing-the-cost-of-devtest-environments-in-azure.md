---

title: Minimizing the cost of dev/test environments in Azure
date: 2015-02-25T07:04:58+01:00


guid: http://www.lybecker.com/blog/?p=1340
permalink: /blog/2015/02/25/minimizing-the-cost-of-devtest-environments-in-azure/
dsq_thread_id:
  - "3538037289"
  - "3538037289"
categories:
  - Useful tools
  - Windows Azure
tags:
  - Dev/Test
  - MSDN Subscription
  - Windows Azure
---
I use Windows Azure as my dev/test environment because it is fast and convenient to create new virtual machines or services. I use the [MSDN Subscription Azure Benefits](http://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/), which includes free Azure Credits. The Azure Credits cover my dev/test needs even though I use more than a handful of VMs and services. I make smart use of the free Azure Credits by turning off VMs at night and during weekends, when I am not using them. Which means that I can use 3-4 times more VMs on Azure compared to just letting them run all the time. VMs are costly compared to the PaaS services such as Azure WebSites, SQL Azure and Cloud Services. So the PaaS services are not a cost issue.

I manage the Azure VMs and almost everything with the Server Explorer in Visual Studio. It is a quick way to start VMs in the morning.

[<img loading="lazy" class=" size-full wp-image-1341 aligncenter" src="http://www.lybecker.com/blog/wp-content/uploads/StartStopAzureVmFromVs2013.gif" alt="StartStopAzureVmFromVs2013" width="469" height="528" />](http://www.lybecker.com/blog/wp-content/uploads/StartStopAzureVmFromVs2013.gif)

If I have a list of VMs that I need to manage, then I use the Azure PowerShell cmdlets – see my [How-to start and stop Azure VMs via PowerShell](/blog/2015/02/23/how-to-start-and-stop-azure-vms-via-powershell/).

Finally, I use Azure Automation to ensure that I never have a running Azure VM all night, just because I forgot to shut it down &#8211; see [How-to start and stop Azure VMs at a schedule](/blog/2015/02/25/how-to-start-and-stop-azure-vms-at-a-schedule/). It automatically shuts down any VM running in my MSDN Subscription at 6 p.m. If I work later, I can just start the required VMs again – it only take a couple of minutes.
