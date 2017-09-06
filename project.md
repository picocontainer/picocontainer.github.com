---
layout: article
title: Project
---

## PicoContainer is hosted at GitHub

## Project links

Jira "Bug tracking":http://jira.codehaus.org/browse/PICO/ 

Old "Confluence based documentation":http://docs.codehaus.org/display/PICO/ (for Pico 1.x)

Source control root for Pico 2.x (you will need Maven2 installed to build):

[http://svn.codehaus.org/picocontainer/java/2.x/trunk/](http://svn.codehaus.org/picocontainer/java/2.x/trunk/)

For other languages:

Ruby ("Rico") - http://svn.codehaus.org/picocontainer/ruby/

Php - http://svn.codehaus.org/picocontainer/php/

.NET - http://svn.codehaus.org/picocontainer/dotnet/


Maven repositories for artifact download :

Numbered releases : "https://nexus.codehaus.org/content/repositories/releases/org/picocontainer/":https://nexus.codehaus.org/content/repositories/releases/org/picocontainer/ 

Snapshot (work in progress) releases "http://snapshots.repository.codehaus.org/org/picocontainer/":http://snapshots.repository.codehaus.org/org/picocontainer/ 

### Developer's Note

If you wish to work with the Pico Sources, somtimes the 3rd party library Paranamer is updated before it gets synced to the maven central repository. You can get past frustrating compile error during those few days beteween update and sync by adding the following code to your maven's settings.xml file.

<profiles>
<profile>
<id>pico</id>
<activation>
<activeByDefault>true</activeByDefault>
</activation>
<repositories>
<repository>
<id>codehaus</id>
<snapshots><enabled>false</enabled></snapshots>
<releases><enabled>true</enabled></releases>
<url>https://nexus.codehaus.org/content/repositories/releases/</url>
</repository>
</repositories>
</profile>
</profiles>

## License

Pico is [BSD licensed](http://www.opensource.org/licenses/bsd-license.php) . At least, with us as copyright holder instead of "Regents of the University of California".

You can use it, write extensions for it, from/with software that is:

* licensed with other BSD open source
* Apache licensed software (1.1 or 2.x) open source
* GNU Public license (GPL)
* commercial (closed source) software

Under the terms of the BSD license you don't have to give changes back to us, or make your license the same as ours. If there is something that would benefit PicoContainer we would be pretty happy if you donated it back :-)

