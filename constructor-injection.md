---
layout: article
name: Constructor Injection
---

h2. Overview

p. Constructor Injection is a "Dependency Injection":injection.html variant where a component gets all its dependencies via its constructor.

p. The most important benefits of Constructor Injection are:

* It makes a strong dependency contract
* It makes testing easy, since dependencies can be passed in as "Mock Objects":mock-objects.html 
* It is very succinct in terms of lines of code
* Classes that rely on Constructor Injection are generally "Good Citizens":patterns.html 
* A dependency may be made immutable by making the dependency reference _final_ 

p.  !/images/information.gif! Martin Fowler explains "Constructor Injection":http://www.martinfowler.com/articles/injection.html#ConstructorInjectionWithPicocontainer in more detail.


h2. Origin

p. In 2003, "Rachel Davies":http://www.twelve71.com/rachel/ , was reviewing book "Java Open Source Programming":http://www.wiley.com/WileyCDA/WileyTitle/productCd-0471463620.html for Joe Walnes. She left a Fermat-like margin note when looking at a code sample that used Setter Injection: "Why not use constructors ?". Brilliant and simple.Using constructors per se, is an ordinary OO feature, but having a container that works out what to inject where amongst a list of constructor arguments is something that is useful too. Read more about the "back history":inversion-of-control-history.html .

h2. Example

{% highlight java %}
public class Apple {
  private final Orange orange;
  private final Pear pear;
  private final Banana banana;
  public Apple(Orange orange, Pear pear, Banana banana) {
    this.orange = orange; 
    this.pear = pear; 
    this.banana = banana; 
  } 
  // methods 
}
{% endhighlight %}

p. Note, for this there is no need to declare needs in any other way. No interfaces, no doclet tags, no external XML. Just your simple component(s) and PicoContainer. No need for post assembly/config initialization either. If it is constructed (not withstanding some asserts on nulls) it has its needs satisfied. Components need not be interface/implementation separated. This is the coder's choice.

h3. Using Constructor Injector Components Without a Container.

p. The component can be used directly, without any container. The missing dependency scenario is not an issue since it is impossible to instantiate an object without all dependencies being satisfied.

{% highlight java %}
Apple apple = new Apple(myOrange, aPear, theBanana);
{% endhighlight %}

h3. Using Constructor Injector Components With a PicoContainer

{% highlight java %}
pico = new DefaultPicoContainer(new ConstructorInjection());
pico.addComponent(Apple.class); // etc Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. Constructor Injection, is a default too (via AdaptiveInjection):

{% highlight java %}
pico = new DefaultPicoContainer();
pico.addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class);
{% endhighlight %}

p. The PicoContainer team recommends Constructor Injection over other types - the project was started to pioneer this approach. With PicoContainer is no need to mark up the constructor with an annotation. Having more than one constructor is OK too, as PicoContainer will try to use the one with the most arguments and fall back to ones with fewer if it cannot satisfy the longer ones.

p. The component factory for this is *ConstructorInjection* . It only handles constructor injection types of components. Factory *AdaptiveInjection* defaults to constructor injection, after checking first to see it the component in question is an Annotated Method or Field type (see below).

p(callout). Constructor Injection is idiomatic PicoContainer - choose this style over all others if you can - especially if you are starting out with Dependency Injection.

h2. Summary

p. PicoContainer was the first lightweight container to support and popularize this for of dependency injection. PicoContainer also supports "Setter Injection":setter-injection.html that is idiomatic of the Spring Framework.

