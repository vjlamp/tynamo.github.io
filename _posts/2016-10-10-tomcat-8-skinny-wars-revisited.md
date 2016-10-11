---
layout: post
title: Tomcat 8 and skinny war deployment revisited
---

While Dokku, containerization and the like steal all of the deployment hype these days, the good old skinny wars are still alive and kicking. I was recently ~~giving CPR to~~ maintaining an older Tapestry 5 app that was moved from one of those ultra-cool but expensive PaaS platforms to an el cheapo VPS server (because nowadays, they offer just ridiculously good price/performance ratio and for whatever reason, the app didn't achieve world domination at its time).

The PaaS had its own proprietary deployment API but the app had everything and the kitchen sink as dependencies so I quickly got tired of deploying the whole WAR when less than 1% of it had really changed. Since I've done plenty of skinny war deployments in the past and this app was in maintenance mode anyway, I figured a skinny WAR was the way to go, only to find out that the deployment script I had copied from another old project just didn't work anymore in Tomcat 8.

Seems like there's been lots of internal configuration changes in Tomcat 8. The problem is that there's so much old information available (when you start googling for answers, nobody ever seems to care about specifying the version of Tomcat their advice is for) and that Tomcat's own User Guide is hopelessly succinct, lacking any examples for the common cases or references to what and why something has changed.

Instead of utilizing the classloader hierarchy (common/shared classloaders) or using VirtualWebappLoader (which is removed), in Tomcat 8 you can configure external mount points for webapp Context resources. This is clearly documented in the [relevant class in Tomcat 8's documentation](https://tomcat.apache.org/tomcat-8.0-doc/api/org/apache/catalina/WebResourceRoot.html), the only problem is you have to find the class first. In any case, this is how you'd do it:

```
<Context path="/" docBase="ROOT.war" debug="0">
	<Resources className="org.apache.catalina.webresources.StandardRoot">
		<JarResources className="org.apache.catalina.webresources.DirResourceSet"
			base="${catalina.base}/shared/lib"
			webAppMount="/WEB-INF/lib">
		</JarResources>
	</Resources>
</Context>
```

The *base* can obviously point to anything. These virtual resources are a bit like symbolic links and clearly offer way more flexibility than the previous methods should you ever need it. Now for Tapestry5 apps, loading the app and its classes from these virtual directories did not work, probably because Tapestry does its own classpath scanning for annotation support etc. I had to package my own application classes into jar and deploy it to /WEB-INF/lib to make it work (for Maven, configure the *war* plugin with *<archiveClasses>true</archiveClasses>* ). That opens up interesting possibilities, since I really don't need to even deploy the skinny war but only the application jar to update the app. The problem with that though is that I lose the built-in benefits of parallel and hot deploying WAR files ([see more about versioned parallel deployment to Tomcat](http://www.tynamo.org/Zero+downtime+deployment+to+Tomcat+7+with+Maven/) ) but I guess I'll just live with it for now.
