---
layout: article
name: Component Properties
---

p. DefaultPicoContainer allows for some properties to be set per component at the time each is added. A convenience class called Characteristics lists the supplied properties.

p. Properties can be set for a component in a method 'as' on MutablePicoContainer. If that is the case then the properties will affect the *next* component added only.

p. They can also be set in a more persistent manner for the MutablePicoContainer in question, using the 'change' method. If so, they will affect all subsequent additions of component.

p. Some examples :

{% highlight java %}
import static org.picocontainer.Characteristics.SDI;
import static org.picocontainer.Characteristics.CACHE;
...
pico = new DefaultPicoContainer();
pico.as(CACHE).addComponent(Apple.class);
pico.as(CACHE, SDI).addComponent(Pear.class);
import static org.picocontainer.Characteristics.SDI;
import static org.picocontainer.Characteristics.CACHE;
...
pico = new DefaultPicoContainer();
pico.change(CACHE).addComponent(Apple.class);
pico.change(SDI).addComponent(Pear.class);

pico = new DefaultPicoContainer();
pico.as(Characteristics.CACHE).addComponent(Apple.class);
pico.as(Characteristics.CACHE, Characteristics.SDI).addComponent(Pear.class);

pico = new DefaultPicoContainer();
pico.change(Characteristics.CACHE).addComponent(Apple.class);
pico.change(Characteristics.SDI).addComponent(Pear.class);
{% endhighlight %}

p. Some characteristics are mutually exclusive. For example CDI, SDI. Meaning the last one set via 'as' or 'change' rules. Others are additive like CACHE and HIDE_IMPL. The order of these is not important.

h3. Appropriate Behavior and Injection Factories.

p. Every characteristics set for a component must be handled by a BehaviorFactory or InjectionFactory that recognizes it. They are typically chained together. DefaultPicoContainer's default BehaviorFactory and InjectionFactory are AdaptiveBehaviorFactory and AdaptiveInjectionFactory. These can handle CDI and SDI as well as CACHE and HIDE_IMPL.

p. If you setup DefaultPicoContainer with a BehaviorFactory/InjectionFactory combination that is unaware of the characteristics you subsequently use, Pico will object by way of an exception as you add the component to the container. See "Unprocessed Properties":/help/unprocessed-properties-help.html .

p. Supplied properties (from org.picocontainer.Characteristics) -

* CDI - added components will be Constructor Dependency Injection.
* SDI - added components will be Setter Dependency Injection.
* METHOD_INJECTION - added components will be Method Injection (a method called inject will list the dependencies).
* NO_CACHE - no caching for the component to be added (in the case of Caching being the container's default).
* CACHE - cache the component to be added.
* NO_JMX - JMX is not to be published for the component.
* SYNCHRONIZE - ensure that the component to be added will be created only once, even if multiple threads are seeking the component simultaneously.
* LOCK - an alternate to SYNCHRONIZE that leverages java.lang.concurrent.Lock for the same purpose.
* SINGLE - synonym for CACHE.
* HIDE_IMPL - hide the implementation of the component to be added.
* NO_HIDE_IMPL - don't hide the implementation of the component to be added (in the case of ImplementationHiding being the container's default).
* PROPERTY_APPLYING - allow for the application of properties for the component in question.
* AUTOMATIC - instantiate the component when start() is called on the container even if component is not Startable nor depended on.
* USE_NAMES - use the parameter names of the component's constructor (or injection method) for the binding of configuration or components.

h3. Custom Properties

p. You can make your own properties quite easily - check out the source to "Characteristics.java":http://svn.codehaus.org/picocontainer/java/2.x/trunk/pico/container/src/java/org/picocontainer/Characteristics.java and the classes that refer to it in PicoContainer (various implementations of ComponentFactory and ComponentAdapter).

