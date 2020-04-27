---
layout: post
title: Flurry of new Tynamo releases!
---

Ever since Tapestry 5.5 was released late March, I had been thinking that I *really* should update the Tynamo libraries and fix the issues that had slowly been piling up. However, it was really thanks to performance issues with Spring ACL in an older JSP webapp that caused me to pick up the open source hammer and start hammering. If you know Spring ACL, you know that its ACL rules are detached from the domain data. So, what happens is that a few tens of thousand of data rows can quickly balloon to several million of ACL join table rows with all combinations of individual (read, update, etc.) instance permissions. Worse yet, the ACL rules are often (especially for row level access) matched post data query. Figured it was a great case to demonstrate how it could be rewritten better, so I busted open my trustworthy Tynamo toolbox and pulled out [tapestry-security-jpa](http://www.tynamo.org/tapestry-security-jpa+guide/) from there. Obviously, the tools needed a bit of sharpening and some fixes here and there, but I was generally happy to find out how solid and innovative some of the ideas in them were and still are (perhaps an old school JSP/Spring app is not much of a competition, but still).

Another library I was happy to update was tapestry-resteasy. Alejandro had once upon a time made a swagger integration for it, but it was left rotting there in src/test/java. Swagger had moved ahead by a few generations (to v3) but it wasn't too hard to lift the old code, update it to work with the latest and package it up as a separate module, tapestry-resteasy-swagger, that can be used by simply adding it as a dependency to your project. You'll get the swagger UI in addition to (v2) openapi.json. 

Updating tapestry-security was also a no-brainer, but functionally I really didn't need to do much, mainly it was matter of updating dependencies and cleaning up some obsolete code paths. It's updated to Shiro 1.5.2 as a bonus.

All in all, I made no less than seven Tynamo releases, all updated to work with Tapestry 5.5:

- [tapestry-conversations 0.3.0](http://www.tynamo.org/tapestry-conversations+guide/)
- [tapestry-jpa-seedentity 0.2.0]((http://www.tynamo.org/tapestry-jpa-seedentity+guide/)
- [tapestry-security 0.8.0](http://www.tynamo.org/tapestry-security+guide/)
- [tapestry-security-jpa 0.2.0](http://www.tynamo.org/tapestry-security-jpa+guide/)
- [tapestry-resteasy 0.6.0](http://www.tynamo.org/tapestry-resteasy+guide/)
- [tapestry-resteasy-swagger 0.0.2](http://www.tynamo.org/tapestry-resteasy+guide/)
- [tynamo-test 0.2.1](https://github.com/tynamo/tynamo-test)

Not bad for a weekend's worth of work, enjoy :)
