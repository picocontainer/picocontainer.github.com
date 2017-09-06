---
layout: article
name: Container Dependency
---

## Symptoms

Classes that depend on the container.

Consider the following example. We have a class BImpl that requires an A instance. It declares the dependency on the container so it can look up that A:

```java
public interface A { } 

public class AImpl implements A { } 

public class BImpl implements B {
  private final A a;
  public BImpl(PicoContainer pico) { 
    a = (A) pico.getComponentOfType(A.class); 
    /* alternatively: a = (A) pico.getComponent("a"); */ 
  } 
}
```

The usage would probably look similar to:

```java
MutablePicoContainer pico = new DefaultPicoContainer();
pico.addComponent("a", AImpl.class);
pico.addComponent("b", BImpl.class);
pico.addComponent(pico); 
... 
B b = (B) pico.getComponent("b");
```

It will work, but it is an anti-pattern.

The reasons why the above implementation of BImpl is an antipattern are:

-   It introduces an unneeded dependency from BImpl to the container, breaking the core principal of Inversion of Control
-   This makes BImpl harder to unit test
-   B assumes that the container has a registered an A. As a result, B won't fail fast if it has not. Instead, a will reference null, and BImpl will fail later.

## Causes

-   Missing the historical context of PicoContainer, Dependency Injection and Inversion of Control.
-   Love of 'God class' designs.

## What to do

The simple and elegant solution to this anti-pattern is not to complicate the world more than it is.

Here is how it should be:

```java
public class BImpl implements B {
  private final A a; BImpl(A a) {
    this.a = a; 
  } 
}
```

PicoContainer will figure out that BImpl needs an A instance, and will pass in the AImpl, as this is an implementation of A.

## The One Major Exception

When you go to integrate all your components into a single application, SOMETHING is going to have to wire all these components together. In Pico-land, we call that the"bootstrap"script. (Script, is a loose term, it could easily be a reference to PicoContainer inside a class' main() function.) The pattern of this code will often look like this:

```java
public class Bootstrap {
  private PicoContainer pico = new PicoBuilder().withLifecycle().withCaching().build();
  public static void main(String[] args) { 
    Bootstrap bootstrap = new BootStap(); 
    bootstrap.start();
  }
  public void start() { 
    /* pico.addComponent(A.class), pico.addComponent(B.class) */ 
    /* etc. */ 
    pico.start(); 
    //enter some sort of event loop. 
    //Event loop broken. 
    pico.stop();
  }
  public void stop() {
    pico.stop();
    pico.dispose(); 
  } 
}
```

PicoContainer has a sample project called"bootstrap"that does just this sort of thing. For web applications, Pico uses a WebappContextListener to bootstrap an application-level container, and a Servlet filter to bootstrap a request-level container.
