---
layout: post
title: "Design Principles in the Context of Packages"
date: 2015-05-28 21:45:59 -0500
comments: true
categories:
---

Recently, I have been working my way through Uncle Bob's book, [Agile Software Development, Principles, Patterns, and Practices](Agile Software Development, Principles, Patterns, and Practices). This has resulted in spending a lot of time thinking about and exploring design principles. However, until recently, the principles I was learning applied directly to principles of class design.<!--more--> My mentor suggested I read Chapter 20 in PPP and upon doing so, I had my first exposure to the idea of package design principles. Going into the reading, I had a working knowledge of the concept of software packages, but my experience creating them was definitely lacking. Learning the principles of package design gave me some great ideas on how to organize classes in the [project](http://www.lisahamm.com/blog/2015/05/01/starting-a-java-web-server/) I am currently developing.

### Package Design Principles

Before we dive into the different package design principles, what is a package? A package is a convenient way to organize classes into groups, which can be very helpful in large projects. The principles of package design serve to define better ways of organizing classes within an application, similar to the way SOLID principles define better ways to organize methods in classes. Uncle Bob presents six different package design principles in his book. The first three principles pertain to partitioning classes into packages. The last three principles pertain to the relationships between packages. Let's take a closer look at each principle:

### Principles of Package Cohesion
  -1. Reuse-Release Equivalence Principle (REP)
    This principle states that any package created for reuse must be released and tracked. Incorporating unmaintained packages into a project often results in a headache. When releasing a "reusable" package, the developer(s) must be sure to implement a tracking system and also offer support for any reusers of the package. Because of this, all classes in reusable package must be reusable. If any class is not reusable, the package in which it resides is not actually a reusable package.
  -2. Common-Reuse Principle (CRP)
    This principle provides guidance on which classes should not be grouped together in a package. The principle essentially states if you reuse a single class from a package, you must reuse all classes in that package. There is no sense of depending on a package unless you depend on its entirety.
  -3. Common-Closure Principle (CCP)
    This principle states changes impacting a package affect all classes within the package and no other packages. Think SRP (Single Responsibility Principle) but for packages.

### Principles of Package Coupling
  -4. Acyclic-Dependencies Principle (ADP)
    This principle states cycles in the package-dependency graph should not be allowed. Applications should be divided into releasable packages in order to prevent changes in one part of an application from breaking components elsewhere in the application. In large scale applications, this is especially important in order to maintain stability with the application. Teams should check out the code, make changes, and then release newer versions of packages. This way, all other teams will not be affected by changes until they make the conscious decision to begin using the new release.
  -5. Stable-Dependencies Principle (SDP)
    This principle tells us to depend in the direction of stability. Before reading this chapter, I was familiar with the concept at a class-level, but the idea also holds true within the context of package design. When following package design principles, some packages will be stable, while others will be volatile. When a package is expected to be volatile, packages that are hard to change should not be depending on it.
  -6. Stable-Abstractions Principle (SAP)
    This principle states a package should be as abstract as it is stable. Stability within a package requires extensibility and reusability. Abstract classes lend themselves to code with these qualities. Thus, if a package is designed to be stable, it will contain abstract classes. This principle in conjunction with the Stable-Dependencies Principle can be thought of as the Dependency-Inversion Principle, but within the context of packages.

    ### Conclusion

    Package design principles align with SOLID design principles. These principles help to govern the design of higher level components in an application. Reading this chapter has gotten me thinking about how to apply this in my own work. In my current HTTP server project, I have conceptually been thinking about it as three components, which I now feel could make up three respective packages within the overarching project. First, the actual server required for connecting with clients and reading/writing HTTP messages to/from sockets. Second, the middleware component responsible for manipulating data in the same way for incoming requests and outgoing responses. Third, the actual application handlers/controllers that govern the handling of specific request routes.
