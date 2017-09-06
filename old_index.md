---
layout: article
name: What is PicoContainer?
---

PicoContainer is a highly embeddable, full-service, Inversion of Control (IoC) container for components honor the Dependency Injection pattern. The project started in 2003 and pioneered Constructor Injection auto-wiring. It is also Open Source and therefore free to use. The license is [BSD](http://www.opensource.org/licenses/bsd-license.php) and thus you can safely use this with commercial or other open source software.

You could use it as a lightweight alternative to Sun's J2EE patterns for web applications or general solutions.

What is Dependency Injection? Martin Fowler has a good [article](http://www.martinfowler.com/articles/injection.html) from 2003, but here is another view: It is a good design pattern that, for large enterprise applications, facilitates:

-   easy best practice unit testing *vs* little and difficult unit testing.
-   component reuse *vs* rewriting through ignorance or perceived needs
-   configuration given to components *vs* components reading their own scattered config
-   clean&declarative architecture *vs* a nest of singletons that nobody can make sense of
-   maintainability *vs* developers having difficulties fixing bugs
-   adaptability *vs* developers not knowing where to start to add features
-   transparency *vs* lots of framework code, with consequential lock in

Dependency Injection is quite often, but not exclusively, used by Agile practitioners. It counters situations where enterprise applications:

-   have grown to be thousands of classes, with dozens if not hundreds of Singletons
-   draw similarities to Spaghetti, Hairballs or [Balls of Mud](http://www.laputan.org/mud/)
-   has made development staff looking after it miserable, and wanting to quit
-   suffers repeated allegations of "it cannot be further developed without complete rewrite"

Despite it being very compact in size (the core is ~260K and it has no mandatory dependencies outside the JDK), PicoContainer supports different dependency injection types (Constructor, Setter, Annotated Field / Method) and offers multiple lifecycle and monitoring strategies.

If you're trying to understand the difference between PicoContainer and similar libraries, then its best to think of PicoContainer as a map that where add() is for types and implementations, and get() is for instances.

PicoContainer has originally been implemented in Java but is also available for other platforms and languages. These are detailed [here](http://docs.codehaus.org/display/PICO/Ports) .

### Read More?

<span class="callout">The [Introduction](introduction.html) page discusses basic PicoContainer usage, and has pictures - yay!</span>
