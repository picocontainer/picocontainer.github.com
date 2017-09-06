---
layout: article
name: Behaviors Overview
---

PicoContainer can add behaviors to components automatically during instantiation.

Behaviors are delivered by passing a BehaviorFactory reference into the PicoContainer instance on construction. For the most part BehaviorFactories are completely stateless, and the Behavior instances they make are not.

### General use

DefaultPicoContainer can take a behavior factory instance in its constructor:

```
pico = new DefaultPicoContainer(new BehaviorClass());
pico.addComponent(Foo.class); 
// other components added 
Foo foo = pico.getcomponent(Foo.class) // Foo instance will be affected by an additional behavior.
```

Or it can take behaviors that wrap each other:

```
pico = new DefaultPicoContainer(new BehaviorClass().wrap(new AnotherBehaviorClass()));
pico.addComponent(Foo.class); 
// other components added 
Foo foo = pico.getcomponent(Foo.class) // Foo instance will be affected by two additional behaviors.
```

Behaviors can be signaled by properties per component:

```
pico = new DefaultPicoContainer();
pico.as(SOME_BEHAVIOR).addComponent(Foo.class); 
// the behavior has a property marking it, and the default component factory understands that property 
// other components added 
Foo foo = pico.getcomponent(Foo.class) 
// Foo instance will be affected by an additional behavior.
```

You can build a container with the applicable property:

```
pico = new PicoBuilder().withXXXBehavior().build();
pico.addComponent(Foo.class); 
// other components added 
Foo foo = pico.getcomponent(Foo.class) 
// Foo instance will be affected by an additional behavior.
```

Behaviors can be chained together by PicoBuilder:

```
import static org.picocontainer.behaviors.Behaviors.xxxxxxx;
import static org.picocontainer.behaviors.Behaviors.yyyyyyy;
...
pico = new PicoBuilder().withBehaviors(xxxxxxx(), yyyyyyy()).build();
 pico.addComponent(Foo.class); 
// other components added 
Foo foo = pico.getcomponent(Foo.class) 
// Foo instance will be affected by two additional behaviors.
```

### Specific Behaviors

#### Caching

Caching is where PicoContainer ensures that the same component instance is returned for second and subsequent getComponent() invocations. See the [Caching](caching.html) behavior page. Other Dependency Injection Containers call this Singleton behavior.

#### Implementation Hiding

Hiding Implementations is where PicoContainer ensures that the component that implements a abstract type can only be used as that abstract type by components that depend on it. Casting back to the implementation is vetoed. See the [Hiding](hiding.html) behavior page.

#### Thread Safety

Synchronizing and Locking are variations on where PicoContainer ensures that components can used in a thread-safe way. See the [Thread Safety](thread-safety.html) behavior page.

#### Interception

AOP Style Interception is where PicoContainer can invoke functionality before or after component method execution. See the [Interception](interception.html) behavior page.

### Where Next?

<span class="callout">The [Component Lifecycle](lifecycle.html) page details the lifecycle concepts applicable to some components</span>
