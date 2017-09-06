---
layout: article
title: AOP Style Interception
---

PicoContainer has a rudimentary Aspect Orientated Programing (AOP) capability with its 'Interception' behavior. With respect to the methods of a component, both before and after invocation, control can be handed to an interceptor. You can intercept a method call:

-   before it is invoked (and optionally veto its invocation, with an alternate return value).
-   after it is invoked (and optionally override the return value).

There are some limitations:

-   it will only work for interface/impl separated components
-   it needs a class that implements the interface in question\* all of the methods need null implementations, even if not intended to be intercepted
-   will (likely) break if there's deep recursion into Intercepted instances
-   you can't intercept an implementation's static methods
-   it is not possible to modify a method's arguments
-   the constructor's invocation for the component is not interceptable
-   the component's fields are not interceptable
-   only one component invoking the methods on another component will be intercepted.\* components and related classes calling their own methods will not be intercepted

```java
public static class BiteReporter implements Apple {
  private Intercepted.Controller controller;
  public BiteReporter(Intercepted.Controller controller) {
    this.controller = controller;
  }
  public boolean takeBite(int grams) { 
    System.out.println("Bite of"+ grams +"grams of apple '"+ controller.getInstance().getName() +"'"); 
    return false; // ignored, but necessary. 
  } 
}
...
pico = new DefaultPicoContainer(new Intercepting());
pico.addComponent(Apple.class, BraeburnApple.class); 
Intercepted intercepted = pico.getComponentAdapter(Apple.class).findAdapterOfType(Intercepted.class); 
intercepted.addPreInvocation(Apple.class, new BiteReporter(intercepted.getController())); 
// see also Intercpeted.addPostInvocation(...) method. 
Apple a1 = pico.getComponent(Apple.class); a1.takeBite(100); 
// prints Bite of 100 grams of apple 'Braeburn' ... irrespective of what else Braeburn.takeBite(int) does.
pico = new DefaultPicoContainer();
pico.as(INTERCEPT).addComponent(Apple.class, BraeburnApple.class); // etc
pico = new PicoBuilder.withInterception().build();
pico.addComponent(Apple.class, BraeburnApple.class); // etc
pico = new PicoBuilder.withBehaviors(interception()).build();
pico.addComponent(Apple.class, BraeburnApple.class); // etc
```

**Fine grained participation in interception**
Assuming you're passing in the Interceptor to the classes you're using for interception of a component, you can participate in the fate of the method call. For a 'pre' invocation, you can veto the calling of the 'real' method.

```java
public boolean takeBite(int grams) { 
  if (grams>50) { 
    controller.veto(); 
  } 
  return false; // will be passed back to the caller. 
}
```

For a 'post' invocation, you can override the return value of the 'real' method.

```java
public boolean takeBite(int grams) { 
  if (grams>50) { 
    controller.override(); 
    Apple realApple = (Apple) controller.getInstance(); 
    realApple.takeBite(-1 * grams); // undo ! 
  } 
  return false; // will be passed back to the caller. 
}
```

Also for a 'post' invocation, you can access the return value of the 'real' method.

```java
public boolean takeBite(int grams) { 
  boolean rv = (boolean) controller.getOriginalRetVal(); 
  if (rv == false) { 
    // do something ! 
  } 
  return true; // ignored as no 'override' 
}
```