---

title: Configuring the DTC for WCF with OleTx
date: 2007-03-12T18:01:04+01:00


guid: http://www.lybecker.com/blog/2007/03/12/configuring-the-dtc-for-wcf-with-oletx/
permalink: /2007/03/12/configuring-the-dtc-for-wcf-with-oletx/
dsq_thread_id:
  - "3460102982"
  - "3460102982"
categories:
  - .Net
  - WCF
  - 'ws-*'
tags:
  - dotNet
  - Transactions
  - WCF
---
When a transaction propagates from one process to another, the DTC (Distributed Transaction Manager) must be utilized, because to two or more independent parties are involved in the transaction.

Beforehand the WCF service must be prepared:

  * Decorate the service contract (interface) with the attribute TransactionFlow and specify
    if it is mandatory or allowed.
  * Specify on the service type (implementation) OperationBehavior attribute that a TransactionScope
    is required.
  * Enable transaction propagation on the binding via the transactionFlow element in the
    configuration file for both service and client.

If the service and client are ready and an attempt to propagate a transaction is executed the following TransactionManagerCommunicationException is thrown:

<p style="margin: 0pt 20px">
  System.Transactions.TransactionManagerCommunicationException was unhandled by user code Message=&#8221;Network access for Distributed Transaction Manager (MSDTC) has been disabled. Please enable DTC for network access in the security configuration for MSDTC using the Component Services Administrative tool.&#8221;
</p>

That is because the DTC does not allow inbound of outbound network transactions, by default.

Configure the client DTC to allow outbound network transactions and the service DTC to have inbound network transactions. If both client and service are running on same machine, enable both inbound and outbound network transactions.

To enable inbound or outbound network transaction navigate to

  * Administrative Tools | Component Services | Computer | My Computer.
  * Right-click on My Computer and chose Properties | MSDTC tab | Security Configuration.
  * Check Network DTC Access and under Transaction Communication Manager check Allow Inbound, Allow Outbound or both.

Now transaction propagation via OLE Transactions (OleTx) is possible. These are all the bindings that operate in a homogeneous Windows environment.
