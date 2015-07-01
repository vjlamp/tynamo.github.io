---
layout: guide
title: tapestry-resteasy guide
---

## User guide for Tapestry-resteasy module

tapestry-resteasy provides integration with Tapestry 5 and [JBoss' RESTEasy](http://www.jboss.org/resteasy), an implementation JAX-RS (Java API for RESTful Web Services). JAX-RS is a very nice little spec and if you know what it is about, the following should be very easy to understand. Otherwise, read up on it at [Jersey's overview](https://wikis.oracle.com/display/Jersey/Overview+of+JAX-RS+1.0+Features) (the reference implementation) or [the actual specification](https://jcp.org/en/jsr/detail?id=311). JBoss' RESTEasy provides a few more features over the reference implementation.

### Outline

   - Add the tapestry-resteasy dependency to your pom.xml
   - No need to edit your web.xml.
   - Create a package named "rest" for your rest services.
   - Code your singleton resource.
   - Annotate your entities.
   - Add a provider library to your pom.xml
   - Contribute singleton resources
   - Live class reloading
   - Notes:
   - version table

### Add the tapestry-resteasy dependency to your pom.xml

To use tapestry-resteasy (an independent module of Tynamo), you need to add the following dependency to your pom.xml:

	<dependency>
	    <groupId>org.tynamo</groupId>
	    <artifactId>tapestry-resteasy</artifactId>
	    <version>0.4.0</version>
	</dependency>

### No need to edit your web.xml.

tapestry-resteasy integrates with the Tapestry's HttpServletRequest pipeline.

### Create a package named "rest" for your rest services.

tapestry-resteasy will scan the "rest" package and automatically add singleton resources to the configuration.

If you want to add extra packages, besides InternalConstants.TAPESTRY_APP_PACKAGE_PARAM + ".rest", to be scanned for annotated REST resource classes you can contribute your package names to the ResteasyPackageManager.

	public static void contributeResteasyPackageManager(Configuration<String> configuration)
	{
		configuration.add("org.tynamo.resteasy.ws");
	}

### Code your singleton resource.

Good practices:

   - Name the resource class using the entity name followed by the word "Resource".
   - The @Path should be the entity name in lowercase.

	package org.tynamo.examples.simple.rest;
	
	import org.tynamo.examples.simple.MyDomainObject;
	import org.tynamo.services.PersistenceService;
	
	import javax.ws.rs.*;
	import javax.ws.rs.core.Response;
	import java.util.List;
	
	@Path("/mydomainobject")
	public class MyDomainObjectResource
	{
	
		private PersistenceService persistenceService;
	
		public MyDomainObjectResource(PersistenceService persistenceService) {
			this.persistenceService = persistenceService;
		}
	
		@GET
		@Produces({"application/xml", "application/json"})
		public List<MyDomainObject> getAllDomains()
		{
			return (persistenceService.getInstances(MyDomainObject.class));
		}
	
		@POST
		@Produces({"application/xml", "application/json"})
		public Response post(MyDomainObject domainObject)
		{
			persistenceService.save(domainObject);
			return Response.ok().build();
		}
	
		@GET
		@Path("{id}")
		@Produces({"application/xml", "application/json"})
		public MyDomainObject getDomainObject(@PathParam("id") Long id)
		{
			MyDomainObject domainObject = persistenceService.getInstance(MyDomainObject.class, id);
			if (domainObject == null)
			{
				throw new WebApplicationException(Response.Status.NOT_FOUND);
			}
			return domainObject;
		}
	
	}

### Annotate your entities.

Don't forget to annotate your entities with JAXB annotations. You need at least @XmlRootElement. See: <http://www.caucho.com/resin-3.1/doc/jaxb-annotations.xtp>

	package org.tynamo.examples.simple.entities;
	
	import org.hibernate.validator.NotNull;
	import org.tynamo.descriptor.annotation.PropertyDescriptor;
	
	import javax.persistence.Entity;
	import javax.persistence.GeneratedValue;
	import javax.persistence.GenerationType;
	import javax.persistence.Id;
	import javax.xml.bind.annotation.XmlRootElement;
	
	@Entity
	@XmlRootElement(name = "mydomainobject")
	public class MyDomainObject
	{
		private Long id;
	
		private String name;
	
		@Id
		@GeneratedValue(strategy = GenerationType.AUTO)
		@PropertyDescriptor(index = 0)
		public Long getId()
		{
			return id;
		}
	
		public void setId(Long id)
		{
			this.id = id;
		}
	
		@NotNull(message = "name can't be null")
		public String getName()
		{
			return name;
		}
	
		public void setName(String name)
		{
			this.name = name;
		}
	
		public boolean equals(Object o)
		{
			if (this == o) return true;
			if (o == null || getClass() != o.getClass()) return false;
	
			MyDomainObject that = (MyDomainObject) o;
	
			return getId() != null ? getId().equals(that.getId()) : that.getId() == null;
		}
	
		public int hashCode()
		{
			return (getId() != null ? getId().hashCode() : 0);
		}
	
		public String toString()
		{
			return getName();
		}
	}

### Add a provider library to your pom.xml

<div markdown="1" class="alert alert-warning">
By default tapestry-resteasy does not includes any marshaller/unmarshaller dependency.

You will need to manually add the dependency to your pom.xml
</div>

The previous examples use the JSON marshaller/unmarshaller provided by the resteasy-jettison-provider library. This provider allows you to marshall JAXB annotated POJOs to and from JSON, it wraps the Jettison JSON library to accomplish this.

	<dependency>
	    <groupId>org.jboss.resteasy</groupId>
	    <artifactId>resteasy-jettison-provider</artifactId>
	    <version>3.0-beta-4</version>
	</dependency>

If you don't need JAXB and only you only need JSON support, you can use the JSON marshaller/unmarshaller provided by the resteasy-jackson-provider library. I personally find Jackson's output format more intuitive than the format provided by either BadgerFish or Jettison.

	<dependency>
		<groupId>org.jboss.resteasy</groupId>
		<artifactId>resteasy-jackson-provider</artifactId>
		<version>3.0.7.Final</version>
	</dependency>

You could use JAXB and Jackson together but be aware of the [possible conflicts with the JAXB providers](http://docs.redhat.com/docs/en-US/JBoss_Enterprise_Web_Platform/5/html/RESTEasy_Reference_Guide/json.html#Possible_Jackson_Problems)

### Contribute singleton resources

If the rest services autodiscovery is not enough for you, you can manually contribute singleton resources in your AppModule:

	/**
	 * Contributions to the RESTeasy main Application, insert all your RESTeasy singletons services here.
	 */
	@Contribute(javax.ws.rs.core.Application.class)
	public static void configureRestResources(Configuration<Object> singletons, MyDomainObjectResource myDomainObjectResource)
	{
		singletons.add(myDomainObjectResource);
	}

<div markdown="1" class="alert alert-error">
Since the upgrade to Tapestry 5.3 and the move from Javassist to Plastic your JAX-RS annotations must be on the service interface or RESTEasy will not be able to find them.
</div>

### Live class reloading

<div markdown="1" class="alert alert-warning">
Available since tapestry-resteasy 0.2.1!
</div>

Reloading is limited to services that can be proxied, so to enable live class reloading for your REST services you need to create them as regular Tapestry IoC services with an interface and an implementation of that interface. You then have the option to leave them both in the autodiscovery package or explicitly bind them as services and contribute them to javax.ws.rs.core.Application.class. Read more about how service implementation reloading works in: <http://tapestry.apache.org/reload.html>

Here is an example from the tapestry-resteasy test suite.

public static void bind(ServiceBinder binder)
{
	binder.bind(ReloadableEchoResource.class, ReloadableEchoResourceImpl.class);
}

@Contribute(javax.ws.rs.core.Application.class)
public static void configureRestResources(Configuration<Object> singletons, ReloadableEchoResource reloadableEchoResource)
{
	singletons.add(reloadableEchoResource);
}



### Enjoy

   - <http://localhost:8080/rest/mydomainobject/>
   - <http://localhost:8080/rest/mydomainobject/1>

 

The default mapping prefix under which your rest resources are available is "rest". You can easily change this by overriding the symbol in your application defaults:

	@ApplicationDefaults
	public static void setApplicationDefaults(MappedConfiguration<String, Object> configuration) {
	     configuration.add(ResteasySymbols.MAPPING_PREFIX, "/api");
	}

### Notes:

If you get this exception:

	Caused by: java.lang.ClassCastException: com.sun.xml.stream.ZephyrParserFactory cannot be cast to org.codehaus.stax2.XMLInputFactory2

Add this to your system properties:

	javax.xml.stream.XMLInputFactory=com.ctc.wstx.stax.WstxInputFactory

like so:
**mvn jetty:run**

	<!-- Run the application using "mvn jetty:run" -->
	<plugin>
		<groupId>org.mortbay.jetty</groupId>
		<artifactId>maven-jetty-plugin</artifactId>
		<configuration>
			<!-- Log to the console. -->
			<requestLog implementation="org.mortbay.jetty.NCSARequestLog">
				<!-- This doesn't do anything for Jetty, but is a workaround for a Maven bug
					 that prevents the requestLog from being set. -->
				<append>true</append>
			</requestLog>
			<systemProperties>
				<systemProperty>
					<name>tapestry.compress-whitespace</name>
					<value>false</value>
				</systemProperty>
				<systemProperty>
					<name>tapestry.production-mode</name>
					<value>false</value>
				</systemProperty>
				<systemProperty>
					<name>javax.xml.stream.XMLInputFactory</name>
					<value>com.ctc.wstx.stax.WstxInputFactory</value>
				</systemProperty>
			</systemProperties>
		</configuration>
	</plugin>

Check google for more information: <http://www.google.com/search?hl=en&q=com.sun.xml.stream.ZephyrParserFactory+cannot+be+cast+to+org.codehaus.stax2.XMLInputFactory2&aq=f&oq=&aqi=>
 
### version table
 
| tapestry-resteasy	| Tapestry	| RESTEasy	| notes: | 
| 0.0.1	| 5.1.x	| 1.1.GA	| see [tapestry-resteasy-0.0.1 guide](https://web.archive.org/web/20150506154009/http://tynamo.org/tapestry-resteasy-0.0.1+guide) |
| 0.2.1	| 5.2.x	| 2.0.1.GA |
| 0.3.0	| 5.3.x	| 2.3.0.GA | 
| 0.3.1	| 5.3.x	| 3.0-beta-4 |
| 0.3.2	| 5.3.x	| 3.0.1.Final	|
| 0.4.0	| 5.4.x	| 3.0.7.Final	|
