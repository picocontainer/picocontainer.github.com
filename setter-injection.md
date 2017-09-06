---
layout: article
name: Setter Injection
---

h2. Overview

p. Setter Injection is where the container or embedder hands dependencies to a component via setter methods after instantiation.

h2. Example

p. Consider a plain class that has one private field and one setter for each dependency that it needs.

{% highlight java %}
public class Apple {
  private Orange orange;
  private Pear pear;
  private Banana banana;
  public setOrange(Orange orange) {
    this.orange = orange;
  }
  public setPear(Pear pear) {
    this.pear = pear;
  }
  public setBanana(Banana banana) {
    this.banana = banana;
  }
  public void initialize() { 
    // all setXXXs are now done :-) 
  } 
  // other methods 
}
{% endhighlight %}

p. Some other technologies marked up the need with a doclet tag:

{% highlight java %}
... 
/** 
  * @config name="orange"
  */ 
public setOrange(Orange orange) {
  this.orange = orange; 
} ...
{% endhighlight %}

p. The container use the meta-information to resolve all the dependencies. Components need not be interface/implementation separated. That would be the developer's choice.

h3. Using Setter Injector Components Without a Container.

p. Setter Injection components can be used directly, without any container. The component-using class will continue to compile, but at run time it will be apparent that there are missing dependencies. The downside of this is that a developer may miss a setXXX(..) method invocation if they are using the component directly. That is fairly small as a risk as it would clearly be caught in the development cycle. Caught in the development cycle, but maybe obscurely so with a NullPointerException.

Apple apple = new Apple(); apple.setPear(myPear);h3. Using Setter Injection with PicoContainer

p. The component factory for this is *SetterInjection* . It only handles setter injection types of components.

{% highlight java %}
pico = new DefaultPicoContainer(new SetterInjection());
pico.addComponent(Apple.class);
pico.addComponent(Banana.class);
pico.addComponent(Pear.class);
pico.addComponent(Orange.class); // etc Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. Setter methods (those prefixed with 'set') may not be your preferred choice. You can force a different prefix to be choosable in PicoContainer, such as 'init' or 'inject'. If you want to use an prefix other than 'set'...

{% highlight java %}
pico = new DefaultPicoContainer(new SetterInjection("mySynonymForSet"));
pico.addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class)
{% endhighlight %}

p. Factory *AdaptiveInjection* can also handle setter injection types, though it requires that the component was registered with the "property":properties.html 'SDI' in order to activate the Setter Injection functionality. *AdaptiveInjection* will also fall through to constructor injection if there is no SDI property.

h2. References + Comparison

p.  "Setter Injection":http://www.martinfowler.com/articles/injection.html#SetterInjectionWithSpring is a "Dependency Injection":injection.html variant where an object gets all dependencies via setter methods. PicoContainer support this with "SetterInjection":/javadoc/core/org/picocontainer/injectors/SetterInjection.html class, but the PicoContainer team recommends "Constructor Injection":constructor-injection.html instead.

p. The disadvantage of Setter Injection there is the possibility to forget to inject some of the dependencies, and the component fail later because of that unset dependency.

