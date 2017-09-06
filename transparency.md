---
layout: article
name: Transparency
---

p. Transparency, for PicoContainer, means that components do not have to implement any interface or abstraction in order to be components. Moreover, components do not have to adhere to any naming conventions or bundle any meta-data in order to qualify as components.

p. Constructor Injection is what we recommend, so the minimum definition of a component is a simple class that takes its dependencies in its constructor. For Setter Injection, it is a class with dependencies delivered via simple setters. Your components may be interface / implementation separated, but we are not forcing that for when using PicoContainer.

p. If you live with this ideal, you may be able to use the components you design for Pico with other frameworks or containers. I.e. you are not locked in. We suggest you choose other technologies that are similarly light on API and framework, and could be characterized as transparent. Good examples are XStream, Sitemesh and anything that deals with POJOs.

p. Given this goal, we have some exceptions:

h2. Lifecycle

p. Java does not have a Startable interface, nor does it have a Disposable (.Net has IDisposable), so we made our own. You can use ours or your own in a similar design. If you use your own, you may want to extend StartableLifecycleStrategy and override a couple of methods. That's if you need lifecycle at all.

p. If a Startable inerface goes into J2SE, then it will probably be supported.

h2. Annotations

p. We also support @Inject as an annotation for methods and fields. This is a variation of Setter Injection. The annotations are part of our codebase. Again, you can use your own, or others if you override one of the two classes from PicoContainer's codebase *AnnotatedFieldInjection* and *AnnotatedMethodInjection* .

p. Annotations are a break with transparency, so we don't recommend them - stick to vanilla Constructor Injection if you can.

p. If a simple @Inject annotation goes into J2SE, then it will probably be supported.

