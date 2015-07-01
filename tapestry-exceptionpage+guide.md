---
layout: guide
title: tapestry-exceptionpage
---
<div markdown="1" class="alert alert-info">
**Version status: 0.1.2 beta, used in production**
Tapestry-exceptionpage has graduated! As part of [TAP5-1833](https://issues.apache.org/jira/browse/TAP5-1833), a version of tapestry-exceptionpage has been merged into Tapestry 5.4 core. Use the external dependency only for Tapestry versions before 5.4.
</div>

In your Tapestry 5 applications, are you missing the standard error-page/exception-type configuration option specified by the servlet spec? For some rarely occurring exceptions, it's simpler to just catch them at the outermost layer of your application rather than carry a typed exception through multiple layers of abstractions just so you could show a sensible error message to the user, especially if you can't do anything more clever about it anyway. Unfortunately Tapestry wraps up all of your exceptions inside a ComponentEventException making the error-page/exception-type configuration in your web.xml useless. The default Tapestry 5 exception page is great for development but typically, you don't want to show that in production.

The servlet spec, as always, is rather restrictive in regards to error-page configuration. If your email service or an external payment service goes down, you can't do much more than display an error message to the user, so why would you need to implement separate pages for each exception? Often, it'd be nicer if you could just reuse the page template for any fatal exception and simply display a different error message. Tapestry-exceptionpage allows you to contribute handlers for specific types of exceptions and provide context for rendering the error page for that specific exception.

To use the feature, you need to add the following dependency to your pom.xml:

	<dependency>
	  <groupId>org.tynamo</groupId>
	  <artifactId>tapestry-exceptionpage</artifactId>
	  <version>0.1.2</version>
	</dependency>

You can contribute an error page, mapping it to an exception type:

	public void contributeExceptionHandler(MappedConfiguration<Class, Class> configuration) {
		configuration.add(SmtpNotRespondingException.class, ServiceFailure.class);
	}

If a simple exception type to page mapping doesn't do it for you, you can also contribute a custom handler for that particular exception type. An ExceptionHandlerAssistant can contain arbitrarily complex logic for handling a specific exception type and use other Tapestry services. If ExceptionHandlerAssistant.handleRequestException(Throwable exception, List<Object> exceptionContext) returns an Object representing an URL the main handler will issue a redirect to that URL. It's valid to return either a String, a Link or a Class; the last case implies a page class. If the ExceptionHandlerAssistant returns null, it's assumed that the assistant has independently handled the exception. You can either contribute an instance of an ExceptionHandlerAssistant or a class that implements ExceptionHandlerAssistant. Below, we contribute an instance handling ServiceExceptions:

	public void contributeExceptionHandler(OperationQueue operationQueue, MappedConfiguration<Class, Class> configuration) {
		final ExceptionHandlerAssistant assistant = new ExceptionHandlerAssistant() {
	    @Override
	    public Object handleRequestException(Throwable exception, List<Object> exceptionContext) throws IOException {
	      ServiceException serviceException = (ServiceException)exception;
	      if (serviceException.isInterruptedOperationRecoverable()) {
	      	operationQueue.add(serviceException.getInterruptedOperation());
	      	return OperationScheduled.class;
	      }
	      else return ServiceUnavailable.class;
	    }
		};
		configuration.add(ServiceException.class, assistant);
	}

You can also specify context for the exception page. For generic exceptions, the context is taken from the exception class name minus the word "Exception" in case that's how the class name ends. For example, you have a following class:

	public class SmtpNotResponding extends RuntimeException {
		...
	}

If an SmtpNotRespondingException is thrown during an action request, user is directed to ServiceFailure page with a String context smtpnotresponding (i.e. to URL **/servicefailure/smtpnotresponding**). Tapestry-exceptionpage works both for regular action requests and ajax action requests. In the latter case, the module will use Javascript to redirect to the error page. If the exception thrown wasn't an explicitly specified exception type (i.e. a contributed type), handling is delegated back to the default Tapestry exception handler.

If your custom-handled exception implements the interface *ContextAwareException* you can fully specify the context for the error page. For example, you could implement a following Exception class:

	public class SmtpNotRespondingException extends RuntimeException implements ContextAwareException {
		private Object[] context;
	
		public EmailServiceException(Object[] context) {
			super();
			this.context = context;
		}
	
		// Defined in ContextAwareException interface
		public Object[] getContext() {
			return context;
		}
	}

This exception handling mechanism can easily be overused. Typically, if you can handle the exception locally, you should. Likewise, you shouldn't blindly wrap any checked exceptions inside runtime exceptions just to avoid writing try-catch blocks in higher layers. The exceptionpage module is best used for handling serious but rarely occurring exceptions happening in the action request cycle that you cannot otherwise cope with.