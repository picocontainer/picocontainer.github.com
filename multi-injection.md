---
layout: article
name: Multiple Injection
---

## Overview

This is where a component has is a blend of one or more of Constructor, Setter, Method, Annotated Field etc:

```java
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
```

## Usage

```java
pico = new DefaultPicoContainer(new MultiInjection();
pico.addComponent(Apple.class); // etc 
Apple apple = pico.getComponent(Apple.class);
```

In the case above, Orange comes in through the constructor, Pear by method injection and Banana is via Annotated Field Injection.

The component factory for this is **MultiInjection** .
