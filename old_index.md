---
layout: article
name: What is PicoContainer?
---

p. PicoContainer is a highly embeddable, full-service, Inversion of Control (IoC) container for components honor the Dependency Injection pattern. The project started in 2003 and pioneered Constructor Injection auto-wiring. It is also Open Source and therefore free to use. The license is "BSD":http://www.opensource.org/licenses/bsd-license.php and thus you can safely use this with commercial or other open source software.

p. You could use it as a lightweight alternative to Sun's J2EE patterns for web applications or general solutions.

p. What is Dependency Injection? Martin Fowler has a good "article":http://www.martinfowler.com/articles/injection.html from 2003, but here is another view: It is a good design pattern that, for large enterprise applications, facilitates:

* easy best practice unit testing _vs_ little and difficult unit testing.
* component reuse _vs_ rewriting through ignorance or perceived needs
* configuration given to components _vs_ components reading their own scattered config
* clean&declarative architecture _vs_ a nest of singletons that nobody can make sense of
* maintainability _vs_ developers having difficulties fixing bugs
* adaptability _vs_ developers not knowing where to start to add features
* transparency _vs_ lots of framework code, with consequential lock in

p. Dependency Injection is quite often, but not exclusively, used by Agile practitioners. It counters situations where enterprise applications:

* have grown to be thousands of classes, with dozens if not hundreds of Singletons
* draw similarities to Spaghetti, Hairballs or "Balls of Mud":http://www.laputan.org/mud/ 
* has made development staff looking after it miserable, and wanting to quit
* suffers repeated allegations of "it cannot be further developed without complete rewrite"

p. Despite it being very compact in size (the core is ~260K and it has no mandatory dependencies outside the JDK), PicoContainer supports different dependency injection types (Constructor, Setter, Annotated Field / Method) and offers multiple lifecycle and monitoring strategies.

p. If you're trying to understand the difference between PicoContainer and similar libraries, then its best to think of PicoContainer as a map that where add() is for types and implementations, and get() is for instances.

p. PicoContainer has originally been implemented in Java but is also available for other platforms and languages. These are detailed "here":http://docs.codehaus.org/display/PICO/Ports .

h3. Read More?

 %(callout)The "Introduction":introduction.html page discusses basic PicoContainer usage, and has pictures - yay!% 