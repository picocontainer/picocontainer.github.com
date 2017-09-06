---
layout: article
name: Hiding Implementations
---

h2. Hiding and Swapping of Implementations

h3. Implementation Hiding %(subheading)(Located in PicoContainer Gems)% 

p. This is where the implementation of the component is hidden from other components using it. The instance cannot be cast back to the implementation. It only works if the type has an interface that it implements.

{% highlight java %}
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
{% endhighlight %}

p. This behavior leverages Reflection's dynamic proxy capability.

p. There's another implementation hiding behavior in Pico Gems called %(style1)AsmImplemenationHiding% that leverages ASM to make 'more concrete' hidden implementations than is possible via reflection. It generates real classes using "ASM":http://asm.objectweb.org/ 

h3. Hot Swapping %(subheading)(Located in PicoContainer Gems)% 

p. This builds on the %(style1)ASMImplementationHiding% behavior above, but also allows the hot swapping of component implementations during use. It has implicit caching behavior too.

{% highlight java %}
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
{% endhighlight %}