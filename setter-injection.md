---
layout: article
name: Setter Injection
---

## Overview

Setter Injection is where the container or embedder hands dependencies to a component via setter methods after instantiation.

## Example

Consider a plain class that has one private field and one setter for each dependency that it needs.

```java
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
```

Some other technologies marked up the need with a doclet tag:

```java
... 
/** 
  * @config name="orange"
  */ 
public setOrange(Orange orange) {
  this.orange = orange; 
} ...
```

The container use the meta-information to resolve all the dependencies. Components need not be interface/implementation separated. That would be the developer's choice.

### Using Setter Injector Components Without a Container.

Setter Injection components can be used directly, without any container. The component-using class will continue to compile, but at run time it will be apparent that there are missing dependencies. The downside of this is that a developer may miss a setXXX(..) method invocation if they are using the component directly. That is fairly small as a risk as it would clearly be caught in the development cycle. Caught in the development cycle, but maybe obscurely so with a NullPointerException.

Apple apple = new Apple(); apple.setPear(myPear);h3. Using Setter Injection with PicoContainer

The component factory for this is **SetterInjection** . It only handles setter injection types of components.

```java
pico = new DefaultPicoContainer(new SetterInjection());
pico.addComponent(Apple.class);
pico.addComponent(Banana.class);
pico.addComponent(Pear.class);
pico.addComponent(Orange.class); // etc Apple apple = pico.getComponent(Apple.class);
```

Setter methods (those prefixed with 'set') may not be your preferred choice. You can force a different prefix to be choosable in PicoContainer, such as 'init' or 'inject'. If you want to use an prefix other than 'set'...

```java
pico = new DefaultPicoContainer(new SetterInjection("mySynonymForSet"));
pico.addComponent(Apple.class); // etc
Apple apple = pico.getComponent(Apple.class)
```

Factory **AdaptiveInjection** can also handle setter injection types, though it requires that the component was registered with the [property](properties.html) 'SDI' in order to activate the Setter Injection functionality. **AdaptiveInjection** will also fall through to constructor injection if there is no SDI property.

## References + Comparison

[Setter Injection](http://www.martinfowler.com/articles/injection.html#SetterInjectionWithSpring) is a [Dependency Injection](injection.html) variant where an object gets all dependencies via setter methods. PicoContainer support this with [SetterInjection](/javadoc/core/org/picocontainer/injectors/SetterInjection.html) class, but the PicoContainer team recommends [Constructor Injection](constructor-injection.html) instead.

The disadvantage of Setter Injection there is the possibility to forget to inject some of the dependencies, and the component fail later because of that unset dependency.
