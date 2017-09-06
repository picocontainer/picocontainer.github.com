---
layout: article
name: News
---

{% for post in site.posts %}
## {{ post.date | date_to_string }} &gt;&gt; [{{post.name}}]({{ post.url }}
{{ post.desc }}
{% endfor %}

**Jun 27, 2010 - PicoContainer 2.11 released (312K jar)**

New (since 2.10.2):

-   Alternate 'debug' version of PicoContainer made that does not have shaded Paranamer which in turn uses real ASM. Both those are transitive deps now

Changes (versus 2.10.2):

-   Fix HiddenImplementation to cache delegate per instance not per invocation reported by Chris Floersch
-   JNDI fixes. See [PICO-370](http://jira.codehaus.org/browse/PICO-370)
-   Rare ArrayIndexOutOfBoundsException when adding components fixed [PICO-367](http://jira.codehaus.org/browse/PICO-367)
-   Getters added for 'name' and 'lifecyclestate' for containers.
-   PicoBuilder instances are reusable now.
-   Better support for handling ComponentAdapters while flushing instances.
-   Some component monitors vulnerable to race conditions (now fixed)

**Feb 25, 2010 - PicoContainer 2.10.2 released (308K jar)**

Changes (versus 2.10.1):

-   A fix of a NullPointerException issue when using ProviderAdapter

**Feb 23, 2010 - PicoContainer 2.10.1 released (308K jar)**

Changes (versus 2.10):

-   Further generics work on ComponentAdapter signature as per [PICO-369](http://jira.codehaus.org/browse/PICO-369)

**Feb 21, 2010 - PicoContainer 2.10 released (308K jar)**

New (since 2.9):

-   String [Converters](converters.html) are now overridable and can be leveraged from parent containers if not specified in a child
-   [Named Method Injection](named-method-injection.html)
-   `Named from JSR 330 (`Inject) now supported.

Changes (versus 2.9):

-   FactoryInjectors issue with 'into' being unspecified fixed [PICO-368](http://jira.codehaus.org/browse/PICO-364)
-   Provided instances can now participate in lifecycles

**Nov 2, 2009 - PicoContainer 2.9 released (299K jar)**

New (since 2.8.3):

-   Lifecycle strategy allows for concept of lazy starting. This means components are started at first access. [PICO-355](http://jira.codehaus.org/browse/PICO-355)
-   Regex collecting of components for injection, or just on getComponent() now built in.

Changes (versus 2.8.3):

-   Purposeful injection of a Null parameter fixed [PICO-364](http://jira.codehaus.org/browse/PICO-364)
-   Small performance improvements for getAdapter/getInstance
-   Dependencies for Log4J and SLF4J moved forward
-   Paranamer upgraded to 2.1
-   Exception in Lifecycle methods bug fixed [PICO-363](http://jira.codehaus.org/browse/PICO-363)
-   PicoBuilder able to do more with Lifecycle strategies
-   Factory Injection improved with an 'InjectInto' type [PICO-358](http://jira.codehaus.org/browse/PICO-358)

**Jun 18, 2009 - PicoContainer 2.8.3 released (299K jar)**

New (since 2.8.2):

-   DefaultPicoContainer opened up a to facilitate rudimentary lazy lifecycle

Changes (versus 2.8.2):

-   Providers not passing on exceptions bug fixed: [PICO-357](http://jira.codehaus.org/browse/PICO-357)

**May 27, 2009 - PicoContainer 2.8.2 released (298K jar)**

Changes (versus 2.8.1):

-   Constructor Injection is slightly faster for second/subsequent injections and for multi-constructor components
-   More optimized Paranamer usage and upgrade to 2.0
-   Generic Collections more consistent between Setter and Constructor injection [PICO-354](http://jira.codehaus.org/browse/PICO-354)

**May 20, 2009 - PicoContainer 2.8.1 released (298K jar)**

New (since 2.8):

-   Reinjection calls can use cached results for method invocations

Changes (versus 2.8):

-   Constructor Injection is slightly faster for second/subsequent injections and for multi-constructor components
-   Compatibility with Google's AppEngine
-   Paranamer upgraded to 1.5
-   A couple of bugs fixed [PICO-352](http://jira.codehaus.org/browse/PICO-352) and [353](http://jira.codehaus.org/browse/PICO-353)

**Feb 26, 2009 - PicoContainer-Web 2.3 released**

See the [news page](http://picocontainer.org/web/news.html) for it

**Feb 26, 2009 - PicoContainer 2.8 released (286K jar)**

New (since 2.7):

-   Automatic type conversion for named parameters (from String)

Changes (versus 2.7):

-   JSR 250 annotations themselves (@PostConstruct etc) not included in the Jar anymore
-   Paranamer upgraded to 1.3 and PicoContainer's usage of it changed to better caching and fallback model
-   Improved handling of Constant Parameters for class instances
-   Adapter.findAdapterofType() null handling improved

**Jan 20, 2009 - PicoContainer 2.7 released (278K jar)**

New (since 2.6):

-   Provider added as a type of Injector. Subclasses of this supply one method called provide that takes dependencies and returns an instance of the type it is trying to provide
-   ComponentMonitor: new method newBehavior
-   ReflectionMethodInjection: new annotation @Nullable allows for individual parameters to be null
-   Guarding behavior : before components are injected, another seeming unrelated can be injected too and veto if it sees fit

Changes (versus 2.6):

-   Reinjection changed to allow access to the result in the reinjection method invocation
-   Method decorateComponentInstance on Injector changed to have a return value
-   Scoped components stored in ThreadLocal can now can honor lifecycle concepts reliably
-   ComponentMonitor: newInjectorFactory method renamed to newInjector
-   Fix of issue where ImplementationHiding and Caching together on a component in threadlocal
-   AnnotatedFieldInjection changed to traverse into parent classes for annotated fields to inject into
-   Paranamer (included in PicoContainer jar) has had a couple of bugs fixed.
-   Issued 343 fixed where generic collections were not not being injected correctly

**October 14, 2008 - PicoContainer 2.6 released (265K jar)**

New (since 2.5.1):

-   CompositeLifecycleStrategy supports a mix of LifecycleStrategies in one container tree
-   Method Injection changed to also allow a specific reflection method to be implicated
-   Reinjection added to allow components to be injected into a second time (reflection method injection only)

Changes (versus 2.5.1):

-   Permissions fix for AdaptingInjection

**Aug 14, 2008 - PicoContainer 2.5.1 released (255K jar)**

Changes (versus 2.5):

-   makeChildContainer passes componentMonitor to the child container.

**July 25, 2008 - PicoContainer 2.5 released (255K jar)**

New (since 2.4):

-   Circular dependencies, while always supported via the implementation hiding behavior are now supported explicitly for individual components
-   PicoBuilder improved in that it can add child containers to their parents if wanted. Refer PicoBuilder.addChildToParent()
-   Components can now declare generic collections as dependencies to be Injected. It works the same was as an Array of the type in question always did.
-   Concrete extensions of generic components can satisfy dependencies now. This is *a* way of beating type erasure.

Changes (versus 2.4):

-   ConsoleComponentMonitor does need to be final, thus is not now.
-   Attempts to chain a series of MutablePicoContainer.as() statements are blocked now. Instead users are directed towards the varargs feature of the same method.

**July 02, 2008**

PicoContainer [Web](http://picocontainer.org/web/news.html) , [Script](http://picocontainer.org/script/news.html) , [Persistence](http://picocontainer.org/persistence/news.html) and [Logging](http://picocontainer.org/logging/news.html) 2.x released

See links above for respective news pages

**June 26, 2008 - PicoContainer 2.4 released (253K jar)**

New (since 2.3):

-   DefaultClassLoadingPicoContainer added. Components can now be referenced in different classloaders and by class name. This was formerly DefaultNanoContainer
-   Locking behaviors are now characterizable as LOCK and NO\_LOCK
-   Synchronizing behaviors are now characterizable as SYNCHRONIZE and NO\_SYNCHRONIZE
-   Pooling behaviors are now characterizable as NO\_POOL and POOL
-   HotSwapping behaviors are now characterizable as HOT\_SWAP and NO\_HOT\_SWAP

Changes (versus 2.3):

-   Visitor for trees of PicoContainers, can issue a halt instruction now to stop the visitation early
-   PICO-0316 fixed, whereby iterative injectors would not always find the dependencies to inject, when there were present
-   AsmHiddenImplementation behavior in Gems replaces by HiddenImplementation
-   CommandLinePicoContainer replaces by CommandLineArgumentsPicoContainer (now deprecated)
-   ReflectionLifecycleStrategy can be subclassed now
-   PICO-0314 fixed - StartableLifecycleStrategy can now have custom Disposable alternate classes correctly
-   PICO-0303 fixed - ReflectionLifecycleStrategy was not bubbling lifecycle exceptions correctly

**May 24, 2008 - PicoContainer 2.3 released (241K jar)**

New (since 2.2):

-   FactoryInjector implementations for Log4J, Commons-logging, Java-Logging and SL4FJ
-   JSR-250 compatible (`PreDestroy and `PostConstruct) lifecycle strategy
-   Slf4j based component monitor
-   Visitor for component factories (PICO-221)
-   Thread local Storing behavior now has a mechanism to report the store size

Changes (versus 2.2):

-   Implementation-hiding Behavior has a bug PICO-310 that has been fixed
-   FactoryInjection bug fixed - PICO-311
-   Embedded Paranamer now upgraded to 1.1.3
-   Some of the monitors are now serializable when they were not formerly

**May 9, 2008 - PicoContainer 2.2 released (224K jar)**

New (since 2.1):

-   A new TieringPicoContainer that when used in a container tree, forbids children from seeking dependencies from their grandparents (they can only seek such from their parents)
-   A new ReusablePicoContainer that is 2.5x faster for some niche usages where you are repeatedly discarding a container and then repopulating it (Gems)
-   NamedFieldInjection allows named fields to be injected into (per component) - use when AnnotatedFieldInjection is not what you want
-   TypedFieldInjection allows fields of a specified type to be injected into (per component) - use when AnnotatedFieldInjection is not what you want
-   CompositeInjection allows a very custom setup of what injection-types you want your instance to support. Use when speed of execution is of the essence and AdaptiveInjection is not right for you.
-   Decorating (behavior factory) - do something to a component instance immediately after its instantiation (and formal injection)
-   FactoryAdapter - Allows for a custom instance to be injected into a component. E.g. 'new Logger(Foo.class)' for a Logger type and a Foo instance being the injectee

Changes (versus 2.1):

-   Teams embedding PicoContainer and (completely hiding it from user communities) can extend StartableLifecycleStrategy and provide their own Startable interface (was buggy)

**Mar 31, 2008 - PicoContainer 2.1 released (194K jar)**

New (since 2.0):

-   A new ComponentMonitor method to allow allow for better mocking of select components during unit testing.

**Jan 19, 2008 - PicoContainer 2.0 released (190K jar)**

New (since 2.0-beta-2):

-   Binding Annotations support (disambiguation)
-   Newer and rewritten properties backed containers
-   Parameter names can be leveraged for all relevant types of injection

Changes (versus 2.0-beta-2):

-   Yet More Java5 generics
-   Build moved to JUnit 4.x and JMock 2.x (does not affect users of PicoContainer)
-   Built-in parameter name access, as opposed jar dependency

**August 26, 2007 - PicoContainer 2.0 beta 2 released (152K jar)**

New (versus 2.0-beta-1):

-   Rudimentary AOP capability for components built in
-   Method Injection (one method post-construction with multiple arguments)
-   Automatic JNDI exposure
-   Multi Injection (Setter and after Constructor injection)
-   Store behavior (can extract/replace component store per thread)
-   Automatic components (instantiate irrespective of need)
-   Reentrant-Lock version of Synchronizing behavior

Changes (versus 2.0-beta-1):

-   Better Java5 generics
-   Renames of behaviors and injectors to more verb-like style
-   Startable interface more easily overridden to own choice

**July 15, 2007 - PicoContainer 2.0 BETA-1 released (128K jar)**

New:

-   Properties for components as they are added to containers makes for increased flexibility
-   Field and Method annotation types of injection in addition to traditional Constructor (recommended) and Setter types
-   Parameters names for constructors can now drive otherwise ambiguous injections, as well as configuration
-   Large sets of configuration can be taken from properties files and command line arguments
-   PicoBuilder to make a container with desired behavior.

Changes:

-   Java 5 style (use of generics, varargs, among others)
-   Large scale Refactoring of packages and class names
-   Fluent interface for adding of components to a container

<span class="callout">April, 2007: Work begins on PicoContainer 2.x. It is substantially a refactoring of the PicoContainer 1.x codeline using the inbuilt features of Intellij IDEA and Eclipse. Using the existing unit tests as a guide, many small commits were made to the code-base to take PicoContainer in a more JDK 1.5 direction, and add in other features not seen in competing containers like parameter name access. Along the way, the API was simplified and parts of NanoContainer were bought closer to the core of PicoContainer.</span>

**Mar 18, 2007**

PicoContainer 1.3 released (113K jar)

**January 19, 2006**

PicoContainer 1.2 released (112K jar)

**November 4, 2004**

PicoContainer 1.1 released (75K jar)

**June 5, 2004**

PicoContainer 1.0 released (49K jar)

**February 21, 2004**

PicoContainer 1.0 beta-5 (50K jar)

PicoContainer split into a core jar and 'gems' for things less often used

**January 23, 2004**

PicoContainer 1.0 beta-4 (64K jar)

**November 03, 2003**

PicoContainer 1.0 beta-3 (52K jar)

**September 10, 2003**

PicoContainer 1.0 beta-2 (44K jar)

**August 14, 2003**

PicoContainer 1.0 beta-1 (39K jar)

**July 17, 2003**

PicoContainer 1.0 alpha-2 (36K jar)

Setter Injection added (this was after the Pico team learned of the Spring-Framework project, Spring did not add Constructor Injection until the run up to 1.0-Milestone-3 in November '03 and 'autowiring' in the run up to 1-0-Milestone-4 in January of '04)p. **June 29, 2003**

PicoContainer 1.0 alpha-1 (25K jar)

Generalized Constructor Injection available for the first time. This was very much PicoContainer's idiom. ATG Dynamo had something similar for web components, but Pico released this first as a general embedable API for the Java world. Goals for Pico are a) lightweight/embeddable, b) no XML or meta-data - 'autowiring' only, c) no lock-in, d) popularize Constructor Injection over Avalon and OSGi style of IoC, e) no dependencies for the container itself.

**June 1, 2003**

PicoContainer experimentation begins

Paul and Aslak pair on a precursor to PicoContainer called 'xContainer'. [See the source](http://paulhammant.com/downloads/xContainer.zip)
