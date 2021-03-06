---
title: How-to start and stop Azure VMs at a schedule
excerpt: Azure runbooks can automate cost savings in Azure
permalink: /blog/2015/02/25/how-to-start-and-stop-azure-vms-at-a-schedule/
dsq_thread_id:
  - "3538034672"
  - "3538034672"
categories:
  - PowerShell
  - Azure
tags:
  - PowerShell
  - Virtuel Machine
  - Azure
---
I use Azure VMs for dev/test and I do not want them to run all night, as I have to pay for it. Therefore, I stop the VMs at night with a scheduler, as I do not always remember to stop the VMs after use.

Azure Automation is the right tool for the job. Azure Automation automates Azure management tasks and orchestrates actions across external systems from within Azure. You need an Azure Automation Account, which is a container for all your runbooks, runbook executions (jobs), and the assets that your runbooks depend on.

To execute runbooks, a set of user credentials needs to be stored as an asset. Create a new user as described in [Azure Automation: Authenticating to Azure using Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/).

Below, see guide on how to create the Azure Automation account and the runbook.

![Cretae runbook](/wp-content/uploads/CreateRunbookStopVM.gif)

The new Azure Automation account _lybAutomation_ and the runbook _Stop Windows Azure Virtual Machines on a Schedule_ are created from the gallery. The content in the gallery comes from the Azure Script Center. The [Azure Script Center](http://azure.microsoft.com/en-us/documentation/scripts/) has many PowerShell scripts covering many scenarios, but not all can be used with Azure Automation, as some scripts use features not available in Azure Automation. You do get a warning if you select one that is not supported, but in my mind, it should not be available in the gallery at all.

It burned me the first time I tried Azure Automation. I used the _Stop Windows Azure Virtual Machines on a Schedule_ from the gallery, but it uses an on-premise scheduler.

You need to store the credentials in the runbook of the user created earlier. See below.
![Setup runbook credentials](/wp-content/uploads/SetupRunBookCredentials.gif)

Then you need to configure the runbook script with the credentials and the Azure subscription where the virtual machines reside. See below.

![Configure runbook to stop VM](/wp-content/uploads/ConfigureRunbookVmStop.gif)

You find your subscription name in the top bar “Subscriptions” of the Azure portal.
Now you can test your runbook and all you need is to set up the schedule, so it runs every evening. See guide below.

![Configure runbook schedule](/wp-content/uploads/ConfigureRunbookSchedule.gif)

Be aware that the time is in UTC, so you have to correct the time according to your time zone. I expect the scheduler to get an overhaul, as it is too simple right now.
