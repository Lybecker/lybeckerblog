---

title: Configuring the DTC for WCF with WS-AtomicTransaction
date: 2007-03-12T18:19:43+01:00


guid: http://www.lybecker.com/blog/2007/03/12/configuring-the-dtc-for-wcf-with-ws-atomictransaction/
permalink: /2007/03/12/configuring-the-dtc-for-wcf-with-ws-atomictransaction/
autometa:
  - ""
  - ""
dsq_thread_id:
  - "3501140291"
  - "3501140291"
categories:
  - .Net
  - WCF
  - 'ws-*'
tags:
  - dotNet
  - Transactions
  - WCF
---
If interoperability with other platforms is a requirement WS-Atomic Transaction must be used. It requires all the steps for DTC setup in previous blog entry [Configuring the DTC for WCF for OleTx](/blog/2007/03/12/configuring-the-dtc-for-wcf-with-oletx/), but also a couple of additional ones.

If running Windows XP or Windows Server 2003 a hotfix is required as detailed in the [.Net framework 3.0 release notes](http://msdn2.microsoft.com/en-us/windowsvista/bb188202.aspx). The hotfix can be downloaded from [here](http://go.microsoft.com/fwlink/?linkid=46976).

WS-Atomic Transaction requires a certificate to establishing Mutual Trust between the parties in a transaction. If a certificate issued by a trusted 3rd party is not available, it is possible to issue one for test purposes by running the below statement in the Visual Studio 2005 Command Prompt. It generates and installs a certificate in the LocalMachineMY store.

<p style="margin: 0pt 20px">
  MakeCert -sr LocalMachine -pe -n &#8220;CN=mytestcertificate.com&#8221; -ss My -sky exchange -sp &#8220;Microsoft RSA SChannel Cryptographic Provider&#8221; -sy 12
</p>

To enable a graphical interface for the [WS-AtomicTransaction Configuration Utility (wsatConfig.exe)](http://msdn2.microsoft.com/en-us/library/aa347734.aspx) register the WsatUI.dll by running the following command in the Visual Studio 2005 Command Prompt

<p style="margin: 0pt 20px">
  regasm /codebase %PROGRAMFILES%Microsoft SDKsWindowsv6.0BinWsatUI.dll
</p>

Now open the DTC configuration again by the following steps:

  * Administrative Tools | Component Services | Computer | My Computer.
  * Right-click on My Computer and chose Properties – notice a WS-AT tab is now available
    – select it.
  * Check Enable WS-Atomic Transaction network support
  * Select the certificate under Endpoint certificate

<img loading="lazy" class="alignnone size-full wp-image-244" title="WS-AtomicTrnasaction configuration" src="http://www.lybecker.com/blog/wp-content/uploads/ws-atconfiguration.jpg" alt="WS-AtomicTrnasaction configuration" width="404" height="493" />

Now everything is ready to make use of WS-Atomic Transaction in WCF as long as the client and service are on the same machine.

If the client and service are located on different machines, each machine needs a certificate and any machine participating in a transaction must be explicitly authorized by establishing trust with the counterpart’s certificate.

Establishing trust and authorizing trust. These steps have to be performed on all parties.

  * Export the public key
  * Add the public key certificate to the counterpart’s LocalMachineMY and LocalMachineROOT
    stores.
  * Authorize the counterpart’s certificate in the WS-AT tab.

It seems like a daunting task, but it is worth it. I can’t figure out how I ever got by with old-style ASMX web services without transactional support.
