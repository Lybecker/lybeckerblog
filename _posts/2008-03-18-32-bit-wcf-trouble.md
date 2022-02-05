---
title: 32-bit WCF memory allocation trouble
permalink: /blog/2008/03/18/32-bit-wcf-trouble/
dsq_thread_id:
  - "3456317395"
  - "3456317395"
categories:
  - .Net
  - WCF
---
With a 32 bit platform a process can only address 2 GB of memory by default on windows. For most applications this is not an issue. But today I really messed up – OOM (OutOfMemoryException) exceptions all over 🙁

I added loads of test data to our 32 bit development servers and tried to run a development utility. It failed and failed and failed with OOM exceptions. The cause was actually quite simple as the application tried to allocate more than 500 MB of consecutive memory.

The utility is a console app and the search index is a WCF server and they communicate via TCP buffered binary format (netTCPBinding).

The console utility retrieved all the unique identifiers in the search index (based on Lucene.Net) to compare these with the records in the SQL Server, to see if everything is in sync. The number of unique identifiers where now in the millions, but that was not the root cause. The process consumed twice the memory, because I use buffered transfer mode instead of streamed.

Buffered transfer mode serializes everything before sending the message from the server to the client. So in effect our WCF server not only had the huge array of unique identifiers but also a serialized version. It never got the stage where it could send the message.

Moral of the story – use streamed transfer mode with large messages.

As this is a development utility, I sidestepped 🙂

I enabled the [3GB switch](http://technet.microsoft.com/en-us/library/bb124810.aspx "3GB setup on Windows") allowing processes to allocate up to 3 GB of memory, and tried again. Same result: OOM.

I figured that the .Net might have a limitation and perhaps a switch to enable larger memory allocation. Apparently there is a small program called [Microsoft COFF Binary File Editor](http://msdn2.microsoft.com/en-us/library/xd3shwhf%28VS.80%29.aspx "Microsoft COFF Binary File Editor on MSDN") (EDITBIN.EXE) that modifies Common Object File Format ([COFF](http://en.wikipedia.org/wiki/COFF "COFF explained on Wikipedia")) binary files. This binary file editor can enable applications to address more than 2 GB of memory with the [LARGEADDRESSAWARE](http://msdn2.microsoft.com/en-us/library/203797te(VS.80).aspx "EDITBIN LARGEADDRESSAWARE option on MSDN") option.

The steps to enable LARGEADDRESSAWARE for an application:

  1. Start Visual Studio Command Prompt or run VSVARS32.BAT (in the visual studio subdirectory: Common7Tools) which in effect does the same things.
  2. Run `editbin /LARGEADDRESSAWARE <yourApp>.exe`

Tomorrow I will change the implementation to use streamed transfer mode. Promise 🙂

![simpsonsalwaysusestreamedtransfermode.gif](/wp-content/simpsonsalwaysusestreamedtransfermode.gif)
