---
title: WCF Throttling – Part 1
permalink: /blog/2010/10/06/wcf-throttling-part-1/
dsq_thread_id:
  - "3456318051"
  - "3456318051"
categories:
  - .Net
  - WCF
tags:
  - .Net 4
  - dotNet
  - Performance
  - Throttling
  - WCF
---
{{site.data.messages.dotnetframeworkwarning}}
{: .notice--warning}

The default throttling settings in WCF has always been very conservative. There where configured conservatively to diminish the risk of request flooding. Without throttling settings a large number of requests will make the service unresponsive by consuming all resources trying to respond to all requests simultaneously.

Because of the very conservative settings many developers have run into what seems like WCF performance problems, but was actually incorrectly configured throttling settings.

WCF throttling is a service behavior configuration and each setting has effect dependent on the [InstanceContextMode](http://msdn.microsoft.com/en-us/library/system.servicemodel.servicebehaviorattribute.instancecontextmode(v=VS.100).aspx "InstanceContextMode enum on MSDN") and [ConcurrencyMode](http://msdn.microsoft.com/en-us/library/system.servicemodel.servicebehaviorattribute.concurrencymode.aspx "ConcurrencyMode enum on MSDN") settings.

* `maxConcurrentCalls` (int) the maximum number of concurrent messages processing
* `maxConcurrentInstances` (int) the maximum number of concurrent InstanceContext (service type instances) objects processing
* `maxConcurrentSessions` (int) the maximum number of concurrent sessions processing

These throttling settings can be configured in code via the ServiceThrottlingBehavior in the System.ServiceModel.Description namespace or though configuration like below:

```xml
<system.serviceModel>
    <serviceBehaviors>
      <behavior name="throttlingServiceBehavior">
        <serviceThrottling maxConcurrentCalls="16"
                           maxConcurrentInstances="160"
                           maxConcurrentSessions="10"/>
      </behavior>
    </serviceBehaviors>
</system.serviceModel>
```

The default values in .Net 3.0/3.5 are:

* `maxConcurrentCalls` = 16
* `maxConcurrentSessions` = 10
* `maxConcurrentInstances` = `maxConcurrentCalls` + `maxConcurrentSessions`

The default has changed in .Net 4.0 as the .Net 3.0/3.5 default values were too conservative and the increase in server resources – especially the number of cores available. The default values for .Net 4.0 are:

* `maxConcurrentCalls` = 16 * `Environment.ProcessorCount`
* `maxConcurrentSessions` = 100 * `Environment.ProcessorCount`
* `maxConcurrentInstances` = `maxConcurrentCalls` + `maxConcurrentSessions`

The `Environment.ProcessorCount` property is misleading as the value is the number of cores (Hyper-Threading counts double). In my development laptop with four Hyper-Threading cores looks like this:

![WCF Throttling DotNet 4.0 default settings](/wp-content/uploads/WcfThrottlingDotNet4.png)
