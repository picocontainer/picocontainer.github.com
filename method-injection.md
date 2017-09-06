---
layout: article
name: Method Injection
---

### Overview

This is where a component has an empty constructor and gets <span style="font-weight: bold;;">all</span> its dependencies injected into single method after instantiation:

```java
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
```

### Usage

```java
pico = new DefaultPicoContainer(new MethodInjection();
pico.addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class);
```

Custom injection method prefix:

```java
pico = new DefaultPicoContainer(new MethodInjection("mySynonymForInject");
pico.addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class);
```

Via the default AdaptiveInjection Method injection, via a characteristic:

```java
pico = new DefaultPicoContainer();
pico.as(Characteristics.METHOD\_INJECTION).addComponent(Apple.class); // etc
Apple apple = pico.getComponent(Apple.class);
```

The method name needs be 'inject' unless overridden in the InjectionFactory.

The component factory for this is **MethodInjection** . It only handles method-injection types of components.

Additionally the default component factory **AdaptiveInjection** can also handle method-injection types, but only if the METHOD\_INJECTION characteristic is specified.
