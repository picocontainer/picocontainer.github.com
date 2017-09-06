---
layout: article
title: Injection via Providers
---

A Provider class is a class with a method named provide that can participate in injection

Consider the following example of a provider:

```java
public class Chocolatier implements Provider { 
  public Chocolate provide(CocaoBeans cocaoBeans) {  
    return new Chocolate(cocaoBeans);  
  } 
}
```

And its example usage:

```java
pico.addAdapter(new ProviderAdapter(new Chocolatier()));
pico.addComponent(NeedsChocolate.class);
```

In this example, 'NeedChocolate' and other components added to the PicoContainer instance can depend on a 'Chocolate' instance. Such dependencies are declared in the usual way (Constructor args etc) and PicoContainer will call the provider method to create the Chocolate instance. For the class in question, there must be only one provide method, it must return something other than void, and it can take zero, one or many arguments. Those arguments are themselves dependencies that will be satisfied by PicoContainer at time of invocation.

You will note that the example above shows a transparent class (no implements/extends/throws/imports from PicoContainer's API). It is also possible to extend the Provider class directly to do the same:

```java
public class Chocolatier2 extends ProviderAdapter { 
  public Chocolate provide(CocaoBeans cocaoBeans) { 
    return new Chocolate(cocaoBeans);
  } 
}
...
pico.addAdapter(new Chocolatier());
pico.addComponent(NeedsChocolate.class);
```

It may be that you want more than one dependency for the provide method. If that is the case and you want to leverage parameter names for disambiguation. To do this for the compositional case, choose the constructor to ProviderAdapter that has a boolean flag (set to true). To do this for the extends case, override the useNames() method.

<span class="callout">As of release 2.10, ProviderAdapter has a second constructor that takes a LifecycleStrategy so any provided instances may participate in lifecycles.</span>
