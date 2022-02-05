---
title: Levels of reuse in Software Development
excerpt: Reuse is key in software development hence the DRU principle. To often the wrong level of reuse is causing problems within an organization
permalink: /blog/2010/06/01/levels-of-reuse-in-software-development/
dsq_thread_id:
  - "3456317961"
  - "3456317961"
categories:
  - Everyday coding
  - Ilities
  - Stuff
tags:
  - Design pattern
  - Ilities
  - Methodologies
  - Object-oriented programming
---
![Software Puzzle](/wp-content/uploads/Softwarepuzzle.jpg){: .align-right}

One of the promises of object-orientation is reuse. Developing new software systems is expensive, and maintaining them is even more expensive. Reuse is therefore sensible in both business and technology perspectives.

With assistance of Erich Gamma, I have identified four levels of reuse.

## First level of reuse: Copy/paste

Duplicating code or functionality makes it easy to reuse it. It’s a real timesaver at first, but keeping all the duplicates up-to-date and maintaining them is horrifying task. Not to mention the problems when forgetting to update one or more duplicates…

> "Copy and paste programming is a pejorative term to describe highly repetitive computer programming code apparently produced by copy and paste operations. It is frequently symptomatic of a lack of programming competence, or an insufficiently expressive development environment, as subroutines or libraries would normally be used instead. In certain contexts it has legitimate value, if used with care." [Wikipedia](http://en.wikipedia.org/wiki/Copy_and_paste_programming "Copy/past programming on Wikipedia")

## **Second level of reuse: Class libraries**

Reuse at class level or a set of classes in a software library is common and also fairly easy with object-oriented languages.

> "Libraries contain code and data that provide services to independent programs. This allows the sharing and changing of code and data in a modular fashion. Some executables are both standalone programs and libraries, but most libraries are not executables …" [Wikipedia](http://en.wikipedia.org/wiki/Library_(computer_science) "Software Libraries on Wikipedia")

## **Third level of reuse: Design Patterns**

Patterns allow you to reuse design ideas and concepts independent of concrete code.

> "In software engineering, a design pattern is a general reusable solution to a commonly occurring problem in software design. A design pattern is not a finished design that can be transformed directly into code. It is a description or template for how to solve a problem that can be used in many different situations. Object-oriented design patterns typically show relationships and interactions between classes or objects, without specifying the final application classes or objects that are involved." [Wikipedia](http://en.wikipedia.org/wiki/Design_pattern_(computer_science) "Design Patterns on Wikipedia")

## **Fourth level of reuse: Frameworks**

An object-oriented abstract design to solve a specific problem – often very specialized, like Unit Testing frameworks and Object-Relational Mapping frameworks, but can be large, complex or domain specific.

> "A software framework … is an abstraction in which common code providing generic functionality can be selectively overridden or specialized by user code providing specific functionality. Frameworks are a special case of software libraries in that they are reusable abstractions of code wrapped in a well-defined API, yet they contain some key distinguishing features that separate them from normal libraries." [Wikipedia](http://en.wikipedia.org/wiki/Software_framework "Software Frameworks on Wikipedia")

It’s all about being pragmatic - not all software will reach fourth level of reuse and will be structured as frameworks - frankly it shouldn’t. That said; copy/past style development is unquestionably a wrong path.

What level is your company at?
