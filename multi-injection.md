---
layout: article
name: Multiple Injection
---

h3. Overview

p. This is where a component has is a blend of one or more of Constructor, Setter, Method, Annotated Field etc:

{% highlight java %}
public class Apple {
  private Orange orange;
  private Pear pear; @Inject private Banana banana;
  public inject(Orange orange) {
    this.orange = orange;
  }
  public void inject(Pear pear) {
    this.pear = pear; 
  } 
  // other methods 
}
{% endhighlight %}

h3. Usage

{% highlight java %}
pico = new DefaultPicoContainer(new MultiInjection();
pico.addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. In the case above, Orange comes in through the constructor, Pear by method injection and Banana is via Annotated Field Injection.

p. The component factory for this is *MultiInjection* .

