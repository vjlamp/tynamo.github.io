---
layout: post
title: tapestry-security 0.6.3 released!
---
While working on securing an Ember frontend with Tapestry backend, I noticed that tynamo-resteasy didn't want to play well with security. I'm fairly surprised this issue surfaced only now because tapestry-security is fairly well battle tested. Anyhow, if you've ever had a problem with T5.4 and tapestry-security not being able obtain assigned roles or user identity from the request, it's time to upgrade.

**Release notes**

- Update to T5.4-rc-1 [#25](https://github.com/tynamo/tapestry-security/issues/25)
- Store ShiroHttpServletRequest to RequestGlobals before processing [#24](https://github.com/tynamo/tapestry-security/issues/24)