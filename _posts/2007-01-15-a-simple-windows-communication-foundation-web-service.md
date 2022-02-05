---

title: A simple Windows Communication Foundation Web Service
date: 2007-01-15T22:59:28+01:00

guid: http://www.lybecker.com/blog/2007/01/15/a-simple-windows-communication-foundation-web-service/
permalink: /blog/2007/01/15/a-simple-windows-communication-foundation-web-service/
autometa:
  - webservice webmethod servicehost enter pre helloworldservice helloworldservice host
  - webservice webmethod servicehost enter pre helloworldservice helloworldservice host
dsq_thread_id:
  - "3456317172"
  - "3456317172"
categories:
  - .Net
  - WCF
tags:
  - dotNet
  - WCF
---
In the old days – pre .Net 3.0, a Web Service required the IIS, a class marked with the WebService attribute and one or more methods exposed by the WebMethod attribute. Something like this:

```csharp
[WebService]
public class HelloWorldService
{
  [WebMethod]
  public string HelloWorld()
  {
    return “Hello World”;
  }
}
</pre>

Optionally you could inherit the service class from System.Web.Services.WebService, if you need access to the HttpContext.

You can still use the &#8220;old&#8221; way of programming your Web Services, but with .Net 3.0 you have access to a new programming model to expose your services – Windows Communication Foundation (WCF).

With WCF you have to do a little more work, but you get a lot of added functionality out of the box and you do not have to play around with Web Service Enhancements (WSE) – which in my opinion is a plus.

WCF emphasises contract first design. It is easy with WCF, because you do not have to make the WSDL file yourself. You just have to make a regular .Net interface decorated with some attributes.

```csharp
[ServiceContract]
public interface IHelloWorldService
{
  [OperationContract]
  string HelloWorld();
}
</pre>

The ServiceContract and OperationContract are part of the System.ServiceModel.dll which is part of the .Net 3.0 framework – so you need to add a reference to System.ServiceModel.dll.

If you look at the pre .Net 3.0 Web Service implementation, you see that it is very similar. The ServiceContract attribute defines the interface and the OperationContract attribute describe which operations you can perform on the service, just like the WebService and WebMethod attributes.

Now you have the contract of your server, by which you actually can produce a WSDL file with the ServiceModel Metadata Utility (svcutil.exe) without doing any handcrafted XML, if you like.

Next you have to implement the interface. The implemented class is called service type.

```csharp
public class HelloWorldService : IHelloWorldService
{
  public string HelloWorld()
  {
    return “Hello World”;
  }
}
</pre>

All that is left is a process to host the WCF Web Service. We could use IIS, but I’ll use one of the new possibilities &#8211; a console application. Just because we can!

```csharp
class Program
{
  static void Main(string[] args)
  {
    using (ServiceHost host =
      new ServiceHost(typeof(HelloWorldService)))
    {
      host.Open();

      Console.WriteLine(“The
      HelloWorldService is running.”);

      Console.WriteLine(“Press
      [ENTER] to terminate…”);

      Console.ReadLine();
    }
  }
}
</pre>

Before running the application we need to configure a service endpoint – the address, binding and contract. You properly heard about ABC.
The address is the URL, the binding specifies the protocol and data format and we have already made the contract.

Add a configuration file (App.config) and enter the ABC.

<pre class="brush: xml; title: ; notranslate" title="">< ?xml version=“1.0“ encoding=“utf-8“ ?>
  <configuration>
    <system .serviceModel>
      <services>
        <service name=“HelloWorldService“>
          <endpoint address=“http://localhost:8080/HelloWorldService“
	                 binding=“basicHttpBinding”
	                 contract=“IHelloWorldService“ />
      </service>
    </services>
  </system>
</configuration>
</pre>

The basicHttpBinding is a regular XML Web Service with text encoded SOAP messages transported via HTTP – just like the pre .Net 3.0 implementation.

That’s it. A WCF Web Service.

The beauty with WCF is if you for example want added security, you can encrypt the SOAP body, by using WS-Security, simply by changing the binding in the configuration file to wsHttpBinding. If you ever tried that with WSE 3.0, you know it is a pain.

You can download the working WCF example here: [Hello World WCF Service](http://www.lybecker.com/blog/wp-content/uploads/helloworldwcf.zip)
