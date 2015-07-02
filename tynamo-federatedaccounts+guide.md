---
layout: guide
title: tynamo-federatedaccounts
repository_url: https://github.com/tynamo/tynamo-federatedaccounts
---
<div markdown="1" class="alert alert-info">
**Version status: 0.5.0 beta for T5.4, 0.4.3 beta for T5.3.x**

Check out the federatedaccounts demo

0.4.x introduced a generic Oauth module integration with pac4j

0.3.0 refactored core module for more additional library support

0.2.0 introduced the rolling tokens module

0.1.0 introduced a new, modularized architecture with facebook and twitter modules

Versions before 0.4.x are not maintained anymore
</div>

## Usage

Tynamo-federatedaccounts is an add-on to [tapestry-security](http://www.tynamo.org/tapestry-security+guide/) module and provides an API and components for federated authentication use cases, i.e. authenticating (your application) users with a third-party, such as Facebook, Twitter or Google. The most well-known protocol for it is probably [Oauth](http://oauth.net/). However, the module is purposefully not named as "tynamo-oauth" or similar, since the provided interfaces are designed for any federated accounts (where you need to "bridge" more than one account together), regardless of protocol. Besides Oauth, OpenID, LDAP and custom remote authentication protocols are obvious use cases. The module provides an authenticating realm for each specific third-party and required components and pages to authenticate via a particular federated authentication scheme. The module is designed to be as light-weight and non-invasive as possible with minimal amount of configuration required. For example, for enabling simple authentication with Facebook and Twitter in your (Hibernate-based web) application, you only need to provide the following configuration:

	// dependencies in your pom.xml - use what you need, don't need all of these
	<dependency>
	    <groupId>org.tynamo.security</groupId>
	    <artifactId>tynamo-federatedaccounts-facebook</artifactId>
	    <version>0.5.0</version>
	</dependency>
	<dependency>
	    <groupId>org.tynamo.security</groupId>
	    <artifactId>tynamo-federatedaccounts-twitter</artifactId>
	    <version>0.5.0</version>
	</dependency>
	<dependency>
	    <groupId>org.tynamo.security</groupId>
	    <artifactId>tynamo-federatedaccounts-pac4jbasedoauth</artifactId>
	    <version>0.5.0</version>
	</dependency>
	// Rolling tokens is a (semi-)secure remember me authentication based on one-time authentication tokens
	<dependency>
	    <groupId>org.tynamo.security</groupId>
	    <artifactId>tynamo-federatedaccounts-rollingtokens</artifactId>
	    <version>0.5.0</version>
	</dependency>		

	// in your AppModule.java
	public static void bind(ServiceBinder binder) {
		binder.bind(FederatedAccountService.class, DefaultHibernateFederatedAccountServiceImpl.class);
		// Or for JPA implementation
		// binder.bind(FederatedAccountService.class, DefaultJpaFederatedAccountServiceImpl.class);
	}

	public static void contributeFederatedAccountService(MappedConfiguration<String, Object> configuration) {
		// you can either map each realm to the same entity...
		configuration.add("*", User.class);
		// or, you can use different entities
        // configuration.add(Constants.TWITTER_REALM, TwitterAccount.class);
        // configuration.add(FederatedAccountType.pac4j_.name() + SupportedClient.google2.name(), GoogleAccount.class);

		// Now, you also have to map the desired id (the subject principal) to an attribute of the entity
		configuration.add("facebook.id", "facebookId");
		configuration.add("twitter.id", "twitterId");
	}

  public static void contributeApplicationDefaults(MappedConfiguration<String, String> configuration) {
		// these are the defaults, change as needed
		// configuration.add(FederatedAccountSymbols.COMMITAFTER_OAUTH, "true");
		// configuration.add(FederatedAccountSymbols.HTTPCLIENT_ON_GAE, "false");
		// configuration.add(FederatedAccountSymbols.SUCCESSURL, ""); // empty string implies host name only
	
		// set your oauth app credentials
		configuration.add(FacebookRealm.FACEBOOK_CLIENTID, "<your oauth client id>");
		configuration.add(FacebookRealm.FACEBOOK_CLIENTSECRET, "<your oauth client secret>");
		configuration.add(TwitterRealm.TWITTER_CLIENTID, "<your oauth client id>");
		configuration.add(TwitterRealm.TWITTER_CLIENTSECRET, "<your oauth client secret>");
		// Use the constants in Pac4jFederatedRealm for pac4j app credentials 
    // configuration.add(Pac4jFederatedRealm.GOOGLE_CLIENTID, "<your_google_api_app_key_here>");
  }

It is not strictly mandatory but you should also explicitly configure your application's hostname instead of relying on the hostname taken from request. This is required anyway if you are creating absolute urls without request context (for example in a background process) or when you are running loadbalancers in front of your application server. Oauth provider's callback URL handler may require that hostname matches with the provider's configuration (that's exactly the case with Facebook). Pre-T5.3 you have to override BaseUrlSource, in T5.3 you can simply set the following symbols:

	public static void contributeApplicationDefaults(MappedConfiguration<String, Object> configuration) {
		configuration.add(SymbolConstants.HOSTNAME, "tynamo-federatedaccounts.tynamo.org");
		configuration.add(SymbolConstants.HOSTPORT, "80");
	}

