---
layout: page
title: tapestry-security guide
---

## Overview

Enforcing security by implementing security checks in your application code is labor-intensive and a potentially dangerous practice as you can very easily miss a check and leave a big security hole open. Standard container-managed authentication and authorization was created to address this issue, but it's based purely on roles and URLs, and as such, is typically too constricting for modern web applications. With Tapestry 5, it's relatively easy to get started with securing your application by creating custom filters and dispatchers, but really, every user of Tapestry shouldn't need to create their own security framework. Developing comprehensive, proven bullet-proof security framework is difficult and time consuming. Tynamo's tapestry-security is a comprehensive security module that provides tight integration with Apache Shiro, an established, high-performing and easy-to-use security framework for Tapestry applications.

## Using security

There are several aspects to providing security. Technically you can either hide functionality from user's view, visibly disable or lock certain functionality or display errors when user tries accessing forbidden resources or operations. Security can be enforced at different integration points: for example, you may restrict users' access to certain URLs, secure access to the data itself or make specific checks in an operation of the controlling page class or service before executing certain functionality. Tapestry-security module supports all these different types of authorization mechanisms.

To use the feature, you need to add the following dependency to your pom.xml:

    <dependency>
      <groupId>org.tynamo</groupId>
      <artifactId>tapestry-security</artifactId>
      <version>0.6.2</version>
    </dependency>

Apache Shiro, the security framework that tapestry-security is based on, is modular and extensible, but to get started, you need to understand just three key Shiro concepts: **realms, filters** and **security configuration**. A realm is responsible for authenticating and authorizing users, so you at least need to configure a ready-made realm, or, if you are authenticating users against your own custom database, likely need to implement your own custom realm. Typically, in your AppModule you provide a realm configuration such as:

    @Contribute(WebSecurityManager.class)
    public static void addRealms(Configuration<Realm> configuration) {
    	ExtendedPropertiesRealm realm = new ExtendedPropertiesRealm("classpath:shiro-users.properties");
    	configuration.add(realm);
    }

Obviously, if your Realm needs to use other Tapestry services, you could let Tapestry build your Realm implementation with @Autobuild and inject it as an argument to your WebSecurityManager contribution method. If you create a first-class Tapestry IoC service out of your realm (you can but there should be very little need), make sure you identify your realm to the Tapestry with the right super interface. For example, if your realm is authorizing users, the interface to use is AuthorizingRealm  - if you claimed that the service implements Realm interface only, your realm wouldn't be allowed to participate in authorization, but only in authentication process. See an example of a simple, custom JPA-based entity realm (service). Shiro provides an extensive set of interfaces, often providing different functionalities depending on which features are available.

Storing password
Icon

Apache Shiro is persistence agnostic so you need to decide yourself how to store the passwords and compare their equality. Shiro comes with several built-in matchers to make this simple. The User entity used by the realm sample referred to above also shows an example of storing an SHA-1 hash with per user salt.

Shiro is based on multiple filter chains which is a natural fit with tapestry's (filter) pipeline pattern. In the typical case, you don't have to implement new filters but merely configure them to process desired urls of your application. Refer to the Shiro configuration for more information, but tapestry-security makes the default Shiro filters available so you can refer to them by name.

Shiro supports url-based permission checking out of the box. Tapestry-security also comes with several security annotations and some security components that you can use in your page classes and templates to secure specific operations or access to the page.

## Configuration

Shiro's default configuration model uses an INI file (a property file with sections). However, Tapestry-security 0.4.0 introduced a Tapestry-style configuration through contributions and completely removed support for ini-style configuration. Tapestry-style all-in-Java configuration has many benefits, including type checking, run-time filter re-configuration (should you ever need it...) and better performance (indirectly - 0.4.0 supplies its own "Shiro" filters - this feature enhancement may be pushed upstream in Shiro 2.0 - see configuration per filter instance in Version+2+Brainstorming if interested).

