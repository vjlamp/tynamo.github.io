---
layout: guide
title: tapestry-seedentity
repository_name: tapestry-hibernate-seedentity
redirect_from: "/tapestry-hibernate-seedentity/"
---
<div markdown="1" class="alert alert-info">
**Version status: 0.1.3 release stable, in production**

Don't let the version number fool you. This is code that has been used for years but was refactored into its own module when we founded Tynamo. 0.1.x added SeedEntityUpdater and support for @NaturalId.
</div>

No more generating and running SQL seed scripts! tapestry-hibernate-seedentity module is a simple, object-oriented way to seed your database either for development or production environment or both. This works for any Tapestry5 application using Hibernate and is independent of the rest of Tynamo modules. If you are using JPA,
use the sister module [tapestry-jpa-seedentity](http://www.tynamo.org/tapestry-jpa-seedentity+guide/) instead.

To use the feature, you need to add the following dependency to your pom.xml:

	<dependency>
	  <groupId>org.tynamo</groupId>
	  <artifactId>tapestry-hibernate-seedentity</artifactId>
	  <version>0.1.3</version>
	</dependency>
	
{% include tapestry-seedentity-include.md %}