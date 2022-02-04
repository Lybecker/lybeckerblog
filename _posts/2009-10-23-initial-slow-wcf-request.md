---
id: 367
title: Initial slow WCF request
date: 2009-10-23T20:15:35+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=367
permalink: /2009/10/23/initial-slow-wcf-request/
dsq_thread_id:
  - "3456319699"
  - "3456319699"
categories:
  - .Net
  - Cloud
  - WCF
---
[<img loading="lazy" class="alignright size-full wp-image-370" title="Snail" src="http://www.lybecker.com/blog/wp-content/uploads/Snail.png" alt="Snail" width="200" height="159" />](http://www.lybecker.com/blog/wp-content/uploads/Snail.png)If working with any of the HTTP Bindings you might experience that the first WCF request takes a long time to complete.

This is because the initial HTTP connection tries to get the proxy settings automatically. This is done by requesting the configuration via a HTTP GET http://wpad/wpad.dat. If proxy server automatic configuration is not configured, the request times out and the initial WCF can send the request directly to the destination address. This may add 30 seconds to the initial WCF request!

You can disable this behavior by specifying UseDefaultWebProxy = false on the binding.

You can read more about [Web Proxy Auto-Discovery Protocol ( WPAD ) at Wikipedia](http://en.wikipedia.org/wiki/Web_Proxy_Autodiscovery_Protocol).

This applies to basicHttpBinding, wsHttpBinding, wsDualHttpBinding, webHttpBinding, ws2007FederationHttpBinding, wsFederationHttpBinding, basicHttpContextBinding, wsHttpContextBinding and the new Azure ServiceBus bindings basicHttpRelayBinding, wsHttpRelayBinding, webHttpRelayBinding