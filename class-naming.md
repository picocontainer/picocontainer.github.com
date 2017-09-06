---
layout: article
name: Class Naming
---


p. PicoContainer's class naming changed substantially between 1.0 and 2.0. There are two major influences for this:

# Dan North's"Behavior Driven Design"agenda and his interest in Neuro Linguistic Programming (NLP)
# Steve Yegge's technical fable : "Execution in the Kingdom of Nouns":http://steve-yegge.blogspot.com/2006/03/execution-in-kingdom-of-nouns.html 

p. The take away from both is that we don't have to have long classnames like _CachingComponentAdapterFactory_ and _ImplementationHidingComponentAdapterFactory_ , we can in fact have shorter names, that read better in their intended use:

new DefaultPicoContainer(new Caching());
p. Similarly, method names can play a part in simplicity and meaning for real situations where they are used.

new DefaultPicoContainer(new Caching().wrap(new ImplementationHiding()));
p. Caching, the class, implements an abstraction called BehaviorFactory as do many others. All BehaviorFactory implementations make implementations of Behavior like 'Cached'. Similarly 'ConstructionInjection' (and similar) make implementations of Injector like 'ConstructorInjector' (and similar). Both BehaviorFactory and InjectionFactory are extensions of ComponentFactory.