For 0.3.x and earlier versions, you can use a standard Shiro INI configuration file (see Shiro's documentation for more info) with tapestry-security. All versions support annotation-based security. The simplest and most typical security models are based on url and role permissions, see the following version-specific sections for examples on them.

### Configuration through contributions

**Contributing security configuration**

	// Starting from 0.4.6, you can also use a marker annotation:
	// @Contribute(HttpServletRequestFilter.class) @Security public static void securePaths(...)
	public static void contributeSecurityConfiguration(Configuration<SecurityFilterChain> configuration,
				SecurityFilterChainFactory factory) {
			// /authc/** rule covers /authc , /authc?q=name /authc#anchor urls as well
			configuration.add(factory.createChain("/authc/signup").add(factory.anon()).build());
			configuration.add(factory.createChain("/authc/**").add(factory.authc()).build());
			configuration.add(factory.createChain("/contributed/**").add(factory.authc()).build());
			configuration.add(factory.createChain("/user/signup").add(factory.anon()).build());
			configuration.add(factory.createChain("/user/**").add(factory.user()).build());
			configuration.add(factory.createChain("/roles/user/**").add(factory.roles(), "user").build());
			configuration.add(factory.createChain("/roles/manager/**").add(factory.roles(), "manager").build());
			configuration.add(factory.createChain("/perms/view/**").add(factory.perms(), "news:view").build());
			configuration.add(factory.createChain("/perms/edit/**").add(factory.perms(), "news:edit").build());
		}

Note that above, a call to factory.<filtername>() gives you a new instance of the filter each time. In fact, you get a runtime error if you try to contribute the same filter instance to a different chain with a different configuration.

(Example taken from the AppModule of a security test application)

0.4.1 also introduced a dependency to tapestry-exceptionpage (a version of it is built-in to T5.4). The security exceptions thrown from the filters annotation handlers are all handled by the same exception handler assistant. You can also handle other generic exceptions in your application the same way, read more about it at tapestry-exceptionpage guide (or DefaultRequestExceptionHandler documentation for T5.4). anon() and authc() etc. above refer to the Shiro filter names. See all Shiro filters available by default.

### Routing error codes in your web.xml

In your web.xml, you most likely want to route 401 (Unauthorized) to your own error page instead of the container provided one, for example:

	...
	    <error-page>
	        <error-code>401</error-code>
	        <location>/error401</location>
	    </error-page>
	    <error-page>
	        <error-code>404</error-code>
	        <location>/error404</location>
	    </error-page>
	</web-app>

Also, note that by the servlet spec, application filters don't handle error requests by default (Jetty, at least the old versions, didn't conform). So, to get the Tapestry filter to handle the ERROR request, you also need to configure:

	<filter-mapping>
	    <filter-name>vuact</filter-name>
	    <url-pattern>/*</url-pattern>
	    <dispatcher>REQUEST</dispatcher>
	    <dispatcher>ERROR</dispatcher>
	</filter-mapping>

## Security annotations

To declaratively secure your pages, you can use the following annotations:

/* Shiro annotations, for securing operations */
	@RequiresPermissions
	@RequiresRoles
	@RequiresUser
	@RequiresGuest
	@RequiresAuthentication

For example, to restrict access to users with roles "admin" only, you would add a following annotation to a page class:

	@RequiresRoles("admin")
	public class AdminPage {
	}

You can also secure access to services and service operations, for example:

	@RequiresAuthentication
	public interface AlphaService {
	        @RequiresRoles("admin")
	        void secureMethod();
	
	        void secureMethod2();
	}

Permissions are another interesting aspect of Shiro. Shiro's default WildCardPermission supports a syntax that allows representing different parts and subparts in a permission string. For example:

	public class Index {
	  @RequiresPermissions("news:delete")
	  public void onActionFromDeleteNews(EventContext eventContext) {
	     ...
	  }
	}

In your realm, you allocate individual permission strings to each user that are then matched against the permission strings from the annotations. For more information, read Shiro's documentation on permissions. While the permission concept is flexible, it still doesn't allow you to declaratively secure instances of data. You can programmatically check for instance level permissions but it's cumbersome to allocate the correct permissions and equally cumbersome to later verify them. Entity-Relationship Based Access Control (ERBAC) system allows declaring subject-instance security rules. For example, all users can read each other's profile data, but only modify their own. If you are using JPA, you are in luck since our implementation of ERBAC security concept, tapestry-security-jpa module allows securing your entities with simple annotations @RequiresAssociation and @RequiresRole, check out tapestry-security-jpa!
Security components

There are often cases where it's not enough to simply secure the urls or the pages. If a user doesn't have a permission to invoke a particular action, it's a good practice to also hide it from his view. Tapestry-security module contains several built-in conditional components to make conditional rendering of your page templates and more fine-grained permission control easier.

