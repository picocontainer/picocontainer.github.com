---
layout: article
name: Composite Injection
---

h3. Overview

p. This is also where a component has is a blend of one or more of Constructor, Setter, Method, Annotated Field. The difference is that you know precisely what your injection design is and want to tune specifically for that, or you are some way off the defaults supported by *MultiInjection* 

{% highlight java %}
public class Apple {
  private Orange orange;
  private Pear pear; 
  @FruitNeeded private Banana banana;
  public Apple(Orange orange) {
    this.orange = orange;
  }
  public void fruitNeeded(Pear pear) {
    this.pear = pear; 
  } 
  // other methods 
}
{% endhighlight %}

h3. Usage

{% highlight java %}
pico = new DefaultPicoContainer(
    new CompositeInjection( 
      new ConstructorInjection(), 
      new AnnotatedFieldInjection(FruitNeeded.class), 
      new MethodInjection("fruitNeeded")));
pico.addComponent(Apple.class); 
// etc 
Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. In the case above, Orange comes in through the constructor, Pear by method injection and Banana is via Annotated Field Injection.

p. The component factory for this is *CompositeInjection* .

