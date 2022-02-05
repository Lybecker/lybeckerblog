---

title: 'Microsoft Julekalender låge #7'
date: 2010-12-07T07:45:28+01:00


guid: http://www.lybecker.com/blog/?p=819
permalink: /blog/2010/12/07/microsoft-julekalender-lage-7/
dsq_thread_id:
  - "3456318098"
  - "3456318098"
categories:
  - .Net
  - Announcement
  - WCF
tags:
  - .Net
  - Daniel Frost
  - Julekalender
  - Microsoft
  - Performance
  - WCF
---
[<img loading="lazy" class="alignnone size-full wp-image-821" title="Microsoft Julekalender 2010" src="http://www.lybecker.com/blog/wp-content/uploads/MicrosoftJulekalender2010.png" alt="" width="550" height="157" />](http://danielfrost.dk/post/Mr-Frosts-Julekalender-starter-pa-Onsdag!-Er-du-klar-.aspx)

Sorry – this post is in Danish.

Dagens opgave handler om Windows Communication Foundation. WCF er kompleks pga. mængden af funktionalitet og kan derfor virke indviklet. Kompleksiteten afspejles også i størrelsen på WCF assembly System.ServiceModel.dll, som er klart den største assembly i hele .Net Framework Class Library (FCL) … selv større end mscorlib.dll.

**Opgaven:**

Implementer en klient til nedstående service, som benytter WSHttpBinding med default settings.

```csharp
[ServiceContract(Namespace = "www.lybecker.com/blog/wcfriddle")]
public interface IMyService
{
    [OperationContract(ProtectionLevel =
        ProtectionLevel.EncryptAndSign)]
    string LooongRunningMethod(string name);
}

public class MyService : IMyService
{
    public string LooongRunningMethod(string name)
    {
        Console.WriteLine("{0} entered.", name);

        // Simulate work by random sleeping
        var rnd = new Random(
            name.Select(Convert.ToInt32).Sum() +
            Environment.TickCount);
        var sleepSeconds = rnd.Next(0, 100);
        System.Threading.Thread.Sleep(sleepSeconds * 1000);

        var message = string.Format(
            "{0} slept for {1} seconds in session {2}.",
            name,
            sleepSeconds,
            OperationContext.Current.SessionId);
        Console.WriteLine(message);

        return message;
    }
}
</pre>

Klienten må meget gerne være smukt struktureret og skal:

  * Implementeres i .Net 3.x eller .Net 4.0
  * Simulere et dusin forskellige klienter
  * Være så effektiv som mulig (tænk memory, CPU cycles, GC)

Beskriv kort jeres valg af optimeringer.

For at gøre opgaven nemmere at løse, så har jeg allerede løst den for jer… dog ikke optimalt. [Download min implementation](/blog/wp-content/uploads/WcfRiddle.zip).

Send løsning til anders at lybecker.com inden midnat; vinderen vil bliver offentligt i morgen og vil blive den lykkelige ejer af en fjernstyrret helikopter med tilbehør, så den er klar til af flyve. En cool office gadget. Helikopteren er nem at flyve og kan holde til en del. Det ved jeg af erfaring 🙂

Se helikopteren flyve nedefor.
