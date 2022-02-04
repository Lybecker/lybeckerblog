---
id: 225
title: Handcrafting the WCF client
date: 2009-03-05T20:52:22+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=225
permalink: /2009/03/05/handcrafting-the-wcf-client/
dsq_thread_id:
  - "3456317627"
  - "3456317627"
categories:
  - .Net
  - Visual Studio
  - WCF
tags:
  - dotNet
  - WCF
---
Two years ago I wrote an article [Building a Windows Communication Foundation client](/blog/2007/01/18/building-a-windows-communication-foundation-client/), describing three ways of creating WCF clients. I did not mention the fourth one, as I did not know at the time. Now I am older and wiser 😉

The four ways of creating a WCF client are:

  1. Visual Studio’s &#8220;Add Service&#8221; reference
  2. Service Model Metadata Utility Tool (SvcUtil.exe) command line tool
  3. Dynamic proxy with ChannelFactory<tchannel></tchannel>
  4. Handcrafting the WCF client

The last two options are only viable for WCF to WCF implementations where you either have control of both service and client or the service provider supplies a .Net assembly. Because both of them require the service contract aka the .Net interface marked with ServiceContractAttribute and OperationContractAttribute.

Anybody that has tried the “Add Service” reference in Visual Studio knows it is broken. First of all it generates an enormous amount of files, even for the simplest service contract.

[<img loading="lazy" class="aligncenter size-full wp-image-228" title="Vistual Studio Add Service reference generated files" src="http://www.lybecker.com/blog/wp-content/uploads/addservicereferencegeneratedfiles.png" alt="" width="474" height="139" />](http://www.lybecker.com/blog/wp-content/uploads/addservicereferencegeneratedfiles.png)

Secondly it sometimes corrupts the state of the generated files, so you have to remove the reference and then add it again.. Just try to [google it](http://www.google.com/search?q=%22Add+Service+reference%22+not+working) and you will find many frustrated developers. Do not use it!

The SvcUtil.exe is better and has a vast [number of options](http://msdn.microsoft.com/en-us/library/aa347733.aspx). This is the preferred option for non .Net services or where the service contract interface is not available.

The dynamic proxy with ChannelFactory<tchannel> is useful, but be aware that the interface returned does not implement IDisposable, but the implementation does. See below:

<pre class="brush: csharp; title: ; notranslate" title="">var factory = new ChannelFactory&lt;ihelloworldservice&gt;("myEndPoint");

IHelloWorldService proxy = factory.CreateChannel();

using (proxy as IDisposable)
{
   MessageBox.Show(proxy.HelloWorld());
}
</pre>

Finally the handcrafted version – the one I want to talk about. Writing the WCF client by hand is fairly easy – it requires the implementation to inherit from ClienBase<tchannel> and implementing the service contract interface. Below is first a simple service contract IHelloWorldService:

<pre class="brush: csharp; title: ; notranslate" title="">[ServiceContract(Namespace = "www.lybecker.com/blog/HelloWorldService")]
public interface IHelloWorldService
{
   [OperationContract]
   string HelloWorld();
}
</pre>

Next the handcrafted WCF client with constructors and service contract interface implementation:

<pre class="brush: csharp; title: ; notranslate" title="">public class HelloWorldClient : ClientBase&lt;ihelloworldservice&gt;, IHelloWorldService
{
   public HelloWorldClient()
   { }

   public HelloWorldClient(string configurationName) : base(configurationName)
   { }

   public HelloWorldClient(Binding binding, EndpointAddress address) : base(binding, address)
   { }

   public string HelloWorld()
   {
      return Channel.HelloWorld();
   }
}
</pre>

That’s it. 🙂

Now you have full control of the WCF client implementation.

Download the sample application [Hello World WCF clients including a handcrafted version](http://www.lybecker.com/blog/wp-content/uploads/helloworldwcfwithhandcraftedclient.zip)</ihelloworldservice></tchannel></ihelloworldservice></tchannel>