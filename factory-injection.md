---
layout: article
name: Factory Injection
---

## Overview

This allows an instance to be injected via a factory that is aware of the thing it is injecting into and can make a custom instance just for that injectee. This type of injection is only possible if you add an Adapter for it directly that subclasses **FactoryInjection<T>**

```java
public class Apple {
  private final Log log;
  private final Orange orange;
  private final Pear pear;
  private final Banana banana;
  public Apple(Orange orange, Pear pear, Banana banana, Log log) {
  this.orange = orange; this.pear = pear; this.banana = banana; this.log = log; 
 } 
 // methods 
}
```

## Usage

```java
public class LogInjector extends FactoryInjector<Log>{ 
  public Log getComponentInstance(PicoContainer container, final Type into) throws PicoCompositionException { 
    return LogFactory.getLog((Class) into); 
  } 
}
...
pico = new DefaultPicoContainer(new ConstructorInjection());
pico.addComponent(Apple.class);
pico.addAdapter(new LogInjector()); 
// etc Apple apple = pico.getComponent(Apple.class);
```

We have implementations in this style for Log4j, Commons-Logging, Java-Logging and SLF4J. They are in the org.picocontainer.gems.injectors package.