The User class above is your own persistent type, or in the case of Hibernate/JPA, an @Entity. All types you are contributing to FederatedAccountService need to implement the interface **org.tynamo.security.federatedaccounts.FederatedAccount**. *FederatedAccount* interface is shown below:

	public interface FederatedAccount {
		public enum FederatedAccountType {
			facebook, twitter
		};
	
		public boolean isAccountLocked();
	
		public void setAccountLocked(boolean value);
	
		public boolean isCredentialsExpired();
	
		public void setCredentialsExpired(boolean value);
	
		public boolean federate(String realmName, Object remotePrincipal, Object remoteAccount);
	}

Depending on the FederatedAccountService used, you don't necessarily need to provide any meaningful implementation for federate(...) operation, but it's provided in case you want to merge/update some account properties. See the [example implementation](https://github.com/tynamo/tynamo-federatedaccounts/blob/master/tynamo-federatedaccounts-test/src/test/java/org/tynamo/security/federatedaccounts/testapp/entities/User.java) for ideas (the DefaultJpaFederatedAccountServiceImpl requires you to implement storing the identifying remote property).

FederatedAccountService is a lightweight interface, providing a bridge between your local user accounts and remote accounts. The only operation in FederatedAccountService is:

	AuthenticationInfo federate(String realmName, Object remotePrincipal, AuthenticationToken authenticationToken, Object remoteAccount);

The operation is designed to be invoked after a remote authentication has succeeded. "remotePrincipal" parameter is the username/userid in the remote system and the last parameter is an optional object describing the remote account. The current Facebook realm is using [RestFB](http://restfb.com/) and returns [RestFB User](http://restfb.com/javadoc/com/restfb/types/User.html) object as the remoteAccount. The Twitter realm uses [Twitter4j](http://twitter4j.org/en/index.html) and returns a [Twitter4j User](http://twitter4j.org/en/javadoc/twitter4j/User.html) as the remoteAccount. [DefaultJpaFederatedAccountServiceImpl](https://github.com/tynamo/tynamo-federatedaccounts/blob/master/tynamo-federatedaccounts-core/src/main/java/org/tynamo/security/federatedaccounts/services/DefaultJpaFederatedAccountServiceImpl.java) tries to obtain the configured entity for this realm (see the configuration above) and saves or updates the entity after calling its federate(...) operation.

FederatedAccounts module requires that FederatedAccountService interface is bound to an existing service, but doesn't bind to any by default. This is so you can easily provide a custom implementation for FederatedAccountService, using your own persistence model. Two implementations are provided by default for FederatedAccountService: [DefaultHibernateFederatedAccountServiceImpl](https://github.com/tynamo/tynamo-federatedaccounts/blob/master/tynamo-federatedaccounts-core/src/main/java/org/tynamo/security/federatedaccounts/services/DefaultHibernateFederatedAccountServiceImpl.java) and [DefaultJpaFederatedAccountServiceImpl](https://github.com/tynamo/tynamo-federatedaccounts/blob/master/tynamo-federatedaccounts-core/src/main/java/org/tynamo/security/federatedaccounts/services/DefaultJpaFederatedAccountServiceImpl.java).

<div markdown="1" class="alert alert-warning">
Find it confusing to use this module?

Improvements to the documentation are more than welcome. However, we've purposefully tried to keep the documentation light since this family of library was purposefully designed to cover a wide range of use cases and documenting all the different options and possibilities would make the documentation too heavy and complex. The underlying libraries are well battle tested and all of the federatedaccount libraries are in use in production applications today. If you are new to the world of Oauth and federated account systems in general, it's best to dive in by integrating with a single provider first, compare your implementation with existing tests and samples and build up from there.
</div>

## Extension points

What should you customize and why?

- If you are not using jpa/hibernate as your persistence framework...
	-> provide a custom FederatedAccountService implementation
- If you are handling your own transactions...
	-> set autoCommit to false i.e. contribute application symbol configuration.add(HostSymbols.COMMITAFTER_OAUTH,"false")
- If you are using Hibernate, but need to merge local accounts with remote accounts matching a specific property...
	-> override : DefaultHibernateFederatedAccountServiceImpl.findLocalAccount(Class<?> entityType, String realmName, Object remotePrincipal, Object remoteAccount)
- If you are adding/updating (i.e. cache) property values from remote account to local account...
	-> implement the required logic in FederatedAccount.federate(String realmName, Object remotePrincipal, Object remoteAccount)
- If you are not using DefaultXXXFederatedAccountServiceImpl but need to keep the Oauth access token for later use:
	->You need to do something like:

		SimplePrincipalCollection principalCollection = new SimplePrincipalCollection(remotePrincipal, realmName);
		principalCollection.add(authenticationToken, realmName);
		return new SimpleAuthenticationInfo(principalCollection, authenticationToken.getCredentials());
	
	or, store the access token in your database. Note the (potential) expiration of an access token and cast the authenticationToken argument of federate() call in your FederatedAccountService to OauthAccessToken for easier handling.
	
-	If you are implementing your own remote account provider...
	-> See the current FacebookRealm implementation, FacebookOauth page and FacebookOauthSignIn component and related classes for examples (and contribute back if it's generally useful!)

Check out more examples from our [full-featured functional tests](https://github.com/tynamo/tynamo-federatedaccounts/blob/master/tynamo-federatedaccounts-test/src/test/java/org/tynamo/security/federatedaccounts/FederatedAccountsIntegrationTest.java) or a simple, [live demonstration with the default Facebook authentication](http://tynamo-federatedaccounts.tynamo.org/) in action, running on GAE.

## Configuring realms

The modularized architecture allows each module (and realm) to use their own, provider specific communication library. Each realm can support additional, provider-specific features. For example, you may want to request additional permissions from Facebook users etc. If you don't have a need for provider-specific features (like reading posting tweets on behalf of the user, utilizing the user's social graph etc.) you may use a Pac4jFederatedRealm (based on [Pac4j module](https://github.com/leleuj/pac4j)), which is a generic Oauth module that unifies access to the user's federated profile. See below for documentation on specific realms and sub-modules.

### Pac4jFederatedRealm - in tynamo-federatedaccounts-pac4jbasedoauth module

If you just need a quick & dirty way for adding a few Oauth providers to your application, check out how Pac4j is being used in the [tynamo-federatedaccounts-test module](https://github.com/tynamo/tynamo-federatedaccounts/blob/master/tynamo-federatedaccounts-test/src/test/java/org/tynamo/security/federatedaccounts/testapp/services/AppModule.java). At this time, Pac4j supports *facebook, dropbox, github, google2, linkedIn2, twitter, windowslive, wordpress & yahoo* Oauth providers (the number at the end of the provider key refers to the authentication protocol in case the provider itself supports both). The module even sports a fully functional UI component (see the [federatedSignInOptionsPanel on the login page of the test app](https://github.com/tynamo/tynamo-federatedaccounts/blob/master/tynamo-federatedaccounts-test/src/test/resources/org/tynamo/security/federatedaccounts/testapp/pages/Login.tml)), so you enable specific providers, provider the credentials and entity configuration and you are done! Of course, you don't have to use the options panel but you can also just add specific Oauth logins using the <t:federated.Pac4jOauthSignIn provider="yahoo" / > component. Note that since Pac4j handles multiple specific providers, you need to use the form "pacj_<supported client name>" as a key when setting up entity mapping (e.g.  // configuration.add(FederatedAccountType.pac4j_.name() + SupportedClient.google2.name(), GoogleAccount.class) ).

### TwitterRealm - in tynamo-federatedaccounts-twitter module

This modules uses Twitter4j library. Twitter4j allows you to execute any read/write operations (search tweets, post on behalf of the user etc.) provided by the Twitter API. You typically want to use this module directly if you plan on exercising these Twitter APIs.

### FacebookRealm - in tynamo-federatedaccounts-facebook module

<div markdown="1" class="alert alert-info">
**Want to do more with FB than just authenticate?**

The module uses a fabulous little library, [RestFB](http://restfb.com/), for communicating with Facebook's Graph API. You are free to use the full power of RestFB and Facebook's Graph API in the rest of your application! Just remember that you need to store the access token for later use (see the Extension points below). The [example application at http://tynamo-federatedaccounts.tynamo.org](http://tynamo-federatedaccounts.tynamo.org/) demonstrates application's use of the access token, feel free to [browse the source code for the sample app](https://github.com/tynamo/tynamo-example-federatedaccounts/tree/master/src/main/java/org/tynamo/examples/federatedaccounts)
</div>

First, [create a Facebook application](http://developers.facebook.com/docs/guides/canvas)/[register your website](http://developers.facebook.com/docs/guides/web) (same thing really). If you need to request specific [application permissions](http://developers.facebook.com/docs/authentication/permissions), contribute an additional permission symbol (in addition to your application credentials), for example:

	configuration.add(FacebookRealm.FACEBOOK_PERMISSIONS, "email,user_birthday,user_location");

By default, FacebookRealm will use the Facebook user id as the **principal property**, i.e. that property is used as the remotePrincipal. You may change it by configuring *FacebookRealm.FACEBOOK_PRINCIPAL*. Facebook.PrincipalProperty {id, email, name} are the only supported principals. Using *email* as the principal property may sometimes be valuable for automatically merging existing accounts but remember that you need to explicitly request access to user's email. You can specify *name* as well, but note that it's not a uniquely identifying property (not even within Facebook), so you likely want to implement your own FederatedAccountService in that case and use composite keys or make the principal otherwise unique.

<div markdown="1" class="alert alert-success">
**Tapestry makes it easy!**

Oauth is based on callback URIs back to your application. This module automatically adds an Facebook Oauth page as the callback URI, and handles the mechanics of obtaining and verifying the Oauth access key. Even if you've secured access to the rest of your site (with [tapestry-security](http://tynamo.org/tapestry-security+guide)), *distributed configuration* contributed by this module allows unauthenticated requests to access the callback pages.
</div>

Working together with the FacebookRealm is FacebookOauthSignIn component. This component displays the Facebook login icon and initiates the Oauth callflow. The component requires that you've contributed values for "facebook.clientid" and "facebook.clientsecret" to work. Additionally, you may require Facebook specific permissions and decide to manage transactions yourself (set FederatedAccountSymbols.COMMITAFTER_OAUTH to false, true by default). FacebookSignIn also support three different window modes [blank|inline|self] (e.g. windowmode="blank"). The [live example](http://tynamo-federatedaccounts.tynamo.org/) well demonstrates these configuration choices.

<div markdown="1" class="alert alert-info">
Note on GAE

The module currently uses httpclient 4.x which doesn't run on GAE by default. To make it work, the example uses an additional dependency that is under LGPL. The code was taken from ESXX project and repackaged for Tynamo. This additional library is not included by default, you have to explicitly include it in your project if you want to use it and then set configuration.add(FederatedAccountSymbols.HTTPCLIENT_ON_GAE, "true"); in your AppModule. See the [example pom.xml](http://svn.codehaus.org/tynamo/trunk/tynamo-example-federatedaccounts/pom.xml) for more details.
</div>

### RollingTokenRealm in tynamo-federatedaccounts-rollingtokens

If you've ever wondered how to make a cookie-based rememberMe more secure, you should read the "[Persistent login cookie best practice](http://fishbowl.pastiche.org/2004/01/19/persistent_login_cookie_best_practice/)" blog post. One-time usable, per-IP issued access tokens are as secure as an intentional security hole will get. You might wonder what this has to do with federated accounts but tynamo-federatedaccounts-rollingtokens module well showcases the flexibility of the federatedaccounts core: a rollingtokens realm is treated as just another third-party authentication provider, handling issuing random access tokens and validating them against a persistent, server-side records before passing the control to your local realm. The rollingtokens module also utilizes the more esoteric Shiro/tapestry-security features such as a login listeners and a custom subject factory. Currently tynamo-federatedaccounts-rollingtokens module includes a **JPA only implementation**, sorry (we may include a Hibernate implementation if there's enough demand). To use the feature, add the following to your pom.xml:

	// dependencies in your pom.xml 
	<dependency>
		<groupId>org.tynamo.security</groupId>
		<artifactId>tynamo-federatedaccounts-rollingtokens</artifactId>
		<version>0.4.3</version>
	</dependency>

	@Contribute(FederatedAccountService.class)
	public static void contributeFederatedAccountService(MappedConfiguration<String, Object> configuration) {
		configuration.add("rollingtokens", UserAccount.class);
		configuration.add("rollingtokens" + FederatedAccountService.IDPROPERTY, "id");
	}

	// Need to tell principal type to rolling tokens so it can be persisted properly with the ExpiringRollingToken
	public static void contributeApplicationDefaults(MappedConfiguration<String, String> configuration) {
		configuration.add(RollingTokenSymbols.CONFIGURED_PRINCIPALTYPE, Long.class.getName());
	}
	
	// rollingtokens is currently JPA only
	@Contribute(JpaEntityPackageManager.class)
	public static void addPackagesToScan(Configuration<String> configuration) {
		configuration.add(ExpiringRollingToken.class.getPackage().getName());
	}

Rollingtoken plays especially well with Shiro's built-in rememberMe and Subject.authenticated feature. In Shiro's default rememberMe a Subject "[is remembered, they are NOT considered authenticated](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/subject/Subject.html#isRemembered%28%29)". Together with rollingtokens, two cookies are issued to the user. If the matching principal is found but rollingtoken authentication fails, Subject.isAuthenticated() returns false and true if matching server-side token was found and hadn't expired, just as if user had signed in with a username/password pair. Note however, that rollingtokens **does weaken** the security compared to secure form-based authentication (but is in some ways more secure than BASIC or form-based authentication over plain HTTP).