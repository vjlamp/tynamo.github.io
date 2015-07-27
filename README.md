
**Keep this page up to-to-date**

*Some crucial notes on how the project Tynamo is organized what new devs need to know about the project.
Let's keep the development documentation to the minimum and on this page only as much as possible.*

# Development notes

## Preface

The project is run mainly on Github infrastructure. The original home of Tynamo.org was at now defunct Codehaus and there may still be old links pointing either to http://tynamo.org/... (note without www. in front) or https://docs.codehaus.org/... If you spot one, please report/fix it. I (Kalle) have manually converted most of the guides to Markdown format using [Internet archive's Wayback Machine](https://web.archive.org/web/20150506154000/http://tynamo.org/). Most of the content is still available through it, including the old Confluence pages.

## Website

<http://www.tynamo.org> is implemented as Github pages, using Kramdown (a Markdown dialect that supports interspersed HTML tags, toc macro etc.) Each Tynamo module is kept in a separate Github repository under Tynamo organization. Github Pages for each separate module (the orphan gh_pages branches) is reserved for publishing the Maven site, most importantly for browsing javadocs and dependencies. The organization's Github Page repository, tynamo.github.io is a Jekyll powered site that contains all module specific guides and other development notes. The Jekyll layout isn't too pretty but works for mobile (or any narrow screen sizes), so if you make changes to the layout, make sure it still stays fluid.

### Publishing Maven site

Currently, module specific Maven sites are not published automatically. A project site should be publishable using the command *mvn site scm-publish:publish-scm*. The gh_pages branch needs to exist before (check notes on [this blog post about scm:publish](http://blog.progs.be/517/publishing-javadoc-to-github-using-maven) ). The artifact id of a module **must match** the repository name, otherwise the links to the Maven site won't work in the master layout.

### tynamo.github.io

Most regular content is simply added to the root. Note that several different layout are used to make it easier to differentiate between different types of content (such as the module specific guides, general development notes and other child pages that are not necessarily linked directly from the main layout).

## Source

Each Tynamo module is kept in a separate Github repository under Tynamo organization. We are also using Github's release pages for documenting the changes in each release so make sure to create milestones and tags for each version.

### Coding style

**We use tab to indent because that's what it's meant for**. No weird formatting rules, prefer concise over expanded and elaborated. We don't have a strict style guide so don't worry about it too much. Follow Tapestry coding style (not formatting!) where possible (for example *don't expect null, don't check for null*). Avoid unnecessary code like plague and refactor mercilessly. **Do not** auto-format (all lines of) existing code, **do not** use @author tags.


### Continuous integration

We are using Travis for continuos integration, because it has a great integration with Github and extremely simple to set up. You just need to turn on the build at <https://travis-ci.org/profile/tynamo> and commit the .travis.yml file with contents "language: java" to tell it that this is a Java build.

### Release process

We are making staged releases to Sonatype's [OSSRH repository](http://central.sonatype.org/pages/ossrh-guide.html). You need a PGP key to sign the release, see [Apache's instructions for creating one](http://www.apache.org/dev/release-signing.html). Release process follows the standard Apache/Maven release process (see instructions) except our releases are staged at <http://central.sonatype.org/pages/ossrh-guide.html>.

The usual issue to trip over is setting up your environment for providing PGP key & passphrase. The good news is that this is a one-time cost if you do it properly. Staging for us is mostly a final check before the release - if you are happy with the release, just go ahead and close and release from Nexus. If you are unsure about the contents, close the staging repo, then send the link to the dev list and ask others to verify. The usual Codehaus credentials work on Nexus.

Every individual module has to be releasable by running mvn -B release:prepare release:perform (although up to you if you run it in parts and/or without -B), otherwise the release configuration is wrong. If there are failures in the release process, they should be fixed immediately for the next release. For tapestry-model, we currently keep archetype-catalog.xml at the root of the dav folder (to make it accessible via http://www.tynamo.org/archetype-catalog.xml). The archetype-catalog.xml file needs to be manually updated after the achetype module release. Versions of Tynamo dependencies in examples module also need to be manually updated.

Announcements need to be done manually as well. Github picks up and displays the tags, edit Github release notes for the particular tag to edit release notes manually. Once you've released the module, it takes around 24 hours before the released artifacts are synched to Central. Announcement should be done only after the release is available through Central. This gives us time to do final manual check-ups after the release. If any major issues are found after the release, we should simply abandon the release, leave it unannounced and push out a new release. You should always make the **announcements in at least four places**: on our home page (as a Confluence blog post), users@tapestry.apache.org, user@tynamo.codehaus.org and on Twitter (kaosko owns the twitter handle tynamo_org but you can use your own handle). Twitter announcements need to use the hashtag *#tapestry5*.

If the archetype GA coordinates changes or we publish other archetypes, we need to update the Maven archetype wiki page.

## Branding and marketing

Any project, free-of-charge or not, needs to market their offerings. Tynamo.org has a strong brand recognition within the Tapestry community but is practically non-existent in the wider web, even just among other Java developers. We simply need more back links to Tynamo.org from a variety of content, not just from tapestry.apache.org and stackoverflow.com. Any of the confluence pages need to mention the following keywords at least once: **Apache, Tapestry 5, Java and framework**, and include one or more links to **<http://www.tynamo.org/>**.