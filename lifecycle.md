---
layout: article
name: Component Lifecycle
---

Inversion of Control is three things: component dependencies and configuration, but also Component Lifecycle.

After instantiation, if the component warrants it, a 'start' stage may be required. More specifically, if a container has injected and instantiated all components in a set, one or more of them may require starting in the same order they were instantiated. Later, in reverse order, the same component(s) may require stopping. In fact start and stop may happen more than one for the life of an application. Disposal may happen once only, before the component is eligible for garbage collection.

**Lifecycle is really only going to work for PicoContainers that are also caching component instances. Caching was a default in PicoContainer 1.x, but is not for 2.x - be warned!**

Thus lifecycle implies three methods: start, stop or dispose

In PicoContainer we allow a pluggable LifecycleStrategy, listed here:

### Startable (the default)

Our own interface for startable. We wish it were in the JDK, because we're big into making components unencumbered by the trappings of containment. In English: we'd rather not make components implement/extend/throw anything from our framework. Its a 'transparency' thing.

Here's an example of components fitting that ideal:

```java
public class Apple implements Startable { 
  public void start() { 
    // listen on socket, start thread etc. 
  } 
  public void stop() { 
    // stop listening on socket, kill thread etc. 
  } 
}
...
pico = new DefaultPicoContainer(new StartableLifecycleStrategy());
pico.addComponent(Apple.class);
pico.start(); // start gets called 
Apple a = pico.getComponent(Apple.class);
```

The StartableLifecycleStrategy can be extended if you prefer your own interface for Startable. Just make a subclass, override getStartableInterface() and getDisposableInterface(), possibly also one or more of the getStartMethodName(), getStopMethodName() or getDisposeMethodName() methods.

### Reflection based start/stop/dispose

This works without an lifecycle interface (as above). Instead it works via reflection and configurable method names.

```java
public class Apple { 
  public void start() { 
    // listen on socket, start thread etc. 
  } 
  public void stop() { 
    // stop listening on socket, kill thread etc. 
  } 
}
...
pico = new DefaultPicoContainer(new ReflectionLifecycleStrategy());
pico.addComponent(Apple.class);
pico.start(); // start gets called, and propagates to components 
Apple a = pico.getComponent(Apple.class);
```

By default, ReflectionLifecycleStrategy looks for methods named 'start', 'stop' and 'dispose'. If you have other synonyms for start/stop/dispose, just use the right constructor for ReflectionLifecycleStrategy class and provide them via the cons.

### Java EE 5 annotation based start/dispose

This works without an interface, but with annotations in front of the designated methods

```java
public class Apple { 
  @PostConstruct 
  public void startUp() { 
    // listen on socket, start thread etc. 
  } 
  @PreDestroy 
  public void allOver() { 
	// stop listening on socket, kill thread etc. 
  } 
}
...
pico = new DefaultPicoContainer(new JavaEE5LifecycleStrategy());
pico.addComponent(Apple.class);
pico.start(); // start gets called, and propagates to components 
Apple a = pico.getComponent(Apple.class);
```

These annotations are supplied with Java 6 (and above), but come in a jar for Java 5 and below. See <http://mvnrepository.com/artifact/javax.annotation/jsr250-api>

### No Lifecycle

DefaultPicoContainer does sets StartableLifecycleStrategy by default. You can specify NullLifecycleStrategy instead if you are sure that no components honor any lifecycle concept, and you want a faster operation by some infinitesimal amount. That or you want to deliberately ignore lifecycle strategies that do exist, though that seems to be looking for trouble.

### Lazy Lifecycles

By default PicoContainer will start all startable (or appropriate) components when pico.start() is called. Lazy lifecycle is where the component in question is not started when pico.start() is called, but when the first access to it happens.

It is available for any concept of startable component, but can only happen if you override the the isLazy() method of those classes:

```java
pico = new DefaultPicoContainer(new StartableLifecycleStrategy() { 
  @Override 
  public boolean isLazy(ComponentAdapter<?> adapter) { 
    return true; // or something more conditional 
  } 
});
pico.addComponent(Apple.class);
pico.start(); // start does not get called yet 
Apple a = pico.getComponent(Apple.class); // start gets called now as part of getComponent(..) 
pico.stop(); // stop all components that are startable whether lazy or not
```

### Custom Lifecycles

Write a class that implements LifecycleStrategy, there are just four methods to implement. See LifecycleStrategy.

### Where Next?

<span class="callout">The [Disambiguation](disambiguation.html) page outlines strategies for dealing with choosing which of two potential injectables for one component</span>
