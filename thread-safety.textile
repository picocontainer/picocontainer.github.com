---
layout: article
name: Thread Safety Behaviors
---


h2. Thread Safety

p. When components are created by two threads concurrently, with the intention of the instance being cached, it is possible in a small percentage of cases for the first instance into the cache to be replaced with a second instance. To prevent this, you may want to try one of two behaviors to make the operation thread safe.

h3. Synchronizing

p. Synchronizing wraps object creation in Java's classic %(style1)synchronize% feature:

{% highlight java %}
pico = new DefaultPicoContainer(new Synchronizing().wrap(new Caching()));
pico.addComponent(Apple.class);
Apple a1 = pico.getComponent(Apple.class);
Apple a2 = pico.getComponent(Apple.class); // both the same instance
pico = new DefaultPicoContainer();
pico.as(SYNCHRONIZE, CACHE).addComponent(Apple.class);
Apple a1 = pico.getComponent(Apple.class);
Apple a2 = pico.getComponent(Apple.class); // both the same instance
pico = new PicoBuilder().withSynchronizing().withCaching().build();
pico.addComponent(Apple.class);
Apple a1 = pico.getComponent(Apple.class);
Apple a2 = pico.getComponent(Apple.class); // both the same instance
import static org.picocontainer.behaviors.Behaviors.synchronizing;
import static org.picocontainer.behaviors.Behaviors.caching;
...
pico = new PicoBuilder().withBehaviors(synchronizing(), caching()).build();
pico.addComponent(Apple.class);
Apple a1 = pico.getComponent(Apple.class);
Apple a2 = pico.getComponent(Apple.class); // both the same instance
{% endhighlight %}

h3. Locking

p. Locking wraps object creation in JDK 1.5's ReentrantLock facility. It is suggested that this is a more efficient alternative to the %(style1)Synchronizing% behavior above:

{% highlight java %}
pico = new DefaultPicoContainer(new Locking().wrap(new Caching()));
pico.addComponent(Apple.class);
Apple a1 = pico.getComponent(Apple.class);
Apple a2 = pico.getComponent(Apple.class); // both the same instance
pico = new DefaultPicoContainer();
pico.as(LOCK, CACHE).addComponent(Apple.class);
Apple a1 = pico.getComponent(Apple.class);
Apple a2 = pico.getComponent(Apple.class); // both the same instance
pico = new PicoBuilder().withLocking().withCaching().build();
pico.addComponent(Apple.class);
Apple a1 = pico.getComponent(Apple.class);
Apple a2 = pico.getComponent(Apple.class); // both the same instance
import static org.picocontainer.behaviors.Behaviors.locking;
import static org.picocontainer.behaviors.Behaviors.caching;
...
pico = new PicoBuilder().withBehaviors(synchronizing(), caching()).build();
pico.addComponent(Apple.class);
Apple a1 = pico.getComponent(Apple.class);
Apple a2 = pico.getComponent(Apple.class); // both the same instance
{% endhighlight %}