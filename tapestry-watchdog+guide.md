---
layout: guide
title: tapestry-watchdog
repository_name: tapestry-watchdog
---
<div markdown="1" class="alert alert-info">
**Version status: 0.0.2 experimental (for lack of features), used in production**
</div>
 
It's the usual story: Your application never really crashes until it invariably does. Whether you are running a consumer service or a business app, it's critical to minimize the downtime when the inevitable happens. Granted, it's pretty easy to hack up an OS dependent script using cron or similar to check whether your process is still running but it's a separate configuration step and besides, how do you know that your watchdog is working?

Since it's dead simple to auto-load new modules in Tapestry 5, why not embed the whole watchdog with your application? **tapestry-watchdog** is a T5 service that extracts all the resources it needs from the jar it's packaged into, then spawns a new child process to act as a watchdog for the parent application. The current version only knows to send an email when the parent process is lost but it'd be easy to extend it to execute arbitrary commands.

tapestry-watchdog offers the following benefits over traditional watchdogs:

- embedded into the application
- OS independent
- detects application hang-ups/spin-locks, not only crashes
- can fail the application if watchdog is not able to run

With the following caveats:

- somewhat higher memory consumption than non-VM watchdogs (modern 64-bit JVMs require -Xms8m minimum)
- Requires java to be available on $PATH
- Doesn't work on Google App Engine (and SELinux if not able to write to temp)

Configuring tapestry-watchdog is super simple. First, add the following dependency to your pom.xml (the module has no dependencies to any other Tynamo module):

	<dependency>
	  <groupId>org.tynamo</groupId>
	  <artifactId>tapestry-watchdog</artifactId>
	  <version>0.0.2</version>
	</dependency>

Then add the following:

	public static void contributeFactoryDefaults(MappedConfiguration<String, String> configuration) {
		configuration.add(Watchdog.SEND_EMAIL, "alert@mydomain.com");
		configuration.add("smtp.host", "localhost");
		configuration.add("smtp.port", "25");
	}

And that's all! Note that the watchdog is automatically disabled in non-production mode.
