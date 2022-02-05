---
title: WCF Sessions and Reliable Messaging
permalink: /blog/2007/04/30/wcf-sessions-and-reliable-messaging/
dsq_thread_id:
  - "3456317523"
  - "3456317523"
categories:
  - .Net
  - Ilities
  - WCF
  - 'ws-*'
---
{{site.data.messages.dotnetframeworkwarning}}
{: .notice--warning}

There are a couple of ways to establish a session between client and service. The session is established by identifying clients via a unique identifier. The unique identifier is either conveyed by

* the client credentials when WS-Security is utilized to established secure session
* the TCP/IP socket connection when using an underlying connection-oriented protocol such as TCP
* the WS-ReliableMessaging protocol token when requiring message ordering and message delivery assurance for transport protocols.

Read more about how WS-Reliable Messaging protocol in my article “[How does Reliable Messaging work?](/blog/2007/01/23/how-does-reliable-messaging-work/)”

Enabling sessions are all done in the configuration file. Some bindings are sessionful by default like the tcpBinding and wsHttpBinding.

I continue with Hello World WCF code sample from my earlier articles: “[Building a Windows Communication Foundation client](/blog/2007/01/18/building-a-windows-communication-foundation-client/)” and “[A simple Windows Communication Foundation Web Service](/blog/2007/01/15/a-simple-windows-communication-foundation-web-service/)”. You can download the source code from here: [Hello World WCF with clients](/wp-content/uploads/helloworldwcfwithclients.zip).

Just by changing the binding in the code sample configuration to wsHttpBinding in the service and the clients, you get secure sessions; wsHttpBinding is by default secured with message security. The secure session context is established with the [WS-SecureConversation](http://www-128.ibm.com/developerworks/library/specification/ws-secon/) protocol.

In the service type implementation it is possible to retrieve the session identifier via the OperationContext.Current.SessionId.

If you want un-secure reliable ordered messages with sessions, then change the behavior of the wsHttpBinding by configuring the binding like below. The configuration shown is for the service, but the changes (in bold) are exactly the same as required in the clients’ configuration files.

```xml
< ?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system .serviceModel>
    <behaviors>
      <servicebehaviors>
        <behavior name="myBehavior">
          <servicemetadata httpGetEnabled="true"
                           httpGetUrl="http://localhost:8080/HelloWorldService" />
        </behavior>
      </servicebehaviors>
    </behaviors>
    <bindings>
      <wshttpbinding>
        <binding name="reliableBinding">
          <reliablesession enabled="true" ordered="true"/>
          <security mode="None" />
        </binding>
      </wshttpbinding>
    </bindings>
    <services>
      <service name="HelloWorldService"
               behaviorConfiguration="myBehavior">
        <endpoint address="http://localhost:8080/HelloWorldService"
                  binding="wsHttpBinding"
                  bindingConfiguration="reliableBinding"
                  contract="IHelloWorldService" />
      </service>
    </services>
  </system>
</configuration>
```

Now the session identifiers are conveyed by the WS-Reliable Messaging protocol (line 13-18). It is possible to use secure and reliable messages, but security is disabled to prove the point of WS-Reliable Messaging can be used to establish sessions.

If the implementation of your service requires sessions, decorate the service contract with the SessionMode.Required – this will demand that every endpoint bindings exposing the service support sessions. The possible values of the SessionMode enum are Required, Allowed and NotAllowed where Allowed is default.

```csharp
[ServiceContract(SessionMode=SessionMode.Required)]
public interface IHelloWorldService
{
    [OperationContract]
    string HelloWorld();
}
```

If guaranteed ordering of messages is assumed by the implementation and therefore required for dependable behavior of the service, then decorate the service contract with the DeliveryRequirements attribute with the parameter RequireOrderedDelivery set to true.

```csharp
[ServiceContract(SessionMode=SessionMode.Required)]
[DeliveryRequirements(RequireOrderedDelivery=true)]
public interface IHelloWorldService
{
    [OperationContract]
    string HelloWorld();
}
```

If the configuration of an endpoint does not fulfill the requirements, the host throws an exception detailing the missing requirements.

> When to use the sessions. Sessions in WCF does not deliver the same functionality as in ASP.Net or ASMX based Web Services. For instance the <a href="http://msdn2.microsoft.com/en-us/library/system.web.sessionstate.httpsessionstate.aspx">HttpSessionState</a> store is not available, not even if the WCF service is hosted in the IIS. WCF sessions are similar to Remoting sessions and are only for service instancing.

```csharp
[ServiceBehavior(InstanceContextMode = InstanceContextMode.PerSession)]
public class HelloWorldService : IHelloWorldService
{
    public string HelloWorld()
    {
        Console.WriteLine("Invoked by client with session ID {0}",
            OperationContext.Current.SessionId);

        return "Hello World";
    }
}
```

To control the instancing of the service type objects, decorate the service type with the ServiceBehavior attribute and specify the InstanceContextMode enum as in the above code.

The InstanceContextMode values are:

* PerCall &#8211; A new InstanceContext object is created prior to and recycled subsequent to each call. If the channel does not create a session this value behaves as if it were PerCall.
* PerSession &#8211; A new InstanceContext object is created for each session.
* Single &#8211; Only one instance of the service type object is used for all incoming calls and is not recycled subsequent to the calls. If a service type object does not exist, one is created.

Download the sample code with reliable messaging here ([Hello World WCF Reliable Messaging](/wp-content/uploads/helloworldwcfreliablemessaging.zip)) and read more about WCF sessions [here](http://msdn2.microsoft.com/en-us/library/ms733040.aspx).
