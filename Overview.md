---
layout: page
title: Overview
---
## Tynamo in a nutshell

Tynamo is model-driven, full-stack web framework based on Tapestry 5.
	
<div markdown="1" class="alert alert-success">
[A full-stack (web application) framework's primary goals are to provide a single set of components, covering the entire spectrum of things you'll need to build web applications, and to ensure that those components work well with one another.][full-stack-definition]
</div>

Tynamo's mission is to make web application development in Java simpler. We leverage existing technologies where possible and provide integrations with proven, clean and compact libraries rather than limit ourselves only to standard Java (JSRs). Tynamo is both comprehensive and modular so you are free to choose the parts you like from our full stack. Certainly, we follow standards where possible but we drive towards simplicity rather than conformance. We are not interested in reinventing the wheel, but we leverage existing projects and libraries where ever possible. And finally, we like Tapestry and our modules use Tapestry IoC extensively. We are well aware of most other front-end Java web technologies, but we don't use Wicket, Spring MVC, Stripes, Struts, Shale or any other and we are not planning to integrate with those technologies.

Our older sibling, [Trails framework](https://www.openhub.net/p/trails), offered the same goodies for Tapestry 4. Trails was started sometime around 2005. The current committers started in the project shortly after and as Tapestry evolved, Trails and we evolved with it. The concepts in Tynamo are largely the same as in Trails, but Tynamo is more modular, simpler and easier to use. We have several stand-alone modules for Tapestry that you are free to use without the rest of Tynamo goodies, or, if you just about to start with your journey to developing Java web applications, you can start with Tynamo archetype (read the [Quick start](http://www.tynamo.org/Quick+start/)) and then gradually improve your understanding of the technologies in play while adding features and customization to your application.

We are also all about modeling the domain in an object-oriented manner. Currently, this means adopting an ORM (Object-Relational-Mapping) tool, namely JPA2 (or if you so prefer, native Hibernate). We are provide direct support for native Hibernate and some modules for JDO but not full modelling support for the latter. Unfortunately, while current ORM technologies are fairly advanced, there's still an impedance mismatch and they are not always easy to use, so you often need to understand the basics of relational database to be productive with ORM.

## Tynamo is modular

We provide several independent or semi-indent modules for use in Tapestry 5 applications. There are release versions available of most, if not all, modules with support for recent versions of Tapestry (5.4). Check the guide for a specific module for more detailed information about version compatibility. You'll find a quick description of some of the main modules below.

### tapestry-model

[tapestry-model](http://www.tynamo.org/tapestry-model+guide/) is the main Tynamo module. Tapestry-model allows rapid CRUD application development by automatically creating list, display and edit pages for POJOs. Tynamo uses introspection to gather information about bean properties and creates representations and editors depending on their type, for example.

Tapestry-model itself consists of three main components: a persistence service, a descriptor service that provides meta data for the domain beans and an editor service that provides appropriate editor components based on meta data. To do the heavy lifting, Tynamo depends on several other frameworks. To understand tapestry-model, it is necessary to understand a bit about the frameworks it uses, specifically Tapestry and JPA/Hibernate.

### tapestry-security

[tapestry-security](http://www.tynamo.org/tapestry-security+guide/) is a comprehensive security module that provides tight integration with Apache Shiro, an established, high-performing and easy-to-use security framework for Tapestry applications. The modules started as thin layer, but has grown to support a variety of security use cases and techniques, including annotation and URL-based as well as procedural security checks. Tapestry-security forms the foundation for supporting security modules, such as tynamo-federatedaccounts and tapestry-security-jpa.

### tapestry-routing

Inspired by sitebricks, resteasy and rails routing, [tapestry-routing](http://www.tynamo.org/tapestry-routing+guide/) allows you to provide your own custom mapping between Tapestry 5 pages and URLs.

### tapestry-resteasy

[tapestry-resteasy](http://www.tynamo.org/tapestry-resteasy+guide/) provides integration with Tapestry 5 and JBoss’ RESTEasy, an implementation JAX-RS (Java API for RESTful Web Services). JAX-RS is a very nice little spec and if you know what it is about, the following should be very easy to understand. Otherwise, read up on it at Jersey’s overview (the reference implementation) or the actual specification. JBoss’ RESTEasy provides a few more features over the reference implementation.

## Downloading Tynamo

Tynamo uses Maven (3.x). All released Tynamo modules are available through Maven central repository. If you just started with Tynamo, it's easiest to use Maven and follow the Quick Start guide. Since Tynamo is intended for implementing applications on top of it, you need a build system any way and Maven will make your life easier - just take our word for it.

Naming convention is such that the group id always starts with org.tynamo. The artifact id indicates the project or the module's standalone status (such as tapestry-conversations that can be used without dependencies to other Tynamo modules). tapestry-model consists of several sub-modules.

If for one reason or another, you hate Maven and absolutely refuse to use it, you can download the desired Tynamo releases from the main Maven repository at repo1.maven.org.

## Feedback and community
Your best and fastest way to get help online: [tapestry-users mailing list](http://tapestry.apache.org/community.html)

Since moving to Github after the demise of Codehaus (the original project home), the project hasn't had a developers mailing list. Open issues at Github, ping us on the mailing list or contact directly if you need help with a specific issue.

[full-stack-definition]: http://www.b-list.org/weblog/2007/feb/19/python-framework-design/ "full-stack framework in a nutshell by James Bennett"

