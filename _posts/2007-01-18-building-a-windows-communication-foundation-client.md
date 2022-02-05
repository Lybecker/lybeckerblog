---
title: Building a Windows Communication Foundation client
permalink: /blog/2007/01/18/building-a-windows-communication-foundation-client/
dsq_thread_id:
  - "3470415846"
  - "3470415846"
categories:
  - .Net
  - WCF
tags:
  - dotNet
  - WCF
---
{{site.data.messages.dotnetframeworkwarning}}
{: .notice--warning}

In the [previous blog entry](/blog/2007/01/15/a-simple-windows-communication-foundation-web-service/) I showed how to build a Windows Communication Foundation Web Service.
Now I’ll show you how to build a client for that web service. I’ll build the client in two different ways to show you the possibilities.
If you control both the client and the web service; you can just share the service contract implemented as a regular .Net interfaces – you remember the IHelloWorldService interface… right?

```csharp
[ServiceContract]
public interface IHelloWorldService
{
  [OperationContract]
  string HelloWorld();
}
```

On the client you need to create a factory that builds the web service proxy for you, so you can utilize the operation. You do that with the ChannelFactory<> from the System.ServiceModel.dll.

```csharp
ChannelFactory<ihelloworldservice> factory =
  new ChannelFactory</ihelloworldservice><ihelloworldservice>(“myEndPoint”);

IHelloWorldService proxy = factory.CreateChannel();

MessageBox.Show(proxy.HelloWorld());
```

Notice the parameter of the ChannelFactory constructor which is the name of the endpoint configuration. The client endpoint configuration is very similar to the web service configuration and is made of ABC (Address, Binding and Contract).

```xml
< ?xml version=“1.0“ encoding=“utf-8“ ?>
<configuration>
  <system .serviceModel>
    <client>
      <endpoint name=“myEndPoint“
                address=“http://localhost:8080/HelloWorldService”
                binding=“basicHttpBinding”
                contract=“IHelloWorldService“ />
    </client>
  </system>
</configuration>
```

If you compare this client configuration file with the one for the web service, you will see that they are almost identical.
That is all it takes to implement a WCF client to the HelloWorldService.
You are now thinking – what if you do not have control over the web service or the web service is implemented in another programming language like Java? Then you do not have a service contract implemented as a .Net interface. That is where WSDL comes into the picture.

The web service metadata is not published by default, so you need to do a little server configuration. Don’t worry – it is simple. You just have to change the behavior of the service by defining a behavior and map the service to that behavior.

```xml
< ?xml version=“1.0“ encoding=“utf-8“ ?>
<configuration>
  <system .serviceModel>
    <behaviors>
      <servicebehaviors>
        <behavior name=“myBehavior“>
          <servicemetadata httpGetEnabled=“true“
                           httpGetUrl=“http://localhost:8080/HelloWorldService“ />
        </behavior>
      </servicebehaviors>
    </behaviors>
      <services>
        <service name=“HelloWorldService“
                 behaviorConfiguration=“myBehavior“>
          <endpoint address=“http://localhost:8080/HelloWorldService“
                    binding=“basicHttpBinding”
                    contract=“IHelloWorldService“ />
      </service>
    </services>
  </system>
</configuration>
```

The service behavior instructs the service to publish metadata via HTTP by a certain address. Now everybody can retrieve the WDSL file and implement you web service. That can be done with the ServiceModel Metadata Utility (svcutil.exe). This tool automatically implements a .Net proxy class for the web service.

```cmd
svcutil http://localhost:8080/HelloWorldService /NoConfig
```

Run the above statement in a command prompt (you need to have the service running) and the tool generates a .Net file called HelloWorldService.cs. This file contains a proxy implementation of the web service enabling you to call the web service without doing any IO programming.
Why did I add the /NoConfig flag – that because the svcutil.exe generates a very verbose client configuration file. The auto generated client configuration file works, but essentially it is the same client configuration file used with the dynamic client.
Add the HelloWorldService.cs file to your solution, add a reference to the System.ServiceModel.dll
and copy the client configuration file from dynamic client (the above client configuration).
Notice you do not need to reference the assembly with the service contract IHelloWorldService.

```csharp
IHelloWorldService proxy = new HelloWorldServiceClient(“myEndPoint”);

MessageBox.Show(proxy.HelloWorld());
```

You are done! Build, compile, test, package and then ship you code.

If you want to super simple Visual Studio way of implementing web services – Add Web Reference kind of functionality, you can install Visual Studio Extensions for .Net 3.0 Framework. Then you get an Add Service Reference (similar to Add Web Reference), but all it does is call the `svcutil.exe`. The extension also includes a GUI configuration editor, which makes you life a lot easier if you are not a XML fetishist.
You can download the working WCF example including the Web Service and the two client implementations here: [Hello World WCF with clients](/wp-content/uploads/helloworldwcfwithclients.zip)
