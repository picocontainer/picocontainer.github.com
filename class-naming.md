------------------------------------------------------------------------

layout: article
name: Class Naming
---

PicoContainer's class naming changed substantially between 1.0 and 2.0. There are two major influences for this:

1.  Dan North's"Behavior Driven Design"agenda and his interest in Neuro Linguistic Programming (NLP)
2.  Steve Yegge's technical fable : [Execution in the Kingdom of Nouns](http://steve-yegge.blogspot.com/2006/03/execution-in-kingdom-of-nouns.html)

The take away from both is that we don't have to have long classnames like *CachingComponentAdapterFactory* and *ImplementationHidingComponentAdapterFactory* , we can in fact have shorter names, that read better in their intended use:

```java
new DefaultPicoContainer(new Caching());
```

Similarly, method names can play a part in simplicity and meaning for real situations where they are used.

```java
new DefaultPicoContainer(new Caching().wrap(new ImplementationHiding()));
```

Caching, the class, implements an abstraction called BehaviorFactory as do many others. All BehaviorFactory implementations make implementations of Behavior like 'Cached'. Similarly 'ConstructionInjection' (and similar) make implementations of Injector like 'ConstructorInjector' (and similar). Both BehaviorFactory and InjectionFactory are extensions of ComponentFactory.
