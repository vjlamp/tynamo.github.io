---
layout: guide
title: tapestry-activiti
repository_name: tapestry-activiti
---
<div markdown="1" class="alert alert-info">
**version status: 0.0.1 alpha released**

feedback needed!! please give us your feedback via the tynamo mailing list.

**tapestry-activiti** is brought to you by Omar Carvajal and [Carvajal Consultants, Inc](http://carvajalonline.com/). They did all the work and we thank them for it!.
</div>

Activiti is a workflow and Business Process Management (BPM) platform. Its core is a super-fast and rock-solid BPMN 2 process engine for Java. It's extremely lightweight and based on simple concepts.

The Tapestry-Activiti module allows you to interact with your Activiti instance and its key process entities from Tapestry. You can also call Tapestry services from within your process definition. 

## Setup

**Requirements**

In order to start using Tapestry-Activiti, you need to meet the following requirements:

- Some basic knowledge of Activiti, check out their User Manual if you haven't already.
- Have a working BPMN2 process (which can be created using the Eclipse Designer or any other Activit designer tool).

**Dependencies**

You need the following module in order to use Activiti in your Tapestry application:

- tapestry-activiti

Maven2 configuration:

	<dependency>
		<groupId>org.tynamo</groupId>
		<artifactId>tapestry-activiti</artifactId>
		<version>0.0.1</version>
	</dependency>

These artifacts are available in the maven main repository. No need to configure any additional repositories.

**Configuration**

For the module to work, you first need to tell tapestry-activiti what processes need to be deployed:

**AppModule.java**

	public void contributeProcessEngine(Configuration<Resource> deploymentResources) {
		/**
		 * Remember: the name of the resource must end with "bpmn20.xml".
		 * @see: BpmnDeployer.BPMN_RESOURCE_SUFFIX
		 */
		deploymentResources.add(new ClasspathResource("SimpleTest.bpmn20.xml"));
	}

There are various configuration options that may be set, for example if you want to use a different database other than the default H2 database:

**AppModule.java**

	public static void contributeApplicationDefaults(MappedConfiguration<String, String> configuration) {
		configuration.add(ActivitiSymbols.DATABASE_TYPE, "postgres");
		configuration.add(ActivitiSymbols.JDBC_URL, "jdbc:postgresql://localhost/activiti");
		configuration.add(ActivitiSymbols.JDBC_DRIVER, "org.postgresql.Driver");
		configuration.add(ActivitiSymbols.DATABASE_SCHEMA_UPDATE, "true");
	}

Make sure to check the Tapestry-Activiti Javadocs and the Activiti User Manual for a list of the configuration options that may be set.

## How to use Tapestry Activiti

**Services**

Tapestry-Activiti defines various Activiti services, including:

- ProcessEngine
- RepositoryService
- RuntimeService
- FormServiceTaskService
- HistoryService
- ManagementService

These can be used throughout your Tapestry application to manipulate the Activiti process.

**Initiating a Process Instance**

You may use the Activiti services throughout your application using Tapestry injection, for example:

	public class MyPage {
	    @Inject
	    private RuntimeService runtimeService;
	
	    void onActionFromStart() {
	        runtimeService.startProcessInstanceByKey("SimpleProcess");
	    }
	}

**Calling a service method from a process**

You may at one point need to call a method defined in a Tapestry service from an Activiti process.

Suppose we have the following service defined:

**Service**

	public class ServiceA {
	    public void doWork() {
	        //Service logic here
	    }
	}

We could then call this service method from a process by using an Activiti expression:

	<serviceTask id="simple" activiti:expression="#{ServiceA.doWork()}"/>

The service's service-id is always used to reference a service from an Activiti process.
