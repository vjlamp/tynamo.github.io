---
layout: post
title: tapestry-security 0.7.1 and tapestry-resteasy 0.5.0 released!
---
It's that time of the year again. Actually, the community has been asking these to be released for a while. Thanks to @homburgs, you can now contribute chains with [RegExPatternMatcher to tapestry-security](https://github.com/tynamo/tapestry-security/pull/50). Also, 0.7.1 is now using the latest version of Tapestry and I cleaned up a few [annoying default configuration errors that used to pop up in the logs](https://github.com/tynamo/tapestry-security/issues/49). See [docs for tapestry-security for more](http://www.tynamo.org/tapestry-security+guide/).

Tapestry-resteasy 0.5.0 now comes with the latest version of Tapestry and resteasy (3.1.4.Final) as well. Thanks to @jochenberger for tirelessly keeping the versions in sync. See [docs for tapestry-resteasy for more](http://www.tynamo.org/tapestry-resteasy+guide/).

As a side-note, while our Maven & [OSS](https://www.sonatype.com/nexus-repository-oss) based release process has worked well for the community for years, it does feel clunky and almost pointless for those of us who've ever used [Bintray's Github-based release "process"](https://blog.bintray.com/2013/06/05/bintray-github-synergistic-love-story/). Using Bintray also nicely solves the problem of building snapshots since you can use ant git(hub) hash as a version in case you need access to some unreleased features. I don't know if I have appetite to make changes to move over though, given that most of our libraries are pretty mature and slow moving at this point. Anyhow, something to keep in mind in case we come up with a brand new module.
