---
layout: guide
title: tapestry-jpa-seedentity
repository_name: tapestry-jpa-seedentity
redirect_from: "/tapestry-seedentity/"
---
<div markdown="1" class="alert alert-info">
**Version status: 0.2.0 release stable, in production, tested to work with T5.5. 0.1.6 tested to work with T5.4 **

Don't let the version number fool you. This is code that has been used for years but was refactored into its own module when we founded Tynamo. 0.1.x added SeedEntityUpdater and support for @NaturalId.
</div>

No more generating and running SQL seed scripts! tapestry-jpa-seedentity module is a simple, object-oriented way to seed your database either for development or production environment or both. This works for any Tapestry5 application using JPA and is independent of the rest of Tynamo modules. If you are using Hibernate,
consider using the sister module [tapestry-hibernate-seedentity](http://www.tynamo.org/tapestry-hibernate-seedentity+guide/).

To use the feature, you need to add the following dependency to your pom.xml:

	<dependency>
	  <groupId>org.tynamo</groupId>
	  <artifactId>tapestry-jpa-seedentity</artifactId>
	  <version>0.2.0</version>
	</dependency>

{% include tapestry-seedentity-include.md %}
