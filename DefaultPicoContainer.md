---
layout: article
name: DefaultPicoContainer
---

DefaultPicoContainer is the main workhorse container.

Here is an example of use:

```java
DefaultPicoContainer dpc = new DefaultPicoContainer(); 
dpc.addComponent(Foo.class, FooImpl.class); 
Foo foo = dpc.getComponent(Foo.class)
```

Parameters in its constructors can be:

-   [Behavior Factories or Component Factories](behaviors.html)
-   [Lifecycle Strategies](lifecycle.html)
-   [Component Monitors](monitors.html)
-   Parent Containers of various types

