---
layout: article
title: Downloads
---

## Released Binary and Source Jars (1.x and 2.x)

Core: <http://central.maven.org/maven2/org/picocontainer/picocontainer/>

Gems: <http://central.maven.org/maven2/org/picocontainer/picocontainer-gems/>

Distribution: <http://central.maven.org/maven2/org/picocontainer/picocontainer-distribution/>

## Snapshot Binary and Source Jars

*<span class="progress in work Current"></span>*

Core: <http://snapshots.repository.codehaus.org/org/picocontainer/picocontainer/>

Gems: <http://snapshots.repository.codehaus.org/org/picocontainer/picocontainer-gems/>

Distribution: <http://snapshots.repository.codehaus.org/org/picocontainer/picocontainer-distribution/>

Currently, the Core jar is ~247K, and the Gems jar is ~112K. The distribution zip contains all jars and documentation.

## Using Maven Artifacts

If you use Maven as the build system (or one that is compatible with the Maven Repository), you can declare dependencies as

org.picocontainerpicocontainer\[-gems\]\[version\]p. specifying the version. Declaring this dependency will pull in all other required dependencies (ie transitive dependencies). Note that PicoContainer core artifactId is simply "picocontainer".
