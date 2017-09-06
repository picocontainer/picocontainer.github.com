---
layout: article
title: Other Behaviors
---

## Property Applying

This is where there are a number of setters for a component that will be could be set after instantiation. A way of handing in some configuration if you like.

```java
class Foo {  
  String message;  
  public void setMessage(String message) {  
    this.message = message;  
  }  
  public String toString() {  
    return message;  
  }  
}  
...  
pico = new DefaultPicoContainer(new PropertyApplying());  
pico.addComponent(Foo.class);  
PropertyApplicator pa = (PropertyApplicator) pico.getComponentAdapter(Foo.class);  
pa.setProperty("message", "hello");  
System.out.println(pico.getComponent(Foo.class)); // prints hello  
pico = new DefaultPicoContainer();  
pico.as(APPLY_PROPERTIES).addComponent(Foo.class);  
PropertyApplicator pa = (PropertyApplicator) pico.getComponentAdapter(Foo.class);  
pa.setProperty("message", "hello");  
System.out.println(pico.getComponent(Foo.class)); // prints hello  
pico = new PicoBuilder().withProperties().build();  
pico.addComponent(Foo.class);  
PropertyApplicator pa = (PropertyApplicator) pico.getComponentAdapter(Foo.class);  
pa.setProperty("message", "hello");  
System.out.println(pico.getComponent(Foo.class)); // prints hello  
import static org.picocontainer.behaviors.Behaviors.propertyApplying;  
...  
pico = new PicoBuilder().withBehaviors(propertyApplying()).build();  
pico.addComponent(Foo.class);  
PropertyApplicator pa = (PropertyApplicator) pico.getComponentAdapter(Foo.class);  
pa.setProperty("message", "hello");  
System.out.println(pico.getComponent(Foo.class)); // prints hello  
```

## Handling Behavior Chains

```java
import static org.picocontainer.behaviors.Behaviors.caching;  
import static org.picocontainer.behaviors.Behaviors.propertyApplying;  
...  
pico = new PicoBuilder().withBehaviors(caching(), propertyApplying()).build();  
pico.addComponent(Foo.class);  
Cached cached = (Cached) pico.getComponentAdapter(Foo.class);  
PropertyApplicator pa = (PropertyApplicator) getDelegate(PropertyApplicator.class);  
pa.setProperty("message", "hello");  
System.out.println(pico.getComponent(Foo.class)); // prints hello  
```

## Automatic

This is where a component is going to be instantiated regardless of whether:

-   it is looked up
-   any component that needs it is looked up
-   it is Startable

It is most likely that you're doing this because the component in question is self contained and doing something once only. Alternatively, you're cutting a legacy codebase over from nest-of-singletons to dependency injection in stages.

```java
class Foo { 
  public Foo() { 
    System.out.println("Foo was instantiated"); 
  } 
}
...
pico = new DefaultPicoContainer(new Automatic());
 pico.addComponent(Foo.class);
 pico.addComponent("bar", String.class);
 pico.getComponent("bar"); // Foo instantiated too\.
pico = new DefaultPicoContainer();
 pico.as(AUTOMATIC).addComponent(Foo.class);
 pico.addComponent("bar", String.class);
 pico.getComponent("bar"); // Foo instantiated too\.
pico = new PicoBuilder().withAutomatic().build();
 pico.addComponent(Foo.class);
 pico.addComponent("bar", String.class);
 pico.getComponent("bar"); // Foo instantiated too\.
import static org.picocontainer.behaviors.Behaviors.automatic;
...
pico = new PicoBuilder().withBehaviors(automatic()).build();
 pico.addComponent(Foo.class);
 pico.addComponent("bar", String.class);
 pico.getComponent("bar"); // Foo instantiated too.
```
