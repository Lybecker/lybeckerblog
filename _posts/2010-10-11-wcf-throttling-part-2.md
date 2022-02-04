---

title: WCF Throttling – Part 2
date: 2010-10-11T19:05:40+01:00


guid: http://www.lybecker.com/blog/?p=773
permalink: /2010/10/11/wcf-throttling-part-2/
dsq_thread_id:
  - "3456384902"
  - "3456384902"
categories:
  - .Net
  - WCF
tags:
  - Performance
  - Throttling
  - WCF
---
[<img loading="lazy" class="alignright size-full wp-image-780" title="Funnel" src="http://www.lybecker.com/blog/wp-content/uploads/Funnel.jpg" alt="" width="158" height="140" />](http://www.lybecker.com/blog/wp-content/uploads/Funnel.jpg)In the [WCF Throttling – Part 1](/blog/2010/10/06/wcf-throttling-part-1/ "WCF Throttling - Part 1") article the service throttling behavior was introduced.

There are other throttling features in WCF that are designed to protect the service from request flooding.

These WCF throttling feature are configured on the binding, service behaviors and endpoint behaviors.

Binding properties:

  * MaxConnections (int) &#8211; specifies the maximum number of outbound and inbound connections the service creates and accepts respectively. Default value is 10 connections. This setting only applies for statefull TCP connections like [netTcpBinding](http://msdn.microsoft.com/en-us/library/system.servicemodel.configuration.nettcpbindingelement.aspx "netTcpBinding configuration on MSDN") and not stateless HTTP protocols like [basicHttpBinding](http://msdn.microsoft.com/en-us/library/system.servicemodel.configuration.basichttpbindingelement.aspx "basicHttpBinding configuration on MSDN"), [wsHttpBinding](http://msdn.microsoft.com/en-us/library/system.servicemodel.configuration.wshttpbindingelement.aspx "wsHttpBinding configuration on MSDN") or [webHttpBinding](http://msdn.microsoft.com/en-us/library/bb412176.aspx "webHttpBinding configuration on MSDN").
  * MaxReceivedMessageSize (long) &#8211; the maximum size of a message (including headers), that can be received on a channel. The sender of a message exceeding this limit will receive a fault and the receiver will drop the message. The default value is 65,536 bytes (64K).

There are two additional properties on the binding that one might mistakenly think is request throttling properties. These are the MaxBufferPoolSize and MaxBufferSize properties and they control [WCF memory Buffer Manager](http://obsessivelycurious.blogspot.com/2008/04/wcf-memory-buffer-management.html "Detailed explanation of the WCF memory Buffer Manager").

Note: remember to set the MaxReceivedMessageSize and MaxBufferSize properties to the same value if using TransferMode.Buffered or an ArgumentException will be thrown at runtime with the message “For TransferMode.Buffered, MaxReceivedMessageSize and MaxBufferSize must be the same value.”

Binding properties for the [readerQuotas](http://msdn.microsoft.com/en-us/library/ms731325.aspx "readerQuotas element on MSDN") element – used by XmlReader under the hood:

  * MaxArrayLength (int) &#8211; the maximum allowed array length of data received from a client. The default is 16,384 (16K).
  * MaxBytesPerRead (int) &#8211; the maximum allowed bytes returned per read for the XmlReader. The default is 4,096 (4K).
  * MaxDepth (int) &#8211; the maximum XML nested node depth. The default is 32.
  * MaxNameTableCharCount (int) &#8211; the maximum characters allowed in a table name. This is the maximum length of an XML element or attributes identifier including XML namespace. The default is 16,384 (16K).
  * MaxStringContentLength (int) &#8211; the maximum characters allowed in XML element or attribute content. The default is 8,192 (8K).

The [DataContractSerializer](http://msdn.microsoft.com/en-us/library/ms405768.aspx "DataContractSerializer on MSDN") is by default used to serialize and deserialize messages as it is much faster the XMLSerializer, but with less features. The DataContractSerializer has a single property that can be configures at the endpoint or service behavior:

  * MaxItemsInObjectGraph (int) &#8211; maximum number of items in an object graph to serialize or deserialize. The default is 65,536 (64K).

Resist the temptation of settings any of these properties to Int.MaxValue and the likes, because determining the correct values are difficult. Throttle the service, so some clients gets served instead of risk boggling down the service with request flooding, resulting in no clients get served.

<div>
  <div>
    You will become the service hero in your organization by throttling instead of letting the service run wild 🙂
  </div>

  <p>
    Example of configuration file:
  </p>

  <pre class="brush: xml; title: ; notranslate" title="">
&lt;system.serviceModel&gt;
  &lt;behaviors&gt;
    &lt;endpointBehaviors&gt;
      &lt;behavior name="endpointBehavior"&gt;
        &lt;dataContractSerializer maxItemsInObjectGraph="65536"/&gt;
      &lt;/behavior&gt;
    &lt;/endpointBehaviors&gt;
    &lt;serviceBehaviors&gt;
      &lt;behavior name="serviceBehaviors"&gt;
        &lt;dataContractSerializer maxItemsInObjectGraph="65536"/&gt;
      &lt;/behavior&gt;
    &lt;/serviceBehaviors&gt;
  &lt;/behaviors&gt;
  &lt;bindings&gt;
    &lt;netTcpBinding&gt;
      &lt;binding name="netTcpBindingConfig"
                maxReceivedMessageSize="65536"
                maxConnections="10"&gt;
        &lt;readerQuotas maxArrayLength="16384"
                      maxBytesPerRead="4096"
                      maxDepth="32"
                      maxStringContentLength="8192"
                      maxNameTableCharCount="16384"/&gt;
      &lt;/binding&gt;
    &lt;/netTcpBinding&gt;
  &lt;/bindings&gt;
&lt;/system.serviceModel&gt;
</pre>
</div>
