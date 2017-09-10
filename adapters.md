---

layout: article
title: Component Adapters
---

### What Are [ComponentAdapters](/javadoc/core/org/picocontainer/ComponentAdapter.html) and why are they useful?

ComponentAdapters are probably PicoContainer's most highly misunderstood (and coincidentally, most powerful) feature. They combine the power of an object factory, and an object interceptor rolled into one.

The key to the component adapter is only one method:

```java
T getComponentInstance(PicoContainer container, Type into) throws PicoCompositionException
```

In essence, the sole purpose of the ComponentAdapter is to provide a PicoContainer with an actual object instance. In fact, [DefaultPicoContainer](/javadoc/core/org/picocontainer/DefaultPicoContainer.html) does not contain a Map of Classes, a Map of object Instances, or anything like that. Instead **it contains a map of ComponentAdapters** .

Each time you use addComponent(..) on PicoContainer, behind the scenes a new ComponentAdapter instance is set up. There is one ComponentAdapter implementation per component instance. The ComponentAdapters can be added to a container directly, or made by the ComponentFactory the container is using. Both for BehaviorFactory and InjectionFactory implementations. Its all hidden from you to make PicoContainer's API appear simple.

### Call Order

So what happens when PicoContainer.getComponent() is called?

![](images/componentAdapterSequence.png)

Our goal with this sequence diagram is to show that the primary function of PicoContainer here is to locate the ComponentAdapter that has been previously registered via [MutablePicoContainer](/javadoc/core/org/picocontainer/MutablePicoContainer.html) .addComponent() or MutablePicoContainer.addAdapter().

### Instantiation and [Injectors](/javadoc/core/org/picocontainer/Injector.html)

Once PicoContainer locates the appropriate ComponentAdapter, it calls the fabled **ComponentAdapter.getComponentInstance()** . A very simple ComponentAdapter could merely call Class.newInstance() on the type of the component being instantiated. Of course, PicoContainer may be small, but it is hardly simple!

PicoContainer provides different ways of instantiating a component through different Injectors. The Injector interface extends ComponentAdapter with some additional services. Implementations of Injectors provide the actual methods of injecting Dependencies. (The reason we first came to discover PicoContainer in the first place!)

The two most popular methods of injecting dependencies are: [SetterInjector](/javadoc/core/org/picocontainer/injectors/SetterInjector.html) , and [ConstructorInjector](/javadoc/core/org/picocontainer/injectors/ConstructorInjector.html) . However, PicoContainer provides other injectors that go beyond the scope of this article. However, just remember that under the hood. If you have code:

```java
MutablePicoContainer pico = new PicoBuilder().withCDI().build(); //withCDI() indicates: Pico: use ConstructorInjector pico.addComponent(MyObject.class); //... More goes by MyObject 
instance = pico.getComponent(MyObject.class);
```

That whenever you call addComponent(), Pico, behind the scenes, is wrapping MyObject.class with a ConstructorInjector ComponentAdapter. When you call Pico.getComponent(), PicoContainer calls the ConstructorInjector, which in turns, uses ConstructorInjection to create the MyObject instance.

### Post Instantiation Modification using [Behaviors](/javadoc/core/org/picocontainer/Behavior.html) .

If the only job of a ComponentAdapter was to construct an object, we would probably call it a *ComponentFactory* and be done with it. However, as you are most assuredly an astute reader, you can clearly tell that since there is more scrollbar left on your browser.... there is more to this story.

You see, ComponentAdapters were meant to be *chained* together using the Decorator pattern. It is this chaining that allows for the "Post Processing" step that is listed on the sequence diagram. There are many things that can be done to an object once it is created.

For example, maybe you don't want a new object instance to be created each and every time you call PicoContainer.getInstance(). Perhaps you want an object to be stateless and shared across multiple objects? (The Spring Framework, and Guice calls this behavior Singleton).

In this case, you would insert in a "Component Adapter Chain", the [Caching](/javadoc/core/org/picocontainer/behaviors/Caching.html) behavior, so the ComponentAdapter Chain looked like this:

Caching -&gt;ConstructorInjector

With an adapter chain like this, the first time PicoContainer.getInstance() is called, Caching would pass the request down to ConstructorInjector. Once it receives the instance returned back, it would store that instance for future calls and return that instance back to the caller. Upon future calls, Caching would return its own *cached* instance. (Thus the name Caching ComponentAdapter instead of SingletonComponentAdapter). Of course, other storage options are possible: HttpSession, ThreadLocal, etc.

Also keep in mind that behaviors are not limited to how an object is stored. For example [HiddenImplementation](/javadoc/core/org/picocontainer/behaviors/HiddenImplementation.html) wraps the instantiated component in a dynamic proxy, thereby enforcing that receiver of that component cannot cast the received interface into an implementation.

### Using ComponentAdapter directly.

Some examples :

```java
public class MyAdapter extends AbstractAdapter {
  private QuantumEntanglement qe = new QuantumEntanglementImpl();
  public MyAdapter() {
    super(QuantumEntanglement.class, QuantumEntanglementImpl.class);
  }
  public Object getComponent(PicoContainer pico) { 
    Auditor a = pico.getComponent(Auditor.class); 
    a.audit("QE used", new Date()); return qe;
  }
  public void verify(PicoContainer container) { 
  } 
}

...

pico = new DefaultPicoContainer();
pico.addAdapter(new MyAdapter()); 
QuantumEntanglement e = pico.getComponent(QuantumEntanglement.class);
```

### Changing the default ComponentAdapter for a new PicoContainer

#### The easy way:

PicoContainer 2.0 has provided a Builder class series to help define which ComponentAdapter to use with a given container by default. There are different builders with each subproject. So PicoContainer has [PicoBuilder](/javadoc/core/org/picocontainer/PicoBuilder.html) , PicoContainer-Gems has [PicoGemsBuilder](/javadoc/gems/org/picocontainer/gems/PicoGemsBuilder.html) , and so on. For the purpose of clarity, we will only use PicoBuilder in this document.

Example 1: Caching Behaviors

```java
pico = new PicoBuilder().withCaching().build();
Example 2: Caching + ImplementationHiding (same imports)

pico = new PicoBuilder().withCaching().withHiddenImplementations().build();
```

In both of these Examples, ConstructorInjection is automatically used by default.

Example 3: Setter Injection

```java
pico = new PicoBuilder().withSDI().build();h4. The slightly harder (but flexible way)
```

Since there is one ComponentAdapter chain per registration in a mutable PicoContainer, there needs to be an object that creates a ComponentAdapter every time MutablePicoContainer.addComponent() is called. Thus enters the ComponentFactories. (Each ComponentFactory creates ComponentAdapter).

The key here is creating the new DefaultPicoContainer with the appropriate ComponentFactories

Example 1: Caching Behaviors:

```java
pico = new DefaultPicoContainer(new Caching().wrap(new ConstructorInjection()));
```

Example 2: Caching + Implementation Hiding

```java
pico = new DefaultPicoContainer(new Caching().wrap(new ImplementationHiding().wrap(new ConstructorInjection)));
```
Example 3: Setter Injection

```java
pico = new DefaultPicoContainer(new SetterInjection());
```

### Changing the default ComponentAdapter when registering objects.

When you modify behaviors, you are, in essence, swapping the default ComponentFactory being used for further registrations. More details about this have been discussed in the section " [Modifying Behaviors](behaviors.html) "

As each ComponentAdapter implementation only does one specific job, you can see that it is quite simple, but the range of possibilities are endless.
