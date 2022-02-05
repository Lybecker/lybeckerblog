---

title: GOTO Aarhus 2012 – Tuesday
date: 2012-10-02T20:02:46+01:00


guid: http://www.lybecker.com/blog/?p=1074
permalink: /blog/2012/10/02/goto-aarhus-2012-tuesday/
dsq_thread_id:
  - "4808173176"
categories:
  - .Net
  - Conference
tags:
  - GOTOaar
---
[<img loading="lazy" class="alignright size-full wp-image-1077" title="typescript-preview" src="http://www.lybecker.com/blog/wp-content/uploads/typescript-preview.png" alt="" width="275" height="100" />](http://www.lybecker.com/blog/wp-content/uploads/typescript-preview.png)The morning keynote by [Scott Hanselman](https://twitter.com/shanselman) was about the true power of JavaScript. He argued that JavaScript in the browser is a full operating system running as a virtual machine within the browser – so we should treat it so. Don’t use Java Applets, Flash, Flex or Silverlight as it just another (slow) abstraction upon an already powerfull engine – the browser. It was a great talk leading up to the pre-release of [TypeScript](http://www.typescriptlang.org/).

I followed a couple of sessions the continuous delivery by [Sam Newman](https://twitter.com/samnewman), [Michael T. Nygard](https://twitter.com/mtnygard) (author of Release It) and [Jez Humble](https://twitter.com/jezhumble) (author of Continuous Delivery).
Continuous Integration is a prerequisite of Continuous Delivery, but many still don’t use apply Continuous Integration to their solution, with daily incremental check-ins, automated build and unit tests.

To simplify Continuous Delivery, everything must be automated. To ease the task of automation, things must be simplified. To simplify, start by decomposing the system into manageable pieces, so each can be deployed separately. How?
Decompose the system into disconnected services makes it easier to deploy a subset of the system. This limits the impact of a deployment. It even makes it possible to mitigate the risk further by making small incremental changes by only deploying one subsystem at the time.

These services have to be structured as application silos and share nothing, not even the database schema.

By automating and decomposing your system into disconnected application silo services you too can do Continuous Delivery.
After the conference the GOTO Aarhus guys had joint up with the local community and user groups to hos open sessions. I attended the [ANUG](http://www.anug.dk/) (Aarhus .NET User Group) session with Anders Hejlsberg. He presented the brand new [TypeScript](http://www.typescriptlang.org/) – a superset of JavaScript that compiles into plain JavaScript and runs in any browser (similar concept as [CoffeeScript](http://coffeescript.org/)). It has great tooling support in Visual Studio with intelliSense and static verification.

I’m looking forward to the last day of the conference tomorrow.
