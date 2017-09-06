---
layout: article
title: Hiding Implementations
---

## Hiding and Swapping of Implementations

### Implementation Hiding <span class="Gems PicoContainer in Located"></span>

This is where the implementation of the component is hidden from other components using it. The instance cannot be cast back to the implementation. It only works if the type has an interface that it implements.

```java
pico = new DefaultPicoContainer(new ImplementationHiding());
pico.addComponent(Apple.class, AppleImpl.class);
Apple a1 = pico.getComponent(Apple.class); // cannot cast back to AppleImpl

pico = new DefaultPicoContainer();
pico.as(HIDE_IMPL).addComponent(Apple.class, AppleImpl.class);
Apple a1 = pico.getComponent(Apple.class); // cannot cast back to AppleImpl

pico = new PicoBuilder().withImplementationHiding().build();
pico.addComponent(Apple.class, AppleImpl.class);
Apple a1 = pico.getComponent(Apple.class); // cannot cast back to AppleImpl

import static org.picocontainer.behaviors.Behaviors.implementationHiding; 
... 
pico = new PicoBuilder().withBehaviors(implementationHiding()).build();
pico.addComponent(Apple.class, AppleImpl.class);
Apple a1 = pico.getComponent(Apple.class); // cannot cast back to AppleImpl
```

This behavior leverages Reflection's dynamic proxy capability.

There's another implementation hiding behavior in Pico Gems called <span class="style1">AsmImplemenationHiding</span> that leverages ASM to make 'more concrete' hidden implementations than is possible via reflection. It generates real classes using [ASM](http://asm.objectweb.org/)

### Hot Swapping <span class="Gems PicoContainer in Located"></span>

This builds on the <span class="style1">ASMImplementationHiding</span> behavior above, but also allows the hot swapping of component implementations during use. It has implicit caching behavior too.

```java
pico = new DefaultPicoContainer(new HotSwapping());
pico.addComponent(Map.class, HashMap.class);
Map firstMap = pico.getComponent(Map.class);
firstMap.put("foo","bar"); 
HotSwappable hs = (HotSwappable) pico.getComponentAdapter(Map.class); 
Object oldMap = hs.getSwappable().swap(new HashMap());
Map secondMap = pico.getComponent(Map.class); 
secondMap.put("apple","orange"); 
// first map and second map are the same 
// 'foo' is not a key in the map, wereas 'apple' is
```