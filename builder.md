---
layout: article
name: Building Containers
---

p. Rather than new up a PicoContainer instance with the right ComponentFactory, LifecycleStategy and ComponentMonitor, your can use PicoBuilder for convenience. Refer to Martin Fowler's "'Fluent Interface'":http://www.martinfowler.com/bliki/FluentInterface.html article, for some of the motivation behind this class.

p. Some simple examples :

{% highlight java %}
pico = new PicoBuilder().build();
 pico.addComponent(Apple.class);
pico = new PicoBuilder().withCaching().build();
 pico.addComponent(Apple.class);
pico = new PicoBuilder().withCaching().build();
 pico.addComponent(Apple.class);
pico = new PicoBuilder().withHiddenImplementations().build();
 pico.addComponent(Apple.class);
{% endhighlight %}

p. More variations for behaviors :

{% highlight java %}
pico = new PicoBuilder().withLifecycle().withConsoleMonitor().build();
pico.addComponent(Apple.class);
pico = new PicoBuilder().withMonitor(ConsoleComponentMonitor.class).build();
pico.addComponent(Apple.class);
import static org.picocontainer.injectors.Injectors.SDI;
import static org.picocontainer.behaviors.Behaviors.caching;
import static org.picocontainer.behaviors.Behaviors.implementationHiding;
import static org.picocontainer.behaviors.Behaviors.synchronizing;
...
pico = new PicoBuilder(SDI()).withBehaviors(caching(), implementationHiding(), synchronizing()).build();
pico.addComponent(Apple.class);
{% endhighlight %}

p. Setting a parent container :

{% highlight java %}
child = new PicoBuilder(parentContainer).build(); child.addComponent(Apple.class);
{% endhighlight %}

p. Specifying an injection type:

{% highlight java %}
pico = new PicoBuilder().withAnnotationInjection().build();
 pico.addComponent(Apple.class);
{% endhighlight %}

p. Specifying an injection type a different way:

{% highlight java %}
import static org.picocontainer.injectors.Injectors.SDI;
...
pico = new PicoBuilder(SDI()).build();
pico.addComponent(Apple.class);
{% endhighlight %}

p. A custom container component used by a custom ComponentBehavior (though could be used by ComponentMonitor or LifecycleStrategy as easily):

{% highlight java %}
pico = new PicoBuilder() 
  .withCustomContainerComponent(new MyQuantumPhysicsConnector())
  .withComponentFactory(MyQuantumPhysicsConnectionNeedingComponentBehavior.class)
  .build();
pico.addComponent(Apple.class);
{% endhighlight %}

p. A custom container implementation (DefaultPicoContainer is the default):

{% highlight java %}
MyCustomPicoContainer pico = (MyCustomPicoContainer) new PicoBuilder().implementedBy(MyCustomPicoContainer.class).build();
 pico.addComponent(Apple.class);
{% endhighlight %}
