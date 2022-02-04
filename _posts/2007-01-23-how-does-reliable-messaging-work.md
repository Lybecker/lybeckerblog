---
id: 20
title: How does Reliable Messaging work?
date: 2007-01-23T18:05:42+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/2007/01/23/how-does-reliable-messaging-work/
permalink: /2007/01/23/how-does-reliable-messaging-work/
dsq_thread_id:
  - "3459164297"
  - "3459164297"
categories:
  - .Net
  - WCF
  - 'ws-*'
tags:
  - dotNet
  - WCF
---
First of all – why do we need a technology like reliable messaging? That is due to the inherently unreliable of communication networks. Back in the old days when everyone was using dial-up modems people were aware of the (un)reliability of the connection. But these days with xDSL and fiber optic Internet connections you may seldom be aware of connection failures. But history has a way of repeating itself and now applications are make use of wireless technologies like GPRS, UMTS and WI-FI where you notice the unreliability again.

Reliable massaging ensures that messages send over a wire are delivered exactly once, at least once or at most once; even in the presence of component, system, or network failures. If required the messages can also be delivered in the same order as they were sent.

When implementing connected solutions the normal message pattern are; send a message and get a response or acknowledge of some kind back.

<img loading="lazy" class="aligncenter size-full wp-image-294" title="Reliable Messaging - send/response" src="http://www.lybecker.com/blog/wp-content/uploads/reliablemessaging1.png" alt="Reliable Messaging - send/response" width="400" height="121" /> 

In a &#8220;happy day&#8221; scenario the message travels back and forth between the initiator and the acceptor with no problems. Initiator and acceptor are used as both clients and servers can initiate a message sequence.

In my experience most organizations developing web services with the .Net 1.x or 2.0 frameworks and ASMX ignore any reliability issues, hoping never to be faced with reliability  
problems. (I have done it myself – shhh don’t tell anybody;-))

The difficulty is how to detect if a message is missing and how to recover.

<img loading="lazy" class="aligncenter size-full wp-image-295" title="Reliable Messaging - Lost send" src="http://www.lybecker.com/blog/wp-content/uploads/reliablemessaging2.png" alt="Reliable Messaging - Lost send" width="400" height="128" /> 

In the event of the initiator’s message never arrives to the acceptor and the acknowledge message will never send back to the initiator. This will causes the initiator to resend the message after a period of time. This requires a message store on the initiator so it can resend any lost messages.

<img loading="lazy" class="aligncenter size-full wp-image-296" title="Reliable Messaging - Lost response" src="http://www.lybecker.com/blog/wp-content/uploads/reliablemessaging3.png" alt="Reliable Messaging - Lost response" width="400" height="147" /> 

In this next scenario the message is received and processed by the acceptor but the acknowledge message gets lost. From the initiators perspective this is exactly the same problem as previous where the initiator’s message got lost.

The initiator therefore resends the message, but if the acceptor processes this message again we might be in serious trouble. The message might say withdraw money from your account – we don’t want that!

Consequently the acceptor stores the received message, so it can track duplicate messages and not reprocess the same message over and over again. But the acceptor still has  
to send the acknowledge message to the initiator, so it knows that that the initial message got processed. Therefore the acceptor also has to store processed response messages, so it can resend acknowledge messages.

It is easy to comprehend a technology like reliable messaging when the problem is seen in the right context. If you want to dig deeper into specifications of reliable messaging look at the specification of [WS-ReliableMessaging](http://www-128.ibm.com/developerworks/library/specification/ws-rm/) or [WS-Reliability](http://www.oasis-open.org/committees/tc_home.php?wg_abbrev=wsrm) – it is not hard to read at all. The two specifications solve the same problem but are implemented differently. WS-ReliableMessaging are implemented in Windows Communication Foundation and WS-Reliability is an [OASIS](http://www.oasis-open.org) standard.

You can read more about the differences in the article: [WS-RM and WS-R: Can SOAP be reliably delivered from confusion?](http://www-128.ibm.com/developerworks/library/ws-rmpaper/)