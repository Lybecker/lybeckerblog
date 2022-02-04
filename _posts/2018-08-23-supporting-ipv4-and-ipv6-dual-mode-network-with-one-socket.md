---
id: 1463
title: Supporting IPv4 and IPv6 dual mode network with one socket
date: 2018-08-23T18:54:05+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=1463
permalink: /2018/08/23/supporting-ipv4-and-ipv6-dual-mode-network-with-one-socket/
categories:
  - .Net
tags:
  - .Net Core
  - .Net Standard
  - IPv4
  - IPv6
  - networking
  - Socket
---
IP version 6 has not become the de facto standard yet, even though we are close to have exhausted all IPv4 addresses. I set out to learn more about IPv6 and how to support both protocols when building a network solution. It was fueled by lack of understanding and some issues I ran into when using DotNetty to build a protocol translation gateway. I ran into error messages like:

`System.Net.Sockets.SocketException: An address incompatible with the requested protocol was used. Error Code: 10047`

Which means that I have specified one type of protocol but are using another. So why not figure out how to create an IPv4 and IPv6 agnostic solution? Which I did.

Initially the problem I ran into was caused by the [Dns.GetHostAddressesAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.dns.gethostaddressesasync) method, as it resolves a DNS name to possible multiple IP addresses (e.g. IPv4 and IPv6), but which one should the system choose? With this protocol agnostic solution, it doesn’t matter.

As the BCL (Base Class Library) is not designed to support more than one protocol at the time, it looks at bit clunky when IPv4 addresses are imitating IPv6 adresses, but it works. IPv4 will appear like ::ffff:127.0.0.1, so you will have to use properties and methods like [IsIPv4MappedToIPv6](https://docs.microsoft.com/da-dk/dotnet/api/system.net.ipaddress.isipv4mappedtoipv6) and [MapToIPv4](https://docs.microsoft.com/da-dk/dotnet/api/system.net.ipaddress.maptoipv4).

First you need to specify IPv6 as the protocol via AddressFamily.InterNetworkV6 and then set [DualMode](https://docs.microsoft.com/da-dk/dotnet/api/system.net.sockets.socket.dualmode) to true, which in effect sets SocketOptionName.IPv6Only to false.

```csharp
// TCP Server
var listener = new TcpListener(new IPEndPoint(IPAddress.IPv6Any, 8080));
listener.Server.DualMode = true;
listener.Start();

// TCP Client
var client = new TcpClient(AddressFamily.InterNetworkV6);
client.Client.DualMode = true;
await client.ConnectAsync("127.0.0.1", port: 8080);

// UDP Server
var listener = new UdpClient(AddressFamily.InterNetworkV6);
listener.Client.DualMode = true;
listener.Client.Bind(new IPEndPoint(IPAddress.IPv6Any, 8080));

// UDP Client
var client = new UdpClient(AddressFamily.InterNetworkV6);
client.Client.DualMode = true;
client.Connect("127.0.0.1", port : 8080);
```

For simplicity I used `TcpListener`, `TcpClient` and `UdbClient` event for the UDP server. I set the `DualMode` property on the underlying Socket via the `client.Client` property. The server will open a dual Socket and the client will open either an IPv4 or IPv6 depending on the address given.

Full code samples for both TCP and UDP can be found at my [DotNetCore-DualNetwork-IPv4IPv6 GitHub repo](https://github.com/Lybecker/DotNetCore-DualNetwork-IPv4IPv6/).

During my investigations, I discovered that depending on the `Socket` class constructor used, the `DualMode` property was set to `true` or `false`. The [Socket(SocketType, ProtocolType)](https://docs.microsoft.com/da-dk/dotnet/api/system.net.sockets.socket.-ctor) constructor defaulted the `DualMode` to `true`, but all others to `false`. None of the `TcpListener`, `TcpClient` or `UdbClient` instanciates a Socket with `DualModel` `true`.