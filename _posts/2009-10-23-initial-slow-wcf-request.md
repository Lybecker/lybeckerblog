---
title: Initial slow WCF request
permalink: /blog/2009/10/23/initial-slow-wcf-request/
dsq_thread_id:
  - "3456319699"
  - "3456319699"
categories:
  - .Net Framework
  - Cloud
  - WCF
---
{{site.data.messages.dotnetframeworkwarning}}
{: .notice--warning}

![Slow snail](/wp-content/uploads/Snail.png){: .align-right}

If working with any of the HTTP Bindings you might experience that the first WCF request takes a long time to complete.

This is because the initial HTTP connection tries to get the proxy settings automatically. This is done by requesting the configuration via a HTTP GET http://wpad/wpad.dat. If proxy server automatic configuration is not configured, the request times out and the initial WCF can send the request directly to the destination address. This may add 30 seconds to the initial WCF request!

You can disable this behavior by specifying UseDefaultWebProxy = false on the binding.

You can read more about [Web Proxy Auto-Discovery Protocol ( WPAD ) at Wikipedia](http://en.wikipedia.org/wiki/Web_Proxy_Autodiscovery_Protocol).

This applies to basicHttpBinding, wsHttpBinding, wsDualHttpBinding, webHttpBinding, ws2007FederationHttpBinding, wsFederationHttpBinding, basicHttpContextBinding, wsHttpContextBinding and the new Azure ServiceBus bindings basicHttpRelayBinding, wsHttpRelayBinding, webHttpRelayBinding
