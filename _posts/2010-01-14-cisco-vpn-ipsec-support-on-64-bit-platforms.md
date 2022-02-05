---

title: Cisco VPN (IPSec) support on 64 bit platforms
date: 2010-01-14T19:58:28+01:00


guid: http://www.lybecker.com/blog/?p=507
permalink: /blog/2010/01/14/cisco-vpn-ipsec-support-on-64-bit-platforms/
dsq_thread_id:
  - "3456317890"
  - "3456317890"
categories:
  - Useful tools
tags:
  - Security
  - Useful tools
  - VPN
  - Windows 7
---
[<img loading="lazy" class="alignright size-full wp-image-508" title="Shrew Soft Logo" src="http://www.lybecker.com/blog/wp-content/uploads/shrewsoft-logo.png" alt="Shrew Soft Logo" width="100" height="111" />](http://www.shrew.net/)I like Windows 7 x64, but I hate Cisco’s lack of support for the IPSec protocol on 64-bit platforms. Many of our customers use IPSec and the Cisco VPN Client – therefore I cannot connect to the customer’s network via IPSec VPN tunnels on my primary laptop 🙁

Until today 🙂

A colleague of mine recommended [Shrew Soft VPN Client](http://www.shrew.net/ "Shrew Soft's website"). It’s free and works like a charm. It’s a lot faster connecting and negotiating to the remote network than Cisco VPN Client, so fast in fact, that I initially thought that the connection failed. I’ve been using it for a couple of days, connecting to multiple customers, without any issues.

Why does Cisco implement a VPN client for x64 platforms?

I guess it is a money making scheme. They want to push their new Cisco VPN boxes and their new Cisco AnyConnect VPN client (expensive!), which makes use of SSL VPN.

Greg Ferro has another critical article [Early Death of Cisco VPN Client Forces VPN License Fees](http://etherealmind.com/premature-death-cisco-vpn-client-end-of-life/ "Article Early Death of Cisco VPN Client Forces VPN License Fees") with more details about Cisco’s SSL VPN.

I know of a commercial IPSec VPN client from [NCP](http://www.ncp-e.com/en/solutions/vpn-products/secure-entry-client.html "NCP's CPN client") that works fine with Cisco IPSec VPN tunnels, but the steep price tag of $144 USD + taxes is too much.

**Update May 31, 2010:** Cisco has released an x64 version of their client tools for Windows 7 with IPSec protocol support. Either my money making scheme hypotheses is wrong or Cisco feared the wrath of my blog readers 🙂

<div class="zemanta-pixie" style="margin-top: 10px; height: 15px;">
  <span class="zem-script more-related pretty-attribution"></span>
</div>
