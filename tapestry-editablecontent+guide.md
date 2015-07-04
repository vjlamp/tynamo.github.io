---
layout: guide
title: tapestry-editablecontent
repository_name: tapestry-editablecontent
---

<div markdown="1" class="alert alert-info">
**Version status: 0.0.3 beta**

Depends on tapestry-jpa and tapestry-ckeditor modules
</div>

## Overview

Editablecontent module was born out of the recurring need to modify specific textual segments. Most websites have "about", "privacy policy", "what's new" etc. pages that contain largely textual content that is changed infrequently and often separate or after product updates. Using a traditional, full-blown CMS for modifying the content is often too cumbersome, as the CMS may not offer a suitable data API for pulling the content out, the resource links (images etc.) may need to modified, the CMS doesn't offer good enough performance for high volume read operations and/or the caches remain in stale state for too long. The answer is to modify the content on-line and in-place, backed by a database of your choice. The editablecontent component allows you to simply surround a possibly existing static text on a html page and make it editable with the right permissions, automatically storing the edited contents in the database, with localization support!

Here's how it looks like:

| **When user is unauthenticated** <br/> ![editablecontent uneditable](/assets/media/editablecontent-uneditable.png) <br/> **When authenticated with the configured editor role** <br/> ![editablecontent editable](/assets/media/editablecontent-editable.png) | **Content is being edited** <br/> ![editablecontent being edited](/assets/media/editablecontent-isedited.png) |
{: .table .table-condensed .table-bordered}

## Usage

	<dependency>
	  <groupId>org.tynamo</groupId>
	  <artifactId>tapestry-editablecontent</artifactId>
	  <version>0.0.3</version>
	</dependency>

An example page template:

	<p><strong>The following text is editable with the right permissions</strong></p>
	<t:tynamo.editableContent t:id="testeditablecontent">
		<div>
			lorem lipsum etc.
		</div>
	</t:tynamo.editableContent>

## Configuration

**The module doesn't require any mandatory configuration.**

There are only a few configuration options for which the module provides reasonable defaults:

	public class EditableContentSymbols {
		// type int, default 10 (items), 0 to disable
		public static final String LRU_CACHE_SIZE = "editablecontent.lrucachesize";
		// type boolean, default true (store using current locale)
		public static final String LOCALIZED_CONTENT = "editablecontent.localizedcontent";
		// type boolean, default false (the default for component readOnly parameter)
		public static final String READONLY_BYDEFAULT = "editablecontent.readonlybydefault";
		// type string, default "" (the default for component's author role parameter)
		public static final String DEFAULT_AUTHORROLE = "editablecontent.defaultauthorrole";
		// type string, default "" (if unit name is empty use the configured persistence unit if there's only a single one)
		public static final String PERSISTENCEUNIT = "editablecontent.persistenceunit";
	}

The module uses *HttpServletRequest.getRemoteUser()* and *HttpServletRequest.isUserInRole(String role)* for permission checks. [Tapestry-security](http://www.tynamo.org/tapestry-security+guide/) provides a built-in wrapper for handling these (standard servlet) operations, but you *don't have* to use tapestry-security with this module, instead you could easily provide your own advisor for HttpservletRequest if you like. The module also keeps track of versions, however at the moment, there's no user interface for reverting to previous versions. Currently, JPA is the only persistence strategy implemented. For configuring JPA, see ["Integrating with JPA", in Tapestry 5 documentation](http://tapestry.apache.org/integrating-with-jpa.html).