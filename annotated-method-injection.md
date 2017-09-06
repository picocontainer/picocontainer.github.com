---
layout: article
name: Annotated Method Injection
---

h3. Overview

p. This is where a component has an empty constructor and gets its dependencies injected into annotated methods after instantiation:

{% highlight java %}
public class Apple {
  private Orange orange;
  private Pear pear;
  private Banana banana; 

  @Inject 
  public injectOrange(Orange orange) {
    this.orange = orange; 
  } 
  @Inject 
  public setPear(Pear pear) {
    this.pear = pear; 
  } 
  @Inject public provideBanana(Banana banana) {
    this.banana = banana; 
  } 
  // other methods 
}
{% endhighlight %}

h3. Usage

{% highlight java %}
pico = new DefaultPicoContainer(new AnnotatedMethodInjection();
pico.addComponent(Apple.class); 
// etc 
Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. With an custom annotation instead of PicoContainer's @Inject

{% highlight java %}
pico = new DefaultPicoContainer(new AnnotatedMethodInjection(MyInjectAnnotaton.class);
pico.addComponent(Apple.class); 
// etc 
Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. The method (whatever its name) needs an @Inject annotation. That is from our codebase (org.picocontainer.Inject).

p. The component factory for this is *AnnotatedMethodInjection* . It only handles method-annotation injection types of components.

p. Additionally the default component factory *AdaptiveInjection* can also handle method-annotation injection types, if the @Inject annotation from PicoContainer's code-base is used as the marker for injection. AdaptiveInjection will also fall through to constructor injection if there is no recognized annotation.

