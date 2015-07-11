---
layout: guide
title: tapestry-model
repository_name: tapestry-model
---
<div markdown="1" class="alert alert-info">
**Version status: 0.5.0 beta for T5.4, 0.4.3 beta for T5.3.x**
Version status: 0.5.1 for T5.3 and up, 0.3.1 for T5.2.x, used in production

0.0.x line (for T5.1.x) will not be maintained.

We strongly recommend moving to T5.3.x or beyond
</div>

Tapestry-model is the main Tynamo module and the development of it was started in 2004. Tapestry-model allows rapid [CRUD](http://wikipedia.org/wiki/CRUD_%28acronym%29) development of Tapestry 5 applications by automatically creating list, display and edit pages for [POJOs](http://en.wikipedia.org/wiki/Plain_Old_Java_Object). Tynamo uses introspection to gather information about bean properties and creates representations and editors depending on their type, for example. There's no "scaffolding" generated, the defaults are all built dynamically. The default model is highly customizable. You can change pretty much anything you need and make the changes specific to type, page or instance - a feature that very few other CRUD frameworks offer. Though your whole user interface can be based on tapestry-model, the sweet spot of it is customizable admin interfaces. You can get a fully functional CRUD interface up and running quickly, but you don't have to throw away the effort, rather you just extend it as your requirements become more clear.

To use the module, you need to add the following dependency to your pom.xml:

	<dependency>
	  <groupId>org.tynamo</groupId>
	  <artifactId>tapestry-model-jpa</artifactId>
	  <!-- or, for the native hibernative version
	  <artifactId>tapestry-model-hibernate</artifactId>
	  -->
	  <version>0.5.1</version>
	</dependency>

However, if you are new to Tynamo and Tapestry, it maybe easier to follow our [Quick start](http://www.tynamo.org/Quickstart) to get set up quickly.

<div markdown="1" class="alert alert-warning">
This guide is a Wiki and only as good as you make it. Please contribute by adding more helpful explanations, correcting the wording and providing more examples. The guide will never be truly complete but even as is, it covers most aspects of Tynamo's tapestry-model module and is essential reading for anybody interested in model-driven CRUD applications. 
</div>

## Outline
{:.no_toc}
* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


## Creating your domain model

At this point, you should have gone through at least the [Quick start](http://www.tynamo.org/Quickstart) and preferably [Overview](http://www.tynamo.org/Overview) as well, and have your environment set-up and be initially familiar with Tynamo, Tapestry and Maven. Start by figuring out what your business objects are, what the relationships between them are and then implement simple entities for each with minimal annotations. Keep your application in a running state as much as possible and work in small increments. Once you've verified your entities are recognized and you can add, update and delete them in your web application, start implementing the relationships between them using JPA (or Hibernate) annotations.

### Implementing Tynamo "POJOs"

Your domain objects are just POJOs, Plain Old Java Objects, they really are - only with a few minor exceptions :) First of all, each needs to be marked with *@Entity* so JPA knows this class represents a persistable domain object, and that it should do something about it. Tapestry-model inspects the classes for @Entity annotation that are in a special package named *<application-root-package>.entities* (by default and as explained in the documentation of [tapestry-hibernate-core](http://tapestry.apache.org/tapestry5.1/tapestry-hibernate-core/conf.html)).

In addition to @Entity, each object needs to have a guaranteed unique identifier. Typically, your entity contains:

	@Id @GeneratedValue(strategy = GenerationType.AUTO)
	public Integer getId() {return id;}

@Entity and @Id are the only mandatory additions to a POJO, but it's a good practice to always implement (override) toString(), equals() and hashCode() operations. Tynamo uses toString for example in lists; equals and hashCode are needed to reliably compare object "sameness" based on the object values rather than checking if two object references are pointing to the same instance.

### Annotating entities for Tynamo

Tynamo works its magic on assumptions it makes on your code. Sometimes it's right, sometimes not. In cases, the expected behavior is something else you wanted, you can provide more "hints" with annotations for Tynamo to know what to do.

<div markdown="1" class="alert alert-info">
*More documentation needed here...*

In the meantime, study the [archetype](https://github.com/tynamo/tynamo-archetype) and [examples](https://github.com/tynamo/tynamo-examples/) code.
</div>

## Developing a Tynamo app using an IDE

### Class reloading and hot-code swapping

As explained in Tapestry's [documentation on class reloading](http://tapestry.apache.org/class-reloading.html), Tapestry automatically reloads your changes in templates and classes without having to restart the context/container/JVM. Sun's JVM has also supported hot code swapping for years, though that only works only for changing code within the method body, not for method signatures, but that nicely compliments Tapestry's class reloading as typically your services have better defined interfaces. Generally, if you find yourself restarting the container often, you are doing something wrong. You also want to configure the container's JVM with -Dtapestry.production-mode=false setting to give you nice and exact error reporting (see [Tapestry's configuration guide](http://tapestry.apache.org/configuration.html))

### Using Eclipse

You want to use Eclipse IDE for Java EE Developers, the latest (at the time of writing this) [Eclipse Mars (4.5)](https://projects.eclipse.org/releases/mars) works well and is way faster than previous versions. The Java Developers version comes with Maven support pre-installed (via the [m2e plugin, Maven integration for Eclipse, currently at version 1.6.0](https://projects.eclipse.org/projects/technology.m2e/releases/1.6.0)).

For integrated, in-place development, you have several options: jetty (with Eclipse Run Jetty Run plugin from Eclipse marketplace), Tomcat (via Eclipse Tomcat plugin, also see [Developing with Tomcat and Eclipse](http://www.tynamo.org/Developing+with+Tomcat+and+Eclipse/)), or using JEE's native servlet container support. JEE's native container support tends to be somewhat cumbersome as the concept is generalized to work for multiple servlet containers and doesn't offer as high level of integration for a particular container. One of the issues is that JEE's support works so it scans source folders and copies them to target directories, for which there isn't a real need for. The Eclipse Tomcat plugin offers the highest level of Tomcat integration (reload web app context from a context-specific menu, separate buttons for starting and stopping), but has its own issues for loading the dependent libraries (need to manually install devloader, need to semi-manually keep the libraries in sync when you add them, pukes out if multiple servlet-apis are found on load path).

Jetty is another servlet container and has a no-frills Eclipse plugin that is easy to use. Some claim Jetty is a little faster to start-up than Tomcat which is an advantage in development, but equally configured, it's difficult to notice any real difference in performance.

### Using IntelliJ IDEA

Since version 7.0 IntelliJ IDEA lets you create projects directly from Maven descriptor files, enabling you to quickly switch to your selected development environment.

Starting with version 9, IntelliJ IDEA is offered in two editions: Community Edition, free and open-source, and Ultimate Edition, encompassing 100% of the famous IntelliJ IDEA functionality. <http://www.jetbrains.com/idea/download/>

The Community Edition includes fully integrated Maven support.

Importing Maven projects to IntelliJ IDEA with automatic artifacts downloading is pretty easy:
- Start a new project selecting "Import project from external model"
- Select Maven.
- Next, select the root directory for your new Tynamo project.
Idea also lets you execute and manage Maven goals from within the IDE.

If the Community Edition is not enough for you the Ultimate Edition has beautiful support for both Hibernate & Tapestry.

## Working with databases

tapestry-model-hibernate (a submodule of tapestry-model) extensively uses Hibernate as it's persistence layer. While you can provide a different "persistenceService" implementation, the only one provided by Tynamo is a HibernatePersistenceService, which as the name implies, implements a Hibernate based persistence mechanism. Hibernate is an ORM tool that maps your Java objects to a database schema.

By default, Tynamo uses in-memory H2 database, which means it's super-simple to set up and the database only exists in-memory. H2 is unbelievably small and fast, evolves quickly and is nice to develop with, and even nicer for running your db integration tests against. Because the start-up cost is minimal, you always start from a clean slate and you know it works no matter what the environment is.

With Hibernate it's very well possible and easy to use more than one type of database since the switching cost is minimal - you just provide a different database configuration and off you go.
	
<div markdown="1" class="alert alert-info">
However, one potential issue that you want to be careful with using reserved words in the database, because Hibernate maps your entities directly to a database schema.
</div>

A simple example of what doesn't work is a property named "date", i.e. having getDate() operation in one of your entities. These are typically easy to spot from the error logs when you start up your application after adding a new entity. The theory is that by supporting (and testing with) more databases you make your implementation stronger.

Typical scenarios for different database use cases are:

- Development, where it's nice to have a persistent database so you don't need to seed the data all the time and one that is as fast as possible on a small dataset
- Integration testing(often automatically launched from you build), where you typically set the database state for a testing and roll back the changes after each test
- Production(and production testing), where you obviously need a persistent storage, the set-up costs are often less relevant, it needs to work fast on a larger dataset; and you may require some other features from the database software, like backups, recoverability and clustering

### Choosing the right database

This is not a generic database guide, but a few words about the choices. For development, your best bets are H2, HSQLDB, Derby or MySQL depending on your preferences and what you are used to. Most databases come with some type of command-line interface but H2 has a great, built-in web interface for looking at the raw database results and executing raw SQL queries. If you appreciate automating even the database setup for development, H2 and HSQLDB are easy to embed, and are especially useful when doing integration system with fully automated build systems.

Your production database of choice depends largely on your application type and your deployment environment. For a small-scale prototyping, free or open-source projects, HSLQDB, DERBY, MySQL and PostgreSQL are good choices. If the database choice isn't up to you, you should check that Hibernate first of all supports it and secondly, keep testing frequently that your domain model works with the particular database even if you don't use it during development. H2 is proven to work with millions of records without hick-ups (see [H2's own performance comparisons](http://www.h2database.com/html/performance.html)).

### Configuring Hibernate

Your typical hibernate.properties for development should look something like this:

hibernate.dialect=org.hibernate.dialect.H2Dialect
hibernate.show_sql=true
hibernate.hbm2ddl.auto=update
hibernate.connection.driver_class=org.h2.Driver
hibernate.connection.url=jdbc:h2:mem:test
hibernate.connection.username=sa
hibernate.connection.password=

For other database configurations (and required dependencies, check out our [Sample database configurations](http://www.tynamo.org/Sample+database+configurations/). Using *hibernate.hbm2ddl.auto=create-drop* causes the database to be created at the start-up and then deleted after use which might be advantageous in some cases, like for integration testing. Hibernate also allows you specify *hibernate.hbm2ddl.auto=validate*, which may make sense in a production environment, but notice that instantiating the Hibernate related services would fail if you use *validate* and Hibernate requires changes to be made in the database schema.

## Customizing pages

### Introduction to customizing pages

Remember that tapestry-model provides built-in support for CRUD (Create, Read, Update, Delete) operations, and techniques for customizing each differs. For customizing how an object gets displayed and what parts of it are editable and how, you commonly use annotations, and for customizing the UI, like layout etc. you'd commonly create a custom template for a particular domain model type and modify that template. For the latter type of customization, once you get the hang of customizing a tapestry-model page, you'll realize that you are simply using Tapestry rather than any Tynamo magic.

In other words:

*Tynamo makes a lot of assumptions from your domain model in order to produce a working application. Of course, assumptions are wonderful when they are right - but sometimes they're wrong. Fortunately, Tynamo provides a great deal of flexibility in overriding the assumptions that it makes.*

### Annotating properties

For edit and display pages, Tynamo uses Tapestry's BeanEditForm and BeanDisplay components with custom property editors. The typical Tapestry and Hibernate annotations work as expected, as documented at [Tapestry's BeanEditForm guide](http://tapestry.apache.org/beaneditform-guide.html). The typical property annotations to use are @NonVisual, @Validate and @ReorderProperties (the last one's a class annotation). For now, refer to Tapestry's documentation regarding the annotations, we'll expand this section as needed.

### Customized tapestry-model templates

If you don't customize anything, Tynamo uses default pages. In Tapestry, a component (a page is a component as well) is comprised of two parts: its template and a backing java class. There's a default page for each particular operation, such as: Add, Edit, List and Show. The archetype will put the Java page classes to **src/main/java/<groupid>/pages** and the corresponding templates to **/src/main/webapp**. The cool thing about this is that you can also customize the default look and feel of a page for an operation type. Most of the cases, however, you want to customize a page specific for a particular object.

Tynamo makes decisions about what page to display based on which kind of page is needed and the class of the object(s) involved. It will first look for a page using Add, Edit, Show or List depending on the kind of page needed, prefixed with the unqualified-type name of the object. If it can't find a specific page for a given type, it will instead use the default Add, Edit, Show or List page, respectively. The following table gives some examples of how Tynamo figures out which page to use:

| Operation | Class | Look for page: | If not found, use page: |
|----+----|
| Edit | org.tynamo.recipe.entities.Recipe | org.tynamo.recipe.pages.edit.RecipeEdit | org.tynamo.recipe.pages.Edit |
| List | com.foo.entities.Product | com.foo.pages.list.ProductList | com.foo.pages.List |
| Add | org.wwf.entities.Gazelle | org.wwf.pages.add.GazzelleAdd | org.wwf.pages.Add |

To start customizing a page, make a copy of the appropriate default page files and rename according the the table above. Most of the UI customization happens in the corresponding .tml template. Keep in mind that a Tapestry template is more or less just a html page with selected additional tags starting with <t:some-tapestry-element> that will be replaced by Tapestry components (which can be comprised of other templates and so forth). Read [Tapestry's template guide](http://tapestry.apache.org/component-templates.html) for more comprehensive documentation.

The typical customization needed for edit forms is that you want to render something else for a specific field but you are fine with other defaults. [Property Editor Overrides](http://tapestry.apache.org/beaneditform-guide.html) are exactly for that purpose, see the linked documentation for exact examples. If we wanted to, we could replace the Form component entirely and create a new form from scratch using standard Tapestry components.

### Customizing the page class

You can also customize the pages java side. Just extend from ListPage or from EditPage according to your needs and rename it accordingly.
	
<div markdown="1" class="alert alert-warning">
example needed here
</div>

### Validation

Tapestry-model takes the approach that validation should be specified in your domain object, and makes it easy to do so. In keeping with the theme of not reinventing wheels, tapestry-model leverages the excellent Hibernate validation annotations. Tapestry-model integrates them into the error reporting system provided by Tapestry, with the net result being that adding validation to your domain object takes about as long as you've just spent reading this paragraph.

Let's see it in action by making the title property required.

	@NotNull
	public String getTitle()
	{
	  return title;
	}

That's all there is to it. The @NotNull annotation tells Tynamo (and Hibernate) to make this a required field. When we try to create a recipe with no title, we get a useful error message.

And if we don't like the default message, it's easy to change that, too. All of the Hibernate validation annotations allow a message attribute, so we can change our @NotNull annotation like so:

	@NotNull(message="is required")

There are quite a few [validation annotations](http://www.hibernate.org/hib_docs/validator/reference/en/html/validator-defineconstraints.html) provided by Hibernate. Writing your own is outside of the scope of this article, but not at all difficult to do.

## Testing Tynamo application

Generally speaking, you can test your code at various different levels of granularity. The higher level the test is, the more ground the test typically covers (i.e. executes more lines of code), but the reasons for test failures often become more obscure. Lower-level unit tests are good for pinpointing exact problems but you need a lot of them to build comprehensive coverage over your whole code base. You can often divide your tests into a few different categories, such as unit tests, integration tests and functional tests. Maven has built-in support for executing both unit and integration tests and the Tynamo archetype takes advantage of it. In the archetype, the container-based test is thought to be an integration test and everything else a unit test. Tynamo uses TestNG because Tapestry uses TestNG but you can just as easily use the better known Java unit testing framework, JUnit. TestNG has a few handy things especially for integration testing, but JUnit 4 is roughly on par with TestNG feature-wise.

### Unit testing your application

If you need to test helper operations of a page (class), you can simply instantiate the page and inject mocked-up dependencies as needed (use either Mockito, JMock or EasyMock), but Tapestry also provides a built-in PageTester class for unit testing your pages (see Tapestry's [Unit testing pages or components guide](http://tapestry.apache.org/unit-testing-pages-or-components.html)). With PageTester, you can easily render the page and "click" on its links.

Services can be instantiated manually with mocked-up dependencies just as easily, but often times your services are dealing with persistence layer and you want to specifically test that your Hibernate criterias are working as expected. In that case, there's no substitute for testing against a real database. Fortunately, this is very simple with modern in-memory, embedded Java databases such as H2. Here's one example of a base class that you could use for testing your persistence services:

	public abstract class PersistenceTest {
		protected Session session;
		protected Transaction transaction;
		protected static SessionFactory sessionFactory;
	
		@BeforeClass
		public static void commonSetUp() {
			AnnotationConfiguration configuration = new AnnotationConfiguration();
			configuration.addAnnotatedClass(Game.class);
			// Add more entities as needed
			sessionFactory = configuration.buildSessionFactory();
		}
	
		@Before
		public void beforeTest() {
			session = sessionFactory.openSession();
			transaction = session.beginTransaction();
		}
	
		@After
		public void afterTest() {
			if (transaction != null) if (transaction.isActive()) transaction.rollback();
		}
	}

As you see, the base class will automatically try to roll back the active transaction at the end of the test. You can certainly open multiple transactions and commit them just as long as you properly clean up the modifications after the test execution so your changes won't collide with other tests.

### Integration testing

tapestry-model-test contains a base class called [AbstractContainerTest](http://www.tynamo.org/tynamo-test/apidocs/org/tynamo/test/AbstractContainerTest.html) that is meant for container-based integration testing. The AbstractContainerTest starts up an embedded Jetty instance, runs your web application in place (i.e. picks up the templates from src/main/webapp) and uses the compiled classes in target/classes rather than building a war file) and initializes a HtmlUnit client for testing your page flow as if it was a real user. As a sample, Tynamo-archetype provides a working integration test that uses AbstractContainerTest.

Tapestry also provides support for Selenium-based tests with [tapestry-test module](http://tapestry.apache.org/integration-testing.html). Selenium uses real browser engines (Firefox, IE) so in theory it can better assert that your web site works for browsers your users use and Selenium tests can be recorded via browser plugin-ins. However, Selenium test automation is more complex to set up, they break more easily, they are harder to fix and they run longer than HtmlUnit/AbstractContainerTest-based tests since the latter is an all-java solution that runs everything in-process. If you are using standard building blocks rather than doing much of custom Javascript, typically HtmlUnit's embedded browser is good enough for executing the whole page logic, including Javascript. In other words, if you break your page flow, you typically break it for all browsers (that at least try to follow some modern html standards), not just one, simulated or not. But this depends on your situation - it's completely up to you what to use for your integration testing and you could certainly use a combination of both HtmlUnit and Selenium-based tests as well. Just remember that you want to go for "the biggest bang for buck" - in practice it's impossible to test everything, so you want to have the simplest, fastest way of testing as many of the most likely scenarios as possible.



