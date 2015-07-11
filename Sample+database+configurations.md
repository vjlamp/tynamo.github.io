---
layout: page
title: Sample database configurations
---
## Sample database configurations:

Here is a list of database configurations you can cut & paste into your Tynamo-powered application:

### H2

**db driver maven dependency**

	<dependency>
		<groupId>com.h2database</groupId>
		<artifactId>h2</artifactId>
		<version>1.2.126</version>
	</dependency>

**hibernate.properties**

	hibernate.dialect=org.hibernate.dialect.H2Dialect
	hibernate.connection.driver_class=org.h2.Driver
	hibernate.connection.url=jdbc:h2:tynamo
	hibernate.connection.username=sa
	hibernate.connection.password=
	hibernate.hbm2ddl.auto=update

### HSQLDB

**db driver maven dependency**

	<dependency>
		<groupId>hsqldb</groupId>
		<artifactId>hsqldb</artifactId>
		<version>1.8.0.7</version>
	</dependency>

**hibernate.properties**

	hibernate.dialect=org.hibernate.dialect.HSQLDialect
	hibernate.connection.driver_class=org.hsqldb.jdbcDriver
	hibernate.connection.url=jdbc:hsqldb:tynamo;shutdown=true
	hibernate.connection.username=sa
	hibernate.connection.password=
	hibernate.hbm2ddl.auto=update

### Derby

**db driver maven dependency**

	<dependency>
		<groupId>org.apache.derby</groupId>
		<artifactId>derbyclient</artifactId>
		<version>10.2.2.0</version>
	</dependency>

**hibernate.properties**

	hibernate.dialect=org.hibernate.dialect.DerbyDialect
	hibernate.connection.driver_class=org.apache.derby.jdbc.ClientDriver
	hibernate.connection.url=jdbc:derby://localhost/tynamo;create=true
	hibernate.connection.username=any
	hibernate.connection.password=value
	hibernate.hbm2ddl.auto=update

### MySQL

**db driver maven dependency**

	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<version>5.1.13</version>
	</dependency>

**hibernate.cfg.xml**

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE hibernate-configuration PUBLIC "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
			"/web/20130609031817/http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">
	<hibernate-configuration>
		<session-factory>
			<!-- for more Database configuration options check: /web/20130609031817/http://tynamo.org/DatabaseConfigurations -->
			<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
			<property name="hibernate.connection.url">jdbc:mysql://localhost/tynamo_dev?createDatabaseIfNotExist=true&amp;useUnicode=true&amp;characterEncoding=utf-8</property>
			<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
			<property name="hibernate.connection.username">tynamo_admin</property>
			<property name="hibernate.connection.password">pass</property>
			<property name="hbm2ddl.auto">update</property>
			<property name="hibernate.show_sql">true</property>
			<property name="hibernate.format_sql">true</property>
	
			<!--
				C3P0 configuration.
	
				/web/20130609031817/http://www.mchange.com/projects/c3p0/index.html#hibernate-specific
				/web/20130609031817/http://www.mchange.com/projects/c3p0/index.html#configuration_properties
			-->
			<property name="hibernate.c3p0.min_size">5</property>
			<property name="hibernate.c3p0.max_size">20</property>
			<property name="hibernate.c3p0.timeout">300</property>
			<property name="hibernate.c3p0.max_statements">50</property>
			<property name="hibernate.c3p0.idle_test_period">3000</property>
	
			<property name="hibernate.connection.provider_class">org.hibernate.connection.C3P0ConnectionProvider</property>
	
		</session-factory>
	</hibernate-configuration>

**create_databases.sql**

	/* USAGE: mysql -u root -p < create_databases.sql */
	
	CREATE DATABASE tynamo CHARACTER SET utf8 COLLATE utf8_general_ci;
	CREATE DATABASE tynamo_dev CHARACTER SET utf8 COLLATE utf8_general_ci;
	CREATE DATABASE tynamo_tests CHARACTER SET utf8 COLLATE utf8_general_ci;
	
	GRANT ALL ON tynamo.* TO tynamo_admin@localhost IDENTIFIED BY "pass";
	GRANT ALL ON tynamo_dev.* TO tynamo_admin@localhost IDENTIFIED BY "pass";
	GRANT ALL ON tynamo_tests.* TO tynamo_admin@localhost IDENTIFIED BY "pass";
	
	FLUSH PRIVILEGES;
	exit

### Oracle

**db driver maven dependency**

	<dependency>
		<groupId>com.oracle</groupId>
		<artifactId>ojdbc14</artifactId>
		<version>10.2.0.2.0</version>
	</dependency>

**hibernate.properties**

	hibernate.dialect=org.hibernate.dialect.Oracle9Dialect
	hibernate.connection.driver_class=oracle.jdbc.OracleDriver
	hibernate.connection.url=jdbc:oracle:thin:@localhost:1521:XE
	hibernate.connection.username=system
	hibernate.connection.password=system
	hibernate.hbm2ddl.auto=update

	# The Oracle JDBC driver doesn't like prepared statement caching very much.
	hibernate.statement_cache.size=0
	# or baching with BLOBs very much.
	hibernate.jdbc.batch_size=0
	
	# After a while, Oracle throws this exception: too many open cursors
	# Disable PreparedStatement caching for the connection pool too.
	# /web/20130609031817/http://www.hibernate.org/120.html#A10
	hibernate.dbcp.ps.maxIdle = 0
	
	# Stop hibernate from using the column-names in queries to retrieve data from the resultsets
	# More info in /web/20130609031817/http://www.jroller.com/page/dashorst?entry=hibernate_3_1_something_performance1
	hibernate.jdbc.wrap_result_sets=true

### PostgreSQL.

**db driver maven dependency**

	<dependency>
		<groupId>postgresql</groupId>
		<artifactId>postgresql</artifactId>
		<version>8.2-504.jdbc3</version>
	</dependency>

**hibernate.properties**

	hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
	hibernate.connection.driver_class=org.postgresql.Driver
	hibernate.connection.url=jdbc:postgresql://localhost/tynamo
	hibernate.connection.username=postgres
	hibernate.connection.password=postgres
	hibernate.hbm2ddl.auto=update

### Microsoft SQL Server

**db driver maven dependency**

	<dependency>
		<groupId>net.sourceforge.jtds</groupId>
		<artifactId>jtds</artifactId>
		<version>1.2</version>
	</dependency>

**hibernate.properties**

	hibernate.dialect=org.hibernate.dialect.SQLServerDialect
	hibernate.connection.driver_class=net.sourceforge.jtds.jdbc.Driver
	hibernate.connection.url=jdbc:jtds:sqlserver://localhost:1433/tynamo
	hibernate.connection.username=sa
	hibernate.connection.password=
	hibernate.hbm2ddl.auto=update