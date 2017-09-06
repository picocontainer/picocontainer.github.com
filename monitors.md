---
layout: article
name: Monitoring Component Events
---

p. A ComponentMonitor if injected into DefultPicoContainer allows for certain events to be monitored for the whole container. These are:

* instantiating - referring to a component
* instantiated - referring to a component
* instantiationFailed - referring to a component
* invoking - referring to a method on a component
* invoked - referring to a method on a component
* invocationFailed - referring to a method on a component
* lifecycleInvocationFailed - referring to start/stop/dispose methods on a component
* noComponentFound - referring to getComponent(..) on a PicoContainer instance.

p. Refer to the "API documentation":http://picocontainer.org/javadoc/core/org/picocontainer/ComponentMonitor.html for ComponentMonitor.

p. Using a ComponentMonitor is quite easy, you pass it into the constructor of DefaultPicoContainer. It will also be passed to child containers:

parent = new DefaultPicoContainer(new MyComponentMonitor()); child = parent.makeChildContainer(); // will use the same monitor impl. ... public class MyComponentMonitor implements ComponentMonitor { // etc }h2. Making your own ComponentMonitor implementation

h3. Return Values

p.  _Instantiating_ returns a Constructor. An implementor of ComponentMonitor can choose to return a different Constructor reference. This is one way that implementations can be replaced or decorated (AOP style) with additional/changed functionality.

p.  _noComponentFound_ returns an Object (default of null in most cases). An implementor can choose to return an instance instead. That instance should be of a type that makes sense to the component that was being sought. The getComponent(..) would have otherwise failed, but this hypothetical ComponentMonitor intercepted that failure and provided something that makes sense.

h3. General rules

# ComponentMonitor can generally be chained together. Meaning you can use them in combination.
# Most have NullComponentMonitor (see below) as their end stop.
# Any method on ComponentMonitor is open for interpretation. That means they can throw exceptions as well as return different values, making them modify PicoContainer's behavior.


h2. Supplied ComponentMonitor implementations

h3. Logging or text-centric varieties of ComponentMonitor

p. The PicoContainer team are ideologically opposed to a forced choice of logging framework. If you're hell bent on logging, chose from a ComponentMonitor implementation below:

*  "CommonsLoggingComponentMonitor":http://picocontainer.org/javadoc/gems/org/picocontainer/gems/monitors/CommonsLoggingComponentMonitor.html (Gems) // uses Apache's Commons-Logging
*  "Log4JComponentMonitor":http://picocontainer.org/javadoc/gems/org/picocontainer/gems/monitors/Log4JComponentMonitor.html (Gems) // uses Apache's Log4J
*  "Slf4jComponentMonitor":http://picocontainer.org/javadoc/gems/org/picocontainer/gems/monitors/Slf4jComponentMonitor.html (Gems) // uses Simple Logging Framework for Java (SLF4J)
*  "ConsoleComponentMonitor":http://picocontainer.org/javadoc/core/org/picocontainer/monitors/ConsoleComponentMonitor.html // prints to System.out
*  "WriterComponentMonitor":http://picocontainer.org/javadoc/core/org/picocontainer/monitors/WriterComponentMonitor.html // prints to a supplied OutputStream

h3. NullComponentMonitor

p.  "NullComponentMonitor":http://picocontainer.org/javadoc/core/org/picocontainer/monitors/NullComponentMonitor.html is PicoContainer's default ComponentMonitor. It generally fits the NullObject pattern, but it does add behavior. Specifically if it encounters a lifecycleInvocationFailed(..) method invocation, it throws a PicoLifecycleException in response. If you use something other than NullComponentMonitor (bear in mind the default 'end stop' for most others is also NullComponentMonitor) then you could change the way PicoContainer responds to components it cannot start, stop of dispose of.

h3. LifecycleComponentMonitor

p.  "LifecycleComponentMonitor":http://picocontainer.org/javadoc/core/org/picocontainer/monitors/LifecycleComponentMonitor.html is a ComponentMonitor that stores life-cycle failures and rethrows them later if rethrowLifecycleFailuresException() is called.

h3. DotDependencyGraphComponentMonitor (Gems)

p.  "DotDependencyGraphComponentMonitor":http://picocontainer.org/javadoc/gems/org/picocontainer/gems/monitors/DotDependencyGraphComponentMonitor.html is a ComponentMonitor that makes a dot graph for instantiations of components. You would use it to visualize a graph of the injections in a container or tree of PicoContainers after they have run for some time. Call getClassDependencyGraph() on the monitor instance when you are ready to retrieve the Dot representation. See the "Graphviz":http://www.graphviz.org/ website for applications that can read the .dot format and visualize.

h3. Prefuse (Gems)

p. In a similar way to the DotDependencyGraph above a visualization using "Prefuse":http://prefuse.org/ is possible by coupling a "PrefuseDependencyGraph":http://picocontainer.org/javadoc/gems/org/picocontainer/gems/monitors/prefuse/PrefuseDependencyGraph.html instance to a "ComponentDependencyMonitor":http://picocontainer.org/javadoc/gems/org/picocontainer/gems/monitors/ComponentDependencyMonitor.html one. Call getGraph() to retrieve the graph for use with Prefuse


h2. Composers

p. ComposingMonitor is an implementation of the Monitor design that works specifically in conjunction with noComponentFound(..) to allow greater collaboration in the supply of components for the purposes of injection. By definition it is only going to work if the type or object key that's required is missing from the container.

MutablePicoContainer pico = new DefaultPicoContainer(new ComposingMonitor( /* varargs of composer instances */));
p. If more than one is specified in the varargs above, then the first in the list to actually return non-null will be used. It is also possible for a fallback component monitor to be specified:

{% highlight java %}
ComponentMonitor otherCM = ... 
MutablePicoContainer pico = new DefaultPicoContainer(new ComposingMonitor(otherCM, /* varargs of composer instances */));
{% endhighlight %}

p. See "Regex Composer":regex.html 

h2. Future work

p. Implementations that:

* Record component injections and play them back later (optimized web frameworks)

