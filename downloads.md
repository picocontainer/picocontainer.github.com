---
layout: article
name: Downloads
---

h3. Released Binary and Source Jars (1.x and 2.x)

p. Core: "https://nexus.codehaus.org/content/repositories/releases/org/picocontainer/picocontainer/":https://nexus.codehaus.org/content/repositories/releases/org/picocontainer/picocontainer/

p. Gems: "https://nexus.codehaus.org/content/repositories/releases/org/picocontainer/picocontainer-gems/":https://nexus.codehaus.org/content/repositories/releases/org/picocontainer/picocontainer-gems/

p. Distribution: "https://nexus.codehaus.org/content/repositories/releases/org/picocontainer/picocontainer-distribution/":https://nexus.codehaus.org/content/repositories/releases/org/picocontainer/picocontainer-distribution/

h3. Snapshot Binary and Source Jars

p.  _(Current work in progress)_ 

p. Core: "http://snapshots.repository.codehaus.org/org/picocontainer/picocontainer/":http://snapshots.repository.codehaus.org/org/picocontainer/picocontainer/ 

p. Gems: "http://snapshots.repository.codehaus.org/org/picocontainer/picocontainer-gems/":http://snapshots.repository.codehaus.org/org/picocontainer/picocontainer-gems/ 

p. Distribution: "http://snapshots.repository.codehaus.org/org/picocontainer/picocontainer-distribution/":http://snapshots.repository.codehaus.org/org/picocontainer/picocontainer-distribution/ 

p. Currently, the Core jar is ~247K, and the Gems jar is ~112K. The distribution zip contains all jars and documentation.

h3. Using Maven Artifacts

p. If you use Maven as the build system (or one that is compatible with the Maven Repository), you can declare dependencies as

org.picocontainerpicocontainer[-gems][version]p. specifying the version. Declaring this dependency will pull in all other required dependencies (ie transitive dependencies). Note that PicoContainer core artifactId is simply "picocontainer".

