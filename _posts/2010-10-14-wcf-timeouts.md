---
title: WCF Timeouts
permalink: /blog/2010/10/14/wcf-timeouts/
dsq_thread_id:
  - "3456310341"
  - "3456310341"
categories:
  - .Net Framework
  - WCF
  - 'ws-*'
tags:
  - Performance
  - Throttling
  - Timeout
  - WCF
---
{{site.data.messages.dotnetframeworkwarning}}
{: .notice--warning}

![Timeout](/wp-content/uploads/Timeout-300x300.png){: .align-right}

The last two articles about WCF Throttling [part 1](/blog/2010/10/06/wcf-throttling-part-1/ "WCF Throttling - Part 1") and [part 2](/blog/2010/10/11/wcf-throttling-part-2/ "WCF Throttling - Part 2") would not be complete without looking at WCF timeouts. Any potentially lengthy operation must have a timeout or the system might end up waiting indefinitely – this is remarkably prevalent when working across any network connection (Yes, LAN connections too).

Timeouts are not directly related to throttling properties, but effect the way the service (or client) performance under load. Timeout properties can be perceived as an annoyance when sending larger messages or dealing with slow connections or services. The frustration increase as the naming of the properties can be deceiving. Read on… and I’ll explain 🙂

Below are the binding properties that all throw TimeoutExceptions if any of setting thresholds are exceeded:

* `OpenTimeout` (TimeSpan) the interval of time provided for an open operation to complete including security handshakes (WS-Trust, WS-Secure Conversation etc.). The default is 00:01:00.
* `CloseTimeout` (TimeSpan) the interval of time provided for a close operation to complete. The default is 00:01:00.
* `SendTimeout` (TimeSpan) the interval of time provided for an entire operation to complete. This includes both sending of message and receiving reply! The default is 00:01:00.
* `ReceiveTimeout` (TimeSpan) the interval of time that a connection can remain inactive, during which no application messages are received, before it is dropped. The default is 00:10:00.
  * This setting is only used on the server-side and has no effect on client-side.
  * When using Reliable Sessions remember to set the [InactivityTimeout](http://msdn.microsoft.com/en-us/library/system.servicemodel.reliablesession.inactivitytimeout.aspx "ReliableSession.InactivityTimeout property on MSDN") property on the reliableSession element to the same value as the [ReceiveTimeout](http://msdn.microsoft.com/en-us/library/system.servicemodel.channels.binding.receivetimeout.aspx "Binding.ReceiveTimeout property on MSDN") property, as both inactivity timers has to be satisfied.

Example of configuration file:

```xml
<system.serviceModel>
  <bindings>
    <netTcpBinding>
      <binding name="netTcpBindingConfig"
               openTimeout="00:01:00"
               closeTimeout="00:01:00"
               sendTimeout="00:01:00"
               receiveTimeout="00:10:00">
        <reliableSession enabled="true"
                         inactivityTimeout="00:10:00" />
      </binding>
    </netTcpBinding>
  </bindings>
</system.serviceModel>
```