The names of following components should give you a pretty good hint of their purpose, can you guess what all of them do? 0.5.1 also added support for an p:else block for all the built-in security components.

	Authenticated
	NotAuthenticated
	User
	Guest
	HasAnyRoles
	HasPermission
	HasRole
	LacksPermission
	LacksRole
	LoginForm
	LoginLink

Some simple examples below:

	<t:security.user>
	  Hello, ${username}
	   // else only from 0.5.1 and up
	   <p:else>
	    Hello guest
	   </p:else>
	</t:security.user>
	
	<t:security.guest>
	  <t:actionlink t:id="createAccount">Create account</t:actionlink>
	</t:security.guest>
	
	<t:security.hasRole role="admin">
	  <t:actionlink t:id="delete">delete user</t:actionlink>
	</t:security.hasRole>

### Overriding login, success and unauthorized URLs

**tapestry-security** comes with two very simple login and unauthorized pages, and also by default the success url will be /index

Most likely, you will want to change these URLs to point to your own customized pages. To do that use SecuritySymbols

	public static void contributeApplicationDefaults(MappedConfiguration<String, String> configuration)
	{
		// Tynamo's tapestry-security module configuration
		configuration.add(SecuritySymbols.LOGIN_URL, "/signin");
		configuration.add(SecuritySymbols.UNAUTHORIZED_URL, "/blocked");
		configuration.add(SecuritySymbols.SUCCESS_URL, "/logged");
	}

## More examples

For more extensive examples, take a look at our full-featured integration test web app. See for example the Index page template and class and the AlphaService. Also, check out tynamo-federatedaccounts, an add-on module to tapestry-security for remote & merged authentication use cases, such as Oauth. We have a live example available for federatedaccounts but the example application also demonstrates other useful capabilities of tapestry-security, such as realm as a service, contributing multiple realms, interoperability between them, creating and using a CurrentUser application state object and using permissions. In the example, one realm is responsible for only authenticating users while another one is responsible for authorizing them. Clone the tynamo-example-federatedaccounts repo or browse the sources online, starting from AppModule

### Case study: Securing Javamelody - An example of integrating a non-Tapestry library using a standard ServletFilter

Javamelody is one of the best open-source server health monitoring packages available for Java webapps at the moment. Javamelody is implemented as a ServletFilter. Each request needs to pass through the filter to be accounted for. The same filter is also responsible for producing the reports by handling requests to "/monitoring" url directly. This imposes a problem with securing the "/monitoring" url. If you declare the Javamelody filter before Tapestry filter you couldn't secure the monitoring page (with a Tapestry-based security solution), and if you declared it after the Tapestry filter, the monitoring filter would never be invoked for requests handled by Tapestry. The solution is to inject the Monitoring filter into your security filter chain configuration, like so:

	@Contribute(HttpServletRequestFilter.class)
	@Security
	public static void securePaths(Configuration<SecurityFilterChain> configuration, SecurityFilterChainFactory factory,
	    final ApplicationGlobals applicationgGlobals) throws ServletException {
	    FilterConfig filterConfig = new FilterConfig() {
	        @Override
	        public String getFilterName() {return "monitoring";}
	        @Override
	        public ServletContext getServletContext() {return applicationgGlobals.getServletContext();}
	        @Override
	        public String getInitParameter(String name) {return null;}
	        @Override
	        public Enumeration getInitParameterNames() {return null;}
	    };
	    MonitoringFilter monitoring = new MonitoringFilter();
	    monitoring.init(filterConfig);
	    configuration.add(factory.createChain("/monitoring/**").add(factory.authc()).add(factory.roles(), "admin")
	        .add(monitoring).build());
	    configuration.add(factory.createChain("/**").add(monitoring).build());
	}

Notice that above, we are using the same instance of the monitoring filter in both chains, which is perfectly fine since standard servlet filters do not carry any chain specific configuration (unlike typical Shiro filters). With this configuration, we can now both analyze every request and secure the monitoring reporting page. Any other standard ServletFilters can also easily be configured as part of the security configuration, completely avoiding web.xml configuration. Also, unrelated to security, but Javamelody comes with its own gzipping, so remember to use 1.39.0 version or better and turn off its gzipping (using context parameter gzip-compression-disabled), otherwise it'll conflict with Tapestry's gzipping facilities.
