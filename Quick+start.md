---
layout: page
title: Quick start
---

This guide is designed to get you up and running quickly.
Creating the project

First, you'll need to download and install maven2 if you haven't already done so. You *don't* have to use Maven, but it makes things substantially easier by managing all of the dependencies in Tynamo. Even if it initially takes you a while to learn Maven, it saves you a lot of time as your project gets bigger.
	

If you are not familiar with Maven, here are some links that'll help you get to learn the basics

Maven: The Definitive Guide

Maven Users Centre

Next, issue the following command (on a single line):

mvn archetype:generate -DarchetypeCatalog=http://www.tynamo.org/

Use Maven 2.1.x or newer (otherwise you need to specify the archetype plugin version with mvn org.apache.maven.plugins:maven-archetype-plugin:2.0-alpha-4:generate since the older archetype plugin version doesn't know how to handle :generate goal)

If you really want to use snapshot archetypes, use:

mvn archetype:generate -DarchetypeCatalog=https://nexus.codehaus.org/content/groups/snapshots-group/

Enter suitable values on the command line, for example:

Choose archetype:
1: http://www.tynamo.org/ -> tynamo-archetype (Model-driven web framework based on Tapestry 5)
Choose a number: : 1
Define value for property 'groupId': : com.test
Define value for property 'artifactId': : testme
Define value for property 'version': 1.0-SNAPSHOT:
Define value for property 'package': com.test:
Confirm properties configuration:
groupId: com.test
artifactId: testme
version: 1.0-SNAPSHOT
package: com.test
Y: Y

Once you press enter, Maven will download any required plugins, the Tynamo modules and its dependencies and then create a working Tynamo project.
Running the project

Starting up your application is super simple, just go into your new project directory and do:

mvn jetty:run

This will download all dependencies, build your project and start it in the jetty container. Be warned, this can take some time, especially if you just started using maven. If any downloads fail, try running this again. Sometimes the maven repositories get bogged down. When everything finishes, you will now be able to go to http://localhost:8080/
Import your project

Setting up an Eclipse project is also very simple. First, you will need to tell Eclipse how to find your maven repository. This is a one time step for each Eclipse workspace:

mvn -Declipse.workspace=<path-to-eclipse-workspace> eclipse:add-maven-repo

Now do:

mvn eclipse:eclipse

You should now be able import your project into Eclipse and ready to add your own domain classes!