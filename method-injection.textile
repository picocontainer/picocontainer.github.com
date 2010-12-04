---
layout: article
name: Method Injection
---

h3. Overview

p. This is where a component has an empty constructor and gets %{font-weight: bold;}all% its dependencies injected into single method after instantiation:

{% highlight java %}
public class Apple {
  private Orange orange;
  private Pear pear;
  private Banana banana;
  public inject(Orange orange, Pear pear, Banana banana) {
    this.orange = orange; 
    this.pear = pear; 
    this.banana = banana; 
  } 
  // other methods 
}
{% endhighlight %}

h3. Usage

{% highlight java %}
pico = new DefaultPicoContainer(new MethodInjection();
pico.addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. Custom injection method prefix:

{% highlight java %}
pico = new DefaultPicoContainer(new MethodInjection("mySynonymForInject");
pico.addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. Via the default AdaptiveInjection Method injection, via a characteristic:

{% highlight java %}
pico = new DefaultPicoContainer();
pico.as(Characteristics.METHOD_INJECTION).addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. The method name needs be 'inject' unless overridden in the InjectionFactory.

p. The component factory for this is *MethodInjection* . It only handles method-injection types of components.

p. Additionally the default component factory *AdaptiveInjection* can also handle method-injection types, but only if the METHOD_INJECTION characteristic is specified.

