---
layout: page
title: Quick start
---

This guide is designed to get you up and running quickly.

## Creating the project

First, you'll need to [download](http://maven.apache.org/download.html) and [install](http://maven.apache.org/download.html#installation) [maven3](http://maven.apache.org/) if you haven't already done so. You *don't* have to use Maven, but it makes things substantially easier by managing all of the dependencies in Tynamo. Even if it initially takes you a while to learn Maven, it saves you a lot of time as your project gets bigger.
	
<div markdown="1" class="alert alert-info">
If you are not familiar with Maven, here are some links that'll help you get to learn the basics

[Maven: The Definitive Guide](http://sonatype.com/book/index.html)

[Maven Users Centre](http://maven.apache.org/users/index.html)
</div>

Next, issue the following command (on a single line):

	mvn archetype:generate -DarchetypeCatalog=http://www.tynamo.org/

Use Maven 3.x or newer

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

## Running the project

Starting up your application is super simple, just go into your new project directory and do:

	mvn jetty:run

This will download all dependencies, build your project and start it in the jetty container. Be warned, this can take some time, especially if you just started using maven. If any downloads fail, try running this again. Sometimes the maven repositories get bogged down. When everything finishes, you will now be able to go to <http://localhost:8080/>

## Import the project to your IDE

All major IDEs (Eclipse, IntelliJ IDEA and Netbeans) provide an excellent support for Maven projects nowawadays. The [tapestry-model guide has a section on developing with IDEs](http://www.tynamo.org/tapestry-model+guide/#developing-a-tynamo-app-using-an-ide). If you are en Eclipse user, you may want to read more about [Developing with Tomcat and Eclipse](http://www.tynamo.org/Developing+with+Tomcat+and+Eclipse/). You should now be all ready to [start adding your own domain classes](http://www.tynamo.org/tapestry-model+guide/)!
