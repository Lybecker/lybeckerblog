---
id: 1093
title: Accessing HTTP Request from ASP.NET Web API
date: 2013-06-26T16:33:33+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=1093
permalink: /2013/06/26/accessing-http-request-from-asp-net-web-api/
dsq_thread_id:
  - "3456318170"
  - "3456318170"
categories:
  - .Net
  - Web API
tags:
  - ASP.Net Web API
  - dotNet
---
Do you need access to the bare HTTP request in ASP.NET Web API to access custom header etc.? Then add the HttpRequestMessage:

<pre class="brush: csharp; highlight: [5]; title: ; notranslate" title="">public class CalculatorController : ApiController
{
  public int Add(HttpRequestMessage requestMessage, int x, int y)
  {
    var accessToken = requestMessage.Headers.Authorization.Parameter;
    // use the HTTP header

    return x + y;
  }
}
</pre>

The HttpRequestMessage is automatically bound to the controller action, so you can still execute the action like http://localhost/calculator/add?x=3&y=2

Simple and easy.