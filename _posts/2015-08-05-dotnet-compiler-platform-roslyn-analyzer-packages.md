---

title: .NET compiler platform (Roslyn) analyzer packages
date: 2015-08-05T14:39:40+01:00


guid: http://www.lybecker.com/blog/?p=1362
permalink: /blog/2015/08/05/dotnet-compiler-platform-roslyn-analyzer-packages/
dsq_thread_id:
  - "4005436219"
  - "4005436219"
categories:
  - .Net
  - Everyday coding
  - Useful tools
tags:
  - .Net
  - Roslyn
  - Visual Studio 2015
---
The greatest new feature in Visual Studio 2015 is the .NET compiler platform previously known as Roslyn. The .NET Compiler Platform is an open source compiler for C# and VB with rich code analysis APIs. It enables developers to build code analysis tool like code analyzers, fixes and refactorings.

The community has built a number of packages containing great analyzers, fixes and refactorings. These can be installed either as a Visual Studio 2015 Extension or at project level as NuGet packages.

## Refactoring Essentials

[Refactoring Essentials](http://vsrefactoringessentials.com/) contains approx. 200 code analyzers, fixes and refactorings
Simple defensive code analyzers like parameter checking.

[<img loading="lazy" class="aligncenter size-full wp-image-1365" src="/blog/wp-content/uploads/Roslyn_CheckDictionaryKeyValueCodeRefactoring.png" alt="Roslyn_CheckDictionaryKeyValueCodeRefactoring" width="493" height="146" />](/blog/wp-content/uploads/Roslyn_CheckDictionaryKeyValueCodeRefactoring.png)

Simplyfing code by converting conditional ternary to null coalescing.

[<img loading="lazy" class="aligncenter size-large wp-image-1366" src="/blog/wp-content/uploads/Roslyn_ConvertConditionalTernaryToNullCoalescingAnalyzer-550x174.png" alt="Roslyn_ConvertConditionalTernaryToNullCoalescingAnalyzer" width="550" height="174" />](/blog/wp-content/uploads/Roslyn_ConvertConditionalTernaryToNullCoalescingAnalyzer.png)

## CSharp Essentials

[CSharp Essentials](https://github.com/DustinCampbell/CSharpEssentials) focuses on the new features in C# 6 such as [the nameof operator](/blog/2015/01/08/the-nameof-operator/), [string interpolation](/blog/2015/01/09/awesome-string-formatting/), [auto-properties](/blog/2015/01/15/auto-property-initializers/) and [expression-bodied methods](/blog/2015/01/13/expression-bodied-methods/).

[<img loading="lazy" class="aligncenter size-large wp-image-1369" src="http://www.lybecker.com/blog/wp-content/uploads/Roslyn_StringInterpolation-550x156.jpg" alt="Roslyn_StringInterpolation" width="550" height="156" />](http://www.lybecker.com/blog/wp-content/uploads/Roslyn_StringInterpolation.jpg)

## Code Cracker

[Code Cracker](http://code-cracker.github.io/) is a smaller package for C# and VB with analyzers e.g. for empty catch blocks and if a disposable object is disposed.

[<img loading="lazy" class="aligncenter size-large wp-image-1367" src="http://www.lybecker.com/blog/wp-content/uploads/Roslyn_Disposable-550x203.png" alt="Roslyn_Disposable" width="550" height="203" />](http://www.lybecker.com/blog/wp-content/uploads/Roslyn_Disposable.png)

## SonarLint

[SonarLint](http://vs.sonarlint.org/) for C# has great analyzers too as Christiaan Rakowski points out in the comments. One of them warns about logical paths that will never be reached or simplified.

## [<img loading="lazy" class="aligncenter size-large wp-image-1373" src="http://www.lybecker.com/blog/wp-content/uploads/Roslyn_conditionalstructure-550x123.png" alt="Roslyn_conditionalstructure" width="550" height="123" />](http://www.lybecker.com/blog/wp-content/uploads/Roslyn_conditionalstructure.png)Platform Specific Analyzer

With Windows 10 and the new Universal Windows Platform you as the developer need to make sure that the Windows App does not use an API not supported on the platform you are targeting. This is exactly what the [Platform Specific Analyzer](https://github.com/ljw1004/blog/tree/master/Analyzers/PlatformSpecificAnalyzer) package does for both C# and VB.

[<img loading="lazy" class="aligncenter size-large wp-image-1368" src="http://www.lybecker.com/blog/wp-content/uploads/Roslyn_PlatformSpecific-550x129.png" alt="Roslyn_PlatformSpecific" width="550" height="129" />](http://www.lybecker.com/blog/wp-content/uploads/Roslyn_PlatformSpecific.png)

If you know any other great packages – let me know.
